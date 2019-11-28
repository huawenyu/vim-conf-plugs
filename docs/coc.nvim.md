coc.nvim
===

# setup

1. Add the plugin to vimrc:

```vim
        Plug 'neoclide/coc.nvim', Cond(Mode(['coder',]), {'do': 'yarn install --frozen-lockfile'})
```
2. install depend server which support lsp, here [ccls](https://github.com/MaskRay/ccls)

```sh
	## If fail, repeat multiple times
	$ brew install ccls
```

3. [Build indexer by ccls](https://github.com/MaskRay/ccls/wiki/FAQ#definitions)

```sh
	ccls --index=. --init='{"clang":{"extraArgs": ["-fms-extensions", "-Wno-microsoft-anon-tag", "-Wno-microsoft", "-Wno-gnu-anonymous-struct"]}}' > /dev/null 2>&1
```

4. config coc.vim how to talk with lsp:

Add ~/.vim/coc-settings.json:

```json
{
  "coc.preferences.useQuickfixForLocations": true,
  "coc.preferences.timeout": 500000,
  "coc.preferences.noselect": false,
  "coc.preferences.preferCompleteThanJumpPlaceholder": false,
  "coc.preferences.acceptSuggestionOnCommitCharacter": false,
  "coc.preferences.diagnostic.displayByAle": false,
  "coc.preferences.formatOnType": false,

  "coc.preferences.localityBonus": false,
  "coc.preferences.codeLens.enable": false,
  "coc.preferences.autoTrigger": "none",
  "coc.suggest.autoTrigger": "none",

  "languageserver": {
    "ccls": {
      "command": "ccls",
      "filetypes": ["c", "cpp", "objc", "objcpp"],
      "rootPatterns": [".ccls", "compile_commands.json", ".vim/", ".git/", ".hg/"],
      "initializationOptions": {
        "cache": {
          "directory": ".ccls-cache"
        },
        "clang": {
          "extraArgs": ["-Wno-microsoft"]
        }
      }
    },
    "lua": {
      "command": "lua-lsp",
      "filetypes": ["lua"]
    }
  },
  "python.jediEnabled": false
}
```

5. Done.

# Config Language-servers

[doc](https://github.com/neoclide/coc.nvim/wiki/Language-servers)

