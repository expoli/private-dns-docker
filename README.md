# private-dns-docker
dnsmasq and unbound private dns, use docker.

## 文件结构

```
.
├── dnsmasq.d
│   └── unbound.conf
├── docker-compose.yaml
└── unbound
    └── ssl
        ├── ssl-service-key.key
        └── ssl-service-pem.pem
```

## 启动

### 1. 克隆本项目

```shell
git clone https://github.com/expoli/private-dns-docker.git
```

### 2. 将自己对应的ssl密钥放入

```shell
cp path/to/your_ssl.key ./unbound/ssl/ssl-service-key.key
cp path/to/your_ssl.pem ./unbound/ssl/ssl-service-pem.pem
```

### 3. 启动

```shell
docker-compose up -d
```

### 4. 查看启动日志

```shell
docker-compose logs
```

## 文件示例

### docker-compose.yaml

```yaml
version: "3"
services:
  dnsmasq:
    image: tangcuyu/dnsmasq-server:latest
    restart: always
    ports:
      - "53:53"
    depends_on:
      - "unbound"
    volumes:
      - ./dnsmasq.d/:/etc/dnsmasq.d/
    network_mode: host
    container_name: "dnsmasq-server"

  unbound:
    image: tangcuyu/unbound-server:latest
    restart: always
    ports:
      - "853:853"
    volumes:
      - ./unbound/ssl/:/etc/unbound/ssl/
    environment:
      - DNS_DOMAIN_NAME=your_private_dns_domain_name
      - THREADS_NUM=4
    container_name: "unbound-server"
    network_mode: host
    cap_add:
      - SYS_ADMIN
```

### dnsmasq/unbound.conf

```conf
# Redirect everything to unbound dns server
no-resolv
domain-needed
filterwin2k
no-poll
server=127.0.0.1#853
cache-size=4096
proxy-dnssec
```

## dnsmasq-server 项目

https://github.com/expoli/dnsmasq-server.git

## unbound-server 项目

https://github.com/expoli/unbound-server.git
