# kubernetes-toy-project

AWS 환경에서 쿠버네티스를 사용한 Kafka-Spark-S3 데이터 파이프라인 구성 후 클라이언트까지 구성해서 소규모 서비스를 제공하는 토이프로젝트.

#목표
AWS 서비스는 돈이없어서 사용못하니 EC2 에서 모든것을 구성하는것이 목표


1. EC2 인스턴스 생성

Amazon Linux 기본 옵션으로 생성
  서버스펙
  - Amazon Linux 2023 AMI
  - 가상화: hvm
  - ENA enabled: true
  - 루트 디바이스 유형: ebs
  - 1 vCPU
  - 1GiB 메모리

  그외 설정
  - rsa 키페어 생성 (ssh 로 접속목적)
  - 루트디바이스+4개의 EBS 디스크 (싱글 클러스터에서 고가용성 및 백업환경 구성)

2. 디스크 마운트
  - ssh 접속
 
  $ ssh -i "KEY-NAME.pem" ec2-user@ec2-111-111-111-111.compute-1.amazonaws.com

  - 디스크 구조 확인

    $ lsblk
    
    NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
    
    xvda      202:0    0   8G  0 disk
    
    ├─xvda1   202:1    0   8G  0 part /
    
    ├─xvda127 259:0    0   1M  0 part
    
    └─xvda128 259:1    0  10M  0 part /boot/efi
    
    **xvdb**      202:16   0   5G  0 disk
    
    **xvdc**      202:32   0   5G  0 disk
    
    **xvdd**      202:48   0   5G  0 disk
    
    **xvde**      202:64   0   5G  0 disk

  - 파일 시스템 생성

    $ sudo mkfs -t ext4 /dev/**xvdb**
    
    $ sudo mkfs -t ext4 /dev/**xvdc**
    
    $ sudo mkfs -t ext4 /dev/**xvdd**
    
    $ sudo mkfs -t ext4 /dev/**xvde**

  - 경로 생성

    $ sudo mkdir /data1
    
    $ sudo mkdir /data2
    
    $ sudo mkdir /data3
    
    $ sudo mkdir /data4

  - 마운트

    $ sudo mount /dev/xvdb /data1
    
    $ sudo mount /dev/xvdc /data2
    
    $ sudo mount /dev/xvdd /data3
    
    $ sudo mount /dev/xvde /data4

  - 디스크 마운트 확인

    $ df -h
    
      Filesystem      Size  Used Avail Use% Mounted on
    
      devtmpfs        4.0M     0  4.0M   0% /dev
    
      tmpfs           475M     0  475M   0% /dev/shm
    
      tmpfs           190M  2.9M  188M   2% /run
    
      /dev/xvda1      8.0G  1.6G  6.5G  19% /
    
      tmpfs           475M     0  475M   0% /tmp
    
      /dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
    
      tmpfs            95M     0   95M   0% /run/user/1000
    
      /dev/xvdb       4.9G   24K  4.6G   1% /data1
    
      /dev/xvdc       4.9G   24K  4.6G   1% /data2
    
      /dev/xvdd       4.9G   24K  4.6G   1% /data3
    
      /dev/xvde       4.9G   24K  4.6G   1% /data4
    









