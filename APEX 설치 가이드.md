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
 1.  **APEX 설치**입니다. APEX는 다수의 SCHEMA, PL/SQL PACKAGE 로 이루어져 있으며 DATABASE 서버내에 설치됩니다. 
 
 2.  APEX의 WEB LISTENER 역할을 담당하는 **ORDS 설치**입니다. ORDS는 4가지 모드로 설치 가능합니다.
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

[최신 APEX를 다운로드](https://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 받고 압축파일을 푼 후 

## Rename a file

You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.

## Delete a file

You can delete the current file by clicking the **Remove** button in the file explorer. The file will be moved into the **Trash** folder and automatically deleted after 7 days of inactivity.

## Export a file

You can export the current file by clicking **Export to disk** in the menu. You can choose to export the file as plain Markdown, as HTML using a Handlebars template or as a PDF.


# Synchronization

Synchronization is one of the biggest features of StackEdit. It enables you to synchronize any file in your workspace with other files stored in your **Google Drive**, your **Dropbox** and your **GitHub** accounts. This allows you to keep writing on other devices, collaborate with people you share the file with, integrate easily into your workflow... The synchronization mechanism takes place every minute in the background, downloading, merging, and uploading file modifications.

There are two types of synchronization and they can complement each other:

- The workspace synchronization will sync all your files, folders and settings automatically. This will allow you to fetch your workspace on any other device.
	> To start syncing your workspace, just sign in with Google in the menu.

- The file synchronization will keep one file of the workspace synced with one or multiple files in **Google Drive**, **Dropbox** or **GitHub**.
	> Before starting to sync files, you must link an account in the **Synchronize** sub-menu.

## Open a file

You can open a file from **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Open from**. Once opened in the workspace, any modification in the file will be automatically synced.

## Save a file

You can save any file of the workspace to **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Save on**. Even if a file in the workspace is already synced, you can save it to another location. StackEdit can sync one file with multiple locations and accounts.

## Synchronize a file

Once your file is linked to a synchronized location, StackEdit will periodically synchronize it by downloading/uploading any modification. A merge will be performed if necessary and conflicts will be resolved.

If you just have modified your file and you want to force syncing, click the **Synchronize now** button in the navigation bar.

> **Note:** The **Synchronize now** button is disabled if you have no file to synchronize.

## Manage file synchronization

Since one file can be synced with multiple locations, you can list and manage synchronized locations by clicking **File synchronization** in the **Synchronize** sub-menu. This allows you to list and remove synchronized locations that are linked to your file.


# Publication

Publishing in StackEdit makes it simple for you to publish online your files. Once you're happy with a file, you can publish it to different hosting platforms like **Blogger**, **Dropbox**, **Gist**, **GitHub**, **Google Drive**, **WordPress** and **Zendesk**. With [Handlebars templates](http://handlebarsjs.com/), you have full control over what you export.

> Before starting to publish, you must link an account in the **Publish** sub-menu.

## Publish a File

You can publish your file by opening the **Publish** sub-menu and by clicking **Publish to**. For some locations, you can choose between the following formats:

- Markdown: publish the Markdown text on a website that can interpret it (**GitHub** for instance),
- HTML: publish the file converted to HTML via a Handlebars template (on a blog for example).

## Update a publication

After publishing, StackEdit keeps your file linked to that publication which makes it easy for you to re-publish it. Once you have modified your file and you want to update your publication, click on the **Publish now** button in the navigation bar.

> **Note:** The **Publish now** button is disabled if your file has not been published yet.

## Manage file publication

Since one file can be published to multiple locations, you can list and manage publish locations by clicking **File publication** in the **Publish** sub-menu. This allows you to list and remove publication locations that are linked to your file.


# Markdown extensions

StackEdit extends the standard Markdown syntax by adding extra **Markdown extensions**, providing you with some nice features.

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.


## SmartyPants

SmartyPants converts ASCII punctuation characters into "smart" typographic punctuation HTML entities. For example:

|                |ASCII                          |HTML                         |
|----------------|-------------------------------|-----------------------------|
|Single backticks|`'Isn't this fun?'`            |'Isn't this fun?'            |
|Quotes          |`"Isn't this fun?"`            |"Isn't this fun?"            |
|Dashes          |`-- is en-dash, --- is em-dash`|-- is en-dash, --- is em-dash|


## KaTeX

You can render LaTeX mathematical expressions using [KaTeX](https://khan.github.io/KaTeX/):

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> You can find more information about **LaTeX** mathematical expressions [here](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference).


## UML diagrams

You can render UML diagrams using [Mermaid](https://mermaidjs.github.io/). For example, this will produce a sequence diagram:

```mermaid
sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```

And this will produce a flow chart:

```mermaid
graph LR
A[Square Rect] -- Link text --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NzY2NTk1ODIsLTEwNjQ3NDkzMDUsLT
E0NjUxMTE2NjUsMTA3NDc0OTM3NSwxMDk5OTkyNTYwXX0=
-->