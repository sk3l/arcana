# Building ViM from Source

You may wish to compile your own ViM for variety reasons, e.g. your distro's package manager copy is outdate, you wish to enable ViM features not exposed by the distro package, etc.

Follow the steps below to set up your own copy of ViM, with source version and options of your choice:

1. Retrieve source code of Vim from [GitHub](https://github.com/vim/); you can use `--depth` git option to omit repo history if you're only interested in tip of master (i.e. get the latest)

   git clone --depth 1 https://github.com/vim/vim.git

2. Use the host package manager to install any compile/run time dependencies you wish add to your ViM build. For example, using Red Hat YUM, you can pull in packages required for ViM Lua and Python features like thus:

   sudo yum install -y lua lua-devel luajit luajit-devel python python-devel python36 python36-devel

3. Change directory into `vim/src` folder; run `./configure` script with features you wish to turn on, for example:

   ./configure \
     --disable-nls \
     --enable-cscope \
     --enable-gui=no \
     --enable-multibyte  \
     --enable-luainterp \
     --with-luajit \
     --enable-pythoninterp=yes \
     --with-python-config-dir=/lib64/python2.7/config \
     --enable-python3interp=yes \
     --with-python3-config-dir=/lib64/python3.6/config-3.6m-x86_64-linux-gnu \
     --prefix=$HOME/.local/vim \
     --with-features=huge  \
     --with-tlib=ncurses \
     --without-x \
     --enable-fail-if-missing

4. Assuming successful completion of `configure`, go ahead and install:

   make && make install

5. If desired, alias your freshly-minted personal ViM binary for convenience (e.g. in .bashrc)

   alias vim="/home/mskelton8/.local/vim/bin/vim"
