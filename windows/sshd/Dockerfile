# NOTE: docker build 혹은 run에서 DNS 문제 발생시 --network 옵션으로 알맞은 네트워크로 변경 필요
FROM mcr.microsoft.com/windows/servercore:ltsc2019

RUN powershell -Command Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

ADD https://github.com/PowerShell/Win32-OpenSSH/releases/download/0.0.24.0/OpenSSH-Win64.zip c:/openssh.zip

RUN powershell -Command Expand-Archive c:/openssh.zip c:/; Remove-Item c:/openssh.zip
RUN powershell -Command c:/OpenSSH-Win64/Install-SSHd.ps1
RUN powershell -Command Set-Service sshd -StartupType Automatic
RUN c:/OpenSSH-Win64/ssh-keygen.exe -t ed25519 -N "" -f c:/OpenSSH-Win64/ssh_host_ed25519_key
RUN (echo Port 22\
& echo Protocol 2\
& echo LogLevel DEBUG\
& echo PermitRootLogin yes\
& echo RSAAuthentication no\
& echo PubkeyAuthentication no\
& echo # The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2\
& echo # but this is overridden so installations will only check .ssh/authorized_keys\
& echo AuthorizedKeysFile .ssh/authorized_keys\
& echo # To disable tunneled clear text passwords, change to no here!\
& echo PasswordAuthentication yes\
& echo PermitEmptyPasswords yes)\
> c:/OpenSSH-Win64/sshd_config
RUN powershell -Command c:/OpenSSH-Win64/FixHostFilePermissions.ps1 -Confirm:$false
RUN net USER ssh /ADD && net localgroup "Administrators" "ssh" /ADD
RUN powershell -Command Start-Service sshd

CMD ping -t localhost
