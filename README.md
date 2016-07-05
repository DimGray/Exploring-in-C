# Exploring-in-C
Knowledge discovery in K&amp;R C

第8章 UNIX系统接口
   
    8.2 低级I/O————read和write
        
        在这一节中说明了如何用read和write构造getchar函数，而下面让我们看一下getchar函数在C标准库中是怎样实现的：
        
        int getchar（void）
        {
            return fgetc(stdin);
        }
        
        在标准库中，getchar的实现只有一句，调用了一个fgetc函数，参数是标准输入流stdin.
        下面再让我们看一下fgetc这个函数究竟是怎样的：
        
        int fgetc (FILE *str)
        {
            if (0 < str->_Nback）
            {
                if (--str->_Nback == 0)
                {
                    str->_Rend = str->_Rsave;
                }
                return str->_Back[str->_Nback];
            }
            if (str->_Next < str->_Rend)
            {
                ;
            }
            else if (_Frprep(str) <= 0)
            {
                return EOF;
            }
            return *str->_Next++;
        }
        
        初看这个函数可能会不懂，不过其大意如下：
        首先去寻找被ungetc函数推回的字符，如果不存在，就测试是否有其它字符在缓冲区中.
        它尝试调用_Frprep函数去填充一个空的缓冲区，_Frprep函数描述如下：这个函数返回一个负值表示a read error，
        返回一个零值表示end of file,返回一个正值表示缓冲流包含字符.
        再之后则返回输入流中要被读或写的下一个字符.
        以及下面是一些本函数用到的FILE类型的成员：
        _Next 一个指针，指向下一个将要被读或写的字符，该指针永不为空
        _Rend 一个指针，指向超出被读数据结束的第一个字符to the first character beyond the end of data to be read，永不为空
        _Rsave一个指针，控制_Rend,应该是当pushed back stack为空时使_Rend指向缓冲区的开始，不为空时指向栈的开始，标准库中描述：
                  holds _Rend if characters have been pushed back
        _Back 一个栈，存储被推回的字符
        _Nback表示_Back栈中字符的数目
