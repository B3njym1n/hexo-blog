---
title: 在Unix系统上查找数据的最佳工具和技巧
date: 2024-09-12 13:41:57
tags:
  - Linux
categories:
  - Linux
  
---


# Table of Contents

1.  [在 Unix 系统上查找数据的最佳工具和技巧](#orgb1f7328)


<a id="orgb1f7328"></a>

# 在 Unix 系统上查找数据的最佳工具和技巧

有时候在 Unix 系统上查找信息就如同大海捞针。如果重要的信息被淹没在大量文本中，它们也很难被注意到。目前我们中的很多人都在处理“大数据” —— 从数十亿字节大小的日志文件和巨大的各种格式记录集合中挖掘商业情报。

幸运的是，只有在两种情况下，你才需要在成堆的数据中挖掘，继而完成你的工作 —— 当你知道你要找什么和当你不知道的时候。:) 最佳工具和技巧取决于你面临两种情况中的哪一种。

当你知道的时候

当你知道你要找什么，grep 就是你的朋友，这不只是在你查找特定文本的时候。grep 命令可以帮助你找到任意文本，特定单词，文本模式和有上下文的文本。当你知道文本长什么样时，查找它通常很简单。grep this that 命令会显示“that”文件中包含“this”字符串的每一行。增加 -w 选项就只会显示那些单独包含“this”这个单词的行。换句话说，如果行中包含“thistle” 或 “erethism” 就不会显出来，除非这些行也有 “this” 这个单词。

最简单的 grep 命令不费什么力气就能理解：

查找整个单词可以通过增加 -w 选项完成：

    grep -w find poem

查找模式需要一点技巧。我们的第一个例子中显示了包含“find”单词的行，无论“find”中的“f”是大写还是小写：

    grep [Ff]ind poem

Finding answers

    Finding answers
    finding meaning, finding comfort, finding someone to adore
    Can we find a way to be

如果你想匹配以文本起始或结束的行，你可以使用 ^（起始）或 $（结尾）。

    grep ^find poem

如果你想找到包含两个连续元音音节的单词的行，你可以使用如下所示的“AEIOUaeiou”字符。

    grep -E "[AEIOUaeiou]{2} poem

查找包含 9 个或者 10 个字母的字符串：

    grep -E "[[:alpha:]]{9,10}" poem

查找一个包含 “find” 的长单词：

    grep -E "find[^[:space:]]+" poem

我们中的大多数人不会去查找诗歌，这是显而易见的，但我们可以使用同样的技巧来从我们的系统文件中获取相关的信息。在下面的例子里，我们查找”processor”这个术语，并且按照五行一组（前置两行后置两行）显示出来以便提供一些上下文。如果你希望得到 9 行一组，将 -C 2 变成 -C 4 就可以了。

    grep -C 2 processor /var/log/dmesg

当你不知道的时候
如果你要查找一个已知位置的文本，例如当 Perl 告诉你脚本执行到第 73 行出现了问题，或者你正在处理文件的第 1892 行，你可以使用sed 来显示特定的行（我只是不喜欢数到 1892 行）。而且额外花一点点力气，你就可以只显示这一行。

    sed -n 73p showvars

好了，就是这行，但是我们也没有比之前多知道些什么。通过显示前面几行可以增加一点上下文信息，我们就可以定位错误。这里有一个类似的命令可以显示这行和之前的十行：

    sed -n 64,73p showvars

    if $password eq "a_secret";
       {
           foreach $var(sort(keys(%ENV)))
           {
    	   $var= $ENV{$var};
    	   $var=~s|n|n|h;
    	   $var=~s|"|"|g;
    	   print '${var}="${var}"n'
           };
       }
    else

哎呦！这看上去是某些人在写 if 语句时出了问题！我们可以很容易地修复它。
你还可以使用 sed 命令来强调包含特定内容的行。在下面的命令里，我们增加了一个 “箭头标记” 来强调每一个包含 foreach 命令的行：

    sed 'print/{b label1;{:label1; s/^/# /; s/$/ <===/;}}' showvars

你可以使用类似的命令注释掉你的 print 命令：

    sed '/print/{b label1; {:label1 ; s/^/# / ; s/$/ <===/ ;} }' showvars

大海捞针很难，其实地毯上找针也都不容易。但是通过使用一些最常见 Unix 命令的变形，就可以很容易找到你要找的东西，甚至当你并不知道要找什么的时候。

