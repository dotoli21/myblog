---
layout: post
title:  "Windows에 Ruby 설치하기"
date:   2015-01-19 13:36:00 +0900
categories: ruby
---
윈도우즈에 Ruby를 설치하는 것은, 사용자 PC 환경에 따라 쉬울 수도 있고 어려워질 수도 있다. 여러분의 PC 환경이 가급적 전자이길 바란다.

일단, Windows용 Ruby 설치파일을 [Ruby Installer](http://rubyinstaller.org/downloads/)에서 다운로드 받아 설치한다.

### Requirements

* Ruby for windows 1.9.3
* DevKit tdm32 4.5.2

편의를 위해 ruby를 E:\lang\ruby193에 설치하고, devkit을 E:\lang\devkit에 설치했다고 가정하겠다.

ruby와 devkit의 설치가 끝났다면, 명령 프롬프트를 열어서 devkit이 설치된 디렉토리로 이동한 뒤 `ruby dk.rb init`과 `ruby dk.rb.install` 순서대로 입력하여 설치된 ruby가 devkit을 바라보도록 설정한다.

```
E:\lang\devkit>ruby dk.rb init
[INFO] found RubyInstaller v1.9.3 at E:/lang/ruby193

Initialization complete! Please review and modify the auto-generated
'config.yml' file to ensure it contains the root directories to all
of the installed Rubies you want enhanced by the DevKit.

E:\lang\devkit>ruby dk.rb install
[INFO] Updating convenience notice gem override for 'E:/lang/ruby193'
[INFO] Installing 'E:/lang/ruby193/lib/ruby/site_ruby/devkit.rb'
```

그 다음 RubyGems를 업데이트 해 준다.

```
gem update
```

여기까지 아무 에러메시지 없이 완료되었다면 설치는 끝났다! 하지만, 아래와 같은 오류메시지를 만났다면 추가작업이 필요하다. 

```
E:\>gem update
Updating installed gems
Updating bigdecimal
Temporarily enhancing PATH to include DevKit...
Building native extensions.  This could take a while...
E:\lang\devkit\bin\rm.exe: *** Couldn't reserve space for cygwin's heap (0x60E90000 <0x2660000>) in child, Win32 error 0
E:\lang\devkit\bin\mkdir.exe: *** Couldn't reserve space for cygwin's heap (0x60E90000 <0x2690000>) in child, Win32 error 0
E:\lang\devkit\bin\install.exe: *** Couldn't reserve space for cygwin's heap (0x60E90000 <0x26F0000>) in child, Win32 error 0
```

이 오류는 devkit의 기반인 MinGW의 `msys-1.0.dll` 파일의 메모리 관련 오류인데 MinGW의 고질적인 문제라고 한다. DLL파일의 Rebase를 통해 해결할 수 있는데 이를 위해서는 [Windows용 Git](https://msysgit.github.io)의 설치가 필요하다. Git 자체가 필요하다기 보다는 같이 설치되는 Utility 중 하나를 활용하기 위함이다.

Git이 설치된 디렉토리의 bin 디렉토리를 보면 `rebase.exe` 라는 파일이 존재하는데, 이 파일로 devkit의 `msys-1.0.dll` 파일을 Rebase 할 수 있다. 편의상 Git이 E:\git에 설치되어 있다고 가정하겠다.

```
E:\git\bin\rebase.exe -b 0x64000000 E:\lang\devkit\bin\msys-1.0.dll
```

아무 내용없이 프롬프트가 떨어졌다면 성공적으로 DLL Rebase가 된 것이다. 이 후, 다시 `gem update`를 실행하여 설치를 마친다.

### 참고자료
* *Github RubyInstaller, 'oneclick/rubyinstaller', https://github.com/oneclick/rubyinstaller/wiki/Development-Kit#quick-start*
* *John Robbins, (2004), "Debugging Applications" (정보문화사, 2004), p.79-80*
