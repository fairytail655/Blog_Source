---
title: 通过 Doxygen+Breathe+Sphinx 生成代码文档
date: 2021-06-08
categories: 
- Intern
tags:
- 文档规范
---

# 环境

- Ubuntu16.04
- Doxygen
- Breathe, Sphinx (安装在同一 python 环境下)

<!--more-->

# 步骤

1. 通过 **Doxygen** 生成 ***xml*** 文件;
2. 使用 **Sphinx** 建立文档工程;
3. 修改工程下 **Sphinx** 的配置文件, 通过 **Breathe** 导入 ***xml*** 文件.

## Doxygen

### 生成配置文件

```powershell
doxygen -g [fime_name]    # file_name 默认为 Doxyfile
```

### 修改配置文件

推荐模板如下:

```
# 项目名称，将作为于所生成的程序文档首页标题
PROJECT_NAME            = ""

# 文档版本号，可对应于项目版本号，譬如 svn、cvs 所生成的项目版本号
PROJECT_NUMBER          = "1.0.0"

# 程序文档输出目录
OUTPUT_DIRECTORY        =  
 
# 程序文档输入目录 
INPUT                   = 
 
# 程序文档语言环境
OUTPUT_LANGUAGE         = Chinese
DOXYFILE_ENCODING       = UTF-8

# 只对头文件中的文档化信息生成程序文档 
FILE_PATTERNS           = *.h

# 递归遍历当前目录的子目录，寻找被文档化的程序源文件 
RECURSIVE               = YES

# 如果是制作 C 程序文档，该选项必须设为 YES，否则默认生成 C++ 文档格式
OPTIMIZE_OUTPUT_FOR_C   = YES

#提取信息，包含类的私有数据成员和静态成员
EXTRACT_ALL             = yes
EXTRACT_PRIVATE         = yes
EXTRACT_STATIC          = yes

# 对于使用 typedef 定义的结构体、枚举、联合等数据类型，只按照 typedef 定义的类型名进行文档化
TYPEDEF_HIDES_STRUCT    = YES

# 在 C++ 程序文档中，该值可以设置为 NO，而在 C 程序文档中，由于 C 语言没有所谓的域/名字空间这样的概念，所以此处设置为 YES
HIDE_SCOPE_NAMES        = YES

# 让 doxygen 静悄悄地为你生成文档，只有出现警告或错误时，才在终端输出提示信息
QUIET                   = YES

# 递归遍历示例程序目录的子目录，寻找被文档化的程序源文件
EXAMPLE_RECURSIVE       = YES

# 允许程序文档中显示本文档化的函数相互调用关系
REFERENCED_BY_RELATION  = YES
REFERENCES_RELATION     = YES
REFERENCES_LINK_SOURCE  = YES

# 不生成 latex 格式的程序文档
GENERATE_LATEX          = NO

# 在程序文档中允许以图例形式显示函数调用关系，前提是你已经安装了 graphviz 软件包
HAVE_DOT                = YES
CALL_GRAPH              = YES
CALLER_GRAPH            = YES

# 在最后生成的文档中，把所有的源代码包含在其中
SOURCE_BROWSER          = YES

# 这会在HTML文档中，添加一个侧边栏，并以树状结构显示包、类、接口等的关系
# GENERATE_TREEVIEW     ＝ ALL

# 生成 xml 格式的程序文档, 为了能够被 Sphinx 使用
GENERATE_XML            = YES
```

### 生成 ***xml*** 文件

```powershell
doxygen cfg_file      # cfg_file 为修改好的配置文件
```

使用命令后, 可以看到目录下新增了与配置文件同名的文件夹, 里面有 ***html*** 和 ***xml*** 两个子目录, 其中 ***xml*** 是后面需要用到的.

## Sphinx, Breathe

### 建立初始工程

```powershell
sphinx-quickstart
```

使用命令后提示配置工程, 完成后目录结构如下 (build, source分离):

```
---
  |--- build            # 用于保存编译后生成的文件
  |--- source           # 用于保存资源文件(可以放置自定义 rst 文件等)
    |--- _static
    |--- _templates
    |--- conf.py        # 工程配置文件
    |--- index.rst      # 总目录文件
  |--- make.bat
  |--- Makefile         # 用于执行 make 编译
```

### 修改配置文件 conf.py

目的是通过 **Breathe** 导入 **Doxygen** 生成的 ***xml*** 文件 ([Breathe 文档](https://breathe.readthedocs.io/en/latest/quickstart.html)\).

总结如下:

- **Breathe** 和 **Doxygen** 在同一 python 环境下的话, 无需配置 **Breathe** 路径.
- ***extensions*** 字段新增 `'breathe'` 
- 添加 ***xml*** 文件路径/工程
  ```
  breathe_projects = {                            # 工程名为 Doxygen 配置文件 PROJECT_NAME,
    "myproject1": "/home/me/docproj/doxyxml1/",   # 每个工程名对应一个 xml 文件夹,
    "myproject2": "/home/me/docproj/doxyxml2/",   # 用于被 rst 文件指令调用,
    ...                                           # 如 doxygenindex
  }
  breathe_default_project = "myproject"           # 默认工程名
  ```

### 在 rst 文件中使用

如:

```
directives:

.. doxygenindex::
.. doxygenfunction::
.. doxygenstruct::
.. doxygenenum::
.. doxygentypedef::
.. doxygenclass::

example:

.. doxygenindex::
    :project: PROJECT_NAME          # conf.py 中 breathe_projects 包含的项目名
```
