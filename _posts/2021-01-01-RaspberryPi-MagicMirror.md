---
title: "[Raspberry Pi] Magic Mirror 만들기 - OS 설치"
categories: RaspberryPi, MagicMirror, SmarMirror 
---

놀고 있는 Raspberry Pi를 액자로 만들었던 과정을 기록 

## OS 설치
라즈베리파이를 사용하기 위해서는 OS 우선 설치 해야한다.
전용 툴(Pi Imager) 설치하고 실행 하면 매우 직관적인 UI를 통해 OS 설치가 가능하다.
- [Pi Imager 다운로드](https://www.raspberrypi.org/software/) 
 ![Pi Imager](/assets/images/Pi-Imager.png) 
 간단한 사용법 :
  1. CHOOSE OS -> Raspberry Pi 버전에 맞는 OS 선택
  2. CHOOSE SD CARD -> SD CARD 선택
  3. WRITE 


OS 설치 완료 후 SD CARD를 넣고 전원을 켜주면 된다. 

## OS 설정
- wifi 설정 ( 끊길 경우 )
  - Raspberry와 공유기의 locale 설정을 모두 US로 변경 
  - 전력관리로 wifi 끊기는 문제 수정 ( /etc/rc.local 수정 )
    ```
      # exit 0 윗 줄에 입력 
      # wlan0 : 무선 네트워크 이름 
      iwconfig wlan0 power off
    ```
- 업데이트 
  ```
  $sudo apt-get update
  $sudo apt-get upgrade
  ```
  
- 한글 폰트 설정
  ``` bash
    $sudo apt-get install fonts-unfonts-core
    $sudo apt-get install ibus ibus-hangul
  ```
  위 커맨드로 한글 설정이 되지 않음.. ibus 단축키 등등 설정 필요 ([참고 링크](https://kgu0724.tistory.com/159))
- Display rotate 설정 ( /boot/config.txt 파일 수정 )
  ```
  |  value | description          |
  |    0   |  회전 없음             |
  |    1   |  시계 방향으로 90도 회전  |
  |    2   |  시계 방향으로 180도 회전 |
  |    3   |  시계 방향으로 270도 회전 |
크
  display_rotate=3
  ```

