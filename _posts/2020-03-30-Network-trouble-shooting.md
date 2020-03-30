---
layout: post
title:  "200330_Network trouble shooting"
date:   2020-03-30
author: Verustree
categories: Network
---

리눅스 환경에서 네트워크 설정이 안된다면 다음의 방법을 활용해보자.
Network Trouble Shooting !!!
ping www.google.com -> 잘되면 통신 잘되는거!!

<p>
<h4>1. Cable 연결 여부 확인!  -> LED불이 들어오면 확인 끝!</h4>
</p>

<p>
<h4>2. ping localhost</h4>
	<li>ping 127.0.0.1  -> 안되면 NIC 교체 후 다시 테스트!!</li>
</p>

<p>
<h4>3. 동일 네트워크 통신 확인!!</h4>
	<li>ping [GATEWAY ADDRESS]</li><ul>
	<li>ex) 192.168.40.1  ->  안되면 네트워크 설정 파일 확인!</li></ul>
	<li>VMware 환경일 경우 ethernet 장치 이름 확인!!</li><ul>
	<li>-> dmesg | grep eth  (시스템 로그 파일 확인)</li>
	<li>-> udev: renamed network interface eth0 to eth1  이렇게 나온다면!</li></ul>
	
	1) cd /etc/sysconfig/network-scripts
		mv ifcfg-eth[x] ifcfg-eth[y]
		vi ifcfg-eth[y]
		-> DEVICE=eth[x] -> [y]로 변경
	service network restart
	
	2) vi /etc/udev/rules.d/70-persistent-net.rules 
		-> SUBSYSTEM으로 시작하는 라인중에서 뒤에 MAC 주소를 확인 후 쓰지 않는 SUBSYSTEM으로 시작하는 라인은 전부 삭제
		-> SUBSYSTEM으로 시작하는 라인중에서 마지막 NAME 부분을 원하는 번호로 설정!
		-> 변경 후 저장하고 나감 (:wq)
		system reboot!!(shutdown -r now, init 6, reboot)	
</p>

<p>
<h4>4. 외부 네트워크 통신 확인!</h4>
	ping 8.8.8.8 -> 통신 안되면 라우팅 테이블 확인!!
	linux : netstat -nr ,  Windows : route print -4, Cisco Router : sh ip route
	linux : route add -net [목적지 네트워크 주소]/CIDR  gw  [gateway 주소]
	Windows : route add [목적지 네트워크 주소] mask [subnetmask ] gateway 주소
	cisco router : ip route [목적지 네트워크 주소] [subnetmask] [nexthop 주소]
</p>

<p>
<h4>5. 도메인 주소를 이용한 통신 확인!</h4>
	ping www.google.com -> 통신 안되면..!
	linux 기준  -  /etc/resolv.conf -> 파일 내용 확인!
			-> nameserver [DNS서버주소]
			-> ex) nameserver 210.220.163.82  
	네트워크 설정파일(/etc/sysconfig/network-scripts/ifcfg-eth[x])
	DNS1 옵션이 설정 잘 되어있는지 확인!!
</p>
