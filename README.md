# BDD Variable Order Thinking

## Problem Set

1. **create** variable order
   - `createVar` and `ithVar`
2. **construct** variable order
   - `apply` operation

## Start

Open the project in IDEA. (`/.idea` is preserved for plugin `google-java-format` and other settings)

Generate dataset by

```shell
python3 utils/randomCreateIP.py
```

which is a random IP address generator and will store all IPs in `src/main/resources/org/ants/ips`

Then run the main code in java.

## Why
对ip地址进行编码，两种编码方式分别为ip的最高位为x0,最低位是x31和ip的最高位是x31,最低位是x0。
在组装时，两种编码方式不会有很大差别，BDD的形状和结构也基本一致，所以开销差异不体现在这个阶段。
有的ip地址只有16位，有的有32位。若ip只有16位，则正着编码时，索引x16到x32不对应ip地址中的值，x15会直接指向终端。
在apply函数中，会从优先级高的位数开始构造BDD，递归两个BDD，如果一个BDD到了终端，且能计算出最终值，则结束递归。
在对所有ip地址进行或运算时，正着编码的BDD递归到x15后会直接到达终端，如果此时已经得到1(对于或运算，1或任何值都是1)，
则不会继续递归。
而反着编码时，对于16位ip，从x16位开始才有效，所以32位和16位ip地址进行或运算会先对32位的ip从x0开始递归，
直到运行到x16，16位的ip地址才会有效参与，这浪费了很多递归的时间。所以会有时间开销上的差异。


## Lib

`/lib/jdd-111.jar` is a [modified version](https://github.com/Augists/jdd) of [jdd library](https://bitbucket.org/vahidi/jdd) with variable `mkCount`.
Different order may cause `jdd` to create more BDD variables for these reason in [#Why](https://github.com/Augists/bdd-undergra-thinking#Why).