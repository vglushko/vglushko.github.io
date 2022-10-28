<!--more-->
0. Install VirtualBox Guest Additions. (see http://www.giannistsakiris.com/2008/04/09/virtualbox-access-windows-host-shared-folders-from-ubuntu-guest/)
GUI-less: https://www.techrepublic.com/article/how-to-install-virtualbox-guest-additions-on-a-gui-less-ubuntu-server-host/
1. sudo apt-get update && sudo apt-get dist-upgrade
2. sudo apt install docker.io
docker --version
sudo systemctl start docker
sudo systemctl enable docker
3. Create a folder shared with WinOS host
(see http://www.giannistsakiris.com/2008/04/09/virtualbox-access-windows-host-shared-folders-from-ubuntu-guest/)
sudo mount -t vboxsf folder-name /media/windows-share
Put the following to /etc/fstab for auto mount on startup:
hilti-phoenix           /media/win-shared/hilti-phoenix   vboxsf  rw      0   0
4. Enable symlinks for the shared folder
(see https://stackoverflow.com/questions/23936458/correct-way-to-setup-virtualbox-4-3-to-use-symlinks-on-guest-for-meteor)
5. Create symbolic link to shared folder
 ln -s <real folder> <link folder>
6. sudo apt-get install python2.7
sudo apt install python-minimal
7. 
sudo ln -s "$NVM_DIR/versions/node/$(nvm version)/bin/node" "/usr/local/bin/node"
sudo ln -s "$NVM_DIR/versions/node/$(nvm version)/bin/npm" "/usr/local/bin/npm"
8. Put certificate to trusted root
sudo cp certs/phoenix-selfsigned.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
9. Enable SSH on Ubuntu
https://www.maketecheasier.com/setup-enable-ssh-ubuntu/
10. Run VirtualBox as Administrator
