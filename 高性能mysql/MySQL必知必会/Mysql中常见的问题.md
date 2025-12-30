	主键（primary key）：一列（或一组列），其值能够唯一区分表中每个行，唯一标识表中每行的这个列（或这组列）称为主键
		条件：
		任意两行都不具有相同的主键值；
		每个行都必须具有一个主键值（主键列不允许NULL值）。


	外键（foreign key） 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。
	外键是一种数据库关系，用于连接两个表的数据，一个表中的外键是另一个表的主键。外键用于保证数据的引用完整性，即当一个表中的数据被引用时，该数据的修改和删除操作受到限制，以防止数据不一致。外键的创建通常涉及到在创建表的时候声明，指定哪个字段是外键，并参考哪个表的主键。外键的创建需要遵循一定的规则，例如外键字段的类型和大小需要与主键字段一致，外键字段可以被设置为可为空或不可为空，这取决于所需的关系类型（可选或必需）。


	ORDER BY应放在所有SELECT子句后面


	判断某列是否为空时应该用：WHERE age IS NOT NULL;


	分组嵌套
	# 对性别和学校分组
	SELECT gender, university, COUNT(id), AVG(active_days_within_30), AVG(question_cnt)
	FROM user_profile
	GROUP BY university, gender;
	
	![[Pasted image 20240729150617.png]]


	GROUP BY子句必须出现在WHERE子句之后， ORDER BY子句之前。


	WHERE过滤行，而HAVING过滤分组。WHERE在数据分组前进行过滤， HAVING在数据分组后进行过滤。
	SELECT vend_id, COUNT(*) AS num_prods FROM products WHERE prod_price >= 10 GROUP BY vend_id HAVING COUNT(*) >= 2;
	WHERE子句过滤所有prod_price至少为10的行。然后按vend_id分组数据， HAVING子句过滤计数为2或2以上的分组。
	GROUP BY 之前使用WHERE子句过滤行，GROUP BY 之后使用 HAVING 过滤组


	CASE语句用法：
	`CASE 
	WHEN 条件1 THEN 结果1 
	WHEN 条件2 THEN 结果2 
	... 
	ELSE 默认结果 
	END`


	substring_index函数的语法及其用法：
	语法：substring_index(string,sep,num)
	即substring_index(字符串,分隔符,序号)
	
	参数说明
	string：用于截取目标字符串的字符串。可为字段，表达式等。
	
	sep：分隔符，string存在且用于分割的字符，比如“，”、“.”等。
	
	num：序号，为非0整数。若为正数则表示从左到右数，若为负数则从右到左数。比如“www.mysql.com”截取字符‘www’，分割符为“.”，从左到右序号为1，即substring_index(“www.mysql.com”,‘.’,1)；若从右开始获取“com”则为序号为-1即substring_index(“www.mysql.com”,‘.’,-1)
	如果要取字符串中间的部分可以嵌套这个函数得到。
	比如从www.mysql.com中取mysql, 可以substring_index(substring_index('www.mysql.com','.',-2),'.',1)
	其中substring_index('www.mysql.com','.',-2)将得到mysql.com