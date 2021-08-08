
# MySQL

### 一、什么是mysql（ c/s 架构的DBMS）

---

1. 一种DBMS（Date Base Mangement System）数据库管理系统
    - DB : Date Base，数据库
    - SQL : Structure Query Language,结构化查询语言，用于与DBMS通信的语言

        → 利用 SQL 与 DBMS 交互，完成对 DB 的管理

    - 所以，学习MYSQL是去学习sql的基础上，学会MySQL 数据管理系统的使用，学习过程中我们就是服务端
2. 数据库存储特点：
    - 数据库由多个不同的表组成，每个表都有一个具有唯一性的表名
    - 数据库的表类似 java类或者c结构体，其中每一个数据成员都被称为 字段

### 二、基本语法（powershell管理）

---

1. 服务端启动关闭（管理员身份）：
    - 手动
        - services.msc 命令进入服务界面，找到本地的mysql服务，开启/关闭
    - 命令行
        - 启动：net start mysql80（此处为自己mysql服务器名）

            服务器名查看： services.msc 命令进入服务界面，找到本地的mysql服务


        - 关闭：net stop mysql80



2. 登录服务端
    - 登录（一定要确保服务器开着才能登录）

        mysql -h [localhost] -P 3306 -u root -p   

        -h  host主机名

        -P  port端口号，配置文件里，默认3306

        -u 用户名，root本地用户

        -p 密码，回车输入或者不加空格直接输入

        →  mysql -u root -p

    - 退出（记得停止服务）

        exit


### 三、可视化工具

---

1. SQLyog

    连接时记得开MySQL服务；

    query 写sql语句

    执行后在结果显示（选中谁执行谁）

    查询过程可另存为sql文件，同样保存的sql脚本也可被直接执行，完成相应操作

### 四、 DQL（Data Query Language）

---

- 基础查询

    ```sql
    # 1. 基础查询
    	# 查询当前所有数据库
    		show databases ;

    	# 查询数据库中的表
    		show tables ;  # 当前库中
    		show tables from xxx ;  # 查看库xxx的表

    	# 查询xxx表中字段
    		desc xxx ； 
    	
    	# 查询zzz库中yyy表中数据（当前库下的表）
    		use zzz; # 打开指定数据库
    		select xxx from yyy； # xxx为字段，可加着重号标识
    		select * from yyy; # 查看当前表的所有字段数据

    # 2. 其他查询（select 类似数据库的一个输出语句）
    	# 常量、字符查询（输出）
    		select 1000;
    		select 'orange';  # mysql字符与字符串不区分，都用单引号标识
    	
    	# 表达式
    		select 100*2*24 ;
    	
    	# 函数
    		select version() ; # version函数返回当前MySQL版本号

    # 3. 字段别名输出
    		select a as b,c as 'd#c' from xxx; # a、c字段数据分别以b\d别名输出
    		# 其中关键词as可以省略为空格
    		# 别名中不可有特殊符号，若有，请用引号标识别名
    # 4. 去重（输出去重）
        select distinct a from b ; # 将b表中a字段无重的输出
    		# 关键字 distinct
    # 5. + 号分析
    	/*
    		MySQL中 + 号只有一个功能：算数运算
    			两个操作数都为数值型时，输出算术结果
    			两个操作数其中有一个为字符型时，试图将字符型转换为数值型
    				转换成功，输出数值
    				转换失败，字符型操作数转换为0，参与算术运算，并输出结果
    			两个操作数，只要一方为null，结果肯定为null
    	*/
    		select 100 + 90 ; # 190
    		select '123' + 7 ; # 130 ， 字符型数字转换成功
    		select 'a' + 7 ; # 7 , 单字符转换失败
    		select 'peach' + 5 ; # 5  ，多字符单词转换失败
    		select null + 63 ; # NULL

    # 6. 字符拼接函数 concat(str,str...)
    		select concat(first_name,' ',last_name) as 姓名 from employees ;
    		# 输出字段栏为姓名，值为员工 姓 名；
    		# 若 null 字段拼接，则输出结果也为null

    # 7. null控制语句 ifnull(expr1,expr2)
    	# if (expr1 == null ) return expr2;
    	select ifnull(rate , 0) as 'xx率' ;

    # 8. isnull函数  isnull(expr)
    	# 判断expr值是否为null 是返回1，否返回0
    	select isnull(commission_pct),commission_pct from employees;
    ```

