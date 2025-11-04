#####################################砍手双端-Max################################################

# 需要安装python依赖：pycryptodome, packaging, httpx, httpx[socks]

# 本脚本为快手普通版和快手极速版合并版，ck填写哪个版本的ck就自动运行指定快手版本，脚本自动检测快手版本类型
# 本脚本为快手普通版和快手极速版合并版，ck填写哪个版本的ck就自动运行指定快手版本，脚本自动检测快手版本类型
# 本脚本为快手普通版和快手极速版合并版，ck填写哪个版本的ck就自动运行指定快手版本，脚本自动检测快手版本类型

# 支持系统类型：arm，amd64，不支持windos，支持py版本：3.10，3.11
# 支持系统类型：arm，amd64，不支持windos，支持py版本：3.10，3.11
# 支持系统类型：arm，amd64，不支持windos，支持py版本：3.10，3.11

######################################使用教程区#################################################
# 变量配置教程：https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/jc.txt
# 变量配置教程：https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/jc.txt
# 变量配置教程：https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/jc.txt
# 变量配置教程：https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/jc.txt
# 变量配置教程：https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/jc.txt

import os
import urllib.request
import sys
import time
import requests
import platform
from packaging import version


class SystemChecker:
    @staticmethod
    def is_arm_architecture():
        """检测是否为ARM架构"""
        machine = platform.machine().lower()
        arm_patterns = [
            'arm', 'aarch', 'arm64', 'aarch64',
            'armv7', 'armv8', 'armhf'
        ]
        return any(pattern in machine for pattern in arm_patterns)

    @staticmethod
    def is_amd_architecture():
        """检测是否为AMD/x86架构"""
        machine = platform.machine().lower()
        amd_patterns = [
            'x86_64', 'amd64', 'x86', 'i386', 'i686',
            'amd', 'intel', 'x64'
        ]
        return any(pattern in machine for pattern in amd_patterns)

    @staticmethod
    def is_supported_architecture():
        """检测是否支持ARM或AMD架构"""
        return SystemChecker.is_arm_architecture() or SystemChecker.is_amd_architecture()

    @staticmethod
    def is_linux_supported():
        """检测是否为Linux且支持ARM或AMD架构"""
        return SystemChecker.is_supported_architecture()

    @staticmethod
    def get_architecture_type():
        """获取具体的架构类型"""
        if SystemChecker.is_arm_architecture():
            return 'arm'
        elif SystemChecker.is_amd_architecture():
            return 'amd'
        else:
            return 'unknown'

    @staticmethod
    def get_detailed_info():
        return {
            'os': platform.system(),
            'architecture': platform.machine(),
            'arch_type': SystemChecker.get_architecture_type()
        }


checker = SystemChecker()

if checker.is_linux_supported():
    pass
else:
    info = checker.get_detailed_info()
    print(f'当前系统不支持,当前系统类型: {info["os"]},系统架构: {info["architecture"]}')
    exit(1)

def get_architecture():
    """获取系统架构"""
    arch = platform.machine().lower()
    if 'arm' in arch or 'aarch' in arch:
        return 'arm'
    elif 'x86' in arch or 'amd' in arch or 'i386' in arch or 'i686' in arch:
        return 'amd'
    else:
        return arch

current_arch = get_architecture()

download_attempts = 0
MAX_DOWNLOAD_ATTEMPTS = 3

def GET_SO():
    global download_attempts
    PythonV = sys.version_info
    if PythonV.major == 3 and PythonV.minor == 10:
        PythonV = '10'
        print('当前Python版本为3.10 开始安装...')
    elif PythonV.major == 3 and PythonV.minor == 11:
        PythonV = '11'
        print('当前Python版本为3.11 开始安装...')
    else:
        return False, f'不支持的Python版本：{sys.version}'

    try:
        mirrors = [
            f'https://raw.bgithub.xyz/BIGOSTK/pyso/refs/heads/main/ksmax_{current_arch}_{PythonV}.so',
            f'https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/ksmax_{current_arch}_{PythonV}.so',
            f'https://raw.githubusercontent.com/BIGOSTK/pyso/main/ksmax_{current_arch}_{PythonV}.so',
            f'https://raw.bgithub.xyz/BIGOSTK/pyso/main/ksmax_{current_arch}_{PythonV}.so'
        ]

        last_error = None
        max_attempts = 5
        attempt = 0

        while attempt < max_attempts and download_attempts < MAX_DOWNLOAD_ATTEMPTS:
            for url in mirrors:
                try:
                    attempt += 1
                    download_attempts += 1
                    print(f'尝试从 {url} 下载... (尝试次数: {download_attempts}/{MAX_DOWNLOAD_ATTEMPTS})')
                    with urllib.request.urlopen(url, timeout=15) as response:
                        if response.status == 200:
                            with open('./ksmax.so', 'wb') as out_file:
                                out_file.write(response.read())
                            print('下载更新成功')
                            return True, None
                except Exception as e:
                    last_error = e
                    print(f'下载更新失败: {e}')
                    if attempt < max_attempts and download_attempts < MAX_DOWNLOAD_ATTEMPTS:
                        time.sleep(1)

        return False, f'所有镜像尝试失败: {last_error}'

    except Exception as e:
        return False, e

def get_version():
    url = 'https://gh-proxy.com/https://raw.githubusercontent.com/BIGOSTK/pyso/main/ksmax_ver.json'
    try:
        r = requests.get(url)
        r.raise_for_status()
        remote_version = r.json()['ksmax']['version']
        return remote_version
    except Exception as e:
        return None

def restart_script():
    print("检测到更新，正在重启脚本...")
    python_executable = sys.executable
    script_path = os.path.abspath(__file__)
    os.execv(python_executable, [python_executable, script_path])

def main():
    global download_attempts
    need_restart = False

    if not os.path.exists('./ksmax.so'):
        print("未找到ksmax.so文件，开始下载...")
        success, error = GET_SO()
        if not success:
            print(f'无法获取ksmax.so: {error}')
            return
        need_restart = True
    else:
        try:
            import ksmax
            LocalVersion = ksmax.get_ver()
            YunVersion = get_version()
            if YunVersion is not None and version.parse(LocalVersion) != version.parse(YunVersion):
                print(f"检测到新版本: {YunVersion} (当前: {LocalVersion})，开始更新...")
                success, error = GET_SO()
                if success:
                    need_restart = True
                else:
                    print(f'更新失败: {error}')
        except Exception as e:
            print(f'运行失败: {e}')

    if need_restart:
        restart_script()

    try:
        import ksmax
        ksmax.main()
    except Exception as e:
        print(f'运行失败: {e}')


if __name__ == '__main__':
    main()