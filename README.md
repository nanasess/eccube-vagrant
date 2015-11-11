# 概要
EC-CUBE3の開発に利用することを想定したVagrantです。

CentOS 6.7（32bit）仮想マシンをセットアップします。
EC-CUBE3のディレクトリを仮想マシンからマウントするため、ホスト側で好きなエディタで開発が可能です。

## 必要なソフトウェア

* VirtualBox  [ダウンロード](https://www.virtualbox.org/wiki/Downloads)
* Vagrant  [ダウンロード](http://downloads.vagrantup.com/)
* Git  [ダウンロード](http://git-scm.com/downloads)

### 必要なVagrant Plugin

* vagrant-omnibus
```
> vagrant plugin install vagrant-omnibus
```

* vagrant-cachier(推奨）
```
> vagrant plugin install vagrant-cachier 
```


### 利用環境

+ Mac OS X 10.9.5 - VirtualBox 4.2.26 & Vagrant 1.6.5 & Git 1.9.3
+ Mac OS X 10.10.2 - VirtualBox 4.3.26 & Vagrant 1.7.2 & Git 1.9.5
+ Windows 7 - VirtualBox 5.0.8 & Vagrant 1.7.4 & Git 2.6.2

## この環境に含まれるもの

* PHP 5.4.39
* Apache 2.2
  * /ec-cubeをドキュメントルートに設定
* MySQL Client
* MySQL Server 5.1.73
  * データベース 未作成
* Postgresql Server 8.4.20
  * データベース **cube3_dev**
* Postgresql Client
* git 1.7.1

## セットアップ方法とディレクトリ構成

このリポジトリをEC-CUBEのプロジェクトに追加します:

    $ git submodule add git@github.com:EC-CUBE/eccube-vagrant.git ./vagrant
    $ cd vagrant
    $ vagrant up

ディレクトリ構成は以下のようになります:

    ec-cube/
    ├── html/
    ├── src/
    ├── app/
    ~~~~~~
    └── vagrant/

## vagrant起動
```
$ cd ec-cube/vagrant
$ vagrant up
```

## ec-cubeインストール
```
$ vagrant ssh
$ cd /ec-cube
$ ./eccube_install.sh pgsql
```

## プロジェクトへのアクセス方法

* Web
  * IPアドレス直接: [http://192.168.33.10/](http://192.168.33.10/)
  * PostgreSQL: pgsql:host=192.168.33.10;dbname=cube3_dev

## Thanks to

* https://github.com/kenjis/vagrant-centos6-php
* https://github.com/nanasess/vagrant-lamp
