# Contributor: Andreas Wagner <Andreas dot Wagner at em dot uni-frankfurt dot de>

pkgname=logcheck
pkgver=1.3.17
pkgrel=1
pkgdesc="looks for anomalies in the system logfiles"
license=('GPL')
arch=('i686' 'x86_64')
url="http://packages.qa.debian.org/l/logcheck.html"
depends=('cron'
         'shadow'
         'grep'
         'perl>=5.8.0'
         'lockfile-progs'
         'run-parts'
         'mime-construct>=1.11-2')
optdepends=("syslog-ng: log systemd's journal so that logcheck sees it")
install="$pkgname.install"
backup=(etc/logcheck/logcheck.conf
	etc/logcheck/logcheck.logfiles
	etc/cron.hourly/logcheck
	etc/tmpfiles.d/logcheck)
source=(http://ftp.debian.org/debian/pool/main/l/$pkgname/${pkgname}_${pkgver}.tar.xz
	http://logcheck.org/docs/logcheck.8
	logcheck.tmpfile)

package() {
    cd $srcdir/$pkgname-$pkgver

    make install DESTDIR=$pkgdir

    # binaries to /usr/bin
    install -d -m 755 $pkgdir/usr/bin
    mv $pkgdir/usr/sbin/* $pkgdir/usr/bin
    rmdir $pkgdir/usr/sbin
    sed -i -e 's#/usr/sbin/logtail2#/usr/bin/logtail2#' $pkgdir/usr/bin/logcheck
    sed -i -e 's/\/var\/log\/syslog$/\/var\/log\/syslog.log/' $pkgdir/etc/logcheck/logcheck.logfiles

    # man
    install -d -m 755 $pkgdir/usr/share/man/man{1,8}
    install -m 644 docs/logtail.8 			$pkgdir/usr/share/man/man8
    install -m 644 docs/logtail2.8 			$pkgdir/usr/share/man/man8
    install -m 644 docs/logcheck-test.1 		$pkgdir/usr/share/man/man1
    install -m 644 $startdir/logcheck.8 		$pkgdir/usr/share/man/man8

    # doc
    install -d -m 755 $pkgdir/usr/share/doc/logcheck/tools
    install -m 644 AUTHORS 	$pkgdir/usr/share/doc/logcheck
    install -m 644 CHANGES 	$pkgdir/usr/share/doc/logcheck
    install -m 644 CREDITS 	$pkgdir/usr/share/doc/logcheck
    install -m 644 INSTALL 	$pkgdir/usr/share/doc/logcheck
    install -m 644 TODO 	$pkgdir/usr/share/doc/logcheck
    cp -r docs/*		$pkgdir/usr/share/doc/logcheck
    cp -r debian/*		$pkgdir/usr/share/doc/logcheck
#    install -m 644 docs/README* 	$pkgdir/usr/share/doc/logcheck
#    install -m 755 docs/tools/log-summary-ssh $pkgdir/usr/share/doc/logcheck/tools
#    install -m 644 debian/* 	$pkgdir/usr/share/doc/logcheck
    chmod 755 $pkgdir/usr/share/doc/logcheck/rules

    # mail header
    install -m 644 debian/header.txt $pkgdir/etc/logcheck

    # logfiles entries
    sed -i -e 's/\/var\/log\/syslog$/\/var\/log\/syslog.log/' $pkgdir/etc/logcheck/logcheck.logfiles
    echo "/var/log/messages.log" >> $pkgdir/etc/logcheck/logcheck.logfiles
    echo "/var/log/kernel.log" >> $pkgdir/etc/logcheck/logcheck.logfiles
    echo "/var/log/mail.log" >> $pkgdir/etc/logcheck/logcheck.logfiles
    echo "/var/log/user.log" >> $pkgdir/etc/logcheck/logcheck.logfiles

    # template ignore files
#    mkdir -p $pkgdir/usr/share/logcheck/templates/{ignore.d.server,violations.ignore.d}
#    install -m 644 $srcdir/local-ignorefiles/* $pkgdir/usr/share/logcheck/templates/ignore.d.server
#    install -m 644 $srcdir/local-violationsfiles/* $pkgdir/usr/share/logcheck/templates/violations.ignore.d

    # cron entry
    install -m 755 -d $pkgdir/etc/cron.hourly
    cronfile="$pkgdir/etc/cron.hourly/logcheck"
    echo "#!/bin/bash" > $cronfile
    echo 'if [ -x /usr/bin/logcheck ]; then nice -n 10 su -s /bin/bash -c "/usr/bin/logcheck" logcheck; fi' >> $cronfile
    chmod 755 $cronfile

    # tmpfile
    mkdir -p $pkgdir/etc/tmpfiles.d/
    install -m 644 $srcdir/logcheck.tmpfile $pkgdir/etc/tmpfiles.d/logcheck.conf

}

md5sums=('420f28a35e6eee7057319b3e4b7e770c'
         'a9d8c93ecc12d61b0e87bf07ed951ac8'
         '3101ebf1af75f3d9dd6e5de8c3bd087f')
