---
layout:     post
title:      Homebrew 설치 및 삭제 스크립트
summary:    OS X 용 패키지 관리자 Homebrew 설치 및 삭제 스크립트
image: /public/images/homebrew.png
---

OS X Yosemite 업그레이드 이후에 [Homebrew](http://brew.sh/index_ko.html)가 동작하지 않아서 찾아보니 Yosemite로 업그레이드 되면서 [Ruby](https://www.ruby-lang.org/ko/) 버전이 1.8에서 2.0으로 버전이 올라갔는데 brew.rb 파일에서는 여전히 1.8을 참조하면서 생기는 문제였다.

해결책은 **/usr/local/Library/brew.rb** 파일을 다음과 같이 수정해주면 된다.

```ruby
#기존
#!/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -W0

#변경
#!/System/Library/Frameworks/Ruby.framework/Versions/Current/usr/bin/ruby -W0
```

위와 같이 했는데도 문제가 생기는 경우에는 깔끔하게 삭제 후 재설치하는 방법을 추천한다. 

**아래 방법을 사용하면 기존에 설치되어 있는 패키지들이 전부 다 삭제되니 유의!**

설치 스크립트는 홈페이지 메인에 있지만 삭제 스크립트는 쉽게 찾을수가 없다. 아래는 [Homebrew FAQ](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/FAQ.md#faq)에서 겨우 찾은 [Homebrew Uninstall 스크립트](https://gist.github.com/mxcl/1173223) ~~([설치는 마음대로지만 삭제는 아니란다...](https://mirror.enha.kr/wiki/%EC%84%B1%20%EC%A0%95%EC%B2%B4%EC%84%B1%EC%9D%84%20%EA%B9%A8%EB%8B%AC%EC%9D%80%20%EC%95%84%EC%9D%B4))~~

```bash
#!/bin/sh
# Just copy and paste the lines below (all at once, it won't work line by line!)
# MAKE SURE YOU ARE HAPPY WITH WHAT IT DOES FIRST! THERE IS NO WARRANTY!
 
function abort {
  echo "$1"
  exit 1
}
 
set -e
 
/usr/bin/which -s git || abort "brew install git first!"
test -d /usr/local/.git || abort "brew update first!"
 
cd `brew --prefix`
git checkout master
git ls-files -z | pbcopy
rm -rf Cellar
bin/brew prune
pbpaste | xargs -0 rm
rm -r Library/Homebrew Library/Aliases Library/Formula Library/Contributions 
test -d Library/LinkedKegs && rm -r Library/LinkedKegs
rmdir -p bin Library share/man/man1 2> /dev/null
rm -rf .git
rm -rf ~/Library/Caches/Homebrew
rm -rf ~/Library/Logs/Homebrew
rm -rf /Library/Caches/Homebrew
```

위 스크립트 실행 후 아래 설치 스크립트를 실행하면 재설치가 완료된다.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
