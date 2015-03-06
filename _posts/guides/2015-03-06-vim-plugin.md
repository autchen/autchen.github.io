---
layout: post
title:  "My Vim plugin collection"
date:   2015-03-06 14:03:06
categories: guides
---

This plugin package is mainly configured for developing C/C++ project. Therefore many setup may not be as convenient for all-purpose editing.

For impatience, downloads [here]({{ site.baseurl }}/resources/tools/_vim.tar.gz).

The following are the details of the package.

### Misc

Line number is relative by default for the ease of counting lines and offsets. 81th column is highlighted for code formatting.

{% highlight vim %}
set rnu  
set cc=81   
{% endhighlight %}

Key mappings

insert mode: jj to normal mode, zz to adjust display region, C-l/k to move cursor around.

normal mode: C-h/j/k/l to switch among split window, F12 to close window, F2 to insert date.

{% highlight vim %}
inoremap jj <ESC>  
inoremap zz <ESC>zza  
inoremap <C-l> <Right>  
inoremap <C-k> <Left>  
  
nnoremap <C-h> <C-w>h   
nnoremap <C-j> <C-w>j  
nnoremap <C-k> <C-w>k   
nnoremap <C-l> <C-w>l   
  
map <F2> a<C-R>=strftime("%c")<CR><Esc>""  
map <F12> <ESC>:close<CR>  
{% endhighlight %}

### Taglist: 

