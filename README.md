## TAL customizations

### The problem
When varnish tries to schedule a session, but fails to do so, it tries to put it back in the pool so it can be reused. This caused Varnish to "panic" and the workers start dying, causing cache failing.

### The solution
According to this ticket https://www.varnish-cache.org/trac/ticket/1607, it looks as simple as letting varnish rather spawn new sessions than try to re-add the existing session worker.

## How to compile and set up

On a new box, install all the dependecies:
```
sudo apt-get install automake autotools-dev libedit-dev libjemalloc-dev libncurses-dev libpcre3-dev libtool pkg-config python-docutils python-sphinx
```

When done, clone this repo and check out the `fix_1607` branch
```
git clone https://github.com/Stii/Varnish-Cache.git varnish-cache
cd varnish-cache
git fetch && git checkout fix_1607
```

Start compiling!
```
sh autogen.sh
sh configure
make
```

If you have time (in other words, if the world isn't ending like it did 25/11/2014) then run the tests and checks.
This takes quite a while, but if everything passes, you should be golden. If it fails, let us know.
```
make check
```

If all checks passed, you're good to go. 
```
sudo make install
```

Well done, you've succesfully compiled Varnish. Now to get it running as it didn't put everything where `apt-get` would've put it.

### Setting up your compiled version
You need to create the following files and directories:
```
/etc/varnish/<default.vcl> # <- I.o.w. add your VCL 
/etc/varnish/secret
/etc/default/varnish # Your configuration defaults
/etc/init.d/varnish # A startup script
```
In the `/etc/init.d/varnish` startup script, make sure to add `/usr/local/bin` and `/usr/local/sbin` to your path.

Start varnish `sudo /etc/init.d/varnish start`

Should be golden...
