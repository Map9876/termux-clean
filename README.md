# termux-clean
termux 清理空间储存

```
#!/bin/bash

##   Termux 垃圾清理工具    :       安卓终端清理工具
##   作者                  :       ArjunCodesmith
##   版本                  :       0.2.0
##   GitHub                :       https://github.com/ArjunCodesmith

##   Termux-Junk-Cleaner 版权所有 (C) 2024  ArjunCodesmith (https://github.com/ArjunCodesmith)

author="ArjunCodesmith"
version="v0.2.0"

LOG_FILE="cleanup_log.txt"

# 在日志文件中设置当前日期
echo -e "\e[1;34m-------------------------------\e[0m" >> "$LOG_FILE"
echo -e "\e[1;34m日期: $(date)\e[0m" >> "$LOG_FILE"
echo -e "\e[1;34m-------------------------------\e[0m" >> "$LOG_FILE"

# 模拟打字效果函数
typing_effect() {
    local text="$1"
    for ((i=0; i<${#text}; i++)); do
        echo -n "${text:$i:1}"
        sleep 0.03
    done
    echo
}

echo -e "\e[1;31m
         ┌─────────┐     ┌─────────┐
       ──────│\e[94m [▓▓▓▓▓▓▓▓░░░░░] \e[1;31m│──────
 ─────────── │  \e[38;5;83m Termux 垃圾 \e[1;31m  │ ───────────
 ─────────── │ \e[38;5;83m 清 理 工 具 \e[1;31m │ ───────────
       ──────│\e[94m [░░░░░▓▓▓▓▓▓▓▓] \e[1;31m│──────
         └─────────┘     └─────────┘\e[0m"
echo -e "              \033[40;38;5;83m 版本 \033[30;48;5;83m $version \033[0m"
echo -e "         \033[30;48;5;83m    版权 \033[40;38;5;83m ${author}\033[0m"
echo -e " \e[1;34m--------------------------------------------\e[0m"

# 清理选项函数
cleanup_options() {
    read -p $'\n\e[1;35m 是否清理不必要的日志文件? (y/n): \e[0m' clean_logs
    read -p $'\e[1;35m 是否清理缓存文件? (y/n): \e[0m' clean_cache
    read -p $'\e[1;35m 是否清理缓存的软件包? (y/n): \e[0m' clean_packages
    read -p $'\e[1;35m 是否移除不必要的或未使用的软件包? (y/n): \e[0m' clean_unused_packages
    read -p $'\e[1;35m 是否清理临时文件? (y/n): \e[0m' clean_temp
    read -p $'\e[1;35m 是否清理临时备份文件? (y/n): \e[0m' clean_temp_backup
}

# 清理缓存文件函数
clean_cache() {
    typing_effect $'\n\e[1;32m正在清理缓存文件...\e[0m'
    deleted_cache=$(find /data/data/com.termux/files/home/.cache/ -type f -delete -print 2>/dev/null)
    deleted_app_cache=$(find /data/data/com.termux/cache -type f -delete -print 2>/dev/null)
    echo "$deleted_cache" >> "$LOG_FILE"
    echo "$deleted_app_cache" >> "$LOG_FILE"
}

# 清理缓存的软件包函数
clean_cached_packages() {
    typing_effect $'\n\e[1;32m正在清理缓存的软件包...\e[0m'
    deleted_packages=$(apt-get clean 2>/dev/null | grep "cleaned")
    echo "$deleted_packages" >> "$LOG_FILE"
}

# 移除不必要的软件包函数
remove_unused_packages() {
    typing_effect $'\n\e[1;32m正在移除不必要的软件包...\e[0m'
    removed_unused_packages=$(apt autoremove -y 2>/dev/null)
    echo "$removed_unused_packages" >> "$LOG_FILE"
}

# 清理临时文件函数
clean_temp_files() {
    typing_effect $'\n\e[1;32m正在清理临时文件...\e[0m'
    deleted_temp=$(find /data/data/com.termux/files/home/tmp/ -type f -delete -print 2>/dev/null)
    echo "$deleted_temp" >> "$LOG_FILE"
}

# 清理临时备份文件函数
clean_temp_backup_files() {
    typing_effect $'\n\e[1;32m正在清理临时备份文件...\e[0m'
    deleted_temp_backup=$(find /data/data/com.termux/files/home/ -type f -name "*.bak" -delete -print 2>/dev/null)
    echo "$deleted_temp_backup" >> "$LOG_FILE"
}

# 清理不必要日志函数
clean_unnecessary_logs() {
    typing_effect $'\n\e[1;32m正在清理不必要的日志...\e[0m'
    echo -e "\n\\e[1;34m-----------------------------------
日期: $(date)\e[0m
\e[1;34m-----------------------------------
\e[1;31m旧的清理日志已删除。" > "$LOG_FILE"
    deleted_logs=$(find /data/data/com.termux/files/home -type f -name "*.log" -delete -print 2>/dev/null)
    echo "$deleted_logs" >> "$LOG_FILE"
}

# 成功消息函数
success_msg() {
    echo -e "\n\e[40;38;5;83m清理完成。详细信息已记录在 ${LOG_FILE} 中 \e[0m\n" 
}

# 清理所有垃圾函数
clean_all() {
    clean_unnecessary_logs
    clean_cache
    clean_cached_packages
    remove_unused_packages
    clean_temp_files
    clean_temp_backup_files
}

# 检查并清理函数
check_and_clean() {
    # 检查是否选择清理日志
    if [[ "$clean_logs" == "y" || "$clean_logs" == "Y" ]]; then
        clean_unnecessary_logs
    else
        echo -e "\n\e[1;33m已跳过清理不必要的日志。\e[0m"
        echo -e "\n\e[1;33m已跳过清理不必要的日志。\e[0m \n" >> "$LOG_FILE"
    fi

    # 检查是否选择清理缓存
    if [[ "$clean_cache" == "y" || "$clean_cache" == "Y" ]]; then
        clean_cache
    else
        echo -e "\n\e[1;33m已跳过清理缓存文件。\e[0m"
        echo -e "\n\e[1;33m已跳过清理缓存。\e[0m \n" >> "$LOG_FILE"
    fi

    # 检查是否选择清理缓存的软件包
    if [[ "$clean_packages" == "y" || "$clean_packages" == "Y" ]]; then
        clean_cached_packages
    else
        echo -e "\n\e[1;33m已跳过清理缓存的软件包。\e[0m"
        echo -e "\n\e[1;33m已跳过清理缓存的软件包。\e[0m \n" >> "$LOG_FILE"
    fi

    # 检查是否选择清理未使用的软件包
    if [[ "$clean_unused_packages" == "y" || "$clean_unused_packages" == "Y" ]]; then
        remove_unused_packages
    else
        echo -e "\n\e[1;33m已跳过移除不必要的软件包。\e[0m"
        echo -e "\n\e[1;33m已跳过清理不必要的软件包。\e[0m \n" >> "$LOG_FILE"
    fi

    # 检查是否选择清理临时文件
    if [[ "$clean_temp" == "y" || "$clean_temp" == "Y" ]]; then
        clean_temp_files
    else
        echo -e "\n\e[1;33m已跳过清理临时文件。\e[0m"
        echo -e "\n\e[1;33m已跳过清理临时文件。\e[0m \n" >> "$LOG_FILE"
    fi

    # 检查是否选择清理临时备份文件
    if [[ "$clean_temp_backup" == "y" || "$clean_temp_backup" == "Y" ]]; then
        clean_temp_backup_files
    else
        echo -e "\n\e[1;33m已跳过清理临时备份文件。\e[0m"
        echo -e "\n\e[1;33m已跳过清理临时备份文件。\e[0m \n" >> "$LOG_FILE"
    fi
}

# 显示帮助函数
display_help() {
    echo -e "\n\e[1;34m使用方法:\e[0m"
    echo -e "\e[1;33m  clean [选项]\e[0m"
    echo -e "\n\e[1;34m选项:          含义:\e[0m"
    echo -e "\e[1;33m  -h, --help    显示帮助信息\e[0m"
    echo -e "\e[1;33m  -c            清理缓存文件\e[0m"
    echo -e "\e[1;33m  -p            清理缓存的软件包\e[0m"
    echo -e "\e[1;33m  -n            移除不必要的软件包\e[0m"
    echo -e "\e[1;33m  -t            清理临时文件\e[0m"
    echo -e "\e[1;33m  -b            清理临时备份文件\e[0m"
    echo -e "\e[1;33m  -l            清理不必要的日志\e[0m"
    echo -e "\e[1;33m  -a            清理所有类型的垃圾\e[0m"
    echo -e "\n\e[1;34m示例:\e[0m"
    echo -e "\e[1;33m  clean -c\e[0m   # 清理缓存"
    echo -e "\n\e[1;34m注意:\e[0m"
    echo -e "\e[1;33m  可以组合多个选项，例如 clean -c -p -t\e[0m\n"
}

# 检查选项并执行对应的清理函数
options=("$@")

for option in "${options[@]}"; do
    case $option in
        "-h" | "--help")
            display_help
            exit 0
            ;;
        "-a")
            clean_all
            ;;
        "-c")
            clean_cache
            ;;
        "-p")
            clean_cached_packages
            ;;
        "-n")
            remove_unused_packages
            ;;
        "-t")
            clean_temp_files
            ;;
        "-b")
            clean_temp_backup_files
            ;;
        "-l")
            clean_unnecessary_logs
            ;;
           *)
            echo -e "\n\e[1;31m无效选项。\e[1;33m 使用 '$0 -h' 查看帮助。\e[0m \n"
            echo -e "\e[1;31m无效选项。\e[1;33m 使用 '$0 -h' 查看帮助。\e[0m\n" >> "$LOG_FILE"
            exit 1
            ;;
    esac
done

# 默认情况（无参数时）
if [ "$#" == "0" ]; then
    cleanup_options
    check_and_clean
fi

# 清理完成后的默认操作
success_msg


```

```
正在清理不必要的日志...                                                                                                                                                                                                                                                                                      正在清理缓存文件...


直接给我对应步骤的bash命令，不需要sh脚本的太冗余
我需要查看每个步骤执行的具体命令是什么，给我对应的几行命令即可

给我markdown格式，一二三四
```

以下是各个清理步骤对应的具体bash命令，按步骤列出：

```markdown
# Termux垃圾清理对应命令

## 一、清理不必要的日志文件
```bash
find /data/data/com.termux/files/home -type f -name "*.log" -delete
```

## 二、清理缓存文件
```bash
find /data/data/com.termux/files/home/.cache/ -type f -delete
find /data/data/com.termux/cache -type f -delete
```

## 三、清理缓存的软件包
```bash
apt-get clean
```

## 四、移除不必要的软件包
```bash
apt autoremove -y
```

## 五、清理临时文件
```bash
find /data/data/com.termux/files/home/tmp/ -type f -delete
```

## 六、清理临时备份文件
```bash
find /data/data/com.termux/files/home/ -type f -name "*.bak" -delete
```

## 七、查看清理日志
```bash
cat cleanup_log.txt
```

注意：执行前建议先备份重要数据，部分命令需要root权限才能完全生效。
```
