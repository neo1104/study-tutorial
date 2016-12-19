#Bash脚本 手册


##1.变量

###1.1变量命名
Shell的变量必须以字符或者下划线开头，后面可以跟字母、数字、下划线，变量的长度没有限制。注意:Shell的变量区分大小写。

###1.2变量的赋值和取值
Shell中变量的赋值非常简单明了:

	变量名=变量值
	
但是需要注意以下几点:

1. 变量名和变量值之间用等号紧紧相连，之间没有任何空格
2. 当变量中有空格时必须用引号括起，否则会出现错误。其中引号可以是双引号，也可以是单引号。

变量的取值也非常简单，只需要在变量名前加上$符号即可，严谨一点的写法是${}。

大括号{}在Shell中的用法很多，最常见的用法就是引用变量名，又叫变量扩展,常见用法如下:

|表达式|作用|
|:----|:----|
|${VAR}|取出变量VAR的值|
|${VAR-DEFAULT}|如果VAR没有定义，则以$DEFAULT作为其值|
|${VAR:-DEFAULT}|如果VAR没有定义，或者VAR值为空，则以$DEFAULT作为其值|
|${VAR=DEFAULT}|如果VAR没有定义, 则以$DEFAULT作为其值, 同时VAR被赋值为DEFAULT|
|${VAR:=DEFAULT}|如果VAR没有定义，或者VAR值为空，则以$DEFAULT作为其值, 同时VAR被赋值为DEFAULT|
|${VAR+VALUE}|如果定义了VAR,则值为$VALUE,否则为空字符串|
|${VAR:+VALUE}|如果定义了VAR并且VAR不为空，则值为$VALUE,否则值为空字符串|
|${VAR?MSG}|如果VAR没有定义，则打印MSG|
|${VAR:?MSG}|如果VAR没有定义或未被赋值，则打印MSG|
|${!PREFIX@} ${!PREFIX*}|匹配所有以PRIFIX开头的变量|
|${#STR}|返回${STR}的长度|
|${STR:POSITION}|从位置$POSITION处提取子串|
|${STR:POSITION:LENGTH}|从位置$POSITION处提取长度为$LENGTH的字串|
|${STR#SUBSTR}|从变量$STR的开头处开始寻找，删除最短匹配$SUBSTR的子串|
|${STR##SUBSTR}|从变量$STR的开头处开始寻找，删除最长匹配$SUBSTR的子串|
|${STR%SUBSTR}|从变量$STR的结尾处开始寻找，删除最短匹配$SUBSTR的子串|
|${STR%%SUBSTR}|从变量$STR的结尾处开始寻找，删除最长匹配$SUBSTR的子串|
|${STR/SUBSTR/REPLACE}|使用$REPLACE替换一个匹配的$SUBSTR|
|${STR//SUBSTR/REPLACE}|使用$REPLACT替换所有匹配的$SUBSTR|
|${STR/#SUBSTR/REPLACE}|如果$STR以$SUBSTR开始，则用$REPLACT替换匹配的$SUBSTR|
|${STR/%SUBSTR/REPLACE}|如果$STR以$SUBSTR结尾，则用$REPLACT替换匹配的$SUBSTR|

###1.3特殊变量

Shell中有一些预先定义的特殊变量，它们的取值只有在脚本运行时才能确定。

####1.3.1 位置变量
位置变量的命令简单直接，比如，脚本本身为$0,第一个参数为$1, 第二个参数为$2，第三个位$3，以此类推。当位置变量的个数大于9时，需要用${}括起来标识，例如${10}。

另外, $#表示脚本参数的总个数，$@或$*表示脚本的所有参数。

####1.4.1 脚本或命令返回值

$?永远表示上一个命令的返回值。 在Linux中规定正常退出的命令或脚本以0作为返回值，任何非0得返回值都表示命令未正确退出或未正常执行。

##2.引用

引用是指将字符串用某种符号括起来，以防止特殊字符被解析为其它意思。

Shell中一共有4种引用，分别如下:

1. 双引号，又称为弱引用或部分引用，可以引用除 $符、反引号、转义符之外的所有字符
2. 单引号, 又称为强引用或全引用，可以引用所有字符,单引号中间不能再有单引号，否则Shell无法判断到底哪里是单引号的起止位置
3. 反引号, 会将括起的字符串届时为系统命令
4. 转义符, 即'\'符号，用来引用单个字符


##3.命令替换
命令替换是指将命令的标准输出作为值赋值给某个变量。

Shell中有两种方式可以完成命令替换，一种是反引号(`)，一种是$()。使用方法如下:

	`命令`
	
和

	$(命令)
	
如果被引用的命令输出包含多行，此时若不通过引用的方式输出变量，则输出内容会被删除换行符，换行之间会使用系统的默认空字符来进行输出，即输出的内容只有一行。


	LS=`ls -l`
	#不引用变量进行输出
	echo ${LS}
	#引用变量进行输出
	echo "${LS}"
	
大部分情况下，使用反引号的地方都可以使用$()进行替代，但是使用$()会使代码相对比较清晰，且在某些情况下只能使用$():$()支持嵌套，而反引号不行:

	#Fir_File_Lines=$(wc -l $(ls | sed -n '1p'))
	#echo $Fir_File_Lines
	36 anaconda-ks.cfg
	
最后，需要注意的是, $()仅在Bash中有效，而反引号可以用在多种UNIX Shell中使用。

##4.算数扩展
算数扩展是Shell提供的整数变量的运算机制，是Shell的内建命令之一，语法如下:

	$((运算表达式))
	
其中运算表达式由变量和运算符组成。若表达式中的变量没有定义，则在计算时，其值会被假设为0。


##5.测试

测试的第一种结构是使用test命令，该命令的格式如下:

	test expression
	
其中 expression 是一个表达式，可以是算数比较、字符串比较、文件和文件属性比较等。

第二种测试方式是使用"["启动一个测试，再写expression, 再以"]"结束测试。需要注意的是，左边的方括号"["后面有一个空格，右边的方括号"]"前面也有一个空格，如果任意一边缺少了这个空格都会造成Shell报错,如下:

	[ expression ]
	
建议使用第二种方法。

###5.1 文件测试
文件测试格式如下:

	test file_operator FILE
	
或者
	
	[ file_operator FILE ]
	
其中file_operator是文件测试符，FILE可是是文件，也可以是目录。

文件测试符如下:


|文件测试|说明|
|------|----|
|-b FILE|当文件存在且为块文件时为真，否则为假|
|-c FILE|当文件存在且为字符文件时为真，否则为假|
|-d FILE|当文件存在且为目录时为真，否则为假|
|-e FILE|当文件或目录存在时为真，否则为假|
|-f FILE|当文件存在且为普通文件时为真，否则为假|
|-x FILE|当文件存在且为可执行文件时为真，否则为假|
|-w FILE|当文件存在且为可写文件时为真，否则为假|
|-r FILE|当文件存在且为可读文件时为真，否则为假|
|-l FILE|当文件存在且为链接文件时为真，否则为假|
|-p FILE|当文件存在且为管道文件时为真，否则为假|
|-s FILE|当文件存在且文件大小不为0时为真，否则为假|
|-S FILE|当文件存在且为socket文件时为真，否则为假|
|-g FILE|当文件存在且设置了SGID时为真，否则为假|
|-u FILE|当文件存在且设置了SUID时为真，否则为假|
|-k FILE|当文件存在且设置了sticky属性时为真，否则为假|
|-G FILE|当文件存在且属于有效的用户组时为真，否则为假|
|-O FILE|当文件存在且属于有效的用户时为真，否则为假|
|FILE1 -nt FILE2|当FILE1比FILE2文件新时为真，否则为假|
|FILE1 -ot FILE2|当FILE1比FILE2文件旧时为真，否则为假|

###5.2 字符串测试

Shell中的字符串测试主要包含等于、不等于、大于、小于、是否为空等测试，如下所示:

|字符串测试|说明|
|--------|---|
|-z "string"|字符串string为空时返回真，否则为假|
|-n "string"|字符串string为非空时返回真，否则为假|
|"string1" = "string2"|字符串string1和字符串string2相同时返回真，否则为假|
|"string2" != "string2"|字符串string1和字符串string2不同时返回真，否则为假|
|"string1" > "string2"|按照字典排序，字符串string1大于字符串string2时为真，否则为假|
|"string2" < "string2"|按照字典排序，字符串string1小于字符串string2时为真，否则为假|

需要注意的是，比较字符串大小时, '>' 和 '<'都需要进行转义，如果不想进行转义，可以用  [[ ]]括起:

		[[ "str1" > "str2" ]]
		[[ "str1" < "str2" ]]
		
##5.3整数比较
整数测试是一种简单的算数运算，作用在于比较两个整数的大小关系

|整数比较|说明|
|------|----|
|"num1" -eq "num2"|如果num1等于num2则返回真，否则为假|
|"num1" -gt "num2"|如果num1大于num2则返回真，否则为假|
|"num1" -lt "num2"|如果num1小于num2则返回真，否则为假|
|"num1" -ge "num2"|如果num1大于等于num2则返回真，否则为假|
|"num1" -le "num2"|如果num1小于等于num2则返回真，否则为假|
|"num1" -ne "num2"|如果num1不等于num2则返回真，否则为假****|


##5.4逻辑测试以及逻辑运算

逻辑测试用于连接多个测试条件，并返回整个表达式的值。逻辑测试主要有逻辑非、逻辑与、逻辑或三种，如下所示:


|逻辑测试符|说明|
|--------|---|
|! expression|如果expression为真，则结果为假|
|expression1 -a expression2|expression1和expression2同时为真，则测试结果为真，否则为假|
|expression1 -o expression2|expression1和expression2只要一个为真，则测试结果为真，否则为假|


Shell中也存在逻辑运算符

|逻辑运算符|说明|
|--------|---|
|!|逻辑非，对真假取反|
|&&|逻辑与，连接两个表达式，只有两个表达式都为真结果才为真，否则为假|
|\|\||逻辑或，连接两个表达式，只要有一个表达式为真则结果为真，否则为假|

##6.判断
###6.1 if

	if expression; then
		command
	fi

###6.2 if/else
	
	if expression; then
		command1
	else
		command2
	fi

###6.3 if/elif/else

	if expression1; then
		command1
	elif expression2; then
		command2
	elif expression3; then
		command3
	else
		command4
	fi
	
###6.4 case

	case VAR in
	var1) command1;;
	var2) command2;;
	var3) command3;;
	...
	*) command4;;
	esac
	
这里需要注意的是，case判断结构中的var1、var2、var3只能是常量或者正则表达式。

##7.循环

###7.1for循环
####7.1.1 带列表的for循环

	for variable in (list)
	do
		command
	done
	
这里的list可以是常量列表，也可以是变量，同时也可以是通过命令替换产生的列表。 需要提到的是Shell提供一种用于技术的方式， {1..5}

####7.1.2 不带列表的for循环
	for varibale
	do
		command
	done

使用不带列表的for循环时，需要在运行脚本时通过参数的方式给for循环传递变量值，如下所示:

	# for_list.sh
	for VARIABLE
	do
		echo -n "$VARIABLE"
	done
	#bash for_list.sh 1 2 3
	1 2 3
	
这种语法虽然可以工作，但是可读性较差，所以不建议使用。可以列用$@特殊变量，改写上面的结构:

	# for_list.sh
	for VARIABLE in $@
	do
		echo -n "$VARIABLE"
	done
	#bash for_list.sh 1 2 3
	1 2 3
	
####7.1.3 类C的for循环

	for ((expression1; expression2; expression3))
	do
		command
	done
	
其中，expression1为初始化语句，一般用作变量定义和初始化；expression2为判断表达式，用于测试表达式返回值并以此控制循环，返回值为真则继续循环，返回值为假则退出循环；expression3用于变量修改，从而影响expression2的返回值，并以此影响循环行为。

###7.2while循环

	while expression
	do
		command
	done
	
只要expression表达式为真，则继续循环

###7.3until循环

	until expression
	do
		command
	done
	
until循环和while循环类似，但是until是测试假值，只有expression结果为假，才继续循环。

###7.4select循环

	select VARABLE in (list)
	do
		command
	done
	
select循环是一种菜单扩展循环方式，其语法和带有列表的for循环类似。

当程序运行到select循环时，会自动将列表中的所有元素生成可用1，2，3等数字选择的列表，并等待用户的输入。用户输入并回车后，select可判断输入并执行后续命令。如果用户在等待输入的光标后面直接按回车键，select将不会退出而是再次生成选择列表等待输入。

select经常和case结合进行使用。


###7.5 break

break用于终止当前整个循环体。一般情况下break都是和if判断语句一起使用，当if条件满足时使用break终止循环。

###7.6 continue

continue语句用于结束当前循环而进入下一次循环，注意这是和break不同的地方:continue并不会终止当前整个循环体，它只是提前结束本次循环，而循环体还将继续执行，break则是会结束整个循环体。






	




 
















