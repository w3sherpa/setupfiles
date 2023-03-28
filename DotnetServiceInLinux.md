# Deploying to linux as Daemon

## Server pre-requisites
>Install donet runtimes
Open a terminal and run the following commands:
### Get Ubuntu version
`declare repo_version=$(if command -v lsb_release &> /dev/null; then lsb_release -r -s; else grep -oP '(?<=^VERSION_ID=).+' /etc/os-release | tr -d '"'; fi)`

### Download Microsoft signing key and repository
`wget https://packages.microsoft.com/config/ubuntu/$repo_version/packages-microsoft-prod.deb -O packages-microsoft-prod.deb`

### Install Microsoft signing key and repository
`sudo dpkg -i packages-microsoft-prod.deb`  

### Clean up
`rm packages-microsoft-prod.deb`  

###  Update packages
`sudo apt update`

### Install .NET
`apt install aspnetcore-runtime-7.0`

## Service installation
1. Publish project to a local drive.( "c:/deployment/servicebus
2. Log in to the linux server and create /var/servicebus directory
3. Open powersh shell and run followin command
`scp -rp C:/deployment/servicebus/* root@104.237.135.153:/var/servicebus`

4. create a Systemd unit file at /etc/systemd/system directory using followin command
`sudo nano /etc/systemd/system/servicebus.service`

5. Add the content of servicebus.service file in this directory
6. Run `sudo systemctl daemon-reload`
7. Run `sudo systemctl enable servicebus.service`
8. Run `sudo systemctl start servicebus.service`
9. Run `sudo systemctl status servicebus.service`