# TSDK
淘宝系统SDK，用于爬虫


##  淘宝系列爬虫系列SDK
  
  - SDK更新到第二版，对一部分进行了优化
    * 优化掉通用类，此类本身最开始是为了能够使用无授权API，但是之后发现臃肿繁杂，还不如直接请求网址URL，觉得有些画蛇添足了，所以直接去掉
    * 优化开放平台类，繁杂的配置文件，多余的加载，我用了Node的SDK之后发现还不如Node的方便，只需要传入API名称和数据就行了，那么我弄了配置文件又有什么用，没有达到简单可用的目的遂改成Node一样的方式请求数据
    * 优化H5API类，经过我思考后觉得配置文件太过繁杂，且不够灵活也不多变，爬虫本身就是需要多变的，所以移除配置文件，通过传递参数直接构建，更加灵活方便


```python
  from TSDK.mTop import Client

  top = Client()
  #获取淘宝二维码，可以通过扫码登录淘宝
  res = top.login(timeout=40)
  print(res.text)

  #设置开放平台的appkey和密钥，然后传递API和配置可以直接获取数据
  top.open.config['appkey'] = ''
  top.open.config['appsecret'] = ''

  res = top.open.execute('taobao.tbk.item.get',{
        'fields':'num_iid,title,pict_url,small_images,reserve_price,zk_final_price,user_type,provcity,item_url,seller_id,volume,nick',
        'q':'女装',
        'cat':'16,18'
    })
  print(res.text)

  #获取宝贝详情
  res = top.H5.execute({
        'api':'mtop.taobao.detail.getdetail',
        'v':'6.0',
        'jsv':'2.4.8',
        'dataType':'json',
        'type':'json',
        'ttid':'2017%40taobao_h5_6.6.0',
        'AntiCreep':'true',
        'data':{
            'itemNumId':'585559878166'
        }
    })
  print(res.text)
```

### Cookie的同步

```python
  from requests.cookies import RequestsCookieJar

  dt = top.cookies.get_dict('.login.taobao.com')

  jar = RequestsCookieJar()
  for name in dt:
    jar.set(name,dt[name],path='/',domain='.login.taobao.com')
  
  # 通过设置cookie来共享登录状态
  top.cookies.update(jar)

```


###  淘宝APP端API

  此API设定还不完全，不过通过观察请求的方式可以发现加密放在了请求头里面，原本的链接上面没有的加密，不过也不一定，有的请求还是有携带加密参数的，不过之后的API设定应该会简单些，请求头的设置可以动态设置和计算，不过现在加密方式未解决，还未破解出来

