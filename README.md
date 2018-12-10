# java-captcha
    关于java-captcha项目，他是一个基于Java平台，滑动拼图解锁的一种验证，或者说一种解决方案。
    受限于自身研究。具体解锁时的因素可以有更多。目前只是简单的判断是否在范围正负内。有能力的，可以将滑动轨迹等传入后台进行判断。

## 何为验证码
    验证码，通常是为了识别操作的发起者，是人还是机器。目前现有的验证码有图片验证，即让你输入图片中的字符，中文等。
    也有12306那样的点击对应图片，以及选择图中对应汉字等。实现逻辑，各有千秋。但都是为了简单方便准确的区别开，判断是人还是机器。

## 滑动拼图验证码
    滑动拼图验证码是这两年流行起来的一种方式，验证码只需要，在滚动条上将拼图拖到对应位置，符合即可验证成功。不需要输入，不需要计算等。
    目前流行的有极验平台：https://www.geetest.com/Sensebot 有兴趣的可以关注下。
    关于拼图组成：我采用原始图片，拼图图片，水印图片组成。拼图图片为原始图片上一个区域内的一块，水印图片是指在原图上新增一块拼图图片对应的黑块，或者别的颜色的区域。

## 验证思路
    用户进入验证页面---->后台选择原始图片---->后台生成水印图片与拼图图片---->放置到缓存中并记录偏移量---->返回给前台
    ---->前台进行加载三张图片---->滑动验证码---->后台校验偏移量---->返回一个随机码给前台---->前台操作时带上返回的验证码---->完成校验

## 验证设计
    大体分为
    RestController:
        captcha: 选择并生成原始图片，拼图图片与水印图片，并存入缓存，在将缓存对应的key返回给前台。
        image: 前台根据后台返回的key，调用此接口从缓存中加载对应的图片
        check: 验证偏移量，同时返回成功与否，成功则带上一个随机码，并放入缓存中
    Service:
        captcha: 这里改成了Utils，作为一个工具类，方便别的地方直接引用，主要作用完成图片的生成
        auth: 缓存相关，以及验证随机码。
    
### 为什么采用缓存存储图片
    验证码生成的图片多小而多，且复用性低。缓存效率会高很多，同时易于清理。
### 为什么读取图片采用自己写的Resources去读取
    读取选择图片有很多种方式，这里可以自己去抽取封装下，方便改造成自己使用的。前后分离中，可以去直接调某些接口，甚至直接访问某些别的接口获取图片。
    同时在springboot的jar包部署中。只能精准去读取对应的图片。不能模糊读取对应目录。不然无法读取到内部路径。
### 为什么采用验证偏移量
    因个人实力和需求限定。只是简单的判断最终拼图所在的偏移量。这里其实可以扩展开，比如前台记录并传入后台，拼图在滑动过程中的轨迹，同时可以用上
    机器学习或人工智能来判断滑动的轨迹，是人为，还是机器所为。(人移到过超中理论上会上下有所偏移)