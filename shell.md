

#
### $* 和 $@ 的区别
$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以"$1" "$2" … "$n" 的形式输出所有参数。

但是当它们被双引号(" ")包含时，"$*" 会将所有的参数作为一个整体，以"$1 $2 … $n"的形式输出所有参数；"$@" 会将各个参数分开，以"$1" "$2" … "$n" 的形式输出所有参数。

### echo
-e 开启转义
```
echo -e "\033[字背景颜色；文字颜色m字符串\033[0m" 

printf "\e[颜色值m 文本\n"

  33[0m 关闭所有属性
  33[1m 设置高亮度
  33[4m 下划线
  33[5m 闪烁
  33[7m 反显
  33[8m 消隐
  33[30m 至 33[37m 设置前景色
  33[40m 至 33[47m 设置背景色
  33[nA 光标上移n行 
  33[nB 光标下移n行
  33[nC 光标右移n行
  33[nD 光标左移n行
  33[y;xH设置光标位置
  33[2J 清屏
  33[K 清除从光标到行尾的内容
  33[s 保存光标位置 
  33[u 恢复光标位置
  33[?25l 隐藏光标
  33[?25h 显示光标
```

##

```
readonly bold=$(tput bold)
readonly reset=$(tput sgr0)


get_attention(){
    echo -ne '\007'
    sleep 0.25
    echo -ne '\007'
    sleep 3
}

log(){
    echo -e "$*"

    # tee -a "$log_file" <<< "$*" > /dev/null
}
# 6 洋红色
log_warn(){
    log "[ $(tput setaf 6)${bold}+ WARNING +${reset} ] $@"
}


log "Date/Time: $(date -u +"%Y-%m-%d %H:%M:%S UTC")"
log_warn "Can't get GCP project name from gcloud config."
```
