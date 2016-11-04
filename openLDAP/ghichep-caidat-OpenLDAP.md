# Ghi chép cài đặt OpenLDAP

## Môi trường: 
     
     - Ubuntu 14.04

## Bắt đầu cài đặt

- Tải gói 

	```sh
	apt-get -y install slapd ldap-utils
	```

- Khi cài, bạn cần nhập mật khẩu cho tài khoản `admin`. Nhớ mật khẩu này để sử dụng lại trong các phần dưới.
- Sau khi cài đặt xong các gói của OpenLDAP, kiểm tra bằng lệnh `slapcat` có kết quả như sau

	```sh
	root@u14-vagrant:~# slapcat
	dn: dc=nodomain
	objectClass: top
	objectClass: dcObject
	objectClass: organization
	o: nodomain
	dc: nodomain
	structuralObjectClass: organization
	entryUUID: c0f413f4-34f5-1036-93fa-d9d5e354404f
	creatorsName: cn=admin,dc=nodomain
	createTimestamp: 20161102031112Z
	entryCSN: 20161102031112.622854Z#000000#000#000000
	modifiersName: cn=admin,dc=nodomain
	modifyTimestamp: 20161102031112Z

	dn: cn=admin,dc=nodomain
	objectClass: simpleSecurityObject
	objectClass: organizationalRole
	cn: admin
	description: LDAP administrator
	userPassword:: e1NTSEF9RWpxalpsZURvOXNDK0VLWnNEc0pwTzFhZXlTbzI2aDM=
	structuralObjectClass: organizationalRole
	entryUUID: c0f50598-34f5-1036-93fb-d9d5e354404f
	creatorsName: cn=admin,dc=nodomain
	createTimestamp: 20161102031112Z
	entryCSN: 20161102031112.629040Z#000000#000#000000
	modifiersName: cn=admin,dc=nodomain
	modifyTimestamp: 20161102031112Z
	```


- Chạy lệnh dưới để khai báo lại các thông tin với LDAP vì mặc định sẽ là `nodomain`. Khi nhập domain mới nên nhớ để khai báo cho các dòng bên dưới, ví dụ của tôi là domain `congto.vn`

	```sh
	sudo dpkg-reconfigure slapd
	````

- Nhập các tùy chọn khai báo theo hướng dẫn ở màn hình	

- Sử dụng lệnh slapcat để kiểm tra, kết quả như dưới (lưu ý domain congto.vn)

    ```sh
    root@u14-vagrant:~# slapcat
	dn: dc=congto,dc=vn
	objectClass: top
	objectClass: dcObject
	objectClass: organization
	o: CONGTO
	dc: congto
	structuralObjectClass: organization
	entryUUID: 88c7f1e2-34f7-1036-8647-5de91d1cb62b
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032357Z
	entryCSN: 20161102032357.374676Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032357Z

	dn: cn=admin,dc=congto,dc=vn
	objectClass: simpleSecurityObject
	objectClass: organizationalRole
	cn: admin
	description: LDAP administrator
	userPassword:: e1NTSEF9RUwyVXZEQWZ6NWdHb1IrRVpseDBCZkt4dFpwamlzREQ=
	structuralObjectClass: organizationalRole
	entryUUID: 88c8deae-34f7-1036-8648-5de91d1cb62b
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032357Z
	entryCSN: 20161102032357.380735Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032357Z

	root@u14-vagrant:~#
	```


- Tạo file `base.ldif` với nội dung dưới

	```sh
	dn: ou=people,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou: people

	dn: ou=groups,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou: groups
	```

- Chạy lệnh dưới để add các OU (people, groups)

	```sh
	ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f base.ldif
	```

- Kết quả của lệnh trên
	
	```sh
	root@u14-vagrant:~# ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f base.ldif
	Enter LDAP Password:
	adding new entry "ou=people,dc=congto,dc=vn"

	adding new entry "ou=groups,dc=congto,dc=vn"

	root@u14-vagrant:~#
	```

- Sau khi add thêm 2 OU, sử dụng lệnh `slapcat` sẽ thấy có thêm output mới như bên dưới

	```sh
	root@u14-vagrant:~# slapcat
	dn: dc=congto,dc=vn
	objectClass: top
	objectClass: dcObject
	objectClass: organization
	o: CONGTO
	dc: congto
	structuralObjectClass: organization
	entryUUID: 88c7f1e2-34f7-1036-8647-5de91d1cb62b
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032357Z
	entryCSN: 20161102032357.374676Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032357Z

	dn: cn=admin,dc=congto,dc=vn
	objectClass: simpleSecurityObject
	objectClass: organizationalRole
	cn: admin
	description: LDAP administrator
	userPassword:: e1NTSEF9RUwyVXZEQWZ6NWdHb1IrRVpseDBCZkt4dFpwamlzREQ=
	structuralObjectClass: organizationalRole
	entryUUID: 88c8deae-34f7-1036-8648-5de91d1cb62b
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032357Z
	entryCSN: 20161102032357.380735Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032357Z

	dn: ou=people,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou: people
	structuralObjectClass: organizationalUnit
	entryUUID: fcac0864-34f7-1036-8a89-0d5876863912
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032711Z
	entryCSN: 20161102032711.807452Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032711Z

	dn: ou=groups,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou:: Z3JvdXBzIA==
	structuralObjectClass: organizationalUnit
	entryUUID: fcac3d3e-34f7-1036-8a8a-0d5876863912
	creatorsName: cn=admin,dc=congto,dc=vn
	createTimestamp: 20161102032711Z
	entryCSN: 20161102032711.808809Z#000000#000#000000
	modifiersName: cn=admin,dc=congto,dc=vn
	modifyTimestamp: 20161102032711Z

	root@u14-vagrant:~#
	```

## Tạo user trong OpenLDAP 

- Sinh ra mật khẩu để sử dụng bằng lệnh `slappassword`

	```sh
	slappasswd 
	``

