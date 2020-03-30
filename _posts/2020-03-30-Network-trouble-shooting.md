---
layout: post
title:  "Network trouble shooting"
date:   2020-03-30
author: Verustree
categories: Network
---

<strong>리눅스 환경에서 네트워크 설정이 안된다면 다음의 방법을 활용해보자.</strong><br>
우선 들어가기에 앞서...www.google.com으로 핑을 날렸을 때 잘 되면 통신 잘되는 거!<br>
핑이 안 날라간다면 밑에 있는 항목을 차근차근 따라해보자<br>

<p>
<h4>1. Cable 연결 여부 확인  -> LED불이 들어오면 확인 끝</h4>
</p><br>


<p>
<h4>2. ping localhost</h4>
	<li>ping 127.0.0.1  -> 안되면 NIC 교체 후 다시 테스트!!</li>
</p><br>

<p>
<h4>3. 동일 네트워크 통신 확인</h4>
우선 들어가기에 앞서 다음의 사항들을 먼저 확인해보자.<br>
	<li>ping [GATEWAY ADDRESS]</li><ul>
	<li>ex) 192.168.40.1  ->  안되면 네트워크 설정 파일 확인</li></ul>
	<li>VMware 환경일 경우 ethernet 장치 이름 확인!!</li><ul>
	<li>-> dmesg | grep eth  (시스템 로그 파일 확인)</li>
	<li>-> udev: renamed network interface eth0 to eth1  이렇게 나온다면</li></ul>
만약 이래도 안된다면..!<br>
	1) cd /etc/sysconfig/network-scripts
		<li>mv ifcfg-eth[x] ifcfg-eth[y]</li>
		<li>vi ifcfg-eth[y]</li>
		-> DEVICE=eth[x] -> [y]로 변경
	<li>service network restart</li><br>
	
  2) vi /etc/udev/rules.d/70-persistent-net.rules <br>
		-> SUBSYSTEM으로 시작하는 라인중, MAC 주소를 확인 후 쓰지 않는 SUBSYSTEM으로 시작하는 라인은 전부 삭제<br>
		-> SUBSYSTEM으로 시작하는 라인중, 마지막 NAME 부분을 원하는 번호로 설정<br>
		-> 변경 후 저장하고 나감 (:wq)  <br>
	<li>	system reboot해주기(shutdown -r now, init 6, reboot)	</li>
</p><br>

<p>
<h4>4. 외부 네트워크 통신 확인</h4>
	1) ping 8.8.8.8 -> 통신 안되면 라우팅 테이블 확인<br>
	2) linux : netstat -nr ,  Windows : route print -4, Cisco Router : sh ip route<br>
	3) linux : route add -net [목적지 네트워크 주소]/CIDR  gw  [gateway 주소]<br>
	4) Windows : route add [목적지 네트워크 주소] mask [subnetmask ] gateway 주소<br>
	5) cisco router : ip route [목적지 네트워크 주소] [subnetmask] [nexthop 주소]
</p><br>

<p>
<h4>5. 도메인 주소를 이용한 통신 확인</h4>
구글로 핑을 날렸는데 핑이 안간다면...
	1. linux 기준  -  /etc/resolv.conf -> 파일 내용 확인!<br>
			-> nameserver [DNS서버주소]<br>
			-> ex) nameserver 210.220.163.82  <br>
	2. 네트워크 설정파일(/etc/sysconfig/network-scripts/ifcfg-eth[x])<br>
	DNS1 옵션이 설정 잘 되어있는지 확인
</p>
