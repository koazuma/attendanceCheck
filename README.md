# 勤怠システムチェックツール

## 概要

CyberXeedの登録内容をチェックし、チェック結果をTeamsの指定チャネルにポストするためのツールです。

* 残業時間チェック
* 申請忘れチェック

## 環境要件

* python3.8以上
* selenium chrome webdriver
* dateutil
* syukujitsu.csv

## 事前準備

以下はwindows7での設定内容です。

### 環境構築

* python3系
  インストールする。
* 必要ライブラリ
  pipで以下ライブラリをインストールする。
  * selenium
  * dateutil
  * pymsteams
* chrome webdriver
  以下より使用しているChromeと同一バージョンのchromedriverをダウンロードしておく。
  ChromeDriver <https://chromedriver.chromium.org/downloads>
* 本プロジェクト
  Git CLONEもしくはダウンロードしておく。
* 祝日リスト
  内閣府HPより国民の祝日csvをダウンロードしてプロジェクトと同じディレクトリに"syukujitsu.csv"というファイル名で配置しておく。
  内閣府HP <https://www8.cao.go.jp/chosei/shukujitsu/gaiyou.html#syukujitu>

### アプリケーション環境設定

setting.iniを編集して環境設定をします。

* chromedriver設定
  ダウンロードしたchromedriverのフルパスを記載します。
  ※ディレクトリの区切りは\ではなく/を使用する

  ```ini
  [environment]
  chromedriver = c:/path/to/chromedriver.exe
  ```

* ログイン情報
  cyberxeedのログイン情報を記載します。

  ```ini
  [siteinfo]
  url = https://cxg8.i-abs.co.jp/cyberx/login.asp
  
  # login info
  cp = xxx    ←変更(自分の会社コード)
  id = xxx    ←変更(自分の個人コード)
  pw = xxx    ←変更(自分のパスワード)
  ```

### 社員情報設定

members.jsonを編集して社員情報の設定をします。
社員番号をkeyとして"氏名、除外設定、グループ"のオブジェクトがvalueという形の配列になっています。
無視設定を0以外にすると通知対象から除外されます。

```json
{
    "100":{
        "name":"山田太郎",
        "ignore":"0",
        "group":"営業本部"
    },
    "101":{
        "name":"佐藤健",
        "ignore":"0",
        "group":"管理本部"
    },
    "102":{
        "name":"鈴木一郎",
        "ignore":"1",
        "group":"2G"
    }
}
```

## 使用方法

### usage

```batch
> python attendanceCheck.py -h
usage: attendanceCheck.py [-h] -m {1,2} [-d DATE] [-e] [-c [id [id ...]]]

optional arguments:
  -h, --help            show this help message and exit
  -m {1,2}, --mode {1,2}
                        チェック種別 1:残業時間 2:打ち忘れ
  -d DATE, --date DATE  yyyymmdd形式で指定した日に実行した仮定で処理される。
  -e, --exholiday       土日祝日の場合はチェックをしない。
  -c [id [id ...]], --cmpcodefilter [id [id ...]]
                        指定した社員番号のみチェック実施。ブランク区切りで複数指定可能。
```

### オプション

#### -m --mode

チェック種別を指定する必須オプション。

* 1 : 残業時間チェック
  残業時間チェックは-mオプションに"1"を指定して下さい。
  指定日(デフォルトは前日)を含む期間の残業時間をチェックします。

  ```batch
  python attendanceCheck.py -m 1
  ```

* 2 : 打ち忘れチェック
  打ち忘れチェックは-mオプションに"2"を指定して下さい。
  指定日(デフォルトは前日)を含む期間の打ち忘れチェックをします。

  ```batch
  python attendanceCheck.py -m 2
  ```

#### -d --date

yyyymmddの日付つきでdオプションを指定すると、指定日に実行した仮定でコマンドが実行されます。前月分の状況確認等に。

```batch
python attendanceCheck.py -m 1 -d 20220401
```

#### -e --exholiday

eオプションを指定すると土日祝日は実行しなくなります。
eオプションを指定する場合は以下からDL可能な内閣府配布の祝日リストを同ディレクトリに格納している事が前提となります。
<https://www8.cao.go.jp/chosei/shukujitsu/gaiyou.html#syukujitu>

```batch
python attendanceCheck.py -m 2 -e
```

#### -c --cmpcodefilter

社員番号付きで指定すると、指定した社員のみチェックされます。ブランク区切りで複数指定可能。mode1,3のみ効果があり、mode2は指定しても挙動は変わりません。

```batch
python attendanceCheck.py -m 1 -c 111 286
```

## 著作者

[koazuma](https://github.com/koazuma)
