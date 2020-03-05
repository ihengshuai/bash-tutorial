# 字符串操作

## 字符串的长度

获取字符串长度的语法如下。

```bash
${#varname}
```

下面是一个例子。

```bash
$ path=/home/cam/book/long.file.name
$ echo ${#path}
29
```

## 返回子字符串

Bash 返回一个字符串的子串，语法如下。

```bash
${varname:offset:length}
```

上面语法的含义是返回`$varname`的子字符串，从位置`offset`开始（以`0`作为基点），长度为`length`。

```bash
$ count=frogfootman
$ echo ${count:4:4}
foot
```

上面例子返回字符串`frogfootman`从4号位置开始的长度为4的子字符串。

如果省略`length`，则从位置`offset`开始，一直返回到字符串的结尾。

```bash
$ count=frogfootman
$ echo ${count:4}
footman
```

上面例子是返回变量`count`从4号位置一直到结尾的子字符串。

如果`offset`为负值，表示从字符串的末尾开始算起。注意，负数前面必须有一个空格， 以防止与`${variable:-word}`的变量设置默认值的形式混淆。这时，如果还指定`length`，则`length`不能小于零。

```bash
$ foo="This string is long."
$ echo ${foo: -5}
long.
$ echo ${foo: -5:2}
lo
```

上面例子中，`offset`为`-5`，表示从倒数第5个字符开始截取，所以返回`long.`。如果指定长度为`2`，则返回`lo`。

## 字符串的处理

Bash 提供字符串处理的多种方法。

**（1）删除开头的匹配。**

以下语法可以删除字符串开头匹配的部分，但不会改变原始变量。匹配模式可以使用`*`、`?`、`[]`等通配符。

```bash
# 如果 pattern 匹配变量 variable 的开头，
# 删除最短匹配（非贪婪匹配）的部分，返回剩余部分
${variable#pattern}

# 如果 pattern 匹配变量 variable 的开头，
# 删除最长匹配（贪婪匹配）的部分，返回剩余部分
${variable##pattern}
```

上面两种语法会删除变量字符串开头的匹配部分，返回剩下的部分。区别是一个是最短匹配（又称非贪婪匹配），另一个是最长匹配（又称贪婪匹配）。

下面是一个例子。

```bash
$ path=/home/cam/book/long.file.name

$ echo ${path#/*/}
cam/book/long.file.name

$ echo ${path##/*/}
long.file.name
```

上面例子中，匹配模式是`/*/`，其中`*`可以匹配任意的多个字符，所以最短匹配是`/home/`，最长匹配是`/home/cam/book/`。

下面写法可以删除文件路径的目录部分，只留下文件名。

```bash
$ path=/home/cam/book/long.file.name

$ echo ${path##*/}
long.file.name
```

上面例子中，模式`*/`匹配目录部分，所以只返回文件名。

**（2）删除结尾的匹配。**

以下语法可以删除字符串结尾匹配的部分，但不会改变原始变量。

```bash
# 如果 pattern 匹配变量 variable 的结尾，
# 删除最短匹配（非贪婪匹配）的部分，返回剩余部分
${variable%pattern}

# 如果 pattern 匹配变量 variable 的结尾，
# 删除最长匹配（贪婪匹配）的部分，返回剩余部分
${variable%%pattern}
```

上面两种语法会删除变量字符串结尾的匹配部分，返回剩下的部分。区别是一个是最短匹配（又称非贪婪匹配），另一个是最长匹配（又称贪婪匹配）。

下面是一个例子。

```bash
$ path=/home/cam/book/long.file.name

$ echo ${path%.*}
/home/cam/book/long.file

$ echo ${path%%.*}
/home/cam/book/long
```

上面例子中，匹配模式是`.*`，其中`*`可以匹配任意的多个字符，所以最短匹配是`.name`，最长匹配是`.file.name`。

下面写法可以删除文件路径的文件名部分，只留下目录部分。

```bash
$ path=/home/cam/book/long.file.name

$ echo ${path%/*}
/home/cam/book
```

上面例子中，模式`/*`匹配文件名部分，所以只返回目录部分。

下面的写法可以替换文件的后缀名。

```bash
$ file=foo.png
$ echo ${file%.png}.jpg
foo.jpg
```

上面的例子将文件的后缀名，从`.png`改成了`.jpg`。

**（3）替换子串。**

以下语法可以将字符串的最长匹配（贪婪匹配），换成其他的字符串返回，但不会改变原始变量。

```bash
# 如果 pattern 匹配变量 variable 的一部分，
# 最长匹配（贪婪匹配）的那部分被 string 替换，但仅替换第一个匹配
${variable/pattern/string}

# 如果 pattern 匹配变量 variable 的一部分，
# 最长匹配（贪婪匹配）的那部分被 string 替换，所有匹配都替换
${variable//pattern/string}
```

上面两种语法都是最长匹配（贪婪匹配）下的替换，区别是前一个语法仅仅替换第一个匹配，后一个语法替换所有匹配。

下面是一个例子。

```bash
$ path=/home/cam/foo/foo.name

$ echo ${path/foo/bar}
/home/cam/bar/foo.name

$ echo ${path//foo/bar}
/home/cam/bar/bar.name
```

上面例子中，前一个命令只替换了第一个`foo`，后一个命令将两个`foo`都替换了。

下面的例子将分隔符从`:`换成换行符。

```bash
$ echo -e ${PATH//:/'\n'}
```

上面例子中，`echo`命令的`-e`参数，表示将替换后的字符串的`\n`字符，解释为换行符。

如果省略了`string`部分，那么就相当于匹配的部分替换成空字符串，即删除匹配的部分。

```bash
$ path=/home/cam/foo/foo.name

$ echo ${path/.*/}
/home/cam/foo/foo
```

上面例子中，第二个斜杠后面的`string`部分省略了，所以模式`.*`匹配的部分`.name`被删除后返回。

这个语法还有两种扩展形式。

```bash
# 模式必须出现在字符串的开头
${variable/#pattern/string}

# 模式必须出现在字符串的结尾
${variable/%pattern/string}
```

下面是一个例子。

```bash
$ foo=JPG.JPG
$ echo ${foo/JPG/jpg}
jpg.JPG
$ echo ${foo//JPG/jpg}
jpg.jpg
$ echo ${foo/#JPG/jpg}
jpg.JPG
$ echo ${foo/%JPG/jpg}
JPG.jpg
```
