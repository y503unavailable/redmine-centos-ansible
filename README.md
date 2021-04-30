# redmine-centos-ansible

最小構成でインストールしたCentOSにRedmine/Redmicaを自動インストールするためのAnsibleプレイブックです。

コマンド数個をコピペ実行し、あとはしばらく放置プレイすればインストールが完了します。

## 注意事項

~~本プレイブックは、Redmine4.0対応 UnofficialCooking版(闇鍋版)です。~~

### 本プレイブックは、fts-mariadb10-utf8mb4用開発ブランチです。feature-fts-maria10-utf8mb4 ###

カテゴリPJ継承の4.X(trunk)用開発ブランチから作成したブランチです。feature-category-trunk20190629

試行中なので自己責任でご利用ください。（自己責任は他も同じ）

## システム構成

* Redmine 4.1(trunk)
* CentOS 7
* mariadb-10
* Apache

* utf8-mb4対応
* full-text-search plugin ,mroonga 取込

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。

以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.2をCentOS 7.1にインストールする手順](http://blog.redmine.jp/articles/3_2/install/centos/)

## Redmine標準からの変更内容

### カテゴリのサブプロジェクト継承機能を追加（標準のバージョンと同等機能 2019/06/30）

https://github.com/y503unavailable/redmine/issues/14

機能紹介 https://www.slideshare.net/y503unavailable/ss-189711783

### OR条件、文字列not match のフィルタ機能を追加（本家4939-note56取込 2020/1）

http://www.redmine.org/issues/4939#note-56

https://github.com/y503unavailable/redmine/issues/33

機能紹介 https://redmine.tokyo/issues/281

その他、取り込んだ機能はRedmine.TokyoのUnofficialCookingで説明しています。
https://redmine.tokyo/projects/unofficialcooking

## 同時インストールするプラグイン

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-centos-ansible/tree/feature-fts-maria10-utf8mb4/roles/redmine-plugins/tasks/main.yml

## 同時インストールするテーマ

farend_basic,redmine_flat,gitmike,PurpleMine2,minimalflat2,flatly_light,kodomo,farend_bleuclair,kodomo_midori

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-centos-ansible/tree/feature-fts-maria10-utf8mb4/roles/redmine-themes/tasks/main.yml

## admin初期パスワードの変更

Redmineインストール直後のadmin初期パスワードは admin で固定されており、インストール直後に乗っ取られる可能性を否定できません。（特にインターネット上VPS等を利用する場合）

そのため、情報セキュリテイ対策として、admin初期パスワードを変更しました。初期パスワードは必要に応じ変更ください。

admin初期パスワード  unofficial-cracking

## Mariadb/Mroongaのインストール失敗時

Mariadb/Mroongaのバージョンが上がり、インストールに失敗する場合は、下記箇所を修正してみてください。
（MariaDBの最新が10.4の間は大丈夫だと思いますが）

```
roles/mariadb/templates/MariaDB.repo
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
```
```
roles/mariadb/tasks/main.yml
yum: name='mariadb-10.4-mroonga' enablerepo=epel
```

## plantumlプラグインインストール失敗時

インストール時にplantumlのバージョンエラーが発生した場合は、下記ファイルを修正してから、再度ansibleで実行してください。
/var/lib/redmine/plugins/plantuml/init.rb
  requires_redmine version: '2.6'..'4.1'     <- 4.1を4.2に修正

---

## AWS上での追加設定

AWS(EC2/Lightsail)上で利用する場合は、本Playbookの実行前に下記操作を行ってください。

### スーパーユーザに移行しておく（rootにパスワードが設定無いため）

下記実行（またはパスワード設定）
```
$ sudo su -
```

### firewalldをインストールする

```
# yum install -y firewalld
```
### swap領域を作成する(メモリ1GB以下の場合）

passengerのビルド中、メモリ不足でインストール失敗する場合があるため、swap領域を追加する。
https://mseeeen.msen.jp/redmine-amazon-linux-ansible/

```
dd if=/dev/zero of=/swap bs=1M count=1024
chmod 600 /swap
mkswap /swap
swapon /swap
```

---

## Redmine_knowledgebase Plugin利用時の注意点

~~本Playbookでは、Redmine_Tags Pluginを初期導入しています。~~

Redmine_Tags Pluginは、Redmine_knowledgebase Pluginと同居できませんので、下記手順で対応してください。
https://github.com/alexbevi/redmine_knowledgebase/issues/320

Redmine_knowledgebase Plugin以外の、'redmine_acts_as_taggable_on'を使用しているPluginも、同様に対応する必要があります。
http://www.redmine.org/issues/1448#note-124

### Redmine_knowledgebase Pluginのインストール前に、Redmine_Tags Pluginをアンインストールする。

```
rake redmine:plugins:migrate NAME=redmine_tags VERSION=0 FORCE_REDMINE_TAGS_TABLES_REMOVAL=yes
```

### 本PlayBook実行前に、Redmine_Tagsをインストール対象から外す。

roles/redmine-plugins/tasks/main.yml から、Redmine_Tagsの行を削除する。

---

## Redmineのインストール手順

インストール直後の CentOS 7 に root でログインし以下の操作を行ってください。

### パッケージの更新

```
yum -y update
```

### Ansibleとgitのインストール

```
yum install -y epel-release
yum install -y ansible git
```

### playbookのダウンロード(feature-fts-maria10-utf8mb4ブランチ）

```
git clone -b feature-fts-maria10-utf8mb4 https://github.com/y503unavailable/redmine-centos-ansible.git
```

初期設定を変更する場合は、この時点で行ってください。

### playbook実行

下記コマンドを実行してください。Redmineの自動インストールが開始されます。

```
systemctl enable firewalld
systemctl start  firewalld
systemctl stop iptables
systemctl disable iptables
```

```
cd redmine-centos-ansible
ansible-playbook -i hosts site.yml
```

10〜20分ほどでインストールが完了します。

webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。

初期パスワードは admin/ unofficial-cracking です。（標準から変更）

---

## 初期設定の変更

### Redmine admin 初期パスワードの変更

admin初期パスワードを変更する場合は、下記箇所を変更してから実行ください。

group_vars/redmine-servers

```
redmine_admin_passwd: unofficial-cracking
```

### Redmine 初期テーマの変更

初期設定されるテーマを変更する場合は、下記箇所を変更してから実行ください。

インストールされるテーマの一覧は、Redmineインストール下の/public/themes/を参照ください。

group_vars/redmine-servers

```
redmine_default_theme: redmine_flat
```

### Redmineオリジナルで利用したい場合

下記箇所を変更してから実行ください。（2018/3現在）

group_vars/redmine-servers

#### Redmine-本家gitミラー

```
redmine_git_url: https://github.com/redmine/redmine.git
redmine_git_branch: 4.0-stable
```
### Redmicaで利用したい場合

下記箇所を変更してから実行ください。（2019/11現在）

なお、Redmine4.1.XがRedmica 1.0-stable, 
4.1.0リリース後のtrunk(4.2候補)がRedmica masterに
相当するものと思われます。(4.1.0の開発はほぼ完了しているため）

group_vars/redmine-servers

#### Redmica

```
redmine_git_url: https://github.com/redmica/redmica.git
redmine_git_branch: stable-1.0
```

### mariadbに設定するパスワードの変更

ダウンロードしたプレイブック内のファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` と、`db_passwd_root` を適切な内容に変更してください。これはmariadbのRedmine用ユーザー redmine に設定されるパスワードです。

---

## Dockerを使用したPlaybookの実行

### Docker最新版のインストール

CentOSの場合、下記手順でdockerの最新版をインストールし、起動してください、（Docker CE 17以降）

CentOSのパッケージから導入すると、旧バージョンがインストールされ、正常に動作しない場合があります。
```
curl -sSL https://get.docker.com/ | sh

systemctl enable docker
systemctl start  docker
```

### Dockerコンテナのビルド

下記のコマンドでPlaybookを実行できるDockerコンテナのビルドができます。
```
$ git clone https://github.com/y503unavailable/redmine-centos-ansible.git
$ cd redmine-centos-ansible
$ docker build -t redmine-centos-ansible docker
```

### Dockerコンテナの起動とPlaybook実行

下記のコマンドでビルドしたDockerコンテナでPlaybookを実行できます。
```
$ docker run --privileged --name redmine-centos-ansible -d -p 8080:80 redmine-centos-ansible /sbin/init
$ docker exec -ti redmine-centos-ansible /bin/bash
```
以下はDockerコンテナ内操作
```
# cd /tmp
# git clone https://github.com/y503unavailable/redmine-centos-ansible.git
# cd redmine-centos-ansible
# ansible-playbook -i hosts site.yml
```

Webブラウザで `http://サーバIPアドレス:8080/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。

---

## 起動後の設定変更

### kodomo テーマで利用したい場合

起動後、kodomoテーマを選択し、 message_customizeプラグインで用語を変更してください。

https://github.com/akiko-pusu/redmine_theme_kodomo

## ライセンス

MIT License


## 作者

y503unavailable （Redmine.Tokyoスタッフ unofficial redmine cooking 担当）

連絡先    [Twitter y503unavailable](https://twitter.com/y503unavailable)　、Discord、slack

[Redmine.tokyo unofficial cooking](https://redmine.tokyo/projects/unofficialcooking/)   

Docker対応は  Tatsuya Saito <twopackas@gmail.com> さんによります。

## 本プレイブックについて

原作
[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/)
https://github.com/farend/redmine-centos-ansible

Fork元
https://github.com/ssaito/redmine-centos-ansible
