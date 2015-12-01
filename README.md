# build_squid

yum -y groupinstall development tools nano mc

wget http://www.squid-cache.org/Versions/v3/3.5/squid-3.5.12.tar.gz

--prefix=/usr --exec-prefix=/usr --bindir=/usr/bin --sbindir=/usr/sbin --datadir=/usr/share --libdir=/usr/lib64 --libexecdir=/usr/libexec --sharedstatedir=/var/lib --mandir=/usr/share/man --infodir=/usr/share/info --localstatedir=/var --includedir=/usr/include --sysconfdir=/etc/squid CXXFLAGS=-DMAXTCPLISTENPORTS=10000 --enable-ltdl-convenience --disable-strict-error-checking --exec_prefix=/usr --with-logdir=$(localstatedir)/log/squid --with-pidfile=$(localstatedir)/run/squid.pid

make all
make install

ulimit -n 65536
ulimit -a

nano /usr/lib/systemd/system/squid.service

[Unit]
Description=SQUID 4to6 proxyservice
After=network.target

[Service]
Type=forking
#ExecStartPre=/usr/libexec/squid/cache_swap.sh
ExecStart=/usr/sbin/squid -f /etc/squid/squid.conf
ExecReload=/usr/sbin/squid -k reconfigure -f /etc/squid/squid.conf
ExecStop=/usr/sbin/squid -k shutdown -f /etc/squid/squid.conf
Restart=on-abort

[Install]
WantedBy=multi-user.target


[Install]
WantedBy=multi-user.target

useradd pxyuser

mkdir /var/log/squid
touch /var/log/squid/store.log
touch /var/log/squid/access.log
touch /var/log/squid/cache.log

chmod -R 777 /var/log/squid/store.log
chmod -R 777 /var/log/squid/cache.log
chmod -R 777 /var/log/squid/access.log

squid -NCd1