- 条件查询

    ```sql
    # 1. 基本语法
    	select 
    				lists 
    	from 
    				table_A 
    	where
    				conditions; 
    	# 首先查看表，其次看条件，最后select输出

    # 2. 条件表达式类型
    	# 条件表达式： > < = !=（<>） >= <= 
    	select 
    				name
    	from
    				employees
    	where
    				department_id <> 90;  # != 等价于 <>

    # 3. 逻辑表达式类型
    	# 逻辑表达式： and or not，用于连接多个条件表达式
    	select
    			last_name,
    			salary 
    	from
    			employees
    	where
    			salary < 20000 and salary > 10000 ;

    # 4. 模糊查询
    	# 关键词： like，between and，in，is null

    	# like
    		/*
    		一般搭配通配符食用
    			% 任意0个或多个字符
    			_ 任意单个字符
    		*/
    		# 查询员工姓名第三个字母为n，第五个字母为l的员工信息
    			select 
    							*
    			from
    						employees
    			where 
    						last_name like '__n_l%'; # ⚠ 注意 '%%'查询不会出现null
    		# 查询对象为数值型
    			select
    						last_name,
    						salary
    			from
    						employees
    			where
    						salary like '1_____%';  # salary，double型，如10020.00，查询一万以上2万一下工资
    	
    		# 转移字符，当查询对象中存在通配符时
    			# 查询姓名第二个字符为_的员工薪水
    			select 
    						salary
    			from
    						employees
    			where 
    						last_name like '_$_%' escape '$';
    			/*
    				方法一：采用转义符  例如 \_ 代表 _
    				方法二：自行定义转义符，利用escape关键词定义，如例子所示
    			*/

    		# between and
    			#数值判断
    				select
    					last_name,
    					salary 
    				from
    					employees
    				where
    					salary between 10000 and 20000 ; 
    					# [10000,20000],边界包含,部分等价于条件判断加逻辑判断
    		# in
    			select
    					last_name,
    					job_id
    			from
    					employees
    			where
    					job_id in('IT_PROT','AD_VP','AD_PRES') ;
    			# in列表值类型必须一致或者兼容
    			# in 本质上类似于 = 比较，而不是like模糊搜索，所以不支持通配符 
    			
    		# is null （仅用于判断NULL类型）
    			select 
    						last_name,
    						commission_pct
    			from 
    						employees
    			where
    						commission_pct is null;
    				#   commission_pct is  not null; 否定形式写法

    		#  <=> 安全等于 
    			# 可用于判断 null 与其他类型数据
    ```

- 排序查询

    ```sql
    # 1. 基本语法
    	select 
    				lists 
    	from 
    				table_A 
    	order by
    				table_tobe_sorted asc|desc; 
    	# table_tobe_sorted - 待排序列表，后面关键词标识增序降序，默认升序排列asc
    	# 例子
    		select 
    					*
    		from 
    					employees
    		order by 
    					salary desc;
    # 2. 按表达式、别名排序
    	select 
    				last_name,salary*12*(1+ifnull(commission_pct,0)) as 年薪
    	from 
    				employees
    	order by
    				年薪 desc;

    # 3. 按函数排序
    	select 
    				last_name,salary
    	from 
    				employees
    	order by
    				length(last_name) ;

    # 4. 多个字段排序
    	select
    				*
    	from 
    				employees
    	order by
    				salary asc,job_id desc;
    		# 位于前面的优先级高，优先考虑
    ```

