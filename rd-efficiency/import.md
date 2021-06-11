#import

## 基本环境
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

import getopt  #getopt命令行的C风格解析器

import sys 
# 该模块提供对解释器使用或维护的一些变量的访问，以及与解释器强烈交互的函数
# sys.argv argv[0]是脚本名称
# sys.call_tracing（func，args ) 呼叫func(*args)，同时启用跟踪。跟踪状态被保存，然后恢复。这是从调试器从检查点调用，以递归调试其他一些代码。
# sys._current_frames（）返回一个字典，将每个线程的标识符映射到调用该函数时该线程中当前活动的最顶层堆
# sys.exc_info（）此函数返回三个值的元组，这些值提供有关当前正在处理的异常的信息。返回的信息特定于当前线程和当前堆栈帧。如果当前堆栈帧未处理异常，则从调用堆栈帧或其调用者获取信息
# sys.exit 由于exit()最终“only”引发了一个异常，它只会在从主线程调用时退出进程，并且异常不会被截获。

import os
# https://blog.csdn.net/weixin_44897792/article/details/95455084?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162104706516780262582130%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162104706516780262582130&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-95455084.first_rank_v2_pc_rank_v29&utm_term=os&spm=1018.2226.3001.4187
#OS模块简单的来说它是一个Python的系统编程的操作模块，可以处理文件和目录这些我们日常手动需要做的操作。
# os.getcwd() 查看当前所在目录（路径）
# os.chdir() os.chdir(r'/etc/sysconfig/') 切换目录（路径）
# os.curdir、os.pardir 
# os.makedirs() os.makedirs('/home/test/xuan') 生成一个多层递归目录
# os.removedirs() 若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依次类推;
# os.mkdir() 创建一个目录；
# os.rmdir() 删除一个目录，若目录不为空则无法删除，报错
# os.listdir() 显示指定目录下，所有的文件和子目录，包括隐藏文件
# os.remove() 删除文件，不能删除文件夹；
# os.sep 输出操作系统特定的路径分隔符 ，如：windows 为‘\\’,Linux为‘/’；
# os.system() 运行shell命令，直接显示（相当于启动一个全新的shell，然后去执行那条命令，命令执行完成过后，shell直接退出）；
# os.path.split(path) 将path分割成目录和文件名二元组返回;
# os.path.abspath(path) 返回path规范化的绝对路径;
# os.path.dirname(path) 返回path的目录;
# os.path.basename(path) 返回path最后的文件名（一个绝对路径只返回最后的文件名）

Base_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.append(Base_DIR)

from models import Base
from controllers.icafe_bug_controller import IcafeBugController
from controllers.icafe_support_controller import IcafeSupportController
from managers.engine_manager import EngineManager 数据库引擎

```

``` python
def main(argv):
    """
    :param argv:
    :return:
    """
    mode_type = ("icafebug", "icafesupport")  # 所有支持的mode在这里声明
    opt_str = " or ".join(mode_type)
    mode = None  # type see mode_type
    last_modify_start = None  # 最后修改时间的开始
    last_modify_end = None  # 最后修改时间的开始

```

```python
    
    try:
        opts, args = getopt.getopt(argv, 'h', ["mode=", "source=", "mstart=", "mend="])
    except getopt.GetoptError:
        print('import.py <mode> <source> <mstart> <mend>')
        sys.exit(2)
    for opt, arg in opts:
        if opt == '-h':
            print('import.py <mode> <source> <mstart> <mend>')
            sys.exit()
        elif opt == "--mode":
            mode = arg
        elif opt == "--mstart":
            last_modify_start = arg
        elif opt == "--mend":
            last_modify_end = arg

    if mode is None:
        sys.exit("[Error]empty mode arg. " + opt_str)

    if mode in mode_type:
        engine = EngineManager('mysql').get() 
        Base.metadata.create_all(engine)  # 这里会自动创建表,只有在这句执行前被import过的orm类才会创建表
        if mode == "icafebug":
            icafe_bug_controller = IcafeBugController(engine)
            icafe_bug_controller.read(last_modify_start, last_modify_end)
            icafe_bug_controller.input()
        elif mode == "icafesupport":
            icafe_support_controller = IcafeSupportController(engine)
            icafe_support_controller.read(last_modify_start, last_modify_end)
            icafe_support_controller.input()
    else:
        sys.exit("[Error]wrong mode arg. " + opt_str)


if __name__ == '__main__':
    main(sys.argv[1:])

```

## 知识点
### EngineManager 
```python
        engine = EngineManager('mysql').get() 
        Base.metadata.create_all(engine)  # 这里会自动创建表,只有在这句执行前被import过的orm类才会创建表
```

### Base declarative_base
```python
#declarative_base() 创建了一个 BaseModel 类，这个类的子类可以自动与一个表关联。
#https://www.jb51.net/article/49789.htm
from models import Base
from sqlalchemy.ext.declarative import declarative_base

def _to_dict(self):
    """
    to dict
    """
    d = {}
    for c in self.__table__.columns:
        if c.name not in self._hidden_not_safe:
            val = getattr(self, c.name, None)

            # 处理 datetime 格式化
            if isinstance(val, datetime):
                val = val.strftime("%Y-%m-%d %H:%M:%S")

            d.update({
                c.name: val
            })

    return d
```
### Base.metadata
```python
print(Base.metadata)  #输出sql执行的日志
```

### read & input
```
            icafe_bug_controller = IcafeBugController(engine)
            icafe_bug_controller.read(last_modify_start, last_modify_end)
            icafe_bug_controller.input()

```






