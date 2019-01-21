# APEX 및 ORDS 설치 가이드

해당 문서는 ON-PREMISE 환경에서 APEX 설치 과정을 설명합니다. WEB SERVER를 위한 TOMCAT 세팅이 완료되었다는 가정하에 문서가 작성되었습니다.

## 버전 정보
- ORACLE DATABASE : 11GR2
- ORACLE APEX : 18.2
- TOMCAT : 8.5.37
- ORDS (ORACLE RESTFUL DATA SERVICE) : 18.4.0

## 설명
 APEX는 ORACLE DATABASE 안에 내장되어 있는 WEB DEVELOPMENT를 위한 PLATFORM 입니다. ORACLE DB를 설치하면 기본적으로 내장되어 있으나 버전이 최신이 아닐 수 있으므로 OTN(ORACLE TECHNOLOGY NETWORK)에 등재되어 있는 [최신 APEX를 다운로드](https://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 받아서 사용하는 것을 권장합니다. 
 
 APEX 설치는 크게 두가지의 단계로 이루어져 있습니다.
 1.  **APEX 설치**. APEX는 다수의 SCHEMA, PL/SQL PACKAGE 로 이루어져 있으며 DATABASE 서버내에 설치됩니다. 
 
 2.  APEX의 WEB LISTENER 역할을 담당하는 **ORDS 설치**. ORDS는 4가지 모드로 설치 가능합니다.
 -- STANDALONE 모드 : APEX가 설치된 DB 서버에 ORDS 설치
 -- ORACLE WEBLOGIC SERVER에 설치
 -- GLASSFISH SERVER에 설치
 -- **APACHE TOMCAT에 설치**
이 문서는 APACHE TOMCAT에 설치하는 것을 설명합니다.
     >NOTE :  
     APEX, ORDS 버전 별 지원되는 플랫폼이 상이할 수 있으므로 APEX버전, ORDS 버전, TOMCAT 버전을 필히 확인 후 설치하시기 바랍니다.
[설치 전 버전 확인](https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/18.3/aelig/installing-REST-data-services.html#GUID-57B22E09-081E-4326-A9D5-61635B518931)

## APEX 설치 [DB 서버에서 작업]
> Note:
>설치 전 시스템 요구사항을 확인 후 설치합니다. [링크로 이동](https://docs.oracle.com/en/database/oracle/application-express/18.2/htmig/Oracle-AE-installation-requirements.html#GUID-02BE4A34-B631-412C-8A82-EB92DABBACE0)
### APEX tablespace 생성
APEX에서 사용하는 SCHEMA, TABLE들의 관리 편이성를 위하여 별도의 tablespace를 두고 사용하는 것이 좋습니다.

	-- For OMF
	CREATE TABLESPACE apex2 DATAFILE SIZE 100M AUTOEXTEND ON NEXT 1M; 
	-- For non-OMF
	CREATE TABLESPACE apex DATAFILE '/path/to/datafiles/apex01.dbf' SIZE 100M AUTOEXTEND ON NEXT 1M;

### APEX 다운로드 및 설치

1. [최신 APEX를 다운로드](https://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 받고 압축파일을 푼 후 압축파일 푼 폴더의 경로로 이동합니다.
2. SQL *Plus 에서 DATABASE에 접속. APEX는 SYSDBA role을 가진 SYS 유저로 진행합니다.

		$ sqlplus /nolog
		SQL> CONNECT SYS as SYSDBA
		Enter password: SYS_password
  
	    SQL> @apexins.sql APEX APEX TEMP /i/ -- Full development 환경을 설치
    - 설치 패키지 뒤의 parameter는 각각 순서대로 APEX APPLICATION USER tablespace, APEX FILE USER tablespace, APEX 가상 이미지 디렉토리 이름을 의미합니다.

3. APEX 설치가 완료되면 APEX_180200, FLOWS_FILES, APEX_PUBLIC_USER 가 생성됩니다

		SQL> select username, created, account_status, lock_date from dba_users where created > trunc(sysdate) order by 2; --생성한 USER 확인
		
### APEX instance admin account 생성
APEX 개발 환경을 총 관리하는 APEX ADMIN ACCOUNT를 생성합니다. ADMIN 유저는 APEX 설치 후 최초 접속 시에도 사용합니다.

	SQL> @apxchpwd.sql --추후에 ADMIN 패스워드를 잊어버렸을 때에도 이 스크립트를 돌려 PASSWORD RESET할 수 있음.

### APEX_PUBLIC_USER 설정
APEX_PUBLIC_USER는 최초 생성 시 RANDOM PASSWORD로 설정되어 있습니다. 초기 비밀번호 세팅을 수행합니다.

	SQL> ALTER PROFILE DEFAULT limit PASSWORD_LIFE_TIME UNLIMITED ;


APEX_PUBLIC_USER는 ORDS에서 ORACLE APEX 내 PL / SQL Gateway 작업 (예 : 모든 Oracle Application Express 작업)을 호출 할 때 사용되는 데이터베이스 사용자입니다. 
ORACLE DATABASE 11G 이상 버전 부터는 DEFAULT USER PROFILE에 PASSWORD LIFE TIME이 180일로 설정되어 있습니다. 이 제한으로 인해 ORDS에서 APEX로 CONNECTION 시 PASSWORD가 LOCK-IN되어 문제가 발생할 수 있으므로 해당 유저의 PASSWORD 설정을 변경합니다.

	SQL> ALTER PROFILE DEFAULT limit PASSWORD_LIFE_TIME UNLIMITED;
	


## ORDS 설치 [Tomcat 서버에서 작업]
>Note:
>이 문서는 ORDS 구성에 필요한 Tomcat 및 JAVA가 이미 설치되어 있다는 가정하에 작성되었습니다. Tomcat 및 JAVA 설치 및 환경 세팅관련은 다른 문서를 참조하세요.

### 설치 환경
- OS : OEL(Oracle Enterprise Linux) 6.9
- JDK 8 이상
- Apache Tomcat 8.5 이상
### 사전 점검 사항
TOMCAT 서버와 DATABASE 서버와의 통신을 위해 DB 서버 LISTENER 포트를 열어 줍니다. 

### ORDS 다운로드
[다운로드 링크](https://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html) 에서 최신 ORDS를 다운로드 받습니다.

	$ mkdir ords 
	$ unzip ords-18.4.0.354.1002.zip -d ords	

### ORDS 설치
압축을 푼 경로로 들어갑니다.

	$ cd ords 
	$ ls -al
	drwxr-xr-x. 7 root root     4096 Jan 18 07:51 .
	drwxr-xr-x. 3 root root     4096 Jan 18 07:06 ..
	drwxr-xr-x. 2 root root     4096 Jan 18 07:51 conf
	drwxr-xr-x. 3 root root     4096 Jan 11 10:15 docs
	drwxr-xr-x. 6 root root     4096 Oct 15 17:29 examples
	-rw-r--r--. 1 root root    31362 Dec 14 15:32 index.html
	drwxr-xr-x. 3 root root     4096 Jan 18 07:40 ords
	-rw-r--r--. 1 root root 59033835 Jan 18 07:40 ords.war
	drwxr-xr-x. 2 root root     4096 Jan 18 08:25 params

다음의 명령어로 ORDS를 설치합니다. 아래 예시를 참고하여서 설치를 진행합니다. 

	$ java -jar ords.war install advanced
	Enter the name of the database server [DB host name]: <Database Server IP>
	Enter the database listen port [1521]: 1521
	Enter 1 to specify the database service name, or 2 to specify the database SID [1]:1
	Enter the database service name: <database service name>
	Enter 1 if you want to verify/install Oracle REST Data Services schema or 2 to skip this step [1]:1
	
	-- 위의 정보로 database에 jdbc connection pool을 생성하여 ORDS_PUBLIC_USER를 생성함 
	
	Enter the database password for ORDS_PUBLIC_USER:
	Confirm password:           
	Requires SYS AS SYSDBA to verify Oracle REST Data Services schema.

	Enter the database password for SYS AS SYSDBA:
	Confirm password:

	Retrieving information.
	Enter the default tablespace for ORDS_METADATA [SYSAUX]: APEX
	Enter the temporary tablespace for ORDS_METADATA [TEMP]: TEMP
	Enter the default tablespace for ORDS_PUBLIC_USER [USERS]: APEX
	Enter the temporary tablespace for ORDS_PUBLIC_USER [TEMP]: TEMP
	Enter 1 if you want to use PL/SQL Gateway or 2 to skip this step.
	If using Oracle Application Express or migrating from mod_plsql then you must 	enter 1 [1]: 1
	Enter the PL/SQL Gateway database user name [APEX_PUBLIC_USER]: APEX_PUBLIC_USER
	Enter the database password for APEX_PUBLIC_USER:
	Confirm password:
	Enter 1 to specify passwords for Application Express RESTful Services database users (APEX_LISTENER, APEX_REST_PUBLIC_USER) or 2 to skip this step [1]:1
	Enter the database password for APEX_LISTENER:
	Confirm password:
	Enter the database password for APEX_REST_PUBLIC_USER:
	Confirm password:
	Jan 18, 2019 8:59:00 AM
	INFO: reloaded pools: []
	Installing Oracle REST Data Services version 18.4.0.r3541002
	... Log file written to /root/ords_install_core_2019-01-18_085900_00587.log
	... Verified database prerequisites
	... Created Oracle REST Data Services schema
	... Created Oracle REST Data Services proxy user
	... Granted privileges to Oracle REST Data Services
	... Created Oracle REST Data Services database objects
	... Log file written to /root/ords_install_datamodel_2019-01-18_085910_00552.log
	... Log file written to /root/ords_install_apex_2019-01-18_085911_00486.log
	Completed installation for Oracle REST Data Services version 18.4.0.r3541002. Elapsed time: 00:00:11.757

	Enter 1 if you wish to start in standalone mode or 2 to exit [1]:2
설치 진행 시 생성되는 DATABASE USER에 대한 설명은 아래의 표를 참고하세요.
|USER NAME        |설명                                     
|---------------- |-------------------------------
|`APEX_REST_PUBLIC_USER` |Application Express 작업 공간에 정의 된 RESTful 서비스에 액세스중인 경우 Oracle Application Express RESTful Services를 호출 할 때 사용되는 데이터베이스 사용자           
|`APEX_LISTENER`           |Application Express 작업 공간에 정의 된 RESTful 서비스에 액세스중인 경우 Oracle Application Express에 저장된 RESTful 서비스 정의를 쿼리하는 데 사용되는 데이터베이스 사용자
|`ORDS_METADATA`           |많은 Oracle REST Data Services 기능을 구현하는 데 사용되는 PL / SQL 패키지의 소유자. ORDS_METADATA는 Oracle REST Data Services 사용 가능 스키마에 대한 메타 데이타가 저장되는 곳입니다.Oracle REST Data Services는 직접 액세스하지 않습니다. Oracle REST Data Services 애플리케이션은 ORDS_METADATA 스키마에 연결을 생성하지 않습니다. 스키마 암호가 임의 문자열로 설정되고 연결 권한이 철회되며 암호가 만료됩니다.
|`ORDS_PUBLIC_USER`|Oracle REST Data Services 사용 가능 스키마에서 RESTful 서비스를 호출하는 사용자.
### DB서버에서 방금 생성된 USER 확인
	SQL> select username, created from dba_users order by created desc;
	USERNAME                       CREATED
	------------------------------ ---------
	ORDS_METADATA                  18-JAN-19
	ORDS_PUBLIC_USER               18-JAN-19
	APEX_LISTENER                  18-JAN-19
	APEX_REST_PUBLIC_USER          18-JAN-19
	APEX_INSTANCE_ADMIN_USER       17-JAN-19
	FLOWS_FILES                    17-JAN-19
	APEX_PUBLIC_USER               17-JAN-19
	APEX_180200                    17-JAN-19
	.....

### ORDS 이미지 디렉토리 생성
1. DATABASE 서버에 APEX 가 설치된 경로에 있는 images 폴더를 TOMCAT서버에 복사합니다. 아래의 명령어는 참고용입니다.

		scp -i /home/oracle/privateKey -r /home/oracle/apex/images opc@<db server ip>:<target directory>
	
2. TOMCAT 서버에 이미지 디렉토리 i 생성
APEX 설치 시 이미지 가상 디렉토리를 /i/ 로 설정해주었으므로 TOMCAT 설치경로의 webapps 디렉토리에 /i/를 생성하고 방금 가져온 images 폴더의 내용을 복사합니다.
		
		$ mkdir $CATALINA_HOME/webapps/i/
		$ cp -R /tmp/apex/images/* $CATALINA_HOME/webapps/i/
3. ords.war 파일을 TOMCAT의 webapps 폴더 안에 복사합니다.

		cd <ords 설치경로>
		cp ords.war $CATALINA_HOME/webapps/		

### 접속 확인
위의 작업을 정상적으로 마쳤다면 아래의 URL로 APEX가 접속 가능합니다. 
http://<server-ip<dfdf>>:8080/ords/

![enter image description here](https://lh3.googleusercontent.com/P00p1crSLckMKbA_7piO0tDdWZMHx6wYBhx_N3c8mUjPgR50VFGhMDUT5fVqUW0vW4v5C4ZPzhVb)


## APEX SETTING
APEX 최초 접속 시 INTERNAL WORKSPACE에 ADMIN 유저로 접속합니다.

- WORKSPACE : INTERNAL
- USERNAME : ADMIN
- PASSWORD : APEX 설치 시 입력했던 ADMIN PASSWORD

![enter image description here](https://lh3.googleusercontent.com/P00p1crSLckMKbA_7piO0tDdWZMHx6wYBhx_N3c8mUjPgR50VFGhMDUT5fVqUW0vW4v5C4ZPzhVb)

화면 상단에 Create Workspace 버튼을 클릭하여 workspace 를 생성합니다. 
![enter image description here](https://lh3.googleusercontent.com/PKM2inLXPp4LtNTay9W318JX5bk2Q9IZBPuPMyxFAC1F_Q6XTJWe2jQdlKkPuWs1desOV5Ii1J-e)

WORKSPACE와 SCHEMA와의 관계는 아래의 그림을 참고하세요. 
![enter image description here](https://lh3.googleusercontent.com/HDICVNY9zsGpK8V7R8qCHYm43BA4Q59kv7fGMrkfN1OiT9d6h6fdZqAtFmNkdjgxGwa-tSyvYszW)

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA4MTI4OTg2LDE4OTI0OTg1MDMsMzE4OT
M0MDA1LDczMzM0MjI5Miw0ODU3OTcyODYsLTM3NjkxMjczMiwt
MTAwMjkzMzE2LDE5NDY1MzY4OTQsOTIxMTQ1MzQsMTMwNTE4NT
cwOCwtMTA2NDc0OTMwNSwtMTQ2NTExMTY2NSwxMDc0NzQ5Mzc1
LDEwOTk5OTI1NjBdfQ==
-->