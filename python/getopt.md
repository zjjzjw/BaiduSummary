# getopt — C-style parser for command line
getopt命令行的C风格解析器

getopt模块是一个用于命令行选项的解析器，其API被设计为C getopt（）函数的用户所熟悉。不熟悉C getopt（）函数或希望编写更少代码并获得更好帮助和错误消息的用户应考虑改用argparse模块。

此模块帮助脚本解析sys.argv中的命令行参数。它支持与Unix getopt（）函数相同的约定（包括“-”和“-”形式的参数的特殊含义）。与GNU软件支持的选项类似的长选项也可以通过可选的第三个参数来使用。

此模块提供两个功能和一个例外：

## getopt.getopt(args, shortopts, longopts=[])
分析命令行选项和参数列表。args是要分析的参数列表，没有对正在运行的程序的前导引用。通常，这意味着sys.argv[1:]。shortopts是脚本想要识别的选项字母字符串，其中的选项要求参数后跟冒号（'：'；i、 例如，与Unix getopt（）使用的格式相同）。

>与GNU getopt（）不同，在一个non-option参数之后，所有进一步的参数也被认为是非选项。这类似于非gnunix系统的工作方式。


## getopt.gnu_getopt(args, shortopts, longopts=[])
此函数的工作方式类似于getopt（），只是默认情况下使用GNU样式的扫描模式。这意味着选项和非选项参数可能是混合的。一旦遇到非选项参数，getopt（）函数就会停止处理选项。


## 实战
```
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
