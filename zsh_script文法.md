# zsh script

# ファイル名の編集
- 起動スクリプト名から、拡張子なしのファイル名を取得
- $0はフルパスのファイル名

```Sh
myname=/usr/local/bin/add_squid.zsh
```

## 拡張子を削除
```Sh
echo $myname:r
/usr/local/bin/add_squid
```

## パスを削除（ファイル名のみ）
```Sh
echo $myname:t
add_squid.zsh
```

```Sh
echo ${myname##*/}
add_squid.zsh
```






# コマンドの結果を利用
c.f. # while コマンドの実行結果でループ
c.f. https://qiita.com/mollifier/items/f897b3fddd2d10369333#%E5%BF%9C%E7%94%A8%E4%BE%8B

```Sh
#!/bin/zsh
# cmd="mysql -s -h macmini -umariadb -pmariadb$ comdbtest -e 'show tables;' | awk 'NR>0{print $0}'"

lines=(${(@f)"$( mysql -s -h macmini -umariadb -pmariadb$ comdbtest -e 'show tables;' | awk 'NR>0{print $0}' )"})
# lines=(${(@f)"$($cmd)"})  # NG

# lines=(${(M)lines:#*test*} )   # include "test"
# lines=(${lines:#*test*} )      # exclude "test"
for mem in $lines; do
  echo "<${mem}>"
done
```


# 関数の引数と ""を渡した場合の処理


```Sh
#!/usr/bin/zsh

IFS=,

func test_func(){
   echo "start:$0"
   echo $#
   echo "$@"
   echo "$*"

    for (( cnt=1 ; cnt <= $# ; cnt++ ))
    do
        if [ "$argv[$cnt]" != "" ] ; then
            echo $cnt , $argv[$cnt]
        else
            echo $cnt , "NULL"
        fi
    done
    echo "end:$0"
}
```


```
test_func 1 2 3

test_func "1" "2" "" "3"
      
 start:test_func
3
1 2 3
1,2,3
1 , 1
2 , 2
3 , 3
end:test_func
start:test_func
4
1 2  3
1,2,,3
1 , 1
2 , 2
3 , NULL
4 , 3
end:test_func
 ```

# ファイル名の切り出し
```Sh
for mem in /var/tmp/work/BYAMLS/*.yaml ; do
    #echo $mem

    #	ファイル名
    echo ${mem##*/}
	#	ディレクトリ名
    echo ${mem%/*}
done
```

# コマンドの実行結果を変数に取り込む（ただし、１行の時のみ）
```Sh
myid=$(mysql -s -umysql -pmysql -e"select id from keystone.role where name='dummy_role'" | tail -1)
echo $myid
```


# 変数代入
```Sh
cnt=1
```
※  =の前後にスペースを空けてはいけない。

```Sh
cnt = 1   # <<<  これは、文法エラー
```
# for
## 形式１
```Sh
for tgt in aaa bbb ccc
do
  echo $tgt
done
```
＜実行結果＞
aaa
bbb
ccc

## 形式２
```Sh
for tgt in aaa bbb ccc ; echo $tgt
```
＜実行結果＞
形式１と同じ


## 形式３
```Sh
for tgt (aaa bbb ccc ) echo $tgt
```
＜実行結果＞
形式１と同じ


## 形式４
```Sh
for (( cnt = 0 ; cnt < 3 ; cnt++ ))
do
echo $cnt
done
```
＜実行結果＞
0
1
2


## 形式５
```Sh
for (( cnt = 0 ; cnt < 3 ; cnt++ )) echo $cnt
```
＜実行結果＞
形式４と同じ



# while
・形式１
```Sh
cnt=0
while [ $cnt -le 3 ]
do
  echo $((cnt++))
done
```
＜実行結果＞
0
1
2
3

・形式２
```Sh
cnt=0;while [[ $cnt -le 3 ]] { echo $((cnt++)) }
```




# 標準出力と標準エラー出力を分けて、ファイルへ出力
```Sh
eval $cmd  > /var/tmp/xggg_csv_diff.res 2> /var/tmp/xggg_csv_diff.err
```

# eval
```Sh
if [[ "$2" = "eval" ]] eval $mycmd
```

# 文字列の置換
 相手のホスト名を取得する
```Sh
my_name=`hostname`
target=$my_name:s/-a//
target=$target:s/-b//
```


# 設定ファイルの有効順位
ログインシェルとしてzshが起動された場合
1 ~/.zshenv
2 ~/.zprofile
3 ~/.zshrc
4 ~/.zlogin

インタラクティブシェル（ログインシェルとしてではない）としてzshが起動された場合

1 ~/.zshenv
2 ~/.zshrc

シェルスクリプトを実行するコマンドとしてzshが起動された場合

1 ~/.zshenv

ログインシェルとして起動されたzshからログアウトする場合

1 ~/.zlogout

