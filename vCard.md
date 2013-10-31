### vCard介绍
* http://en.wikipedia.org/wiki/VCard
* http://www.w3.org/2002/12/cal/vcard-notes.html

### 一个简单的vCard数据

    联系人信息:
    姓：Doe
    名：John
    公司：Example.com Inc.
    职位：Imaginary test person
    Email：johnDoe@example.org
    工作电话：+16175551212
    移动电话：+17815551212
    家庭电话：+12025551212
    家庭住址：2 Example Avenue Anytown NY USA 01111
    
    对于的vCard信息即为：
    
    BEGIN:VCARD
    VERSION:3.0
    N:Doe;John;;;
    FN:John Doe
    ORG:Example.com Inc.;
    TITLE:Imaginary test person
    EMAIL;type=INTERNET;type=WORK;type=pref:johnDoe@example.org
    TEL;type=WORK;type=pref:+1 617 555 1212
    TEL;type=CELL:+1 781 555 1212
    TEL;type=HOME:+1 202 555 1212
    ADR;type=HOME:;;2 Example Avenue;Anytown;NY;01111;USA
    END:VCARD

### vCard数据格式的规则
* vCard数据以BEGIN:VCARD 开始，以 END:VCARD 结尾
* vCard数据第二行最好带上版本号信息 VERSION:3.0 目前最新版本为4.0
* vCard数据每行代表一个字段信息，每行内容都应该包括字段标签和字段内容，标签和内容以‘:’分割，标签和内容的分段信息又以‘;’分割，因此切记需要避免字段数据带有上面两个符号
* vCard定义了丰富的基本标签字段，并且还允许软件自定义额外的标签，额外标签以‘X-’为前缀，但是为了统一性，应该尽量避免自定义的额外标签字段，具体内容可以参见vCard标准
* 对于所有的字段标签，‘type=’表示该标签的属性，标签属性可以连续定义，其中‘type=pref’表示假如该vCard数据包含有同样的标签（联系人允许多字段 ），且这些标签的属性又是一样的，那么用‘type=pref’表示这个字段为首选字段

### 姓名字段规则

    姓名的全部信息规则 N
    N:A; B; C; D;E;
    A – 表示姓
    B – 表示名
    C – 表示中间名
    D – 表示前缀
    E – 表示后缀
    上述的内容规则，分号隔开的各个部分其实可以自行定义，但是上述的表示应该为最标准的表示，假如还有更多内容，后面可以再带入F;G…

    昵称信息规则 NICKNAME
    NICKNAME:A
    A – 表示昵称信息
    
    名字显示信息规则 FN
    FN:A
    A – 表示名字信息
    名字显示规则用来表示某些通讯录能够自定义显示的名字内容，该内容和上述的‘N’内容可以不一样，假如‘N’内容为空，那么将建议用该字段内容作为名字

### 公司、职位、备注字段规则

    公司部门信息规则 ORG
    ORG:A; B;
    A – 表示公司
    B – 表示部门
    上述的内容规则，分号隔开的各个部分其实可以自行定义，但是上述的表示应该为最标准的表示，假如还有更多内容，后面可以再带入C;D…

    职位信息规则 TITLE
    TITLE:A
    A – 表示职位

    备注信息规则 NOTE
    NOTE:A
    A – 表示备注

### 电话、地址、邮箱、URL、即时通讯多字段规则

    电话信息规则 TEL
    TEL;type=A;type=B:C
    A – 表示多标签中的Label属性，目前通用的有 CELL、HOME、WORK、MAIN、PAGER、OTHER
    B – 表示电话号码类型，目前通用的有 VOICE、FAX
    C – 电话号码内容，假如你需要表示一个家庭传真，那么正确的写法应该是：
        TEL;type=HOME;type=FAX:0755-88899999

    地址信息规则 ADR
    ADR;type=A:B-1;B-2;B-3;C;D;E;F
    A – 表示多标签中的Label属性，目前通用的有 HOME、WORK、OTHER
    B – 表示地址的街道信息，其中B-1，B-2，B-3分别表示街道的多行信息，最多3行，假如街道信息没有3行，那么应该往后对齐，前面的‘；’不能省略
    C – 表示地址的城市信息
    D – 表示地址的省份/州信息
    E – 表示地址的邮政编码信息
    F – 表示地址的国家信息

    邮箱信息规则 EMAIL
    EMAIL;type=A;type=B:C
    A – 表示邮箱类型，目前通用的有 INTERNET
    B – 表示多标签中的Label属性，目前通用的有 HOME、WORK、OTHER
    C – 表示邮箱信息

    URL信息规则 URL
    EMAIL;type=A:B
    A – 表示多标签中的Label属性，目前通用的有 HOMEPAGE、HOME、WORK、OTHER
    B – 表示URL信息

    IM信息规则 IMPP
    IMPP;type=A:B:C
    A – 表示多标签中的Label属性，目前通用的有 HOME、WORK、OTHER
    B – 表示IM类型 AIM、Facebook、Gadu-Gadu、Google Talk、ICQ、Jabber、MSN、QQ、Skype、Yahoo
    C – 表示IM信息

### 照片、生日字段规则

    照片信息规则 PHOTO
    PHOTO;encoding=A;type=A:C
    A – 表示照片的编码方式，目前通用为Base64编码 B
    B – 表示照片类型信息 JPEG、PNG、GIF
    C – 表示照片内容的Base64编码字符串

    生日信息规则 BDAY
    BDAY:A
    A – 表示生日信息，格式为YYYYMMDD

### 自定义Label字段规则

    自定义Label字段规则由于通讯录的差异性，这里只列举apple的方案：
    item1.EMAIL;type=INTERNET:johnDoe@example.org
    item1.X-ABLabel:XXX

    前缀item1为自增的一个序号，对于所有的字段都适用，即使不带入自定义标签信息；item1.后面的内容和之前的规则一样；
    对于自定义label需要重新换行，并且还是相同序号，apple以X-ABLabel:作为标示符，‘:’后门为label名字。
    下面列举一个‘苹果中国’的vCard样式：
    
    BEGIN:VCARD
    VERSION:3.0
    PRODID:-//Apple Inc.//Address Book 6.1.3//EN
    N:;;;;
    FN:苹果中国
    ORG:苹果中国;
    TEL;type=MAIN;type=pref:800 810 2323
    item1.TEL:400 6272273
    item1.X-ABLabel:Apple Care
    item2.TEL:400 6927753
    item2.X-ABLabel:Apple Store
    item3.ADR;type=WORK;type=pref:;;朝阳区建国门外大街甲 12 号，新华保险大厦 9 层;北京;;100022;中国
    item3.X-ABADR:cn
    item4.URL;type=pref:http://www.apple.com.cn/
    item4.X-ABLabel:_$!<HomePage>!$_
    X-ABShowAs:COMPANY
    X-ABUID:F478F832-0078-4EB7-A37F-C987BEDD6535:ABPerson
    END:VCARD