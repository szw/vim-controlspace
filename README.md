<div align="center">
<img alt="Vim-CtrlSpace" src="https://raw.github.com/vim-ctrlspace/vim-ctrlspace/master/gfx/logo5.png" />
<br><br>
</div>

Welcome to **Vim-CtrlSpace** which provides

* tabs / buffers / files management,
* fast fuzzy searching (powered by Go),
* workspaces (sessions), and
* bookmarks for your favorite projects.

If you have already starred this repo, thank you!
If you have a question, a feature request, or a new idea, then don't hesitate to post new issues or pull requests:
Collaboration is the most awesome thing in the open source community!

# Idea by Analogy

`Vim-CtrlSpace` interface is a window you can invoke by pressing `<C-Space>`.
(The plugin name follows the convention of naming fuzzy-search plugins after their default mappings, like _Command-T_ or _CtrlP_)
The window displays a list of items that you can select with the `<j>`,
`<k>`, and `<CR>` keys.

<p align="center">
<img alt="Vim-CtrlSpace" src="https://raw.github.com/vim-ctrlspace/vim-ctrlspace/master/gfx/cs5_window.png" />
</p>

`Vim-CtrlSpace` can display five lists of:

* Buffers
* Files
* Tabs
* Workspaces and
* Bookmarks.

If Vim is a writing desk, then your projects are like drawers:

- The **Bookmark List** simply displays your favorite projects.
- To get documents from a drawer you would need a **File List**.
    It allows you to easily look up contents of a given project.
- Once you locate and pick up a file it becomes a **buffer**.
    A buffer is like a sheet of paper lying on the desk.
    Sometimes you can have a blank piece of paper -- that's a new unsaved buffer.
    It would become eventually a file on the disk once saved (put into a drawer).
    To manage all buffers on the desk you would need a **Buffer List**.

So far our analogy is fairly simple.
This workflow is straightforward but with a large amount of files it is hard to keep an overview.
Therefore each tab holds a **separate** list of buffers, so that each tab, along the work space analogy, is a pile of documents.

With tabs you can, for example:

* group related buffers,
* extract to other tabs,
* name them accordingly, or
* move or copy them.

Tabs usage in **Vim-CtrlSpace** is quite more extensive than in Vim.
This is because they serve mainly as independent buffer lists, so you are
likely to have plenty of them.
Tabs can be accessed and managed within **Tab List**.

- All your buffers, tabs, and tab layouts can be persisted as a workspace.
    It's like taking a picture of your desk with an instant camera.
    You can save multiple workspaces per project with **Workspace List**.

# Getting Started

## Installation