# 他のソースを読み込む
```Sh
source ~/.zsh.d/package.zsh
```

# サブルーチン
```Sh
route_console_lan()
{
    if [ $1 -eq 8 ]; then
    #for aguc
        /usr/sbin/route add -net 10.2.1.0/24 10.2.5.200
        /usr/sbin/route add -net 10.2.4.0/24 10.2.5.200

    elif [ $1 -eq 9 ]; then
    #for aguk
        /usr/sbin/route add -net 10.2.1.0/24 10.2.6.250
        /usr/sbin/route add -net 10.2.4.0/24 10.2.6.250

    else
        /usr/sbin/route add -net 10.2.1.0 10.2.4.1
        #/usr/sbin/route add -net 10.2.1.0 10.2.4.2
    fi
}
```

```Sh
# route_console_lan( $ME_index )    <<<< ( はつけてはダメ
route_console_lan $ME_index
```
- ( はつけてはダメ
- return は整数だけ
- 受け取りは、$?
- 関数名からは受け取れない


# 実行ユーザのチェック
```Sh
if [ ${UID} -ne 0 ]; then
    echo do root
    exit
fi
```

# 引数チェック
```Sh
if [ "$1" =  "" ]; then
    echo zrsync.zsh target
    echo target : xggg xweb net ag
    exit
fi
```

# エラー出力

「|&」で繋ぐ、
略記をしないなら「2>&1 |」でも良い。
標準出力も同時にパイプに繋がれる。

```Sh
/usr/local/bin/php server.php |& logger -p local6.info -t server &
```

```Sh
/usr/local/bin/php server.php 2>&1 |  /usr/bin/logger -p local6.debug -t server &
```


# 条件式
※文字列と数値では、比較方式が異なる点に注意！（perlと逆）
※ = [ ] 文字列の間は、スペースを空けること！

	s1 = s2	文字列s1とs2が等しい
	s1 != s2	文字列s1とs2が等しくない
	
	n1 -eq n2	数値n1とn2が等しい
	n1 -ne n2	数値n1とn2が等しくない
	
	-e file	fileが存在する
	-z s1	s1の長さが0である

# 引数処理
```Sh
#!/usr/bin/zsh

for (( cnt=1 ; cnt <= $# ; cnt++ ))
do
    echo $cnt
    echo $argv[$cnt]
done
```


# sample1( for if , 外部コマンド実行 )
```Sh
#!/usr/bin/zsh

cd /var/tmp/ipfilter
rm_cmd="/usr/bin/rm -i ipfilter*"
echo $rm_cmd

p_cmd="/usr/local/php/bin/php /home/mysrv/lmf/ipf2mysql.php"

for mysv in qfw-a qfw-b
do
    r_cmd="/usr/local/bin/rsync ${mysv}:/var/log/ipfilter.log ."
    g_cmd="${p_cmd} ipfilter.log"

    echo $r_cmd
    echo $g_cmd

    if [ "" != "$1" ]
    then
        for (( cnt=0 ; cnt < $1 ; cnt++ ))
        do
           r_cmd="/usr/local/bin/rsync ${mysv}:/var/log/ipfilter.log.${cnt}.gz ."
           g_cmd="/usr/bin/gunzip -c ipfilter.log.${cnt}.gz | ${p_cmd}"
           echo $r_cmd
           echo $g_cmd
        done
    fi
done
```


# sample2( foreach ,外部コマンド実行 )
```Sh
#!/usr/bin/zsh

mytime=`date '+%Y%m%d_%H%M%S'`
bkfile="/var/tmp/backup_"

for target in /home/mysrv /etc/logadm.conf /etc/nsswitch.conf /etc/inet/hosts   \
              /etc/inet/netmasks /etc/inet/services /etc/syslog-ng/syslog-ng.conf \
              /etc/rc3.d/S99startsrv
do
    mycmd="7z a ${bkfile}${mytime}.7z ${target}"
    echo $mycmd
    eval $mycmdzsh 
done
```

# sample3( foreach ,文字列から、取り出す )
```Sh
case "${HOST}" in
xggg-*)
    target_all="zexgg zinfi zdb"
    ;;
xweb-*)
    target_all="zexweb zinweb zmgmt"
    ;;
esac

for target in ${(z)target_all}
do
    mycmd="zoneadm -z ${target}-on boot"
    echo $mycmd
    mycmd="zoneadm -z ${target}-off boot"
    echo $mycmd
done
```



# while コマンドの実行結果でループ

## table毎に定義とデータとをバックアップ
```Sh
#!/bin/zsh

mysql -s -h macmini -umariadb -pmariadb$ comdbtest -e "show tables;" | awk 'NR>2{print $0}' | \
while read LINE ; do
  echo ${LINE}
  # cmd="mysqldump -h macmini.incspace.dip.jp -umariadb -pmariadb$ -t comdbtest $LINE > ${LINE}_data.sql"
  # echo $cmd
  mysqldump -h macmini -umariadb -pmariadb$ -t comdbtest $LINE > ${LINE}_data.sql
  mysqldump -h macmini -umariadb -pmariadb$ -d comdbtest $LINE > ${LINE}_def.sql
done
unset LINE
```




# while 無限ループ
```Sh
while [ 1 ]; do
  $1
  sleep 1
done
```





# ファイル、ディレクトリーの存在
```Sh
if [ ! -d "/var/tmp/cas" ]
then
    echo "Not exist /var/tmp/cas"
    echo "mkdir /var/tmp/cas"
    exit
fi
```


# 条件判定
# 文字列条件式
  文字列1 = 文字列2    文字列1 と文字列2 が一致なら真
  文字列1 != 文字列2   文字列1 と文字列2 が一致しないなら真
  -n 文字列1           文字列1 が空 (null) でなければ真
  -z 文字列1           文字列1 が空 (null) であれば真

 ○数式条件式
  整数1 -eq 整数2      整数1 と整数2 が等しければ真 (整数1 == 整数2)
  整数1 -ge 整数2      整数1 が整数2 以上であれば真 (整数1 >= 整数2) 
  整数1 -gt 整数2      整数1 が整数2 よりも大きければ真 (整数1 > 整数2)
  整数1 -le 整数2      整数1 が整数2 以下であれば真 (整数1 <= 整数2)
  整数1 -lt 整数2      整数1 が整数2 よりも小さければ真 (整数1 < 整数2)
  整数1 -ne 整数2      整数1 と整数2 が等しくなければ真 (整数1 != 整数2)

 ○ファイル条件式
  -d ファイル1         ファイル1 がディレクトリであれば真
  -f ファイル1         ファイル1 が通常ファイルであれば真
  -r ファイル1         ファイル1 がスクリプト実行ユーザで読み出し可能であれば真
  -s ファイル1         ファイル1 が空ファイルでなければ真
  -w ファイル1         ファイル1 がスクリプト実行ユーザで書き込み可能であれば真
  -x ファイル1         ファイル1 がスクリプト実行ユーザで実行可能であれば真

 ○論理演算子
  !                    直後に続く条件式の結果を否定する
  -a                   2 つの条件式を AND 条件で結ぶ
  -o                   2 つの条件式を OR 条件で結ぶ

# function
.zshrcに記述

## Functions例
```Sh
function bk(){
    cp -p $1 $1_`/usr/bin/date +%Y%m%d_%H%M%S`
}
```

```Sh
function my7z(){
    /usr/bin/7z u $1_`/usr/bin/date +%Y%m%d_%H%M%S` $1
}
```

```Sh
function 7zd(){
    7z a $1_`/usr/bin/date +%Y%m%d_%H%M%S` $1
}
```

```Sh
function s2e(){
    /usr/bin/cp -p $1 $1_sjis
    /usr/local/bin/iconv  -f SJIS -t EUC-JP < $1_sjis > $1
}
```

```Sh
function zls(){
    /usr/sbin/zoneadm list -vc
}
```

```Sh
function zboot(){
    /usr/sbin/zoneadm -z $1 boot
}
```

```Sh
function zhalt(){
    /usr/sbin/zoneadm -z $1 halt
}
```
```Sh
function zinfo(){
    /usr/sbin/zonecfg -z $1 info
}
```


```Sh
function svcrst(){
    /usr/sbin/svcadm restart $1
    sleep 1
    /usr/bin/svcs $1
}
```

# prompt
```zsh

case ${UID} in

0)
	#PROMPT="%{${fg[red]}%}%n%{${reset_color}%}@%{${fg[red]}%}%m%{${reset_color}%}[%{${fg[red]}%}%(5~,%-2~/.../%2~,%~)%{${reset_color}%}]# "
	PROMPT="%n%{${fg[red]}%}@%{${reset_color}%}%m%{${fg[red]}%}[%{${reset_color}%}%(5~,%-2~/.../%2~,%~)%{${fg[red]}%}]# %{${reset_color}%}"
	PROMPT2="${HOST}@%{${fg[red]}%}%_#%{${reset_color}%} "
	;;
*)
	PROMPT="%n%{${fg[red]}%}@%{${reset_color}%}%m%{${fg[red]}%}[%{${reset_color}%}%(5~,%-2~/.../%2~,%~)%{${fg[red]}%}]%% %{${reset_color}%}"
	PROMPT2="${HOST}@%{${fg[red]}%}%_%%%{${reset_color}%} "
	;;
esac
```


# refresh
.zshrcから呼ばれる外部ソースを更新したときに、これを反映する関数
```zsh
r() {
  local f
  f=(/root/.zsh/functions/Completion/*(.))
  unfunction $f:t 2> /dev/null
  autoload -U $f:t
}
```