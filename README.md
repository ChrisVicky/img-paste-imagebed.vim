# img-paste.vim

Yet simple tool to paste images into markdown files

## Use Case

You are editing a markdown file and have an image on the clipboard and want to paste it into the document as the text `![](img/image1.png)`. Instead of first copying it to that directoryk you want to do it with a single `<leader>p` key press in Vim. So it hooks `<leader>p`, checks if you are editing a Markdown file, saves the image from the clipboard to the location `img/image1.png`, and inserts `![](img/image1.png)` into the file.

By default, the location of the saved file (`img/image1.png`) and the in-text reference (`![](img/image1.png`) are identical. You can change this behavior by specyfing an absolute path to save the file (`let g:mdip_imgdir_absolute = /absolute/path/to/imgdir` on linux) and a different path for in-text references (`let g:mdip_imgdir_intext = /relative/path/to/imgdir` on linux).

## Installation

Using Vundle

```
Plugin 'img-paste-devs/img-paste.vim'
```

## Usage

Add to .vimrc

```
autocmd FileType markdown nmap <buffer><silent> <leader>p :call mdip#MarkdownClipboardImage()<CR>
" there are some defaults for image directory and image name, you can change them
" let g:mdip_imgdir = 'img'
" let g:mdip_imgname = 'image'
```

### Extend to other markup languages

Simply add a custom paste function that accepts the relative path to the image as an argument, and set `g:PasteImageFunction` to the name of your function. E.g.

```
function! g:LatexPasteImage(relpath)
    execute "normal! i\\includegraphics{" . a:relpath . "}\r\\caption{I"
    let ipos = getcurpos()
    execute "normal! a" . "mage}"
    call setpos('.', ipos)
    execute "normal! ve\<C-g>"
endfunction
```

Then in your .vimrc:

```
autocmd FileType markdown let g:PasteImageFunction = 'g:MarkdownPasteImage'
autocmd FileType tex let g:PasteImageFunction = 'g:LatexPasteImage'
```

The former sets the (default) markdown paste function for markdown files, while the latter sets the new latex paste function to be used in latex/tex files. The above LatesPasteImage has already been added to the plugin, see `plugin/mdip.vim`. Existing paste functions:

Finally, add the file type (e.g. `tex`) to the first line you added, as

```
autocmd FileType markdown,tex nmap <buffer><silent> <leader>p :call mdip#MarkdownClipboardImage()<CR>
                        '----'
```

| Filetype | Function name      | Content                                  |
| -------- | ------------------ | ---------------------------------------- |
| Markdown | MarkdownPasteImage | `![Image](path)`                         |
| Latex    | LatexPasteImage    | `\includegraphics{path} \caption{Image}` |
| N/A      | EmptyPasteImage    | `path`                                   |

PRs welcome

### For linux user

This plugin gets clipboard content by running the `xclip` command.

install `xclip` first.

## Acknowledgements

I'm not yet perfect at writing vim plugins but I managed to do it. Thanks to [Karl Yngve Lervåg](https://vi.stackexchange.com/users/21/karl-yngve-lerv%C3%A5g) and [Rich](https://vi.stackexchange.com/users/343/rich) for help on [vi.stackexchange.com](https://vi.stackexchange.com/questions/14114/paste-link-to-image-in-clipboard-when-editing-markdown) where they proposed a solution for my use case.

## New Features

### 2023-09-25 - Image bed integration

- Additional Configuration: (in `lua`)

```lua
vim.g.mdip_upload = "~/.local/bin/upload-img-github"
```

or in `vimscript`

```vimrc
let g:mdip_upload= "~/.local/bin/upload-img-github"
```

- `upload-img-github` is an image-bed host that does One and the only thing: **INPUT** a path of a locally stored image and **OUTPUT** a url of its path in the internet.
- I have implemented an image bed via Github in Golang: <https://github.com/ChrisVicky/image-bed-go>
- ![Vim-Integration](https://raw.githubusercontent.com/ChrisVicky/image-bed/main/2023-10/vim-image-bed-integration.gif)

### 2022-12-22 - Image with Title

1. HTML Style as default
   Example

```html
<center>
  <img
    style="border-radius: 0.3125em;box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="img/image_xxxx-xx-xx.png"
  /><br />
  <div
    style="color:orange; border-bottom: 1px solid #d9d9d9;display: inline-block;color: #999;padding: 2px;"
  >
    img
  </div>
</center>
```

Add to .vimrc

```vimrc
autocmd FileType markdown nmap <buffer><silent> <leader>tt :call mdip#MarkdownClipboardImage()<CR><ESC>
autocmd FileType markdown nmap <buffer><silent> <leader>pp :call mdip#MarkdownClipboardImageTitleMode()<CR><ESC>k$2F>
```
