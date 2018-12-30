# study_fabic_fabric-ca


## Virtualbox or VM Ware + Ubuntu 설치 (v18.04.1)

네트워크 1개더 추가 하여  Host-only 설정



## 환경 설치


- 관리자 접속

_# sudo -i_


- go 다운로드

_# wget https://storage.googleapis.com/golang/go1.10.4.linux-amd64.tar.gz_


- 압축해제

_# tar -xvf go1.10.4.linux-amd64.tar.gz_


- PATH 설정

_# mkdir /root/gopath_
_# gedit /etc/profile_

- /etc/profile  하단작성
```
export GOPATH=/root/gopath
export GOROOT=/root/go
export PATH=$PATH:$GOROOT/bin
```

- 저장 종료 후 시스템 업데이트.

_# source /etc/profile_

- 환경변수 설정확인

_# cd $GOPATH_
_# cd $GOROOT_

- 기타 개발도구 설치
```
# apt-get install python-pip
# apt-get install git
# apt-get install curl
# apt-get install libltdl-dev
# apt-get install tree
# apt-get install openssh-server
# apt-get install net-tools
```

- docker, docker-compose 설치

- 도커 다운로드 및 테스트

_# wget https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb_

_# dpkg -i docker-ce_17.06.2~ce-0~ubuntu_amd64.deb_

_# docker run hello-world_



- docker-compose 설치

_# pip install docker-compose_

_# docker-compose --version_


- Atom

_# add-apt-repository ppa:webupd8team/atom_

_# apt-get update_

_# apt-get install atom_


- 하이퍼레저 패브릭 설치
- 3 버전 다운로드

_# mkdir -p $GOPATH/src/github.com/hyperledger_

_# cd $GOPATH/src/github.com/hyperledger_

_# git clone -b release-1.3 https://github.com/hyperledger/fabric_



- 하이퍼레저 패브릭 소스 컴파일

_# cd fabric_

- git reset --hard 명령어를 통해 필자가 사용한 하이퍼레저 패브릭 프로그램과 동일한 커밋 시점으로 이동

_# git reset --hard d942308df6302d3510e835bad62f861ad854b4b3_

_# make_

- unit-test_1이 반복적으로 나타나게 됩니다. 이때 Ctrl + C를 눌러 unit-test_1을 강제 종료합니다.



- 하이퍼레저 패브릭 환경변수 설정

_# gedit /etc/profile_

- 파일 하단에 다음과 같이 입력합니다.
```
export FABRIC_HOME=/root/gopath/src/github.com/hyperledger/fabric
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric/.build/bin
```

- 저장 후 종료

_# source /etc/profile_

- 정상적 설치 확인 'cryptogen' 명령어 실행

_# cryptogen_



- 실습전 e2e_cli 테스트 실행

_# cd $FABRIC_HOME/examples/e2e_cli_

_# ./network_setup.sh up_


// ctrl-c 테스트종료

_# ./network_setup.sh down_



- 작업 디렉터리 생성

_# mkdir /root/testnet_

_# cd testnet_


- 시스템 기본설정 파일 경로 지정

_# cp /root/gopath/src/github.com/hyperledger/fabric/sampleconfig/core.yaml /root/testnet/core.yaml_

_# cp /root/gopath/src/github.com/hyperledger/fabric/sampleconfig/orderer.yaml /root/testnet/orderer.yaml_

_# gedit /etc/profile_

- 파일 하단에 다음과 같이 입력한다
```
export FABRIC_CFG_PATH=/root/testnet
```

- 저장 후 종료

_# source /etc/profile_


## 네트워크 구축 (Fabric-ca)

- hostname 및 hosts 파일 설정

_# gedit /etc/hosts_

// 아래내용 입력

-  hosts 설정

_\# gedit /etc/hosts_
```
127.0.0.1	localhost

10.0.1.11	peer0 
10.0.1.12	peer1
10.0.1.21	peer2
10.0.1.22	peer3
10.0.1.31	orderer0
10.0.1.32	kafka-zookeeper
10.0.1.41	client
10.0.1.13	admin@org0
10.0.1.23	admin@org1
10.0.1.33	admin@ordererorg0
```

- fabric-fa-server와 fabric-ca-client 설치

_# cd $GOPATH/src/github.com/hyperledger_

_# git clone -b release-1.3 https://github.com/hyperledger/fabric-ca_

_# cd fabric-ca_

_# make fabric-ca-server_

_# make fabric-ca-client_


- Fabric-ca 환경변수 설정(루트 가상 머신에서 실행)

_# gedit /etc/profile_

'''
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric-ca/bin
'''

_# source /etc/profile_




## Fabric-CA 서버 실행 및 Fabric-CA 서버 운영자 계정 생성

- fabric-ca 설정파일 저장 경로 설정(fabric-ca 노드에서 실행)

_# gedit /etc/profile_

'''
export FABRIC_CA_SERVER_HOME=/root/testnet/
'''

_# source /etc/profile_

_# cd $FABRIC_CA_SERVER_HOME_ _//설정한 경로로 이동하는지 확인_

- Fabric-CA 서버 구동(fabric-ca 노드에서 실행)
- -b admin:adminpw Fabric-CA ID:PWD 지정
- --cfg.affiliations.allowremove 조직 추가/삭제 권한
- --cfg.identities.allowremove 사용자 추가/삭제 권한
- -d 서버 상세로그 표시

_# fabric-ca-server start -b admin:adminpw --cfg.affiliations.allowremove --cfg.identities.allowremove -d_