Installing using [vim-plug](https://github.com/junegunn/vim-plug) is
recommended for its post-install/post-update hook support, which enables
Linux/macOS/*BSD users to simply add the following line to their `.vimrc` or
`init.vim`:

```VimL
Plug 'vim-ctrlspace/vim-ctrlspace', {'do': 'bin/fetch-engine.sh'}
```

Other plugin managers supporting install/update hooks, such as
[dein.vim](https://github.com/Shougo/dein.vim) can also be used to
automatically fetch the appropriate engine binaries by adjusting the above
installation line accordingly.

The `fetch-engine.sh` script detects your system's OS and
architecture, and downloads the corresponding pre-compiled
binary stored in the repo's [`file_engine_binaries`
branch](https://github.com/vim-ctrlspace/vim-ctrlspace/tree/file_engine_binaries/bin).

The script can optionally accept one argument, the name of the file engine.
So if for example you know you'd need `file_engine_linux_mips64`, which is an
architecture the script does not handle, you can just do:

```VimL
Plug 'vim-ctrlspace/vim-ctrlspace', {'do': 'bin/fetch-engine.sh file_engine_linux_mips64'}
```

When all that fails (incorrect hardware detection, no `curl`, etc.), you
can always just manually download the needed file engine binary from the
storage branch above, place it in the `bin/` directory of your installed
`vim-ctrlspace`, and should be good to go. Alternatively, compiling the binary
yourself, whose source can be found at `go/file_engine.go`, is also an option.

If you're using Cygwin, MinGW or WSL, the shell script should work
(though this isn't tested). However as of right now, there is no
`fetch-engine.cmd` script supporting Windows proper (PR welcome!), so users
wishing to use the file engine under Windows must also manually fetch
`file_engine_windows_amd64.exe` from the above location.

And if all of the above still fails, `vim-ctrlSpace` will simply fall back to
using Vim built-ins as its file engine. You can check if this is the case by
pressing `<?>` from any of the list modes, where the name of the engine in use
will be shown at the top.


## Basic Settings

First please make sure that you set `nocompatible` and `hidden` options,
and set `encoding=utf-8` (as required by the plugin) in your `.vimrc`: >

```VimL
set nocompatible
set hidden
set encoding=utf-8
```

If you feel brave enough **turn off** tabline:

```VimL
set showtabline=0
```

Tabline in Vim has very limited capabilities and as Vim-CtrlSpace makes
use of tabs intensively, tabline would just get in your way. **Tab List**
(`<l>`) makes tabline obsolete ;).


### Neovim

Neovim requires adding the following line to your `.vimrc` or `init.vim`:

```VimL
let g:CtrlSpaceDefaultMappingKey = "<C-space> "
```

Note the trailing space at the end of the mapping. Neovim doesn't mind
it, but it makes vim-ctrlspace's "is the mapping left at default" check
fail so it won't change the mapping to `<nul>`.


### Go Engine

The plugin provides engine compiled for popular operating systems and
architectures. By default it will attempt to detect your os and
architecture. To see if auto detection was successful press `<?>`.

To speed up the startup of Vim, replace it by a custom simpler one that
restricts to those architectures most probably used by you, and does not
involve system calls. For example, if you use
[vim-plug](https://github.com/junegunn/vim-plug), then by adding to your
`vimrc`:

```vim
if has('win32')
    let s:vimfiles = '~/vimfiles'
    let s:os   = 'windows'
else
    let s:vimfiles = '~/.vim'
    if has('mac') || has('gui_macvim')
        let s:os = 'darwin'
    else
    " elseif has('gui_gtk2') || has('gui_gtk3')
        let s:os = 'linux'
    endif
endif

let g:CtrlSpaceFileEngine = s:vimfiles . '/plugged/vim-ctrlspace' . '/bin/file_engine_' . s:os . '_amd64'
```

The file engine binaries have been compiled for various OS's and CPU
types, but only those for Linux, MacOS and Windows on 64 bit architectures
are available in the git repository. The other versions for their 32 bit
architecture counterparts, as well as for FreeBSD, NetBSD and OpenBSD
on `ARM`, `MIPS`, `amd64` and `32` bit architectures can be downloaded at:

	<https://git.io/vim-ctrlspace-release-all_os_file_engines>

To find more about setting up the file engines, check:

```VimL
:help g:CtrlSpaceFileEngine
```

<!-- __NOTE__: in a future major release of the plugin, the `g:CtrlSpaceFileEngine` -->
<!-- option will be deprected and eventually removed, as we move towards automatic -->
<!-- engine detection out of the box (see [Installation](#Installation)). -->


### Symbols

Vim-Ctrlspace displays icons in the UI if your font supports UTF8, or
ASCII characters as a fallback. Some symbols (glyphs) might not look well
with the font you are using, so feel free to change and adjust them.

This is the config I use for Inconsolata font in MacVim:

```VimL
if has("gui_running")
    " Settings for MacVim and Inconsolata font
    let g:CtrlSpaceSymbols = { "File": "◯", "CTab": "▣", "Tabs": "▢" }
endif
```

Since it's impossible to provide universal character set that would look well
on any machine, therefore the fine tuning is left up to you.

You can find more about this tuning option in the plugin help:

```VimL
:help g:CtrlSpaceSymbols
```

If you feel that you have found a better symbol for a given view, you are
more than welcome to open a pull request.


### Glob Command

Another important setting is the *Glob* command. This command is used to
collect all files in your project directory. Specifically, I recommend
that you install and use `ag`, as it respects `.gitignore` rules and is
really fast. Once it's installed you can add this line to your `.vimrc`:

```VimL
if executable("ag")
    let g:CtrlSpaceGlobCommand = 'ag -l --nocolor -g ""'
endif
```

### Search Timing

If you usually have to deal with huge projects having 100 000 files you
can increase plugin fuzzy search delay to make it even more responsible by
providing a higher `g:CtrlSpaceSearchTiming` value:

```VimL
let g:CtrlSpaceSearchTiming = 500
```

### Colors

Finally, you can adjust some plugin colors. By default plugin uses
the following setup:

```VimL
hi link CtrlSpaceNormal   PMenu
hi link CtrlSpaceSelected PMenuSel
hi link CtrlSpaceSearch   Search
hi link CtrlSpaceStatus   StatusLine
```

However some color schemes show search results with the same colors as
PMenu groups. If that's your case try to link CtrlSpaceSearch highlight
group to IncSearch instead:

```VimL
hi link CtrlSpaceSearch IncSearch
```

Of course nothing prevents you from providing your own highlighting, for example:

```VimL
hi CtrlSpaceSearch guifg=#cb4b16 guibg=NONE gui=bold ctermfg=9 ctermbg=NONE term=bold cterm=bold
```


## First Steps

Alright! You've hopefully installed, configured Vim-CtrlSpace, and
restarted Vim (otherwise do it!). Now you're wondering how to start using
this thing.

First, you need to select a project. Vim operates in a directory,
described as `CWD` (_Current Working Directory_). If you've just started
a MacVim it's probably pointing to your home directory (issue `:pwd` to
check it).

I advise you to add a project to the Bookmark List by opening the plugin
window (`<C-Space>`) and pressing `<b>`. The plugin will ask for a project
directory.

Make sure that the path **is not your home directory**. Otherwise the
plugin will start indexing all your files which will be pointless and
resource exhaustive. Be concrete and provide a real path to a project.
Once your bookmark is created, you can go there with `<CR>`.

Now open some files with `<o>`. Finally save a workspace with `<w>` by
providing your first workspace name.

For more information please check out Vim-CtrlSpace help directly in Vim:

```VimL
:help ctrlspace
```

For key reference press `<?>` inside the plugin window.

## Fuzzy Search Hints

If you are used to hitting the `<ctrl-P>` key combination for fuzzy search, add
this to your .vimrc file:

```VimL
nnoremap <silent><C-p> :CtrlSpace O<CR>
```

Be sure to remember to refresh your search file list using `<r>` command.

## Automatically Saving Workspace

Ctrl-Space can automatically save your workspace status based on configurations below:

```VimL
let g:CtrlSpaceLoadLastWorkspaceOnStart = 1
let g:CtrlSpaceSaveWorkspaceOnSwitch = 1
let g:CtrlSpaceSaveWorkspaceOnExit = 1
```

# Authors and License

Copyright &copy; 2013-2020 [Szymon Wrozynski and
Contributors](https://github.com/vim-ctrlspace/vim-ctrlspace/graphs/contributors).
Licensed under [MIT
License](https://github.com/vim-ctrlspace/vim-ctrlspace/blob/master/plugin/ctrlspace.vim#L5-L26)
conditions.

**Vim-CtrlSpace** is inspired by Robert Lillack plugin [VIM
bufferlist](https://github.com/roblillack/vim-bufferlist) &copy; 2005
Robert Lillack. Moreover some concepts and inspiration has been taken from
[Vim-Tabber](https://github.com/fweep/vim-tabber) by Jim Steward and
[Tabline](https://github.com/mkitt/tabline.vim) by Matthew Kitt.

Special thanks to [Wojtek Ryrych](https://github.com/ryrych) for help and
patience ;) and all
[Contributors](https://github.com/vim-ctrlspace/vim-ctrlspace/graphs/contributors).
