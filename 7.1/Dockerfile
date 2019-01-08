FROM centos/s2i-base-centos7

# This image provides an Apache+PHP environment for running PHP
# applications.

ENV http_proxy http://10.236.240.71:3128/
ENV https_proxy http://10.236.240.71:3128/

EXPOSE 8080
EXPOSE 8443

# Description
# This image provides an Apache 2.4 + PHP 7.1 environment for running PHP applications.
# Exposed ports:
# * 8080 - alternative port for http

ENV PHP_VERSION=7.1 \
    PHP_VER_SHORT=71 \
    NAME=php \
    PATH=$PATH:/opt/rh/rh-php71/root/usr/bin

ENV SUMMARY="Platform for building and running PHP $PHP_VERSION applications" \
    DESCRIPTION="PHP $PHP_VERSION available as container is a base platform for \
building and running various PHP $PHP_VERSION applications and frameworks. \
PHP is an HTML-embedded scripting language. PHP attempts to make it easy for developers \
to write dynamically generated web pages. PHP also offers built-in database integration \
for several commercial and non-commercial database management systems, so writing \
a database-enabled webpage with PHP is fairly simple. The most common use of PHP coding \
is probably as a replacement for CGI scripts."

LABEL summary="${SUMMARY}" \
      description="${DESCRIPTION}" \
      io.k8s.description="${DESCRIPTION}" \
      io.k8s.display-name="Apache 2.4 with PHP ${PHP_VERSION}" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,${NAME},${NAME}${PHP_VER_SHORT},rh-${NAME}${PHP_VER_SHORT}" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.s2i.scripts-url="image:///usr/libexec/s2i" \
      name="centos/${NAME}-${PHP_VER_SHORT}-centos7" \
      com.redhat.component="rh-${NAME}${PHP_VER_SHORT}-container" \
      version="${PHP_VERSION}" \
      help="For more information visit https://github.com/sclorg/s2i-${NAME}-container" \
      usage="s2i build https://github.com/sclorg/s2i-php-container.git --context-dir=${PHP_VERSION}/test/test-app centos/${NAME}-${PHP_VER_SHORT}-centos7 sample-server" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>"

# Install Apache httpd and PHP
RUN yum install -y centos-release-scl && \
    INSTALL_PKGS="rh-php71 rh-php71-php rh-php71-php-mysqlnd rh-php71-php-pgsql rh-php71-php-bcmath \
                  rh-php71-php-gd rh-php71-php-intl rh-php71-php-ldap rh-php71-php-mbstring rh-php71-php-pdo \
                  rh-php71-php-process rh-php71-php-soap rh-php71-php-opcache rh-php71-php-xml \
                  rh-php71-php-gmp rh-php71-php-pecl-apcu httpd24-mod_ssl" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS --nogpgcheck && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

ENV PHP_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/php/ \
    APP_DATA=${APP_ROOT}/src \
    PHP_DEFAULT_INCLUDE_PATH=/opt/rh/rh-php71/root/usr/share/pear \
    PHP_SYSCONF_PATH=/etc/opt/rh/rh-php71 \
    PHP_HTTPD_CONF_FILE=rh-php71-php.conf \
    HTTPD_CONFIGURATION_PATH=${APP_ROOT}/etc/conf.d \
    HTTPD_MAIN_CONF_PATH=/etc/httpd/conf \
    HTTPD_MAIN_CONF_D_PATH=/etc/httpd/conf.d \
    HTTPD_VAR_RUN=/var/run/httpd \
    HTTPD_DATA_PATH=/var/www \
    HTTPD_DATA_ORIG_PATH=/opt/rh/httpd24/root/var/www \
    HTTPD_VAR_PATH=/opt/rh/httpd24/root/var \
    SCL_ENABLED=rh-php71

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

# Reset permissions of filesystem to default values
RUN /usr/libexec/container-setup && rpm-file-permissions

USER 1001





USER root
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
RUN yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm -y
RUN yum update
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/python-backports-1.0-8.el7.x86_64.rpm
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/python-ipaddress-1.0.16-2.el7.noarch.rpm
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/python-backports-ssl_match_hostname-3.5.0.1-1.el7.noarch.rpm
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/python-setuptools-0.9.8-7.el7.noarch.rpm
RUN yum install supervisor -y
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/libzip-0.10.1-8.el7.x86_64.rpm
RUN rpm -Uvh http://mirror.centos.org/centos/7/os/x86_64/Packages/tcl-8.5.13-8.el7.x86_64.rpm
RUN yum --enablerepo=remi install php71-php-pecl-redis -y
RUN yum --enablerepo=remi-test install php71-php-pecl-redis
RUN bash -c "echo extension=/opt/remi/php71/root/lib64/php/modules/igbinary.so > /etc/opt/rh/rh-php71/php.d/20-igbinary.ini"
RUN bash -c "echo extension=/opt/remi/php71/root/lib64/php/modules/redis.so > /etc/opt/rh/rh-php71/php.d/20-redis.ini"


USER 1001


# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
