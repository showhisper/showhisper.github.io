---
layout: post
title: Google detection api 준비 #2 - object Detection API 다운로드
date: 2017-11-26 19:17:00 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: google_detection_2.png # Add image post (optional)
tags: [Google, detection, api, 준비]
---
# Google Detection api를 사용해보기로 마음을 먹었다.
## Google Detection api 설치
  >Google Detection api를 사용하기 위해서는
  * python 2.7 과 tensorflow이 설치 되어야 한다.
  * api에 필요한 라이브러리 설치
  * 드디어 object decetion api를 사용해서 잘돌아가는지 test 까지 해보자

#### 2. 파이썬 라이브러리 설치
{% highlight ruby %}
$pip install pillow
$pip install lxml
$pip install jupyter
$pip install matplotlib
{% endhighlight %}

#### 3. Object Detection API 다운 및 설치
{% highlight ruby %}
$git clone https://github.com/tensorflow/models
{% endhighlight %}

  * Protocol Buffer Compile
  다음 프로토콜 버퍼를 사용하기 위해서는 protoc로 proto파일을 컴파일 한다</br >
  Object Detection API를 설치한 디렉토리에서 models 디렉토리에 들어가 다음 명령어를 수행하자.
  {% highlight ruby %}
  # models/research/
  $protoc object_detection/protos/*.proto --python_out=.
  {% endhighlight %}

  * PATH 조정하기
  {% highlight ruby %}
  # models/research
  export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
  {% endhighlight %}

  * models
  {% highlight ruby %}
  #이거해서 잘돌아가면 성공!
  $python object_detection/builders/model_builder_test.py
  {% endhighlight %}

### 참고
- 조대협의 블로그 : http://bcho.tistory.com/1182
- Tensorflow Object Detection API home : https://github.com/tensorflow/models/tree/master/object_detection
- Install guide: https://github.com/tensorflow/models/blob/master/object_detection/g3doc/installation.md
- Quick start: https://github.com/tensorflow/models/blob/master/object_detection/object_detection_tutorial.ipynb
- Install to google cloud platform: https://cloud.google.com/solutions/creating-object-detection-application-tensorflow

[protocol 2.6]: https://github.com/google/protobuf/releases/tag/v2.6.1
