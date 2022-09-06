# PCRE2 complie

* [source code](https://github.com/PCRE2Project/pcre2/releases/tag/pcre2-10.40)

* env 3.10.0-327.el7.x86_64

* if use autotools 
    ```shell:
    $ yum install libtool
    ```

* generate an configue file
    ```shell
    $ ./autogen.sh
    ```
    
* add c99 flag run configue
    ```shell
    $ ./configure CFLAGS='-std=c99'
    ```
    
* build with make 
    ```shell
    $ make
    ```
