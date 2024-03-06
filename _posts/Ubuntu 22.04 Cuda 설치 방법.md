
# 1. Nvidia Driver 설치

먼저 Nvidia Driver가 설치되어 있는지 확인
```shell
nvidia-smi
```

![[Pasted image 20240304210400.png]]
이런 화면이 나온다면 드라이버가 이미 설치되어 있는 것!
설치가 되어 있다면 CUDA 설치로 GOGO

위와 같이 화면이 나오지 않고 다른 글씨가 나온다면 nvidia 드라이버가 설치되어 있지 않다는 것.

그렇다면 내 환경에 설치 가능한 드라이버를 알아봅시다
```shell
ubuntu-drivers devices
```

![[Pasted image 20240304210656.png]]

**recoommended** 라고 되어 있는 드라이버를 까는 것을 추천해요
각자 환경에 맞는 드라이버를 설치해주세요

```shell
sudo apt install nvidia-driver-550
```

설치가 끝났으면 컴퓨터 리부트!!!
```shell
sudo reboot
```

컴퓨터가 켜지면 그래픽 드라이버가 잘 잡히는지 확인해봅시다.
```shell
nvidia-smi
```

# 2. CUDA와 CuDNN 설치
CUDA 설치 방법은 크게 2가지로 나뉠 수 있음
1. 컴퓨터 로컬에 설치하기: 이 경우에는 모든 사용자와 모든 conda 환경에서 동일한 CUDA를 사용하게 됩니다. 그렇기에 다른 CUDA 버젼을 사용해야 한다면 골치가 아플 수 있습니다.
2. conda 환경 위에 설치하기: 이 경우에는 각 환경에 다른 CUDA를 설치할 수 있다는 것이 매우 큰 장점. 그러나 일부 코드에서 호환이 안되는 경우가 종종 발생해요.

## 2-1. 컴퓨터 로컬에 설치하기

### CUDA 설치하기 

https://developer.nvidia.com/cuda-toolkit-archive 
위 링크에 들어가서 설치하고 싶은 CUDA 버전을 찾습니다. (대게 Pytorch 버젼을 확인하고 설치하는 것을 추천해요)

만약 CUDA 11.8을 설치하고 싶다면 아래와 같이 설정을 해줍시당. (우리 연구실 기준)
![[Pasted image 20240304211945.png]]
그럼 아래와 비슷한 명령어가 나옵니다.
```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.0-1_all.debsudo 

dpkg -i cuda-keyring_1.0-1_all.deb

sudo apt-get update

sudo apt-get install cuda-11-8
```

==이 때, 웹사이트에 나오는 마지막 명령어는 무조건 최신 CUDA가 설치되니, 
(왕중요!!!!!)자신이 설치하고 싶은 CUDA를 직접 설정해줍시다.== 

```shell
sudo apt-get install cuda-11-8
```

---
CUDA Path 설정하기 위해 bashrc 파일을 열어줍니다.
```shell
gedit ~/.bashrc
```

가장 하단에 다음과 같이 넣어줍니다. (자기 버젼에 맞게~~ 예시는 11.8입니다.)
```shell
export PATH=$PATH:/usr/local/cuda-11.8/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.8/lib64
export CUDADIR=/usr/local/cuda-11.8
```

### CuDNN 설치하기
이어서 CuDNN을 설치해봅시다.

https://developer.nvidia.com/rdp/cudnn-archive
위 링크에 들어가서 자신의 설치하고 싶은 CuDNN을 다운받아줍시다.
(자신이 ssh로 서버에 연결 중이라면 scp 명령어를 사용해서 옮겨줍니다.)

그 다음 압축 해제  (파일 이름은 눈치껏 바꿔줘)
```shell
tar -xvf cudnn-linux-x86_64-8.8.1.3_cuda11-archive.tar.xz
```

복붙복붙 (path는 알아서 잘 바꾸자^^, path 치는 도중에 계속 tab을 누르면 쉽다)
```shell
sudo cp cudnn-linux-x86_64-8.8.1.3_cuda11-archive/include/cudnn*.h /usr/local/cuda/include 

sudo cp cudnn-linux-x86_64-8.8.1.3_cuda11-archive/lib/libcudnn* /usr/local/cuda/lib64 

sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

```shell
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```
설치한 CuDNN 버젼이 나오면 끝~~~~

## 2-2 conda에 설치하기

가상환경을 만들고 아래 명령어와 같이 cuda와 cudnn 버젼만 맞게 설치하면 끝!
```shell
conda install -c conda-forge cudatoolkit=11.8.0 cudnn=8.9.2
```
(참 쉽고 간단해서 좋아용~)