### CrawlSpider

`class scrapy.spiders.CrawlSpider`

属性：

- rules `scrapy.spiders.Rule`对象的列表；Rule定义了爬取网页的行为规则。
- parse_start_url 用于解析start_urls返回的response。



#### Crawling rules

`class scrapy.spiders.Rule(link_extractor, callback=None, cb_kwargs=None, follow=None, process_links=None, process_request=None)`

- link_extractor --- *Link Extractor*对象（该对像定义了怎么提取链接，提取哪些链接）
- callback --- 解析response的回调函数
- cb_kwards --- 是个dict，包含了要传给回调函数的键值对参数
- follow --- 布尔值  指定了 `specifies if links should be followed from each response extracted with this rule. If callback is None follow defaults to True, otherwise it defaults to False.`  *根据Rule提取到链接，并自动发起Request。对于返回的response，当没有指定回调函数（此时follow为true）时，就再次应用rules继续提取链接;当有指定回调函数(此时follow为false)时，response就传给相应的回调函数。* 指定follow为true，会让提取到的匹配的链接继续发起新Request，为false时会将response传给回调函数  
- process_links ---
- process_request ---

```python
import scrapy
from scrapy.spiders import CrawlSpider, Rule
from scrapy.linkextractors import LinkExtractor

class MySpider(CrawlSpider):
    name = 'example.com'
    allowed_domains = ['example.com']
    start_urls = ['http://www.example.com']

    rules = (
        # Extract links matching 'category.php' (but not matching 'subsection.php')
        # and follow links from them (since no callback means follow=True by default).
        Rule(LinkExtractor(allow=('category\.php', ), deny=('subsection\.php', ))),

        # Extract links matching 'item.php' and parse them with the spider's method parse_item
        Rule(LinkExtractor(allow=('item\.php', )), callback='parse_item'),
    )

    def parse_item(self, response):
        self.logger.info('Hi, this is an item page! %s', response.url)
        item = scrapy.Item()
        item['id'] = response.xpath('//td[@id="item_id"]/text()').re(r'ID: (\d+)')
        item['name'] = response.xpath('//td[@id="item_name"]/text()').extract()
        item['description'] = response.xpath('//td[@id="item_description"]/text()').extract()
        return item

#解释：爬取 example.com，会收集目录category链接，和项目item 链接,并会解析item链接返回的response得到item信息。对于category链接得到的response会继续应用rules去提取category和item。

```

