# Ghi chép cài đặt OpenLDAP

## Môi trường: 
     
     - Ubuntu 14.04

## Bắt đầu cài đặt

- Tải gói 

      apt-get -y install slapd ldap-utils

- Sau khi cài đặt xong các gói của OpenLDAP, kiểm tra bằng lệnh `slapcat` có kết quả như sau

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



- Chạy lệnh dưới để khai báo lại các thông tin với LDAP vì mặc định sẽ là `nodomain`

	sudo dpkg-reconfigure slapd

- Nhập các tùy chọn khai báo theo hướng dẫn ở màn hình	

- Sử dụng lệnh slapcat để kiểm tra, kết quả như dưới (lưu ý domain congto.vn)

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


- Tạo file `base.ldif` với nội dung dưới

	dn: ou=people,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou: people

	dn: ou=groups,dc=congto,dc=vn
	objectClass: organizationalUnit
	ou: groups 

- Chạy lệnh dưới để add các OU (people, groups)

	ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f base.ldif 

- Kết quả của lệnh trên

	root@u14-vagrant:~# ldapadd -x -D cn=admin,dc=congto,dc=vn -W -f base.ldif
	Enter LDAP Password:
	adding new entry "ou=people,dc=congto,dc=vn"

	adding new entry "ou=groups,dc=congto,dc=vn"

	root@u14-vagrant:~#


