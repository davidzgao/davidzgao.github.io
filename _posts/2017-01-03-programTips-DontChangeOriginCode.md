---
layout: post
title: 程序编写时的注意--尽量不修改代码
date: 2017-1-3
categories: blog
tags: [技术,编程习惯]
description: 养成好的编程习惯
---
将原先的if-elif-else分支替换成遍历所有的条件类来看一下区别。

###替换前：

`def do_GET(self):`

    try:

        # 文件完整路径
    
        full_path = os.getcwd() + self.path

        # 如果该路径不存在...

        if not os.path.exists(full_path):

            #抛出异常：文件未找到

            raise ServerException("'{0}' not found".format(self.path))

        # 如果该路径是一个文件

        elif os.path.isfile(full_path):

            #调用 handle_file 处理该文件

            self.handle_file(full_path)

        # 如果该路径不是一个文件

        else:

            #抛出异常：该路径为不知名对象

            raise ServerException("Unknown object '{0}'".format(self.path))

    # 处理异常
    except Exception as msg:
        self.handle_error(msg)

### 替换后：

所有可能的情况


`class case_no_file(object):`
    
    '''该路径不存在'''

    def test(self, handler):
        return not os.path.exists(handler.full_path)

    def act(self, handler):
        raise ServerException("'{0}' not found".format(handler.path))


`class case_existing_file(object):`
    
    '''该路径是文件'''

    def test(self, handler):
        return os.path.isfile(handler.full_path)

    def act(self, handler):
        handler.handle_file(handler.full_path)


`class case_always_fail(object):`
    
    '''所有情况都不符合时的默认处理类'''

    def test(self, handler):
        return True

    def act(self, handler):
        raise ServerException("Unknown object '{0}'".format(handler.path))



`Cases = [case_no_file(),
         case_existing_file(),
         case_always_fail()]`

`def do_GET(self):`

    try:

        # 文件完整路径

        full_path = os.getcwd() + self.path

        #遍历所有可能的情况

        for case in self.Cases:

            handler = case()

            #如果满足该类情况

            if handler.test(self):

                #调用相应的act函数

                handler.act(self)

                break

    # 处理异常

    except Exception as msg:

        self.handle_error(msg)

- 结论：这样每当我们需要考虑一个新的情况时，只要新写一个条件处理类然后加到 Cases 中去就行了，是不是比原先在if-elif-else中添加条件的做法看起来更加干净更加清楚呢，毕竟修改原有的代码是一件很有风险的事情，调试起来也非常麻烦。在做功能扩展的同时尽量不要修改原代码是软件开发过程中需要牢记的一点。
