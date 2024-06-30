# saml_test

## 目次
- [saml\_test](#saml_test)
  - [目次](#目次)
  - [概要](#概要)
  - [利用方法](#利用方法)
  - [SAML連携用の設定](#saml連携用の設定)
    - [Realmの作成](#realmの作成)
    - [ロールの作成](#ロールの作成)
    - [ユーザーの追加](#ユーザーの追加)
  - [アプリ側の設定](#アプリ側の設定)

## 概要

SAMLを検証するにあたってIdPをkeycloakで設定するためのリポジトリ

## 利用方法

keycloak環境立ち上げ

```bash
# postgresコンテナビルド
$ docker build ./ -t mygres

# postgresコンテナ立ち上げ
$ docker run -dit --name keycloak_postgres -p 5432:5432 -v /home/user/mydata:/var/lib/postgresql/data mygres

# keycloakコンテナビルド
$ docker build ./ -t mycloak

# keycloakコンテナ立ち上げ（初回）
$ docker run -dit --name keycloak_idp --add-host=host.docker.internal:host-gateway -p 8443:8443 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin mycloak

# keycloakコンテナ立ち上げ（2回目移行）※管理者アカウントを作成する必要がないので
$ docker run -dit --name keycloak_idp --add-host=host.docker.internal:host-gateway -p 8443:8443 mycloak
```

コンテナ状況確認
```bash
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                       PORTS                                                           NAMES
05fcc74f7019   mycloak   "/opt/keycloak/bin/k…"   8 minutes ago    Up 8 minutes                 8080/tcp, 9000/tcp, 0.0.0.0:8443->8443/tcp, :::8443->8443/tcp   admiring_allen
9ad93e202314   mygres    "docker-entrypoint.s…"   10 minutes ago   Up 8 minutes                 0.0.0.0:5432->5432/tcp, :::5432->5432/tcp                       priceless_aryabhata
```

https://localhost:8443 に遷移するとkeycloakのログイン画面に入ることができる。

![login.png](/img/login.png)

この時の認証情報は環境変数で登録したadmin

## SAML連携用の設定

### Realmの作成

ログイン後、画面左サイドバーにあるKeycloakを選択し、Create realmを選択

![craate_realm.png](/img/create_realm.png)

Realm Nameを設定し、Createボタンを実行
![settings_realm.png](/img/settings_realm.png)

Realmの作成が完了
![after_create_realm.png](/img/after_create_realm.png)

### ロールの作成

画面左サイドバーのRealm Roleを選択

任意の名前を入力し、Create roleボタンを選択
![create_role.png](/img/create_role.png)

ロール名：userとして新規作成
![insert_role.png](/img/insert_role.png)

### ユーザーの追加

画面左サイドバーのUsersを選択し、開いた画面にて、Create Userを選択
![users.png](/img/users.png)

任意のユーザーを作成
![create_new_user.png](/img/create_new_user.png)

user作成後に、詳細画面が開かれる。

画面左サイドバーのUsersを選択し、作成したユーザーをクリックする。
![users_select.png](/img/users_select.png)

userの詳細画面の画面上部にあるRole mappingを選択。

Assing roleボタンを選択

モーダル左上のフィルターを「Filter by realm roles」に設定し、作成したロールが出ているのでそれを選択する。
![select_my_role.png](/img/select_my_role.png)

ここまででIdp側のSAML設定は完了？のはず

## アプリ側の設定

