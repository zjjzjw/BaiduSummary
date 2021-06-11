
#实战
```
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
```