[http://www.vim.org/scripts/script.php%3Fscript_id%3D273](http://www.vim.org/scripts/script.php%3Fscript_id%3D273)

Display source code structure and functions, requires [Exuberant ctags](http://ctags.sourceforge.net/), F4 to toggole tag window.

{% highlight vim %}
if MySys() == "windows"
	let Tlist_Ctags_Cmd = '"'.$VIMRUNTIME.'/ctags.exe"' 
elseif MySys() == "linux"
	let Tlist_Ctags_Cmd = '/usr/bin/ctags' 
elseif MySys() == "mac"
        let Tlist_Ctags_Cmd = '/opt/local/bin/ctags'
endif 
nnoremap <silent><F4> :TlistToggle<CR>
let Tlist_Show_One_File = 1
let Tlist_Exit_OnlyWindow = 1
let Tlist_Use_Right_Window = 1
let Tlist_File_Fold_Auto_Close=1
let Tlist_Auto_Open = 0
let Tlist_Auto_Update = 1 
let Tlist_Hightlight_Tag_On_BufEnter = 1 
let Tlist_Enable_Fold_Column = 0 
let Tlist_Process_File_Always = 1 
let Tlist_Display_Prototype = 0 
let Tlist_Compact_Format = 1
{% endhighlight %}

### Nerdtree: 

[http://www.vim.org/scripts/script.php?script_id=1658](http://www.vim.org/scripts/script.php?script_id=1658)

File navigator. F3 to toggle the file tree. m for file operation, x to collapse subtree, C to change directory

{% highlight vim %}
map <F3> :NERDTreeToggle<CR> 
imap <F3> <ESC>:NERDTreeToggle<CR> 
{% endhighlight %}

### A: 

[http://www.vim.org/scripts/script.php?script_id=31](http://www.vim.org/scripts/script.php?script_id=31)

Switch between .h and .c. \aa to switch in current window, \av to open in a split window

{% highlight vim %}
map <leader>av :AV<CR>
map <leader>aa :A<CR>
{% endhighlight %}

### Nerdcommenter: 

[http://www.vim.org/scripts/script.php?script_id=1218](http://www.vim.org/scripts/script.php?script_id=1218)

comment and uncomment code. [number] + \cc to comment, [number] + \cu to uncomment

{% highlight vim %}
let NERDSpaceDelims=1
let NERDCompactSexyComs=1 
{% endhighlight %}

### Doxygen toolkits: 

[http://www.vim.org/scripts/script.php?script_id=987](http://www.vim.org/scripts/script.php?script_id=987)

Generate docs. \dx on functions, \da to insert auther info.

{% highlight vim %}
map <leader>dx :Dox<CR>
map <leader>da :DoxAuthor<CR>
{% endhighlight %}

### SnipMate: 

[http://www.vim.org/scripts/script.php?script_id=2540](http://www.vim.org/scripts/script.php?script_id=2540)

auto templating for code sniplets. type <tab> after keywords such as "for", "if".

The script is modified for compatibility with NeoComplCache. When the auto-completion pops, <tab> rotate around the candidates, otherwise, it triggers sniplet templates.

{% highlight vim %}
fun! TriggerSnippet()
	if exists('g:snipPos')
		return pumvisible() ? "\<C-n>" : snipMate#jumpTabStop(0)
	endif

	let word = matchstr(getline('.'), '\S\+\%'.col('.').'c')
	for scope in [bufnr('%')] + split(&ft, '\.') + ['_']
		let [trigger, snippet] = s:GetSnippet(word, scope)
		if snippet != ''
			if pumvisible()
				call feedkeys("\<C-e>")
			endif
			let col = col('.') - len(trigger)
			sil exe 's/\V'.escape(trigger, '/.').'\%#//'
			return snipMate#expandSnip(snippet, col)
		endif
	endfor

	return pumvisible() ? "\<C-n>" : "\<tab>"
endf
{% endhighlight %}

### NeoComplCache: 

[http://www.vim.org/scripts/script.php?script_id=2620](http://www.vim.org/scripts/script.php?script_id=2620)

auto-completion. <tab> to select candidates. ?? to insert a ->, / to close the pum

{% highlight vim %}
inoremap ?? -><C-X><C-O>
inoremap <expr><CR> pumvisible()?neocomplcache#close_popup():"\<CR>"
inoremap <expr>/ pumvisible()?neocomplcache#cancel_popup():"\/"

let g:neocomplcache_enable_at_startup = 1
let g:neocomplcache_enable_smart_case = 1
let g:neocomplcache_enable_camel_case_completion = 1
let g:neocomplcache_enable_underbar_completion = 1
let g:neocomplcache_min_syntax_length = 3
let g:neocomplcache_lock_buffer_name_pattern = '\*ku\*'

let g:neocomplcache_dictionary_filetype_lists = {
    \ 'default' : '',
    \ 'vimshell' : $HOME.'/.vimshell_hist',
    \ 'scheme' : $HOME.'/.gosh_completions'
    \ }

if !exists('g:neocomplcache_keyword_patterns')
    let g:neocomplcache_keyword_patterns = {}
endif
let g:neocomplcache_keyword_patterns['default'] = '\h\w*'

inoremap <expr><C-g>    neocomplcache#complete_common_string()

inoremap <expr><BS>     neocomplcache#smart_close_popup()."\<BS>"
inoremap <expr><C-y>    neocomplcache#close_popup()
inoremap <expr><C-e>    neocomplcache#cancel_popup() 

let g:neocomplcache_enable_auto_select = 1 
{% endhighlight %}

### Quick-fix

after compilation, e.g. :make, :cn to trigger the fix window

### Ctags: 

[http://ctags.sourceforge.net/](http://ctags.sourceforge.net/)

F6 to generate the tags file. this is essential for C-] jumps to definition

{% highlight vim %}
map <F6> <ESC>:wall<CR>:!ctags -R --c++-kinds=+p --fields=+ialS --extra=+q --langmap=c++:+.cu<CR>
{% endhighlight %}

### Omni cpp completion: 

[http://www.vim.org/scripts/script.php?script_id=1520](http://www.vim.org/scripts/script.php?script_id=1520)

Now the auto-completion understand class and struct

### EchoFunc: 

[http://www.vim.org/scripts/script.php?script_id=1735](http://www.vim.org/scripts/script.php?script_id=1735)

A ( following function name will show the signiture, auto-completion is temporarily disabled to keep the display. ) ends the session and re-activate the auto-completion. Script modified.

{% highlight vim %}
function! EchoFunc()
    let name=s:GetFuncName(getline('.')[:(col('.')-3)])
    if name==''
        call feedkeys(")\<esc>i", 'n')
        return ''
    endif
    call s:GetFunctions(name, 1)
    if len(s:res)==0
        call feedkeys(")\<esc>i", 'n')
        return ''
    endif
    call s:EchoFuncDisplay()
    call neocomplcache#commands#_lock()
    return ''
endfunction

function! EchoFuncClear()
    echo ''
    let s:res=[]
    call neocomplcache#commands#_unlock()
    return ''
endfunction
{% endhighlight %}

