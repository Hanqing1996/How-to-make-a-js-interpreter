# How-to-make-a-js-interpreter
如何制作一个 js 解释器

[《不懂编译也能造JavaScript解释器》](https://www.yuque.com/javascript-interpreter/book)追更中

---
#### 词法分析和语法分析
词法分析负责分词，就是将文本字符串切割成语法分析能够识别的最基本单元（Token）

语法分析则负责接受 Token、分析语法并累积成抽象语法树（AST）

#### [Jison 安装和基本使用](https://www.yuque.com/javascript-interpreter/book/hbhr39) 这一节各部分文件作用
* calc.jison
> 定义语法和词法分析规则
* calc.js
> 通过规则生成出来的语法分析器
* test.calc
> 待分析的代码
>


#### 词法分析过程
```jison
%lex
word                 \\w+

%%
\\s+                 /* skip whitespace */
{word}               return 'WORD'
<<EOF>>              return 'EOF'
/lex

```

词法分析部分由两个部分是由 %lex 和 lex  包括起来的，以 %% 符号进行分隔。

分隔上面的是声明部分，用于正则表达式的复用。比如我们定义了 \w+ 为 word。

分词规则定义在词法分析的下半部分， %% 符号之下 \lex 符号之上。分词部分也分成两边，左边是正则匹配的规则，右边是当你匹配了这串字符串以后，我们怎么把它生成一个 Token。

* 当 {world} 捕获的这个字符串，会生成一个名为 WORD 的 Token。

* 当 \s 规则捕获了一个空字符串以后，发现后面没有对应生成 Token 的规则，那这个规则就只捕获字符串，但是不生成规则。比如我们把无关的空字符都消耗掉。

#### 含有字面量，标识符，保留字的词法分析规则
```jison
const parser = new Parser(`
%lex

/** identifiers **/
identifier                              ("_"|{letter})({letter}|{digit}|"_")*
letter                                  {lowercase}|{uppercase}
lowercase                               [a-z]
uppercase                               [A-Z]
digit                                   [0-9]

/** strings **/
string                                  "'"{stringitem_single}*"'"
stringitem_single                       {stringchar_single}|{escapeseq}
stringchar_single                       [^\\\\\\n\\']
escapeseq                               \\\\.

/** numbers **/
number                                  {integer}{fraction}?
integer                                 {digit}+
fraction                                "."{digit}+

/** null **/
null                                    "null"

/** reserved **/
reserved                                {keywords}\b|{symbols}|{brackets}|{operators}
keywords                                "const"
symbols                                 "=>"|","|"="|";"
brackets                                "("|")"|"{"|"}"
operators                               "*"|"/"|"%"|"**"|
                                        "+"|"-" 
                                        ">"|"<"|">="|"<="|
                                        "=="|"!="|
                                        "||"|"&&"

%%
\\s+                                    /* skip whitespace */
{null}                                  return 'WORD'
{string}                                return 'WORD'
{number}                                return 'WORD'
{identifier}                            return 'WORD'
{reserved}                              return 'WORD'
<<EOF>>                                 return 'EOF'
/lex

```






