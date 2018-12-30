# study_fabic_fabric-ca


## Virtualbox or VM Ware + Ubuntu 설치 (v18.04.1)

네트워크 1개더 추가 하여  Host-only 설정



## 환경 설치


- 관리자 접속
```
# sudo -i
```

- go 다운로드
```
# wget https://storage.googleapis.com/golang/go1.10.4.linux-amd64.tar.gz
```

- 압축해제
```
# tar -xvf go1.10.4.linux-amd64.tar.gz
```

- PATH 설정
```
# mkdir /root/gopath
# gedit /etc/profile
```
- /etc/profile  하단작성
```
export GOPATH=/root/gopath
export GOROOT=/root/go
export PATH=$PATH:$GOROOT/bin
```

- 저장 종료 후 시스템 업데이트.
```
# source /etc/profile
```
- 환경변수 설정확인
```
# cd $GOPATH
# cd $GOROOT
```
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
```
# wget https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb
# dpkg -i docker-ce_17.06.2~ce-0~ubuntu_amd64.deb
# docker run hello-world
```


- docker-compose 설치
```
# pip install docker-compose
# docker-compose --version
```

- Atom
```
# add-apt-repository ppa:webupd8team/atom
# apt-get update
# apt-get install atom
```

- 하이퍼레저 패브릭 설치
- 3 버전 다운로드
```
# mkdir -p $GOPATH/src/github.com/hyperledger
# cd $GOPATH/src/github.com/hyperledger
# git clone -b release-1.3 https://github.com/hyperledger/fabric
```


- 하이퍼레저 패브릭 소스 컴파일
```
# cd fabric
```
- git reset --hard 명령어를 통해 필자가 사용한 하이퍼레저 패브릭 프로그램과 동일한 커밋 시점으로 이동
```
# git reset --hard d942308df6302d3510e835bad62f861ad854b4b3
# make
```
- unit-test_1이 반복적으로 나타나게 됩니다. 이때 Ctrl + C를 눌러 unit-test_1을 강제 종료합니다.



- 하이퍼레저 패브릭 환경변수 설정
```
# gedit /etc/profile
```
- 파일 하단에 다음과 같이 입력합니다.
```
export FABRIC_HOME=/root/gopath/src/github.com/hyperledger/fabric
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric/.build/bin
```

- 저장 후 종료
```
# source /etc/profile
```

- 정상적 설치 확인 'cryptogen' 명령어 실행
```
# cryptogen
```


- 실습전 e2e_cli 테스트 실행
```
# cd $FABRIC_HOME/examples/e2e_cli
# ./network_setup.sh up
```
// ctrl-c 테스트종료
```
# ./network_setup.sh down
```


- 작업 디렉터리 생성
```
# mkdir /root/testnet
# cd testnet
```

- 시스템 기본설정 파일 경로 지정
```
# cp /root/gopath/src/github.com/hyperledger/fabric/sampleconfig/core.yaml /root/testnet/core.yaml
# cp /root/gopath/src/github.com/hyperledger/fabric/sampleconfig/orderer.yaml /root/testnet/orderer.yaml
# gedit /etc/profile
```
- 파일 하단에 다음과 같이 입력한다
```
export FABRIC_CFG_PATH=/root/testnet
```

- 저장 후 종료
```
# source /etc/profile
```

## 네트워크 구축 (Fabric-ca)

- hostname 및 hosts 파일 설정
```
# gedit /etc/hosts
```
-  hosts 설정
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
```
# cd $GOPATH/src/github.com/hyperledger
# git clone -b release-1.3 https://github.com/hyperledger/fabric-ca
# cd fabric-ca
# make fabric-ca-server
# make fabric-ca-client
```

- Fabric-ca 환경변수 설정(루트 가상 머신에서 실행)
```
# gedit /etc/profile
```
```
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric-ca/bin
```
```
# source /etc/profile
```



## Fabric-CA 서버 실행 및 Fabric-CA 서버 운영자 계정 생성

- fabric-ca 설정파일 저장 경로 설정(fabric-ca 노드에서 실행)
```
# gedit /etc/profile
```
```
export FABRIC_CA_SERVER_HOME=/root/testnet/
```
```
# source /etc/profile
```
```
# cd $FABRIC_CA_SERVER_HOME //설정한 경로로 이동하는지 확인
```

