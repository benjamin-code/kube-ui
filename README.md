# kube-ui

1：首先下载kube-ui:v5的镜像。目前好像docker.io上不支持下载。从国内的一个网站上下载(:v5必须要带着)
docker pull index.alauda.cn/googlecontainer/kube-ui:v5

2:创建私有Docker Registry
docker run -d -p 5000:5000 registry

3：给registry加tag:
[root@ip-172-31-17-85 docker]# docker images |grep regi
52.193.220.14:5000/registry               latest              8b162eee2794        12 weeks ago        171.1 MB
docker.io/registry                        2.4.0               8b162eee2794        12 weeks ago        171.1 MB
docker.io/registry                        latest              bca04f698ba8        6 months ago        422.8 MB

4:push出错：
[root@ip-172-31-17-85 Deploy]# docker push 52.193.220.14:5000/registry
The push refers to a repository [52.193.220.14:5000/registry]
unable to ping registry endpoint https://52.193.220.14:5000/v0/
v2 ping attempt failed with error: Get https://52.193.220.14:5000/v2/: EOF
 v1 ping attempt failed with error: Get https://52.193.220.14:5000/v1/_ping: EOF

5：清理一下上面已经启动的Registry容器
 $ docker stop registry
registry
 $ docker rm registry
registry

5：修改Registry server上的Docker daemon的配置（/etc/sysconfig/docker），为DOCKER_OPTS增加–insecure-registry：
DOCKER_OPTS="--insecure-registry 10.10.105.71:5000 ....

6：重启Docker Daemon，启动Registry容器：
 $ sudo service docker restart
 $ docker run -d -p 5000:5000 registry

7:再次push
[root@ip-172-31-17-85 docker]# docker push 52.193.220.14:5000/registry
The push refers to a repository [52.193.220.14:5000/registry]
5f70bf18a086: Image successfully pushed
d73a4514b81b: Image successfully pushed
ec5e6f89215b: Image successfully pushed
7ef965e0bbca: Image successfully pushed
6eb35183d3b8: Image successfully pushed
Pushing tag for rev [8b162eee2794] on {http://52.193.220.14:5000/v1/repositories/registry/tags/latest}


