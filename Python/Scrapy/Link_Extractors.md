### Link Extractors
`from scrapy.linkextractors import LinkExtractor`
Link Extractors用于从网页（scrapy.http.Response objects）里提取链接并 *which will be eventually followed*.
Link Extractors用于scrapy的CrawlSpider类里，但也可以用在不是CrawlSpider的子类的spider里，因为这就只是用来提取链接的。
Link Extractors对象仅有一个方法 extract_links，该方法接受scrapy.http.Response，返回scrapy.link.Link对象的list

`class scrapy.linkextractors.lxmlhtml.LxmlLinkExtractor(allow=(), deny=(), allow_domains=(), deny_domains=(), deny_extensions=None, restrict_xpaths=(), restrict_css=(), tags=('a', 'area'), attrs=('href', ), canonicalize=False, unique=True, process_value=None, strip=True)`

- allow：正则表达式[列表]  据此匹配要获取的链接，没提供的话捕获所有链接。
- deny：正则表达式[列表]  匹配的链接不获取。没提供的话不排除。
- restrict_xpaths：XPath[列表]  通过Xpath定义了从response的哪些区域去提取链接。
- restrict_css ：CSS selector[列表]  通过Css selector定义了从response的哪些区域去提取链接。
- tags：定义从哪些标签去搜索链接，默认为‘a’，‘area’标签。
- attrs：提取指定属性内的链接。
- process_value：接受一个形如func(value)的函数，会对每一个提取到的链接使用该函数进行处理，返回处理结果，抛弃返回None。