- 常见函数

    所有函数都有一个返回值

    ```sql
    # 基础调用语法
    	select method(expr1,....) ;
    ```

    - 单行函数

        ```sql
        # 字符函数
        		# 1.length(str) 获取参数字节数 （中文字符 utf三个字节，gbk两个字节）
        			select length("下一周zzz"); # 12
        		
        		# 2.concat(str1,str2,....)
        		
        		# 3. upper(str),lower(str)
        			select upper("xxx诉求123"); # XXX诉求123
        		
        		# 4.substr - 四种重载
        			select substr("1234567",5); # 567 (str,index)
        			# 索引从1开始，截取从索引，包括索引的部分
        			select substr("123456789",2,3) # 234 (str,index,len)
        			# 截取从指定索引处指定字符长度的字符字串
        		 
        		# 5.instr(str,substr) 用于返回substr在str中的（首个）起始索引，没有返回0

        		# 6.trim(str) 去str前后指定字符，默认空格
        			select length(trim("   xyz   ")) ; # 3
        			select trim('aa' from "aaaxaayzaaaa") # axaayz
        		
        		# 7. lpad,rpad(str,len,padstr) 利用padstr实现str的左(右)填充达到指定长度len，小于则右(左)切割
        			select lpad("12345678",10,'a') #aa12345678
        			select lpad("123",1,'a'); # 1

        		# 8.replace(str,from_str,to_str) 将str中所有from_str替换为to_str
        			select replace("acascaa",'a','1'); # 1c1sc11
        	
        # 数学函数
        		# 1.round(x，d) 对x四舍五入取d位小数，默认0（取整）
        			select round(1.7632,2); # 1.76
        		
        		# 2. ceil(x) ,floor(x)   向上，向下取整
        			select ceil(-0.9); # 0
        			select floor(-0.9) # -1

        		# 3.truncate(x,d) 对x实行d位小数的截断（非进位）
        			select truncate(1.79999,2); # 1.79

        		# 4. mod(n,m) 取余函数 n % m
        			select mod(10,-3); # 1  n - n/m *m

        # 日期函数
        		# 1. now()  返回系统当前日期+时间
        			select now(); # 2021-07-18 21:33:30

        		# 2. curdate() 返回当前系统时间

        		# 3. curtime() 返回当前系统时间

        		# 4. year(date),month[name](date), 返回日期指定字段（年月日），没有则返回0

        		# 5.str_to_date(str,date_for) 字符通过指定格式（解析格式）转换为日期
        			select str_to_date("2001-06-30","%Y-%m-%d");
        		
        		# 6. date_format(date,str_for) 日期按照字符解析模式转换为字符(日期型数据按给定字符格式输出)
        			select date_format(now(),'%Y年%m月%d日'); # 2021年07月18日
        			
        			select 
        						last_name,DATE_FORMAT(hiredate,'%m月%d日 %Y年') 
        			as  
        						output
        			from 
        						employees;

        		# 7.datediff(expr1,expr2) 求两个日期参数的日期差 expr1-expr2
        			select 
        						datediff(now(),'2001-06-30');
        	
        # if(expr1,expr2,expr3)流程函数
        			# 其中参数逻辑功能类似三元运算符 expr1==true ? expr2 : expr3;
        			select 
        						commission_pct,if(commission_pct is null,'没奖金'，'有奖金') as 检查 
        			from
        						employees; 
        	
        # case()函数
        		select
        					salary as 原始工资,
        					department_id,            # 用法一：等值判断（类似switch-case）
        					case department_id        # case 要判断的字段或表达式
        					when 30 then salary*1.1   # when 常量1 then 要显示的值或语句
        					when 40 then salart*1.2   # .....
        					else salary               # else 要显示的值或语句（未匹配状态，类似default）
        					end as new_salary         # end 结束case
        		from 
        					employees;
        		
        		select 
        				salary,                       # 用法二：区间判断（类似if-else）
        				case                          # case 
        				when salary > 20000 then 'A'  # when 条件 then 要显示的值或语句
        				when salary > 10000 then 'B'  # ........
        				else 'C'                      # else 要显示的值或语句（未匹配状态，类似default）
        				end as salary_level           # end 结束case
        	from
        			 employees;

        ```

    - 分组函数（统计函数，组函数或聚合函数）

        ```sql
        # 用于统计功能，都忽略null值

        # 1. sum([distinct]expr) expr-统计字段 -> 求和,支持去重求和

        # 2. avg([distinct]expr) expr-统计字段 -> 求平均
        	# 以上只支持数值型且都忽略null值

        # 3. max([distinct]expr) expr-统计字段 -> 求最大值

        # 4. min([distinct]expr) expr-统计字段 -> 求最小值
        	# 以上支持可排序的数据型，日期，字符串等都可以

        # 5.count([distinct]expr) expr-统计字段 -> 求非空值个数
        	# 支持任何数据型，null型不计数
        	select 
        				count(*) as 表列数 
        	from 
        				employees;
        # 以上函数与其他字段共同拆查询有限制（分组函数输出只有一行），只能是group by的字段
        ```

