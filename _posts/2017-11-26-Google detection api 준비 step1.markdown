---
layout: post
title: Google detection api 준비 #1 - protocol buffer 사용하기
date: 2017-11-26 15:35:00 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: google_detection_1.png # Add image post (optional)
tags: [Google, detection, api, 준비]
---
# Google Detection api를 사용해보기로 마음을 먹었다.
## Google Detection api 설치
  >Google Detection api를 사용하기 위해서는
  * python 2.7 과 tensorflow이 설치 되어야 한다.
  * google api를 사용하려면 protocol buffer라는 것을 이용해야 하는데, 오늘은 protocol buffer가 뭔지 살펴 보도록하자!

#### 1. Protocol Buffer 설치
Object detection api는 내부적으로 protocol buffer를 사용하기 때문에, 설치해주자.

 >프로토콜 버퍼란?
 - 구글에서 개발하고 오픈소스로 공개한 Serialized Data Structure(직렬화 데이터 구조)
 - C++, C#, Go, Java, Python, Object C, Javascrpit, Ruby 등을 지원한다.
<br />직렬화란 데이터를 파일로 저장하거나 네트워크로 전송하기 위하여 바이너리 스트림 형태로 저장하는 것
 - 하나의 파일에 최대 64M까지 지원
 - JSON 파일을 프로토콜 버퍼 파일 포맷으로 전환 가능하고 프로토콜 버퍼 파일을 JSON으로 전환가능하다.

* 직접 설치 하고 싶다면... [github.com/google/protobuf/tree/master/python]를 참고하자!
* 다른 버전도 [github.com/google/protobuf/releases] 요기있다.

# 후, 나중에 문제가 생기지 않게 버전을 2.6으로 받자
[protocol 2.6]에서 Source code 를 받은 후에 폴더로 가서
{% highlight ruby %}
$./configure
$make
$make check
$sudo make install
$which protoc
{% endhighlight %}

#### 설치가 잘 되었는지 확인해보려면
{% highlight ruby %}
protoc --version
{% endhighlight %}

### 이제 설치를 했으니 사용해 보자.
#### proto file(.proto)의 형태는 다음과 같다.
{% highlight ruby %}
syntax = "proto2";

package tutorial;

message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phones = 4;
}

message AddressBook {
  repeated Person people = 1;
}

{% endhighlight %}

#### protocol buffer compile 하기
1. 위의 내용을 addressbook.proto로 저장한다.
2. compiler를 실행하여 소스 디렉토리(소스 코드가 있는 위치, 값을 제공안하면 현재위치)
</br >, 대상 디렉토리(생성된 코드를 이동하려는 위치, 같은 위치면 `$SRC_DIR`)
</br >
{% highlight ruby %}
protoc -I=./ --python_out=./ ./addressbook.proto
{% endhighlight %}
  - -I뒤에는 proto file이 있는 소스 디렉토리
  - --python_out에는 생성된 파이썬 파일이 저장될 디렉토리
  - 마지막은 ptoto file 정의
3. 이렇게 compile하면 --python_out으로 지정된 디렉토리에 addressbook_pb2.py이 생성된다.

#### protocol buffer API 이용하기 step1 = write.py
{% highlight ruby %}
import addressbook_pb2

person = addressbook_pb2.Person()
person.id = 1234
person.name = "John Doe"
person.email = "jdoe@example.com"
phone = person.phones.add()
phone.number = "555-4321"
phone.type = addressbook_pb2.Person.HOME

try:
    f = open('myaddressbook', 'wb')
    f.write(person.SerializeToString())
    f.close()
    print 'file is written'
except IOError:
    print 'file creation error'

{% endhighlight %}

#### protocol buffer API 이용하기 step2 = read.py
{% highlight ruby %}
import addressbook_pb2

person = addressbook_pb2.Person()

try:
    f = open('myaddressbook','rb')
    person.ParseFromString(f.read())
    f.close()
    print person.id
    print person.name
    print person.email
    print person.phones

except IOError:
    print 'file read error'

{% endhighlight %}
#### data Structure
[protocol docs]에서 여러가지 확인 해봐야 함

#### 간단한 Tip! - JSON 변환
protocol buffer의 장점: JSON으로 변환이 가능하고 역도 가능!
{% highlight ruby %}
# read.py에 아래코드를 살짝 첨가해보자 ^^
from google.protobuf.json_format import MessageToJson
jsonObj = MessageToJson(person)
print jsonObj
{% endhighlight %}
### 참고
- https://developers.google.com/protocol-buffers/docs/pythontutorial
- http://bcho.tistory.com/1182

[protocol 2.6]: https://github.com/google/protobuf/releases/tag/v2.6.1
[github.com/google/protobuf/tree/master/python]: https://github.com/google/protobuf/tree/master/python
[github.com/google/protobuf/releases]: https://github.com/google/protobuf/releases
[protocol docs]: https://developers.google.com/protocol-buffers/docs/proto
