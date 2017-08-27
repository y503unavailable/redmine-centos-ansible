# redmine-centos-ansible


最小構成でインストールしたCentOSにRedmineを自動インストールするためのAnsibleプレイブックです。

コマンド数個をコピペ実行し、あとはしばらく放置プレイすればインストールが完了します。

## 注意事項

本プレイブックは、Redmine3.3対応 UnofficialCooking版(闇鍋版)です。

Redmine標準外の変更取込、backport、admin初期パスワードの変更、Pluginの一括インストールを行っています。

くれぐれも自己責任でご利用ください。

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。

以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.2をCentOS 7.1にインストールする手順](http://blog.redmine.jp/articles/3_2/install/centos/)

## Redmine標準からの変更内容

標準からの変更内容は下記参照ください。

取り込んだ機能はRedmine.TokyoのUnofficialCookingで説明しています。

https://github.com/y503unavailable/redmine/blob/3.3-unofficialcooking/README.rdoc

https://redmine.tokyo/projects/unofficialcooking

## admin初期パスワードの変更

Redmineインストール直後のadmin初期パスワードは admin で固定されており、インストール直後に乗っ取られる可能性を否定できません。（特にインターネット上VPS等を利用する場合）

そのため、情報セキュリテイ対策として、admin初期パスワードを変更しました。必要に応じ変更ください。

admin初期パスワード  unofficial-cracking 

## システム構成

* Redmine 3.3
* CentOS 7
* mariadb
* Apache


## Redmineのインストール手順

インストール直後の CentOS 7 に root でログインし以下の操作を行ってください。


### Ansibleとgitのインストール

```
yum install -y epel-release
yum install -y ansible git
```

### playbookのダウンロード(下記は3.3-unofficialcookingブランチ）

```
git clone -b 3.3-unofficialcooking https://github.com/y503unavailable/redmine-centos-ansible.git
```

### Redmine admin 初期パスワードの変更

admin初期パスワードを変更する場合は、下記箇所を変更ください。

group_vars/redmine-servers

redmine_admin_passwd: unofficial-cracking

### mariadbに設定するパスワードの変更

ダウンロードしたプレイブック内のファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` と、`db_passwd_root` を適切な内容に変更してください。これはmariadbのRedmine用ユーザー redmine に設定されるパスワードです。

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

## ライセンス

MIT License


## 作者

y503unavailable （Redmine.Tokyoスタッフ）

連絡先   [Redmine マストドン](https://toot.redmine.jp/@y503unavailable) 、  [Twitter y503unavailable](https://twitter.com/y503unavailable)

[Redmine.tokyo unofficial cooking](https://redmine.tokyo/projects/unofficialcooking/)   

## 本プレイブックについて

原作 
[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/)
https://github.com/farend/redmine-centos-ansible

Fork元
https://github.com/ssaito/redmine-centos-ansible