- Fabric-CA 서버 구동(fabric-ca 노드에서 실행)
- -b admin:adminpw Fabric-CA ID:PWD 지정
- --cfg.affiliations.allowremove 조직 추가/삭제 권한
- --cfg.identities.allowremove 사용자 추가/삭제 권한
- -d 서버 상세로그 표시
```
# fabric-ca-server start -b admin:adminpw --cfg.affiliations.allowremove --cfg.identities.allowremove -d
```


## Fabric-CA 서버 운영자 MSP

- Fabric-CA 클라이언트 설정파일 저장 경로(admin@ordererorg0 노드에서 실행)
```
# gedit /etc/profile
```
```
export FABRIC_CA_SERVER_CLIENT=/root/testnet/
```
```
# source /etc/profile

# cd $FABRIC_CA_CLIENT_HOME
```

- Fabric-CA 운영자 권한 획득(admin@ordererorg0 노드에서 실행)
```
# fabric-ca-client enroll -u http://admin:adminpw@10.0.1.100:7054
```

## 조직 생성 및 조직 운영자 MSP 생성

- 기본 설정 조직 확인 및 제거(admin@ordererorg0 노드에서 실행)
```
# fabric-ca-client affiliation list

# fabric-ca-client affiliation remove --force org1
# fabric-ca-client affiliation remove --force org2

# fabric-ca-client affiliation add org0
# fabric-ca-client affiliation add org1
# fabric-ca-client affiliation add ordererorg0

# fabric-ca-client affiliation list
```

### 운영자 노드에 Fabric-CA 인증서 다운로드

