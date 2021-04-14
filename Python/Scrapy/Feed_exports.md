**Feed exports 将爬取到的Item按照一定格式序列化输出，一般用于提供（feed）给其它系统的输入。**
**scrapy的 Item exporters提供的格式有JSON,JSON Lines,CSV,XML**

---

#### Item Exporters

内置的Item  Exporters：BaseItemExporter（其他Exporter的抽象基类），XmlItemExporter，CsvItemExporter，PickleItemExporter，PprintItemExporter，JsonItemExporter，JsonLinesItemExporter。

##### 使用：

1. 先实例化Item Exporter对象export;
2. 调用  export.start_exporting()，会向exporting  process发送开始信号；
3. 调用export.export_item(item)；
4. 调用export.finish_exporting()给exporting process发送结束信号。

```python
from scrapy.exporters import XmlItemExporter

class PerYearXmlExportPipeline(object):
    """Distribute items across multiple XML files according to their 'year' field"""

    def open_spider(self, spider):
        self.year_to_exporter = {}

    def close_spider(self, spider):
        for exporter in self.year_to_exporter.values():
            exporter.finish_exporting()
            exporter.file.close()

    def _exporter_for_item(self, item):
        year = item['year']
        if year not in self.year_to_exporter:
            f = open('{}.xml'.format(year), 'wb')
            exporter = XmlItemExporter(f)
            exporter.start_exporting()
            self.year_to_exporter[year] = exporter
        return self.year_to_exporter[year]

    def process_item(self, item, spider):
        exporter = self._exporter_for_item(item)
        exporter.export_item(item)
        return item
```

##### BaseItemExporter方法属性



```
class scrapy.exporters.BaseItemExporter(fields_to_export=None, export_empty_fields=False, encoding='utf-8', indent=0)

export_item(item)

serialize_field(field, name, value) ： 用于按照item filed内指定的序列化器serializer处理filed，并返回结果。

indent：排版缩进空格数，默认为0
```

1，命令行参数方式：scrapy crawl命令时，通过-o，-t选项分别指定导出文件路径和导出数据格式。比如scrapy crawl spidername -o out.csv。文件名指定了后缀就不需要用-t明确指定格式了。
2，配置文件方式：FEED_URI 导出文件路径；FEED_FORMAT 导出数据格式；FEED_EXPORT_ENCODING 导出文件编码；FEED_EXPORT_FIELDS 指定要导出的字段及顺序；FEED_EXPORTERS 用户自定义的Exporter 字典{“指定格式”：用户自定义的Exporter类}；