# redmine-centos-ansible


最小構成でインストールしたCentOSにRedmineを自動インストールするためのAnsibleプレイブックです。

コマンド数行コピペ実行するだけで、あとはしばらく放置すればインストールが完了します。

乗っ取りのセキュリテイリスクがあるため、インストール後は直ちにadminパスワードを変更してください。

## 注意事項

本プレイブックは、Redmine3.3対応 UnofficialCooking版(闇鍋版)です。

対応するRedmineバージョンでブランチ作成していますので、利用時はブランチ確認ください。

Redmine標準外の変更取込、backport、Pluginの一括インストールを行っていますので、くれぐれも自己責任でご利用ください。

https://redmine.tokyo/projects/unofficialcooking

## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.2をCentOS 7.1にインストールする手順](http://blog.redmine.jp/articles/3_2/install/centos/)


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

### playbookのダウンロード

```
git clone -b 3.3-unofficialcooking https://github.com/y503unavailable/redmine-centos-ansible.git
```

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

10〜20分ほどでインストールが完了します。（所要時間は動作環境の性能依存）
webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。

初期パスワードはadmin/adminです。
セキュリテイリスクがあるため、インストール後は直ちにadminパスワードを変更してください。

## ライセンス

MIT License

## 作者

y503unavailable

原作
[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/)
https://github.com/farend/redmine-centos-ansible

Fork元
https://github.com/ssaito/redmine-centos-ansible
（mariadb対応、pluginインストール対応、多謝）
