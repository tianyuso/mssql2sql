# mssql2sql

# mssql2sql 使用

[说明]  
本项目使用golang语言开发，借助fn_dblog函数解析sqlserver 日志，返回前滚SQL和回滚SQL  
1、理论上支持sqlserver 2008-2019版本，未完全测试所有版本。  
2、暂时不支持image,varbinary,xml类型解析，后续会慢慢添加  
3、暂时不支持列存储格式，图类型等，后续会慢慢添加  
4、只支持DML语句（update,insert,delete）  
5、ldf过大时，fn_dblog读取会日志会有很大的影响，恢复速度会比较慢。  
6、源码后续考虑开源。  
7、防止恶意程序，请执行前核对md5值是否和程序内的.MD5文件一致，  
   linux MD5命令：md5sum mslog2sql   
   windows MD5命令：certutil -hashfile mslog2sql.exe MD5  


[使用举例]  
1、准备sqlserver 环境，以sqlserver 2017为例  
2、测试表如下：  
CREATE TABLE [dbo].[t_cdc_t6](  
	[id] [int] NOT NULL,  
	[name] [varchar](20) NULL,  
	[addr] [nvarchar](20) NULL,  
	[ttime] [datetime] NULL,  
	[mess] [varchar](55) NULL,  
	[newcon] [nvarchar](100) NULL,  
	[col1] [bit] NULL,  
	[col2] [nchar](20) NULL,  
	[ctime] [datetime2](7) NULL,  
	[tint] [tinyint] NULL,  
	[flcol] [float] NULL,  
	[numer] [decimal](6, 2) NULL,  
	[dmoney] [money] NULL,  
 CONSTRAINT [PK_tcdc_t6] PRIMARY KEY NONCLUSTERED   
(  
	[id] ASC  
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]  
) ON [PRIMARY]  
GO  
3、测试dml语句如下：  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('1','ac','武汉','2023-09-06 09:55:52','tohi','new3',1,'col2s1','2023-09-07 09:55:52.322211',11,98.32,1001.32,1001.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('2','tc','广州','2023-09-06 09:55:52','hi','new1',1,'col2s2','2023-09-08 09:55:52.322211',11,99.32,1002.32,1002.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)  
values('3','ab','武汉','2023-09-06 09:55:52','hi1','new2',1,'col2s3','2023-09-10 09:55:52.322211',11,100.32,1003.32,1003.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)  
values('4','bc','长沙','2023-09-06 09:55:52','go','new5',1,'col2s4','2023-09-11 09:55:52.322211',11,101.32,1004.32,1004.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('5','ca','邯郸','2023-09-06 09:55:52','hi','new1',1,'col2s5','2023-09-12 09:55:52.322211',11,102.32,1005.32,1005.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)  
values('6','cb','深圳','2023-09-25 09:55:52','hi1','new1',1,'col2s6','2023-09-13 09:55:52.322211',11,103.32,1006.32,1006.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('7','cc','北京','2023-09-25 09:55:52','hi1','new1',1,'col2s7','2023-09-14 09:55:52.322211',11,104.32,1007.32,1007.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)  
values('8','cd','天津','2023-09-25 09:55:52','hi1','new1',1,'col2s8','2023-09-15 09:55:52.322211',11,107.32,1008.32,1008.32);  
update [t_cdc_t6] set addr ='hangzhou' where name ='ca';  
update [t_cdc_t6] set addr ='shenzhen' where name ='cd';  
update [t_cdc_t6] set addr ='handan' where name ='ca';  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('9','aa','苏州','2023-09-25 09:55:52','hi1','new1',1,'col2s9','2023-09-15 09:55:52.322211',11,100.32,1009.32,1009.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('10','aa','杭州','2023-09-25 09:55:52','hi1','new1',1,'col2s10','2023-09-15 09:55:52.322211',11,100.32,1010.32,1010.32);  
insert into [t_cdc_t6] ([Id], [name], [addr], [ttime], [mess], [newcon], [col1],[col2],[ctime],tint,flcol,numer,dmoney)   
values('11','cq','重庆','2023-09-25 09:55:52','hi1','new1',1,'col2s11','2023-09-15 09:55:52.322211',11,101.32,1010.32,1011.32);  
delete from t_cdc_t6 where name='cc';  

3、配置config.toml参数如下：  
[mssql]  
username = "sc" #建议使用sys权限用户，最低权限需要能创建临时表，已经查询fn_dblog权限  
password = "password"  
host = "10.10.10.12"  
port = 1433  
dbname = "test1"  
schema = "dbo"  
#start time for analyze, format: yyyy-MM-dd HH:mm:ss  
starttime="2023-10-31 14:40:02"  
#end time for analyze, format: yyyy-MM-dd HH:mm:ss  
endtime ="2023-10-31 14:47:30"  
include-table = ["t_cdc_t6"]  
#easycopy=true  
4、执行 ./mslog2sql  
[root@hana01 mslog2sql]# ./mslog2sql  
2023/11/17 15:52:35 ------------------ begin  print parameter ---------------------------------  
 Host:10.10.10.12  
 Port:1433  
 Dbname:test1  
 Table:dbo.t_cdc_t6  
 Username:sc  
 Password:password  
 StartTime:2023-10-31 14:40:02  
 EndTime:2023-10-31 14:47:30  
 EasyCopy:false  
2023/11/17 15:52:35 ------------------ end    print paramete  ---------------------------------  
2023/11/17 15:52:36 get  transaction log range success..
2023/11/17 15:52:37 get  transaction log  success..
2023/11/17 15:52:37 begin to parse  transaction log  ..
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000456 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000456 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000455 operation:LOP_DELETE_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000455 operation:LOP_DELETE_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000455 operation:LOP_DELETE_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000455 operation:LOP_DELETE_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000454 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000454 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000453 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000453 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000452 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- end transac:0000:00000452 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000451 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- end transac:0000:00000451 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000450 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- end transac:0000:00000450 operation:LOP_MODIFY_ROW --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044f operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044f operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044e operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044e operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044d operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044d operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044c operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044c operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044b operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044b operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:0000044a operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:0000044a operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000449 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- end transac:0000:00000449 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000445 operation:LOP_FORMAT_PAGE --]
2023/11/17 15:52:37 [-- end transac:0000:00000445 operation:LOP_FORMAT_PAGE --]
2023/11/17 15:52:37 [-- begin to analyze transac:0000:00000445 operation:LOP_FORMAT_PAGE --]
2023/11/17 15:52:38 [-- end transac:0000:00000445 operation:LOP_FORMAT_PAGE --]
2023/11/17 15:52:38 [-- begin to analyze transac:0000:00000444 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:38 [-- end transac:0000:00000444 operation:LOP_INSERT_ROWS --]
2023/11/17 15:52:38 parse  transaction log  finished
![image](https://github.com/tianyuso/mssql2sql/assets/23518704/d97e420d-692d-4afa-bf88-39c58a0c9104)
![image](https://github.com/tianyuso/mssql2sql/assets/23518704/85c43a9a-94e8-4d96-b404-b987f6ab7e35)


5、将config.toml文件#easycopy=true修改为easycopy=true，再次执行./mslog2sql，会单独将回滚SQL输出，方便执行回滚操作。  
 Easy EXEC SQL:  
[--------------------------------- begin -------------------------------------------------]  
// LSN:2023/10/31 14:46:58, Transcation BeginTime:00000024:00000589:0005  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=11;  
// LSN:2023/10/31 14:46:16, Transcation BeginTime:00000024:00000587:0005  UNDOSQL:  
insert into [dbo].[t_cdc_t6](id,name,addr,ttime,mess,newcon,col1,col2,ctime,tint,flcol,numer,dmoney,) values(7,'cc',N'北京','2023-09-25 09:55:52.000','hi1',N'new1',1,N'col2s7','2023-09-14 09:55:52.3222110',11,104.32,1007.32,1007.3200);  
// LSN:2023/10/31 14:45:26, Transcation BeginTime:00000024:00000585:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=10;  
// LSN:2023/10/31 14:45:26, Transcation BeginTime:00000024:00000583:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=9;  
// LSN:2023/10/31 14:45:18, Transcation BeginTime:00000024:00000582:0002  UNDOSQL:  
update  [dbo].[t_cdc_t6] set [addr]=N'hangzhou' where [id]=5;  
// LSN:2023/10/31 14:45:13, Transcation BeginTime:00000024:00000581:0002  UNDOSQL:  
update  [dbo].[t_cdc_t6] set [addr]=N'天津' where [id]=8;  
// LSN:2023/10/31 14:44:48, Transcation BeginTime:00000024:00000580:0002  UNDOSQL:  
update  [dbo].[t_cdc_t6] set [addr]=N'邯郸' where [id]=5;  
// LSN:2023/10/31 14:44:41, Transcation BeginTime:00000024:0000057e:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=8;  
// LSN:2023/10/31 14:44:41, Transcation BeginTime:00000024:0000057c:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=7;  
// LSN:2023/10/31 14:44:35, Transcation BeginTime:00000024:0000057a:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=6;  
// LSN:2023/10/31 14:44:35, Transcation BeginTime:00000024:00000578:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=5;  
// LSN:2023/10/31 14:44:35, Transcation BeginTime:00000024:00000576:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=4;  
// LSN:2023/10/31 14:44:19, Transcation BeginTime:00000024:00000574:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=3;  
// LSN:2023/10/31 14:44:19, Transcation BeginTime:00000024:00000572:0002  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=2;  
// LSN:2023/10/31 14:44:19, Transcation BeginTime:00000024:00000569:001a  UNDOSQL:  
delete  from [dbo].[t_cdc_t6] where [id]=1;  
[--------------------------------- end   -------------------------------------------------]  

6、命令行模式,需要将mode参数设置为cmd，可以使用命令行模式执行恢复程序，举例如下：  
./mslog2sql -mode='cmd' -db='test1' -host='10.10.10.12' -port=1433  -user 'sc' -pwd 'password' -schema 'dbo' -table 't_cdc_test6' -starttime '2023-10-30 17:45:02' -endtime '2023-11-15 17:50:30' -easycopy true  


### 感谢  
本程序参考诸多论坛技术文档及开源项目，对各位技术分享表示感谢，在此不一一列举。  
另外由于本人水平有限，程序可能存在不能解析，或者错误等问题，欢迎大家提ISSues
