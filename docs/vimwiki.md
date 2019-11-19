vimwiki


## Install:
  sudo apt-get install uuid-dev
  sudo apt-get install libgnutls-dev
  ## Download v2.5 version, not current v2.6 which require the newer g++ compiler.
  cd taskwarrior; cmake -DCMAKE_BUILD_TYPE=release .; make; sudo make install
  sudo pip3 install --upgrade git+git://github.com/tbabej/tasklib@develop
## Conf:
  <Path>: g:vimwiki_list
[Usage:](http://thedarnedestthing.com/vimwiki%20cheatsheet)
  ==Wiki==
  <leader> ws                     List all wikis
  <leader>ww                      Create a new wiki
  [number]<leader>ww              Choose a existed defined wiki
  <leader> wd                     delete wiki page
  <leader> wr                     rename wiki page
  :VimwikiTOC                     Insert contents index at wiki's top

  ==Search==
  :VWS /blog/
  :lopen

  ==List & Task: todo lists==
  <C-Space>                       toggle list item on/off

  ==editing==
  =                               add header level
  -                               remove header level
  +                               create/decorate links
  glm                             increase indent of list item
  gll                             decrease indent of list item
  gl* or gl8                      switch or insert '*' symbol
  gl# or gl3                      switch or insert '#' symbol
  gl-                             switch or insert '-' symbol
  gl1                             switch or insert '1.' symbol

  ==Diary==
  <leader>w<leader>w              open diary index file for wiki
  [number]<leader>wi              open diary index file for wiki
  <leader>w<leader>i              update current diary index
  [number]<leader>w<leader>w      open today’s diary file for wiki
  [number]<leader>w<leader>t      open today’s diary file for wiki in new tab
  <C-Up>                          open previous day’s diary
  <C-Down>                        open next day’s diary

  ==navigation==
  <Enter>                         follow/create wiki link
  <Backspace>                     go back to previous wiki page
  <C-S-CR>                        follow/create wiki link in new tab
  <Tab>                           go to next link on current page
  <S-Tab>                         go to previous link on current page

  ==Anchor navigation== :help vimwiki-anchors
  Every header, tag, and bold text can be used as an anchor.
  To jump to it, use a wikilink: [[file#anchor]], [[#pay rise]]

            = My tasks =
            :todo-lists:
            == Home ==
              - [ ] bathe my dog
            == Work ==
              - [ ] beg for *pay rise*
            == Knitting club ==
            === Knitting projects ===
              - [ ] a *funny pig*
              - [ ] a *scary dog*


  ==Table==
  :VimwikiTable                   create table
  gqq                             reformat t able

  <A-Left>                        move column left
  <A-right>                       move column right
  <CR>                            (insert mode) go down/create cell
  <Tab>                           (insert mode) go next/create cell
  gqq or gww                      reformat table

  ==text objects==
  ah                              section between 2 headings including empty trailing lines
  ih                              section between 2 headings excluding empty trailing lines
  a\                              table cell
  i\                              inner table cell
  ac                              table column
  ic                              inner table column


