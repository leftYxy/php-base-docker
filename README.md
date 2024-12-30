# php-base-docker
docker-php-基础环境
复制.env.example到.env
docker网络
注意端口映射
nginx配置域名宿主机hosts
创建网路组:docker network create --driver bridge --subnet 172.100.0.0/16 --gateway 172.100.1.1 php_network

