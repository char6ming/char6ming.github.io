

## code base

![ebpf](https://cilium.io/static/8a9c7405255021e779dad75631982c1e/62f43/userspace.png)

### higlight text

```java
package com.char6ming.datatoolkit;

import com.hankcs.algorithm.AhoCorasickDoubleArrayTrie;
import com.hankcs.algorithm.AhoCorasickDoubleArrayTrie.Hit;
import com.hankcs.algorithm.AhoCorasickDoubleArrayTrie.IHit;
import lombok.*;
import lombok.extern.log4j.Log4j;

import java.util.*;

@Log4j
public class TextHighlighter {

    @Getter
    @Setter
    @AllArgsConstructor
    public static class HighlightInfo
    {
        String text;
        String highlightText;

        Hit[] fullHits;
        Hit[] greedyHits;

        public String toString() {
            return String.format("\n[\n\t[%s]\n\t[%s]\n\t[%s]\n\t[%s]\n]"
                    , text, Arrays.toString(fullHits), Arrays.toString(greedyHits), highlightText);
        }
    }

    @Getter
    public static class Tag
    {
        String left;
        String right;

        int leftLen;
        int rigtLen;

        public Tag(@NonNull final String left, @NonNull final String right) {
            this.left = left;
            this.right = right;

            this.leftLen = left.length();
            this.rigtLen = right.length();
        }
    }

    // c cover h
    @SneakyThrows
    boolean isRangeContain(@NonNull final Hit c, @NonNull final Hit h) {

        return (c.begin <= h.begin && h.end <= c.end)
                && (c.end - c.begin > h.end - h.begin);
    }

    List<Hit> highlightWithHits(@NonNull final Tag tag, @NonNull final String []tookens, @NonNull final String text) {

        final List<Hit> fullHits     = new LinkedList<Hit>();
        TreeMap<String, String> map = new TreeMap<String, String>();

        for (String key : tookens) {
            map.put(key, key);
        }

        AhoCorasickDoubleArrayTrie<String> act = new AhoCorasickDoubleArrayTrie<String>();
        act.build(map);

        act.parseText(text, new IHit<String>() {

            @Override
            public void hit(int begin, int end, String value) {

                fullHits.add(new Hit(begin, end, value));
            }
        });

        return fullHits;
    }

    @SneakyThrows
    String highlight(@NonNull final Tag tag, @NonNull final String []tookens, @NonNull final String text) {

        List<Hit>  fullHits  = highlightWithHits(tag, tookens, text);
        List<Hit> greedyHits = greedyHits(fullHits);
        Hit[] hitsArray      = fullHits.toArray(new Hit[fullHits.size()]);

        String highlightText = highlightText(text, greedyHits, tag);

        if (log.isInfoEnabled()) {
            log.info(new HighlightInfo(text, highlightText, hitsArray, greedyHits.toArray(new Hit[greedyHits.size()])));
        }

        return highlightText ;
    }

    @SneakyThrows
    private String highlightText(@NonNull final String text, @NonNull final List<Hit> greedyHits, @NonNull final Tag tag) {
        final int tagFullLen = tag.getLeftLen() + tag.getRigtLen();

        StringBuilder highlightText = new StringBuilder(text.length() + tagFullLen * greedyHits.size());

        int left = 0;

        for (int i = 0; i < greedyHits.size(); ++i) {
            Hit h  = greedyHits.get(i);

            highlightText.append(text.substring(left, h.begin) + tag.getLeft() + h.value + tag.getRight());
            left = h.end;
        }

        if (greedyHits.size() > 0) {
            highlightText.append(text.substring(greedyHits.get(greedyHits.size() - 1).end));
        }

        return highlightText.toString();
    }

    @SneakyThrows
    private List<Hit> greedyHits(@NonNull final List<Hit> fullHits) {

        List<Hit> greedyHits = new LinkedList<Hit>();

        if (fullHits.isEmpty()) {

            return greedyHits;
        }

        fullHits.sort(new Comparator<Hit>() {
            @Override
            public int compare(Hit o1, Hit o2) {
                return 0 == o1.begin - o2.begin ? o2.end - o1.end : o1.begin - o2.begin;
            }
        });

        if (!fullHits.isEmpty()) {
            greedyHits.add(fullHits.get(0));
        }

        if (1 == fullHits.size()) {

            return greedyHits;
        }

        for (int i = 1; i < fullHits.size(); ++i) {
            Hit c = fullHits.get(i);
            Hit h = ((LinkedList<Hit>) greedyHits).getLast();

            if (!isRangeContain(h, c)) {

                greedyHits.add(c);
            }
        }

        return greedyHits;
    }

    public static void main(String[] args) {

        TextHighlighter textHighlighter = new TextHighlighter();
        Tag tag = new Tag("<em>", "</em>");
        String[] tookens = new String[]{
                "信", "阿信", "周姐", "周杰", "周", "杰", "兴","伦","马兴", "杰伦", "周杰伦", "司马兴杰"
        };

        final String text = "我们班马兴同学告诉7班的司马兴杰说：”你信不信，周姐说周杰和阿信看见周杰伦在唱歌“";

        String tr = textHighlighter.highlight(tag, tookens, text);

        System.out.println(tr);
    }
}
```

###  etcd、k8s https usage

```java

    final Map<String, String> apiUrls = new TreeMap<String, String>(){{
        put("etcd",      "https://xx.xx.xxx.xxx:8123");
        put("apiserver", "https://" + System.getenv("SERVERAPI"));
    }};

    final  Map <String, Map<String, String>> caFiles = new TreeMap<String, Map<String, String>>(){{
        put ("etcd",  new TreeMap<String, String>(){{
            put("ca",   "/etc/etcd/ssl/ca.pem");
            put("cert", "/etc/etcd/ssl/etcd.pem");
            put("key",  "/etc/etcd/ssl/etcd-key.pem");
        }});

        put ("apiserver", new TreeMap<String, String>(){{
            put("ca",   "/etc/kubernetes/ssl/ca.pem");
            put("cert", "/etc/kubernetes/ssl/admin.pem");
            put("key",  "/etc/kubernetes/ssl/admin-key.pem");
        }});
    }};
    
    
     SslContext sslContext = SslContextBuilder.forClient()
            .trustManager(new File(caFiles.get("etcd").get("ca")))
            .keyManager(new File(caFiles.get("etcd").get("cert")), new File(caFiles.get("etcd").get("key")))
            .build();
    EtcdClient etcdClient = new EtcdClient(sslContext, URI.create(apiUrls.get("etcd")));
    
    
    Config config = new ConfigBuilder().withMasterUrl(apiUrls.get("apiserver"))
            .withTrustCerts(true)
            .withCaCertFile(caFiles.get("apiserver").get("ca"))
            .withClientCertFile(caFiles.get("apiserver").get("cert"))
            .withClientKeyFile(caFiles.get("apiserver").get("key"))
            .build();
    client = new DefaultKubernetesClient(config);

```
