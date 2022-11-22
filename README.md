Создание стенда для работы NFS

Суть.
Настройка 2х виртуальных машин 
nfss - в качестве NFS сервера
nfsc - в качестве NFS клиента

На сервере в качестве NFS директории выступает 
/srv/share/

На клиенте этот сетевой ресурс монтируется в /mnt.

1. При включении сервера последовательность действий/настроек

установка пакета NFS
yum install -y nfs-utils

включение FW
systemctl enable firewalld --now

добавление исключений в FW для работы NFS
firewall-cmd --add-service="nfs3" --add-service="rpc-bind" --add-service="mountd" --permanent

применение исключений FW
firewall-cmd --reload

включение NFS сервиса
systemctl enable nfs --now

создание на сервере директории для NFS, выставление прав доступа
mkdir -p /srv/share/upload
chown -R nfsnobody:nfsnobody /srv/share
chmod 0777 /srv/share/upload

задание и применение прав доступа к NFS директории в NFS сервисе
echo "/srv/share 192.168.56.11/32(rw,sync,root_squash)" > /etc/exports
exportfs -r

2. При включении клиента последовательность действий/настроек для получения доступа к сетевой папке

установка пакета NFS
yum install -y nfs-utils

включение FW
systemctl enable firewalld --now

добавление параметров монтирования сетевой директории в /etc/fstab
echo "192.168.56.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0" >> /etc/fstab

применение настроек монтирования сетевой директории
systemctl daemon-reload
systemctl restart remote-fs.target

3. После последовательной загрузки сервера затем клиента
Общая директория для чтения/записи
/srv/share/upload - на сервере
/mnt/upload - на клиенте


