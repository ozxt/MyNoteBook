切换frame：
```python
driver.switch_to.frame(reference)  #切换到指定frame
driver.switch_to.parent_frame()  #切换到上层frame
driver.switch_to.default_content()  #切换到主文档，最顶层。
```
切到子frame就不能访问上层frame的元素了，需要切回去。

---

执行js代码

`driver.execute_script("window.scrollTo(x,y)")  #js代码`

---

禁止加载图片
```python
firefox_profile = webdriver.FirefoxProfile()
#禁用加载图片
firefox_profile.set_preference('permissions.default.image', 2)  #某些firefox只需要这个
firefox_profile.set_preference('browser.migration.version', 9001)  #部分需要加上这个
#禁用css
firefox_profile.set_preference('permissions.default.stylesheet', 2)
#禁用flash
firefox_profile.set_preference('dom.ipc.plugins.enabled.libflashplayer.so', 'false')
#禁用js
firefox_profile.set_preference('javascript.enabled', 'false')

driver = webdriver.Firefox(firefox_profile=firefox_profile)
driver.get('https://re.jd.com/'）
```

---

截图

```python
from selenium import webdriver

driver = webdriver.Firefox()
driver.get('http://www.python.org/')
driver.save_screenshot('screenshot.png')
driver.quit()
```

---

隐式等待（等到某元素出现或到了设定的等待时间（设置一次，生命期内有效）），没找到时抛出超时异常。

```python
'''
An implicit wait tells WebDriver to poll the DOM for a certain amount of time when trying to find any element (or elements) not immediately available. The default setting is 0. Once set, the implicit wait is set for the life of the WebDriver object.
'''
from selenium import webdriver

driver = webdriver.Firefox()
driver.implicitly_wait(10) # seconds
driver.get("http://somedomain/url_that_delays_loading")
myDynamicElement = driver.find_element_by_id("myDynamicElement")
```

---

设置代理

```python
profile = webdriver.FirefoxProfile()
profile.set_preference("network.proxy.type", 1)
profile.set_preference("network.proxy.http", "proxy.server.address")
profile.set_preference("network.proxy.http_port", port_number)
profile.update_preferences()
browser= webdriver.Firefox(firefox_profile=profile)
```

---

显示等待

```python
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

WebDriverWait(webdriver, 60, 0.5).until(EC.presence_of_element_located((By.TAG_NAME, 'tbody')  ))
```