- Nhập mật khẩu muốn sử dụng, output sẽ là chuỗi ký tự, hãy chép lại chuỗi này để dùng ở đoạn dưới. Trong ví dụ này sẽ là `{SSHA}GABgiTnkTE9c3Y3Jij9U/122gkt0Ck2b`

- Tạo file `ldapuser.ldif` để khai báo các user cần thiết. Nội dung file dưới sẽ tạo user có tên là `maianhbao` và chuỗi ký tự đã mã hóa cho mật khẩu là `{SSHA}GABgiTnkTE9c3Y3Jij9U/122gkt0Ck2b`. Cần thay đúng với cấu hình của bạn nhé :) 

	```sh
	dn: uid=maianhbao,ou=people,dc=congto,dc=vn
	objectClass: inetOrgPerson
	objectClass: posixAccount
	objectClass: shadowAccount
	cn: maianhbao
	sn: maianhbao
	userPassword: {SSHA}GABgiTnkTE9c3Y3Jij9U/122gkt0Ck2b
	loginShell: /bin/bash
	uidNumber: 1000
	gidNumber: 1000
	homeDirectory: /home/maianhbao

	dn: cn=maianhbao,ou=groups,dc=congto,dc=vn
	objectClass: posixGroup
	cn: maianhbao
	gidNumber: 1000
	memberUid: maianhbao
	```

- Sử dụng lệnh dưới để tạo user `maianhbao`

```sh
ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f ldapuser.ldif 
```

- Nhập mật khẩu của `admin` lúc cài LDAP, kết quả sẽ như bên dưới

```sh
root@u14-vagrant:~# ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f ldapuser.ldif
Enter LDAP Password:
adding new entry "uid=maianhbao,ou=people,dc=congto,dc=vn"

adding new entry "cn=maianhbao,ou=groups,dc=congto,dc=vn"
```


## Add các user trong hệ thống vào LDAP

- Đây là việc add các user của Hệ điều hành (OS) vào LDAP, tức là các USER đã có trước đó (về bản chất 2 user này khác nhau, đây chỉ là động tác add các user của hệ điều hành vào LDAP mà thôi.)