- 인증서를 저장할 디렉터리 생성(admin@org0 노드에 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org0/msp
```
- 인증서 저장(admin@org0 노드에 실행)
```
# fabric-ca-client getcacert -u http://10.0.1.100:7054 -M /root/testnet/crypto-config/peerOrganizations/org0/msp
```

- 인증서를 저장할 디렉터리 생성(admin@org1 노드에 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org1/msp
```
- 인증서 저장(admin@org1 노드에 실행)
```
# fabric-ca-client getcacert -u http://10.0.1.100:7054 -M /root/testnet/crypto-config/peerOrganizations/org1/msp
```


- 인증서를 저장할 디렉터리 생성(admin@ordererorg0 노드에 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/ordererorg0/msp
```
- 인증서 저장(admin@ordererorg0 노드에 실행)
```
# fabric-ca-client getcacert -u http://10.0.1.100:7054 -M /root/testnet/crypto-config/peerOrganizations/ordererorg0/msp
```

- cryptogen 예제의 스크립트 재사용을 위해 인증서명 변경
- cacerts 인증서를 이름변경(admin@org0 노드에 실행) 
```
# mv /root/testnet/crypto-config/peerOrganizations/org0/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/org0/msp/cacerts/ca.crt
```

- cacerts 인증서를 이름변경(admin@org1 노드에 실행) 
```
# mv /root/testnet/crypto-config/peerOrganizations/org1/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/org1/msp/cacerts/ca.crt
```

- cacerts 인증서를 이름변경(admin@ordererorg0 노드에 실행) 
```
# mv /root/testnet/crypto-config/peerOrganizations/ordererorg0/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/ordererorg0/msp/cacerts/ca.crt
```

- 조직 운영자 계정 생성을 위해 Fabric-CA 서버 운영자 노드의 fabric-ca-client-config.yaml(admin@ordererorg0 노드에 실행) 
```
# gedit /root/testnet/fabric-ca-client-config.yaml
```

- admin@ordererorg0의 /root/testnet/fabric-ca-client-config.yaml 파일 123~130번째 줄을 수정
```
id:
  name: Admin@org0
  type: client
  affiliation: org0
  maxenrollments: 0
  attributes:
    - name: hf.Registrar.Roles
      value: client,orderer,peer,user
    - name: hf.Registrar.DelegateRoles
      value: client,orderer,peer,user
    - name: hf.Registrar.Attributes
      value: "*"
    - name: hf.GenCRL
      value: true
    - name: hf.Revoker
      value: true
    - name: hf.AffiliationMgr
      value: true
    - name: hf.IntermediateCA
      value: true
    - name: role
      value: true
```

- Admin@org0 계정 생성(admin@ordererorg0 노드에서 실행)
```
# fabric-ca-client register --id.secret=org0password
```

- admin@ordererorg0의 /root/testnet/fabric-ca-client-config.yaml 파일 123~130번째 줄을 수정
```
id:
  name: Admin@org1
  type: client
  affiliation: org1
  maxenrollments: 0
  attributes:
    - name: hf.Registrar.Roles
      value: client,orderer,peer,user
    - name: hf.Registrar.DelegateRoles
      value: client,orderer,peer,user
    - name: hf.Registrar.Attributes
      value: "*"
    - name: hf.GenCRL
      value: true
    - name: hf.Revoker
      value: true
    - name: hf.AffiliationMgr
      value: true
    - name: hf.IntermediateCA
      value: true
    - name: role
      value: true
```

- Admin@org1 계정 생성(admin@ordererorg0 노드에서 실행)
```
# fabric-ca-client register --id.secret=org1password
```
- admin@ordererorg0의 /root/testnet/fabric-ca-client-config.yaml 파일 123~130번째 줄을 수정
```
id:
  name: Admin@ordererorg0
  type: client
  affiliation: ordererorg0
  maxenrollments: 0
  attributes:
    - name: hf.Registrar.Roles
      value: client,orderer,peer,user
    - name: hf.Registrar.DelegateRoles
      value: client,orderer,peer,user
    - name: hf.Registrar.Attributes
      value: "*"
    - name: hf.GenCRL
      value: true
    - name: hf.Revoker
      value: true
    - name: hf.AffiliationMgr
      value: true
    - name: hf.IntermediateCA
      value: true
    - name: role
      value: true
```

- Admin@org1 계정 생성(admin@ordererorg0 노드에서 실행)
```
# fabric-ca-client register --id.secret=ordererorg0password
```



- org0 조직 운영자 MSP 생성(admin@org0 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/
# fabric-ca-client enroll -u http://Admin@org0:org0password@10.0.1.100:7054 -H /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/
```

- 디지털 인증서/개인키 이름 변경(admin@org0 노드에서 실행)
```
# mv /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/cacerts/ca.crt
# mv /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/keystore/개인키 /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/keystore/server.key
```

- org1 조직 운영자 MSP 생성(admin@org1 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/
# fabric-ca-client enroll -u http://Admin@org1:org1password@10.0.1.100:7054 -H /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/
```

- 디지털 인증서/개인키 이름 변경(admin@org1 노드에서 실행)
```
# mv /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/cacerts/ca.crt
# mv /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/keystore/개인키 /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/keystore/server.key
```

- ordererorg0 조직 운영자 MSP 생성(admin@ordererorg0 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/
# fabric-ca-client enroll -u http://Admin@ordererorg0:ordererorg0password@10.0.1.100:7054 -H /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/
```

- 디지털 인증서/개인키 이름 변경(admin@ordererorg0 노드에서 실행)
```
# mv /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/cacerts/10-0-1-100-7054.pem /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/cacerts/ca.crt
# mv /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/keystore/개인키 /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/keystore/server.key
```

- admincerts 디렉터리 생성 후 공개키 파일 복사(admin@org0 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/admincerts
# cp /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/signcerts/cert.pem /root/testnet/crypto-config/peerOrganizations/org0/users/Admin@org0/msp/admincerts/Admin@org0-cert.pem
```

- admincerts 디렉터리 생성 후 공개키 파일 복사(admin@org1 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/admincerts
# cp /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/signcerts/cert.pem /root/testnet/crypto-config/peerOrganizations/org1/users/Admin@org1/msp/admincerts/Admin@org1-cert.pem
```

- admincerts 디렉터리 생성 후 공개키 파일 복사(admin@ordererorg0 노드에서 실행)
```
# mkdir -p /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/admincerts
# cp /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/signcerts/cert.pem /root/testnet/crypto-config/peerOrganizations/ordererorg0/users/Admin@ordererorg0/msp/admincerts/Admin@ordererorg0-cert.pem
```






