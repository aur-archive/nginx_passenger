# Maintainer: Dimytch <dimytch@mail.ru>
# partially stolen from
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Miroslaw Szot <mss@czlug.icis.pcz.pl>
# Contributor: Yanchenko Igor <yanchenko.igor@gmail.com>

pkgname=nginx_passenger
pkgver=3.0.11
pkgrel=1
_nginxver=1.1.12

_doc_root=/srv/http/nginx
_conf_path=/var/log/nginx
_tmp_path=/var/spool/nginx
_log_path=/var/log/nginx
_user=nginx
_group=nginx
_prefix=/opt/nginx
_passenger_src=/opt/ruby-enterprise/lib/ruby/gems/1.8/gems/passenger-$pkgver/ext/nginx


pkgdesc="Nginx with mod_rails module"
arch=('i686' 'x86_64')
depends=('pcre' 'zlib' 'openssl')
url="http://www.modrails.com"
license=('GPL')
backup=("${_conf_path#/}/nginx.conf" \
        "${_conf_path#/}/koi-win" \
        "${_conf_path#/}/koi-utf" \
        "${_conf_path#/}/win-utf" \
        "${_conf_path#/}/mime.types" \
        "${_conf_path#/}/fastcgi_params" \
        "etc/logrotate.d/nginx")

source=(http://nginx.org/download/nginx-${_nginxver}.tar.gz \
        nginx.logrotate \
        nginx)

md5sums=('2a98411773c87a98e92c5aa68f322338'
         'c2ebd1a3348944944737baacea07d2b3'
         '70b39280671d3cee700bea3883852d32')

build() {
        echo "startdir == ${startdir}"
        echo "srcdir == ${srcdir}"

        local _src_dir=$startdir/src/nginx-${_nginxver}
        local _build_dir=$_src_dir/objs

        cd $_src_dir
        ./configure \
                --prefix=${_prefix} \
                --pid-path=/var/run/nginx.pid \
                --lock-path=/var/lock/nginx.lock \
                --http-client-body-temp-path=${_tmp_path}/client_body_temp \
                --http-proxy-temp-path=${_tmp_path}/proxy_temp \
                --http-fastcgi-temp-path=${_tmp_path}/fastcgi_temp \
                --http-log-path=${_log_path}/access.log \
                --error-log-path=${_log_path}/error.log \
                --user=${_user} --group=${_group} \
                --with-http_ssl_module \
                --with-http_stub_status_module \
                --add-module=${_passenger_src} \
                --with-http_perl_module \
                --with-http_gzip_static_module \
                --with-cc-opt=-Wno-error

        make || return 1
        make DESTDIR="${startdir}/pkg" install || return 1
        echo "startdir == ${startdir}"
        install -d "${startdir}/pkg/etc/logrotate.d/"
        cp -v "${startdir}/src/nginx.logrotate" "${startdir}/pkg/etc/logrotate.d/"
        #sed -i -e "s/\<user\s\+\w\+;/user $_user;/g" "${startdir}/pkg/${_conf_path}/nginx.conf"

        install -d "${startdir}/pkg/${_tmp_path}"

        #echo "# move default document root outside server root"
        #install -d "${startdir}/pkg/${_doc_root}"
        #mv -v $startdir/pkg/$_server_root/html/* $startdir/pkg/$_doc_root/
        #rm -rf $startdir/pkg/$_server_root/html
        #rm -f $startdir/pkg/$_doc_root/index.html

        # let's create links for relative paths in config file
        #ln -s "$_log_path ${startdir}/pkg/${_server_root}/logs" && \
        #ln -s "$_doc_root ${startdir}/pkg/${_server_root}/html"

        install -D -m755 "${startdir}/src/nginx" "${startdir}/pkg/etc/rc.d/nginx" && \
        mkdir -p "${pkgdir}/etc/conf.d"
        #echo "NGINX_CONFIG=/etc/nginx/conf/nginx.conf" > "${pkgdir}/etc/conf.d/nginx"
}