- 分组查询

    ```sql
    # 1. 基本语法

    	select 分组函数,column_X #(指定是groupby后面的列)
      from table_x
    	[where conditions]  # 该条件不需要用到分组后的结果
    	group by  column_X
    	[having conditons]  # 该条件需要用到分组查询后的结果
    	[order by sub_expressions]

    # 2. 简单例子

    	# 查询每个工种的最高工资
    	select 
    				job_id,max(salary) as 最高工资
    	from 
    				employees
    	group by 
    				job_id;

    	# 查询每个位置上的部门个数
    	select 
    				location_id,count(*) as 部门个数
    	from 
    				departments
    	group by 
    				location_id;
    	
    	# 查询邮箱中包含a字符的每个部门的平均工资
    	select 
    				department_id,avg(salary)
    	from 
    				employees
    	where 
    				email like '%a%';
    	group by 
    				department_id;

    	# 查询每个部门，每个工种的最低工资
    	select 
    				department_id,job_id,min(salary)
    	from 
    				employees
    	group by 
    				department_id,job_id;

    # 3.添加分组后的筛选条件 
    	
    	# 查询每个工种 有奖金的员工的 大于10000的最大工资与工种编号
    		SELECT 
    					job_id,MAX(salary)
    		FROM 
    					employees
    		WHERE
    					commission_pct IS NOT NULL
    		GROUP BY 
    					job_id
    		HAVING 
    					MAX(salary)>10000

    		# 查询领导编号>102的每个领导手下的 最低工资大于5000 的领导编号与最低工资
    		select
    					manager_id,min(salary)
    		from
    					employees
    		where 
    					manager_id > 102
    		group by
    					manager_id
    		having 
    					min(salary)>5000;
    	
    ```

- 连接查询（多表查询）

    ```sql

    # 1.笛卡尔乘积现象
    	# 表1有m，表二有n，查询结果有m*n行
    	# 原因：没有正确的连接条件
    	select boy_name,girl_name from boys,girls;

    # 2.连接分类
    	# 语法 --- sql92标准
    		select 
    					column_one,column_two
    		from 
    					table_one as one,table_two as two
    		where 
    					one.id = two.id  # 连接条件 不可以缺！！！！
    		and  
    					conditions
    		group by
    					column_two;
    		order by 
    					column_one;

    		# 等值连接
    		# 查询员工名对应的部门名
    			select 
    						last_name,department_name
    			from
    						employees as e,departments as d # 可以为表起别名，位置可互换
    				# (但是，若有别名，则全语句不能再利用原表名进行查询了)
    				# 原因在于先执行from语句，生成别名；
    			where 
    						e.department_id = d.department_id; 

    		# 非等值查询
    			# 查询员工的工资与工资级别
    				select
    							salary,grade
    				from
    							employees e,grades g
    				where e.salary between g.lowest_sal and g.hignest_sal;	

    		# 自连接 （同一张表内的等值连接，利用起别名将表分成多张表）
    			# 查询员工名与领导名
    			select 
    						e.last_name as 员工名,
    						m.last_name as 领导名
    			from 
    						employees as e,
    						employees as m
    			where 
    						e.manager_id = m.employee_id

    	# 基本语法 - sql99标准
    		select column_one
    		from table_one [连接类型]
    		/*  
    				内连接 - inner （等值，非等值，自连接）
    				外连接: 
    							左外 - left [outer]
    							右外 - right [outer]
    						  全外 - full [outer]
    				交叉连接 - cross
    		*/
    		join table_two
    		on conditions # 连接条件
    		[where conditions ] # 筛选条件
    		[group by xxx]
    		[having conditions]
    		[order by xxx]

    		# 外连接
    			# 内连接显示匹配表对应列+所有主表信息（无匹配则null）
    			# 主表通过连接类型标识
    				# mysql不支持全外连接，功能即左右连接结果和
    				# 交叉连接 - 笛卡尔乘积

    			#一个女生表，一个男生表，查找女生男朋友不在男生表中的女生名字
    				select 
    							g.name
    				from 
    							girl as g
    				left join 
    							boy as b
    				on 
    							g.boyfriend_id = b.id
    				where
    							b.id is null; 
    					# 主表根据连接条件匹配从表，打印主表所有信息，未匹配的主表对应从表信息列值为null

    			
    		
    ```

