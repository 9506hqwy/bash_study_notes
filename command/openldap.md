# OpenLDAP

## 標準スキーマ

[RFC 4524](https://www.rfc-editor.org/rfc/rfc4524.txt) COSINE LDAP/X.500 Schema を追加する。
SASL EXTERNAL 認証 (-Q -Y EXTERNAL) を利用する。

```sh
ldapadd -Q -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
```

```text
adding new entry "cn=cosine,cn=schema,cn=config"
```

[RFC 2798](https://www.rfc-editor.org/rfc/rfc2798.txt) Definition of the inetOrgPerson LDAP Object Class を追加する。

```sh
ldapadd -Q -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```

```text
adding new entry "cn=inetorgperson,cn=schema,cn=config"
```

スキーマを確認する。

```sh
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b "cn=schema,cn=config" dn
```

```text
dn: cn=schema,cn=config

dn: cn={0}core,cn=schema,cn=config

dn: cn={1}cosine,cn=schema,cn=config

dn: cn={2}inetorgperson,cn=schema,cn=config
```

## Memory Mapped DB

MDB はディレクトリの情報を保存するデータベースバックエンドです。

MDB の設定を確認する。

```sh
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config 'objectClass=olcMdbConfig'
```

```text
dn: olcDatabase={2}mdb,cn=config
objectClass: olcDatabaseConfig
objectClass: olcMdbConfig
olcDatabase: {2}mdb
olcDbDirectory: /var/lib/ldap
olcSuffix: dc=my-domain,dc=com
olcRootDN: cn=Manager,dc=my-domain,dc=com
olcDbIndex: objectClass eq,pres
olcDbIndex: ou,cn,mail,surname,givenname eq,pres,sub
```

### Base DN

Base DN を変更する。

```ldif
# base.ldif
dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=home,dc=local
```

変更する。

```sh
ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f base.ldif
```

```text
modifying entry "olcDatabase={2}mdb,cn=config"
```

変更を確認する。

```sh
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config 'objectClass=olcMdbConfig' olcSuffix
```

```text
dn: olcDatabase={2}mdb,cn=config
olcSuffix: dc=home,dc=local
```

### 管理者ユーザ

パスワードを生成する。

```sh
slappasswd
```

```text
New password:
Re-enter new password:
{SSHA}0Ml8+E7OZNc8FVRKKOEQ9V1w3kkl7rIj
```

管理者ユーザを変更する。

```ldif
# admin.ldif
dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=admin,dc=home,dc=local
-
replace: olcRootPW
olcRootPW: {SSHA}0Ml8+E7OZNc8FVRKKOEQ9V1w3kkl7rIj
```

変更する。

```sh
ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f admin.ldif
```

```text
modifying entry "olcDatabase={2}mdb,cn=config"
```

変更を確認する。

```sh
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config 'objectClass=olcMdbConfig' olcRootDN olcRootPW
```

```text
dn: olcDatabase={2}mdb,cn=config
olcRootDN: cn=admin,dc=home,dc=local
olcRootPW: {SSHA}0Ml8+E7OZNc8FVRKKOEQ9V1w3kkl7rIj
```

## エントリ

### 組織

組織を追加する。

```ldif
# domain.ldif
dn: dc=home,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: home org
dc: home

dn: ou=People,dc=home,dc=local
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=home,dc=local
objectClass: organizationalUnit
ou: Group
```

追加する。simple 認証 (-x -D "cn=admin,dc=home,dc=local" -W) を利用する。
管理者のパスワードを入力する。

```sh
ldapadd -x -D "cn=admin,dc=home,dc=local" -W -H ldapi:/// -f domain.ldif
```

```text
Enter LDAP Password:
adding new entry "dc=home,dc=local"

adding new entry "ou=People,dc=home,dc=local"

adding new entry "ou=Group,dc=home,dc=local"
```

追加を確認する。

```sh
ldapsearch -x -LLL -H ldapi:/// -b "dc=home,dc=local"
```

```text
dn: dc=home,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: home org
dc: home

dn: ou=People,dc=home,dc=local
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=home,dc=local
objectClass: organizationalUnit
ou: Group
```

### ユーザ

パスワードを生成する。

```sh
slappasswd
```

```text
New password:
Re-enter new password:
{SSHA}rzir2Q/3mtuCf3XDgBhCLQmQG+xOjy3w
```

ユーザを追加する。

```ldif
# user01.ldif
dn: uid=user01,ou=People,dc=home,dc=local
objectClass: inetorgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: user01
sn: user01
uid: user01
mail: user01@home.local
givenName: user01
userPassword: {SSHA}rzir2Q/3mtuCf3XDgBhCLQmQG+xOjy3w
```

追加する。

```sh
ldapadd -x -D "cn=admin,dc=home,dc=local" -W -H ldapi:/// -f user01.ldif
```

```text
Enter LDAP Password:
adding new entry "uid=user01,ou=People,dc=home,dc=local"
```

追加を確認する。
追加したユーザのパスワードを入力する。

```sh
ldapwhoami -x -D "uid=user01,ou=People,dc=home,dc=local" -W -H ldapi:///
```

```text
Enter LDAP Password:
dn:uid=user01,ou=People,dc=home,dc=local
```

### グループ

グループを追加する。

```ldif
# group01.ldif
dn: cn=group01,ou=Group,dc=home,dc=local
objectClass: groupOfNames
objectClass: top
cn: group01
member: uid=user01,ou=People,dc=home,dc=local
```

追加する。

```sh
ldapadd -x -D "cn=admin,dc=home,dc=local" -W -H ldapi:/// -f group01.ldif
```

```text
Enter LDAP Password:
adding new entry "cn=group01,ou=Group,dc=home,dc=local"
```

追加を確認する。

```sh
ldapsearch -x -LLL -D "uid=user01,ou=People,dc=home,dc=local" -W -H ldapi:/// -b "cn=group01,ou=Group,dc=home,dc=local"
```

```text
Enter LDAP Password:
dn: cn=group01,ou=Group,dc=home,dc=local
objectClass: groupOfNames
objectClass: top
cn: group01
member: uid=user01,ou=People,dc=home,dc=local
```
