ccls
===


## [ccls](clang-based)

(https://github.com/MaskRay/ccls)

### Install
```sh
    $ sudo apt install zlib1g-dev libncurses-dev
    $ sudo apt install clang libclang-dev
    $ brew install ccls
```

### run to build indexer

https://github.com/MaskRay/ccls/wiki/Customization#initialization-options

So we can add ccls to the tag script using it replace cscope, but keep ctags.
For a larger project, you'll need .ccls or compile_commands.json in your project root.

There are four possible ways to customize the behavior of the ccls server.
- The ccls server command line.
  - `--index`, source-code dir, also our current project root dir.
  - `--init`,  parameter used by ccls, here assign the generate output dest dir, is /tmp/ccls.
- The .ccls file, if it exists.
- The `compile_commands.json` file, if it exists.
  - If neither exists, then when ccls starts it will not index anything,
      instead it will wait for LSP clients to open files and index only those files.
- Initialization options:
  - could be command-line's parameter by `--init='<json-string>'`
  - or, from current project dir's the config file '.ccls'.
  - parameters samples:
    - {"clang": {"excludeArgs": ["-frounding-math"]}}
    - {"clang": {"extraArgs": ["-fms-extentions", "-O0 -g"]}}
    - {"index": {"initialBlacklist": ["."]}}        <=== avoid index unless you visit those files

#### compile config: compile_commands.json

https://github.com/MaskRay/ccls/wiki/Project-Setup

A lot of times, we can share `compile_commands.json` between different bugs but the same version.

    $ brew install bear     <== the tool bear will auto generate the `compile_commands.json` by current existed Makefile
    $ bear make image -s -j6 > /dev/null

#### config file: .ccls

By default .ccls compiler flags are applied only to files not listed in compile_commands.json.
If the directive `%compile_commands.json` appears in .ccls then after compile_commands.json is parsed,
   the rest of the .ccls arguments will be appended to the compiler flags for files found in compile_commands.json.

```.ccls
%compile_commands.json
%c -std=c11
%cpp -std=c++14
%c %cpp -pthread
%h %hpp --include=Global.h
-Iinc
```

#### build the current project dir's indexer

```sh
    ### ccls will merge 'compile_commands.json' + '.ccls' together, then call clang
    ###   the project's config '.ccls' file is be a filter for compile_commands.json, for exemple:
    ###     + add/remove flags to clang.
    ###     + the options of ccls for howto build the index.
    $ ccls --index=. > /dev/null 2&>1 &
    $ ccls --index=. --init='{"cache": {"directory": "/tmp/ccls"}}'

    ### convince Clang to use the GCC header files
    $ ccls --index=. --init='{"clang":{"extraArgs": ["--gcc-toolchain=/usr"]}}'
    $ ccls --index=. --init='{"clang":{"extraArgs": ["-fms-extensions", "--gcc-toolchain=/usr"]}}'

    ### compiled with MSVC, you may set the compiler driver in .ccls to clang-cl
    $ ccls --index=. --init='{"clang":{"extraArgs": ["-fms-extensions", "-fms-compatibility", "-fdelayed-template-parsing"]}}'

    ### build linux kernel
    $ ccls -index ~/Dev/Linux -init='{"clang":
        {"excludeArgs":["-falign-jumps=1","-falign-loops=1","-fconserve-stack","-fmerge-constants",
            "-fno-code-hoisting","-fno-schedule-insns","-fno-sched-pressure","-fno-var-tracking-assignments",
            "-fsched-pressure", "-mhard-float","-mindirect-branch-register","-mindirect-branch=thunk-inline",
            "-mpreferred-stack-boundary=2","-mpreferred-stack-boundary=3","-mpreferred-stack-boundary=4",
            "-mrecord-mcount","-mindirect-branch=thunk-extern","-mno-fp-ret-in-387","-mskip-rax-setup",
            "--param=allow-store-data-races=0","-Wa,arch/x86/kernel/macros.s","-Wa,-"],
        "extraArgs":["--gcc-toolchain=/usr"]}}'

    $ cat ~/.vim/coc-settings.json
```
### troubleshooting

    `ccls` options: -log-file=/tmp/ccls.log -v=1

```sh
    ### Dump LSP requests/responses On Linux:
    # sysdig
    sudo sysdig -As999 --unbuffered -p '%evt.type %proc.name %evt.buffer' "proc.exe contains ccls and fd.type=pipe" | egrep -v '^Content|^$'

    # strace
    strace -s999 -e read,write -fp $(pgrep -fn ccls)
```

### Maximum number of file descriptors

Some projects may require more than 1000 file descriptors. Remember to increase RLIMIT_NOFILE.

    $ ulimit -n 32768
    $ cat /etc/security/limits.conf:
        * hard  nofile    32768
        * soft  nofile    32768

## coc.vim

https://github.com/MaskRay/ccls/wiki/coc.nvim

[All the avalable options](https://github.com/neoclide/coc.nvim/blob/master/doc/coc.txt)

### metho-1, config by .vimrc

```vim
	call coc#config('coc.preferences', {
		\ 'timeout': 1000,
		\ 'hoverTarget': 'echo',
		\ 'languageserver': {
		\   'ccls': {
		\     "command": "ccls",
      		\     "trace.server": "verbose",
      		\     "filetypes": ["c", "cpp", "objc", "objcpp"]
		\   }
		\ }
		\})
```

### method-2, config by itself config file '~/.vim/coc-settings.json'

There're two types of user config files, one for vim global, another for project-related:
- The user configuration is named as `coc-settings.json`
    and placed inside the folder $XDG_CONFIG_HOME/nvim or $HOME/.config/nvim by default（or $HOME/.vim for vim).
    Run the command `:CocConfig` to open your user configuration file in vim directly.
- The workspace configuration should be named `coc-settings.json` and be in the directory `<current-proj>/.vim/coc-settings.json`.
    After a file is opened in vim, this directory is resolved from the parent directories of that file.

The active configuration is the merged result of the 'default', 'user' and 'workspace' configuration files,
    the later one has the highest priority.

```json
      {
        "coc.preferences.useQuickfixForLocations": true,

        "languageserver": {
          "ccls": {
            "command": "ccls",
            "filetypes": ["c", "cpp", "objc", "objcpp"],
            "rootPatterns": [".ccls", "compile_commands.json", ".vim/", ".git/", ".hg/"],
            "initializationOptions": {
              "cache": {
                "directory": ".ccls-cache"
              }
            }
          }
        }
      }
```

