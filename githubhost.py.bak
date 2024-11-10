import requests
import re
import os

# GitHub hosts文件URL
GITHUB_HOSTS_URL = 'https://raw.githubusercontent.com/521xueweihan/GitHub520/main/hosts'
# 本地更新时间文件
LOCAL_UPDATE_TIME_FILE = "last_update_time.txt"
# 输出文件名
OUTPUT_FILE = "GitHubHost.plugin"

def fetch_github_hosts():
    """获取最新的GitHub hosts文件内容。"""
    try:
        response = requests.get(GITHUB_HOSTS_URL)
        response.raise_for_status()
        return response.text
    except requests.RequestException as e:
        print(f"获取hosts文件失败: {e}")
        return None

def extract_update_time(hosts_content):
    """从hosts内容中提取更新时间。"""
    match = re.search(r"# Update time:\s+([^\n]+)", hosts_content)
    if match:
        return match.group(1).strip()
    return None

def convert_hosts_to_custom_format(hosts_content, update_time):
    """将hosts文件内容转换为指定格式。"""
    header = f"#!name= GitHub Host\n#!desc= # Update time: {update_time}\n[Host]\n"
    formatted_content = header

    # 匹配每行的IP地址和域名，例如：140.82.114.26 alive.github.com 或 baidu.com
    pattern = re.compile(r"^(\d+\.\d+\.\d+\.\d+)\s+([a-zA-Z0-9.-]+)$", re.MULTILINE)
    matches = pattern.findall(hosts_content)

    # 遍历每个匹配项并应用格式：domain = ip
    for ip, domain in matches:
        formatted_content += f"{domain} = {ip}\n"

    return formatted_content

def get_local_update_time():
    """从本地文件获取上次更新时间。"""
    if os.path.exists(LOCAL_UPDATE_TIME_FILE):
        with open(LOCAL_UPDATE_TIME_FILE, "r") as file:
            return file.read().strip()
    return None

def save_local_update_time(update_time):
    """将最新的更新时间保存到本地文件。"""
    with open(LOCAL_UPDATE_TIME_FILE, "w") as file:
        file.write(update_time)

def main():
    # 获取hosts文件内容
    hosts_content = fetch_github_hosts()
    if hosts_content is None:
        return

    # 提取远程更新时间
    remote_update_time = extract_update_time(hosts_content)
    if not remote_update_time:
        print("未找到更新时间，无法更新。")
        return

    # 获取本地保存的更新时间
    local_update_time = get_local_update_time()

    # 比较本地和远程更新时间
    if local_update_time == remote_update_time:
        print("更新时间相同，无需更新。")
        return

    # 更新时间不同，进行格式转换并保存文件
    formatted_content = convert_hosts_to_custom_format(hosts_content, remote_update_time)
    with open(OUTPUT_FILE, "w") as file:
        file.write(formatted_content)

    # 保存最新的更新时间
    save_local_update_time(remote_update_time)
    print(f"内容已更新，并保存到 {OUTPUT_FILE}")

if __name__ == "__main__":
    main()
