# redmine-centos-ansible


最小構成でインストールしたCentOSにRedmineを自動インストールするためのAnsibleプレイブックです。

コマンド数個をコピペ実行し、あとはしばらく放置プレイすればインストールが完了します。

## 注意事項

本プレイブックは、Redmine3.4対応 UnofficialCooking版(闇鍋版)です。

Redmine標準外の変更取込、backport、admin初期パスワードの変更、Plugin,テーマの一括インストールを行います。(2017/9/10時点では本家+プラグイン+テーマ）

自己責任でご利用ください。

Docker,Vagrant環境上では、そのままで動作しません。（今後の課題）

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。

以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.2をCentOS 7.1にインストールする手順](http://blog.redmine.jp/articles/3_2/install/centos/)

## Redmine標準からの変更内容

標準からの変更内容は下記参照ください。

取り込んだ機能はRedmine.TokyoのUnofficialCookingで説明しています。

https://github.com/y503unavailable/redmine/blob/3.4-unofficialcooking/README.rdoc     （未作成）

https://redmine.tokyo/projects/unofficialcooking

## 同時インストールするプラグイン

full_text_search, view_customize, issue_templates, banner, wiki_lists, work_time,wiki_extensions, xlsx_format_issue_exporter, pivot_table, absolute_dates, startpage  (2017/9/12現在)

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-centos-ansible/tree/3.4-unofficialcooking/roles/redmine-plugins/tasks/main.yml

## 同時インストールするテーマ

farend_basic,redmine_flat,gitmike,PurpleMine2,minimalflat2  (2017/9/12現在)

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-centos-ansible/tree/3.4-unofficialcooking/roles/redmine-plugins/tasks/main.yml

## admin初期パスワードの変更

Redmineインストール直後のadmin初期パスワードは admin で固定されており、インストール直後に乗っ取られる可能性を否定できません。（特にインターネット上VPS等を利用する場合）

そのため、情報セキュリテイ対策として、admin初期パスワードを変更しました。初期パスワードは必要に応じ変更ください。

admin初期パスワード  unofficial-cracking 

## システム構成

* Redmine 3.4
* CentOS 7
* mariadb
* Apache

---

## Redmineのインストール手順

インストール直後の CentOS 7 に root でログインし以下の操作を行ってください。


### Ansibleとgitのインストール

```
yum install -y epel-release
yum install -y ansible git
```

### playbookのダウンロード(3.4-unofficialcookingブランチ）

```
git clone -b 3.4-unofficialcooking https://github.com/y503unavailable/redmine-centos-ansible.git
```

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

admin初期パスワードを変更する場合は、下記箇所を変更ください。

group_vars/redmine-servers

redmine_admin_passwd: unofficial-cracking

### Redmine admin 初期テーマの変更

初期設定されるテーマを変更する場合は、下記箇所を変更ください。

インストールされるテーマの一覧は、Redmineインストール下の/public/themes/を参照ください。

group_vars/redmine-servers

redmine_default_theme: redmine_flat

### mariadbに設定するパスワードの変更

ダウンロードしたプレイブック内のファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` と、`db_passwd_root` を適切な内容に変更してください。これはmariadbのRedmine用ユーザー redmine に設定されるパスワードです。

---

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
