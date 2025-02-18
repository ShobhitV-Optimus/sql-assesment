# sql-assesment



create table t_emp(
Emp_id int identity(1001,2) primary key,
Emp_code varchar(30),
Emp_f_name varchar(30) not null,
Emp_m_name varchar(30),
Emp_l_name varchar(30),
Emp_DOB date check(datediff(year,Emp_DOB,getdate())>=18),
Emp_DOJ date not null
)
drop table t_emp
select * from t_emp
select datediff(year,getdate(),'2001-03-09');

insert into t_emp(Emp_code,Emp_f_name,Emp_m_name,Emp_l_name,Emp_DOB,Emp_DOJ )
values  ('OPT20110507','Rahul','kumar','sharma','2001-02-25','2021-05-05'),
		('OPT20110508','sonu','kumar','shukla','2001-04-21','2022-06-03'),
		('OPT201105010','Abhay','kumar','garg','2001-05-31','2022-02-01'),
		('OPT20110511','Nityanan','kumar','raj','2001-04-30','2022-06-03'),
		('OPT20110512','prakash','kumar','dubey','2001-04-30','2022-06-03'),
		('OPT20110515','prateek','kumar','singh','2001-04-30','2022-06-03')

		---answer to 1st que
		select  concat_ws(' ',Emp_f_name,isnull(Emp_m_name,''),isnull(Emp_l_name,'')) as [Employee full name],Emp_DOB from t_emp where month(dateadd(day,1,Emp_DOB))<>month(Emp_DOB);


		drop table t_activity
		select * from t_activity

		create table t_activity(Activity_id int identity(1,1) primary key,Activity_description varchar (30) )

		insert into t_activity (Activity_description) values
		('code analysis'),
		('lunch'),
		('coding'),
		('knowledge transition'),
		('database'),
		('discussion')


		drop table t_attendence;
		select * from t_attendence
		create table t_attendence(
		Atten_id int identity(1001,1) primary key,
		Emp_id int foreign key references t_emp(Emp_id),
		Activity_id int foreign key references t_activity(Activity_id),
		Atten_start_datetime datetime,
		Attended_hrs int )

		insert into t_attendence(Emp_id,Activity_id,Atten_start_datetime,Attended_hrs) values
		(1001,1,'2011-02-13 10:00:00',2),
		(1003,2,'2011-05-15 10:00:00',3),
		(1005,2,'2011-04-16 10:00:00',5),
		(1007,3,'2012-02-19 10:00:00',8),
		(1009,4,'2013-02-13 10:00:00',6),
		(1011,5,'2010-04-02 10:00:00',7)

		select * from t_salary
		create table t_salary(
		Salary_id int identity(1,1) primary key,
		Emp_id int foreign key references t_emp(Emp_id),
		Changed_date date,
		New_Salary decimal
		)
		delete from t_salary
		insert into t_salary(Emp_id,Changed_date,New_Salary) values
		(1001,'2011-02-13',20000),
		(1001,'2012-03-13',25000),
		(1003,'2011-05-15',20000),
		(1003,'2012-06-15',30000),
		(1005,'2011-05-15',20000)
		insert into t_salary(Emp_id,Changed_date,New_Salary) values
		(1007,'2012-07-15',35000),
		(1007,'2012-06-18',40000),
		
		(1005,'2011-05-15',20000)

		with cte1 as(
		select t1.Emp_id, t1.New_Salary as prevsalary,t2.New_Salary as changed_salary from t_salary t1 join t_salary t2 on t1.Emp_id=t2.emp_id 
		where t1.New_Salary<>t2.New_Salary and t1.New_Salary<t2.New_Salary 
		)

		select concat_ws(' ',Emp_f_name,Emp_m_name,Emp_l_name) as [Employee full name],
		case
		    when prevsalary is null then 'not given'               ---here we can also use coalesce or isnull
			else cast(prevsalary as varchar)
		end as prevsalary
		,
		case
		    when changed_salary is null then 'not given'
			else cast(changed_salary as varchar)
		end as currentsalry
	
       from t_emp left join cte1 on
       t_emp.Emp_id=cte1.Emp_id

	   ---total hpurs worked,last work activity and hours qorked on that


	   select tattend.Emp_id,sum(Attended_hrs) over (partition by Emp_id) as [total hours worked], tattend.Activity_id as[activity performed],Activity_description,Attended_hrs
	    from t_attendence tattend left join t_activity tactivity on tattend.Activity_id=tactivity.Activity_id