- Tạo file `ldapuser.sh` với nội dung dưới

	```
	# extract local users and groups who have 1000-9999 digit UID
	# replace "SUFFIX=***" to your own domain name
	# this is an example
	 #!/bin/bash

	SUFFIX='dc=congto,dc=vn'
	LDIF='ldapuser1.ldif'

	echo -n > $LDIF
	GROUP_IDS=()
	grep "x:[1-9][0-9][0-9][0-9]:" /etc/passwd | (while read TARGET_USER
	do
	    USER_ID="$(echo "$TARGET_USER" | cut -d':' -f1)"

	    USER_NAME="$(echo "$TARGET_USER" | cut -d':' -f5 | cut -d' ' -f1,2)"
	    [ ! "$USER_NAME" ] && USER_NAME="$USER_ID"

	    LDAP_SN="$(echo "$USER_NAME" | cut -d' ' -f2)"

	    LASTCHANGE_FLAG="$(grep "${USER_ID}:" /etc/shadow | cut -d':' -f3)"
	    [ ! "$LASTCHANGE_FLAG" ] && LASTCHANGE_FLAG="0"

	    SHADOW_FLAG="$(grep "${USER_ID}:" /etc/shadow | cut -d':' -f9)"
	    [ ! "$SHADOW_FLAG" ] && SHADOW_FLAG="0"

	    GROUP_ID="$(echo "$TARGET_USER" | cut -d':' -f4)"
	    [ ! "$(echo "${GROUP_IDS[@]}" | grep "$GROUP_ID")" ] && GROUP_IDS=("${GROUP_IDS[@]}" "$GROUP_ID")

	    echo "dn: uid=$USER_ID,ou=People,$SUFFIX" >> $LDIF
	    echo "objectClass: inetOrgPerson" >> $LDIF
	    echo "objectClass: posixAccount" >> $LDIF
	    echo "objectClass: shadowAccount" >> $LDIF
	    echo "sn: $LDAP_SN" >> $LDIF
	    echo "givenName: $(echo "$USER_NAME" | awk '{print $1}')" >> $LDIF
	    echo "cn: $USER_NAME" >> $LDIF
	    echo "displayName: $USER_NAME" >> $LDIF
	    echo "uidNumber: $(echo "$TARGET_USER" | cut -d':' -f3)" >> $LDIF
	    echo "gidNumber: $(echo "$TARGET_USER" | cut -d':' -f4)" >> $LDIF
	    echo "userPassword: {crypt}$(grep "${USER_ID}:" /etc/shadow | cut -d':' -f2)" >> $LDIF
	    echo "gecos: $USER_NAME" >> $LDIF
	    echo "loginShell: $(echo "$TARGET_USER" | cut -d':' -f7)" >> $LDIF
	    echo "homeDirectory: $(echo "$TARGET_USER" | cut -d':' -f6)" >> $LDIF
	    echo "shadowExpire: $(passwd -S "$USER_ID" | awk '{print $7}')" >> $LDIF
	    echo "shadowFlag: $SHADOW_FLAG" >> $LDIF
	    echo "shadowWarning: $(passwd -S "$USER_ID" | awk '{print $6}')" >> $LDIF
	    echo "shadowMin: $(passwd -S "$USER_ID" | awk '{print $4}')" >> $LDIF
	    echo "shadowMax: $(passwd -S "$USER_ID" | awk '{print $5}')" >> $LDIF
	    echo "shadowLastChange: $LASTCHANGE_FLAG" >> $LDIF
	    echo >> $LDIF
	done

	for TARGET_GROUP_ID in "${GROUP_IDS[@]}"
	do
	    LDAP_CN="$(grep ":${TARGET_GROUP_ID}:" /etc/group | cut -d':' -f1)"

	    echo "dn: cn=$LDAP_CN,ou=Group,$SUFFIX" >> $LDIF
	    echo "objectClass: posixGroup" >> $LDIF
	    echo "cn: $LDAP_CN" >> $LDIF
	    echo "gidNumber: $TARGET_GROUP_ID" >> $LDIF

	    for MEMBER_UID in $(grep ":${TARGET_GROUP_ID}:" /etc/passwd | cut -d':' -f1,3)
	    do
	        UID_NUM=$(echo "$MEMBER_UID" | cut -d':' -f2)
	        [ $UID_NUM -ge 1000 -a $UID_NUM -le 9999 ] && echo "memberUid: $(echo "$MEMBER_UID" | cut -d':' -f1)" >> $LDIF
	    done
	    echo >> $LDIF
	done
	)
	```

- Thự thi file `ldapuser.sh`

	```sh
	bash ldapuser.sh
	```

-  Chạy lệnh dưới để add các tài khoản của OS vào LDAP

	```sh
	ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f ldapuser.ldif 
	```

## Xóa các user trong LDAP

```sh
Xem tại: https://www.server-world.info/en/note?os=Ubuntu_14.04&p=ldap&f=2
```

## Cấu hình `phpmyadminldap`

- Cài đặt apache
- Cài đặt php và các gói phụ trợ
- Cài đặt `phpmyadminldap`

```sh
apt-get -y install phpldapadmin
```

- Sửa cấu hình trong file `/etc/phpldapadmin/config.php`