- 子查询

    ```sql
    # 出现在其他语句内部的select语句 - 子查询或内查询
    	# 内部嵌套了其他select语句的为主查询，外查询
    /*
    	基本分类：
    			select后面
    					只支持标量子查询（结果集为一行一列）
    			from后面
    					支持表子查询（结果集多行多列）
    			where/having后面
    					支持标量子查询
    							行子查询（一行多列的结果集）
    							列子查询（一列多行的结果集）
    			exists后面（相关子查询）
    					支持表子查询
    */

    # 1. where/having后面的子查询 子查询作为条件，用()括起来，且子查询执行优先级高于主查询
    	# 标量子查询 搭配 单行操作符
    	  # 查询员工工资大于Abel的员工信息
    			select 
    						*
    			from 
    						employees
    			where 
    						salary > (
    						select salary
    						from employees
    						where last_name = 'Abel'
    						# 此处为标量子查询结果作为条件
    			);
    		
    		# 查询最低工资大于50号部门最低工资的部门id和其最低工资
    			select 
    						`department_id`,MIN(salary)
    			from 
    						employees
    			group by 
    						`department_id`
    			having 
    						MIN(salary) >(
    				select MIN(salary)
    				from employees
    				where `department_id` = 50  
    			);
    	
    			# 非法调用举例
    				/*
    					当标量子查询返回值null时
    					标量子查询没有搭配单行操作符
    				*/
    		# 列子查询 搭配 多行操作符 in(),all() etc
    			# 查询location_id是1400或1700的部门的所有员工姓名
    				select 
    							last_name
    				from
    							employees
    				where
    							department_id in(
    							select department_id
    							from departments
    							where location_id in(1400,1700)
    				)
    		# 行子查询
    			# 查询员工编号最小且工资最高的员工信息
    				select
    							*
    				from 
    							employees
    				where
    							(employee_id,salary) = (
    							select min(employee_id),max(salary)
    							from employees		
    				)

    # 2. select 后面
    			# 查询每个部门的员工个数
    				SELECT 
    						d.department_id,(
    							SELECT COUNT(*)
    							FROM employees AS e
    							WHERE e.department_id = d.department_id
    					) AS numbers
    				FROM 
    						`departments` AS d
    				ORDER BY 
    						d.department_id
    				# or 外连接+分组
    				SELECT 
    						d.`department_id` , COUNT(e.`employee_id`) AS numbers
    				FROM 
    						`departments` AS d 
    				LEFT JOIN 
    						`employees` AS e
    				ON 
    						d.department_id=e.department_id
    				GROUP BY
    					 d.department_id
    				 
    	# 3. exists后面
    		 # exists(expr)  expr 子查询结果集，若无则返回false，若子查询有结果则返回true		
    ```

