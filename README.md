# Docker によるネットワーク機能の調査

ubuntu_with_ssh, ubuntu_with_ssh_client の２つの Docker ファイルからイメージを作成し、
それぞれを起動した。

docker build -t kinoue/ubuntu_with_ssh ./ubuntu_with_ssh
docker build -t kinoue/ubuntu_with_ssh_client ./ubuntu_with_ssh_client
docker run -d --name ssh_server kinoue/ubuntu_with_ssh
docker run -it kinoue/ubuntu_with_ssh_client

これで、 ssh_client に入る。

docker network ls
で network の名前一覧を調べ、いわゆるデフォルトの bridge ネットワークについて、

docker inspect bridge
で調べる。すると、以下のように、コンテナの名前と IP アドレスの対応関係がわかる。

[
    {
        "Name": "bridge",
        "Id": "eb9bf0ae6d9406cc3900bad020e0322942b7ee8371c5978068b6a85afd1af4f3",
        "Created": "2018-08-22T01:33:48.7369944Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "3d95652dd9b2138e050a90d3cff5123250a734e54db9708349be8d3a3946c7fb": {
                "Name": "ssh_server",
                "EndpointID": "b15d8c3081b983f0e3ec5bb58a12e8a6098d16d5c4206ed828e119290bf706d0",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "b995fa5ffc9b8d8da45478ef27bc9ce7ab70c69295b406a9ea94d4dd46a9c217": {
                "Name": "elated_knuth",
                "EndpointID": "b54331e814cf870a8c27e4ab5f49bba14aabc2da713c37a87990ebfbf0a5aa91",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

IP アドレスが分かったら、 ssh_client 上から、

ssh kinoue@(IP address)

とすることで、 ssh で接続できることを確認できた。

しかし現状では、 inspect でいちいち IP アドレスを調べなくてはならず、手間が大きい。
そこで、docker-compose を使うことで、名前でサーバを指定してアクセスできる形で、
２つのサーバを起動することを試みる。

docker-compose.yml を作成し、

docker-compose build
docker-compose up

とした。

その後、

docker container ls
docker exec -it (container_id) bash

で ssh_client コンテナの中に入り、

ssh kinoue@ssh_server

したら、ちゃんと入れた。 ssh_server の名前で、サーバにアクセスできるように設定されている。なぜ機能はうまく動かなかったのだろう？さっぱりわからない
