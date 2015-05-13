# 配置 sublinetext 3

为了统一源码格式，推荐对 sublinetext 3 进行如下一些配置，如去除行尾空格，使用４个空格替代 Tab 键等等。

* 下载
    进入[http://www.sublimetext.com/3](http://www.sublimetext.com/3)下载相应的版本

* 安装 package control
    使用<code>Ctrl+`</code>快捷键或者通过<code>View->Show Console</code>菜单打开下方的命令行，粘贴如下代码：
    ``` shell
    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
    ```

* 自定义设置：
    选择菜单栏<code>Preferences -> Settings – More -> Syntax Specific – User</code>，在打开的文件中追加下面代码的最后四行，注意被接续行的行尾用<code>,</code>结尾：
    ``` shell
    {
    "color_scheme": "Packages/Color Scheme - Default/IDLE.tmTheme",
    "tab_size": 4,
    "draw_white_space": "all",
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true
    }
    ```