- 分页查询，联合查询

    ```sql
    # 分页查询
    	# 也就是控制当前结果集的输出行数
    	# 关键字
    		limit [offset],size;
    			# offset 显示条目的起始索引（从0开始）
    			# size   显示条目的总个数
    		  # 该关键字放在查询语句最后

    		# 查询前五条员工信息
    			select 
    					*
    			from 
    					employees
    			limit 
    					0,5; 
    # 联合查询
    		# 将多条查询结果合并
    				/*
    					应用场景：查询多表，且表无连接关系时
    					注意：:匹配结果列数保持一致，且数据类型最好一致
    								 会自动去重（添加关键词 all）
    				*/
    		# 关键词语法 
    			query_one
    			union[all] # all 包含重复项合并
    			query_two
    			.......
    	
    ```

### 五、DML （Data Manipulation Language）

---

- 库（database）管理

    ```sql
    # 库的创建
    	create database [if not exists] base_one ; 

    # 库的使用
    	use base_one;
    	
    # 库的修改（一般为字符集更改，默认utf8）
    	alter database base_one character set another_set[gbk,utf8...] ;

    # 库的删除
    	drop database [if exists] base_one;
    ```

- 表（table）管理

    ```sql
    # 表的查看
    	show tables; # 所有表名
    	
    	desc table_name; # 查看表内容

    # 表的创建
    	create table table_name(
    			col_name_one col_type_one [],
    			col_name_two col_type_two [],
    			........
    			# 列名  列数据类型  [ 列约束 ] 
    		);

    # 表的修改
    	# 表名修改
    		alter table table_name
    		rename to table_new_name;

    	# 表的删除
    		drop table table_name;

    	# 列名修改
    		alter table table_name 
    		change column col_name
    		col_new_name col_new_type;
    		# 修改时，新的数据类型也要给出
    	
    	# 修改列的数据或约束
    		alter table table_name
    		modify column col_name
    		col_new_type;
    	
    	# 添加新列
    		alter table table_name
        add column col_name,col_type;

    	# 删除列
    		alter table table_name
    		drop column col_name;

    # 表的复制
    	# 表已存在
    		insert into table_to
    		value (...) |  select [子查询];
    	create table table_to like table_from; 
    		# 仅仅复制表的结构

    	create table table_to
    	select col_one,col_two...
    	from table_from
    	where conditions;
    		#  复制表的结构于数据，若不需要数据，只需要更改conditons即可
    ```

- 常见约束

    ```sql
    create table table_name(
    			col_name col_type [col_res 约束条件], 
    			col_name col_type [col_res 约束条件], # 列级约束
    			[ 表级约束 ]

    )

    alter table table_name 
    	
    	modify column column_name
    	col_type,col_cons
    | 
    	add 
    	primary key(col_name) |
    	unique(col_name) 
    |
    	drop
    	constrains;
    	 

    # 约束条件
    	NOT NULL   
    			# 该字段值不能为空

    	DEFAULT
    			#  该字段有默认值

    	PRIMARY
    	    # 主键，保证该字段的值具有唯一性且非空

    	UNIQUE
    			# 该字段值具有唯一性，可以为空

    	CHECK
    	    # 检查约束，mysql不支持

    	FOREIGN KEY
    	    # 外键，用于限制两表关系，用于保证该字段的值必须来自于主表的关联列的值

    	/*
    		列级约束：外键约束不产生效果，其他都有效
    		表级约束：除了非空、默认，其他都支持
    	*/

    # 例子
    	create database if not exists test_base;
    	ues test_base;
    	create table major(
    				id int primary key, # 主键
    				major_name varchar(20)
    	);
    	create table stuinfo(
    				id int primary key,
    							# 主键
    				stu_name varchar(20) not null,
    							# 非空
    				gender char(1) 
    									[check(gender='男' or gender='女')],
    							# 检查，语法如上
    				seat int unique,
    							# 唯一性
    				age int not null,
    							# 非空
    				classid int default 3
    							# 默认值
    				[majorid int  references major(id)]
    							# 外键引用
    	);

    ```

- 标识列

    ```sql
    关键词，类似约束
    	auto_increment;
     
    ```

### 六、TCL (Transaction Control Language)

待补充，to be continued
