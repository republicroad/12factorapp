# Pytest的安装
```python
# 创建虚拟环境
mkdir .venv
# 激活虚拟环境，激活虚拟环境后，后续安装的包只会安装在该虚拟环境中。
.venv\Scripts\Activate.ps1
#在虚拟环境中执行安装pytest
pip install pytest
```

**<font style="color:rgb(64, 64, 64);">测试发现规则</font>**

<font style="color:rgb(64, 64, 64);">Pytest自动发现测试的规则：</font>

```plain
# 默认查找规则：
# 1. 文件名以 test_*.py 或 *_test.py 的文件
# 2. 类名以 Test 开头的类（不能有 __init__ 方法）
# 3. 函数名以 test_ 开头的函数
```

# 用例的筛选以及创建
```python
import pytest

def add(a, b):
    return a + b

# skip 无条件跳过
# skipif 有条件跳过
# xfail 与预期失败
# parametrize 参数化
# 用例筛选 pytest -m api
# pytest -m ut --reruns 5 这个命令可以指定实例跑 5 次

@pytest.mark.skip(reason="此用例暂时不执行")
@pytest.mark.api
def test_int():
    res = add(1, 3)
    assert res == 4

# 有条件跳过示例
@pytest.mark.skipif(condition=2 > 1, reason="当 2 > 1 时跳过此用例")
def test_skipif():
    assert True

# 预期失败示例
@pytest.mark.xfail
def test_xfail():
    assert 1 == 2

# 参数化示例
@pytest.mark.parametrize("a, b, expected", [(1, 2, 3), (4, 5, 9)])
def test_parametrize(a, b, expected):
    assert add(a, b) == expected

# 断言信息
def test_assert():
    # 比较序列
    assert [1, 2] == [1, 3]
    
    # 检查异常
    with pytest.raises(ValueError):
        int('a')
```

**<font style="color:rgb(0, 0, 0) !important;background-color:rgb(249, 250, 251);">解释</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">：</font>

+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">@pytest.mark.skip</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">用于无条件跳过测试用例，可以添加</font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">reason</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">参数说明跳过原因。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">@pytest.mark.skipif</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">根据条件判断是否跳过用例。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">@pytest.mark.xfail</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">标记用例为预期失败。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">@pytest.mark.parametrize</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> 用于参数化测试，可传入多组参数进行测试。</font>

```python
# 新建一个pytest.ini文件
# 执行pytest -m api(api可以修改成web之类的，可自行调整）
[pytest]
addopts = --alluredir=temps --clean-alluredir -m web --driver chrome
markers =
    api: 接口测试
    web: UI测试
    ut: 单元测试
    login: 登陆相关
    pay: 支付相关
    ddt: 数据驱动测试

log_file = ./logs/pytest.log
log_file_level = info
log_file_format = %(levelname)-8s %(asctime)s [%(name)s:%(lineno)s] : %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S

result_log_enable = 1
result_log_separator = 1
result_log_level_separator = warning
result_log_level_verbose = info
```

**<font style="color:rgb(0, 0, 0) !important;background-color:rgb(249, 250, 251);">解释</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">：</font>

+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">addopts</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">用于设置 Pytest 的命令行选项，如生成 Allure 报告、筛选测试用例等。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">markers</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">定义测试用例的标记，方便筛选。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">log_file</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">配置日志文件的路径。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">log_file_level</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">配置日志的级别。</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">其他配置项用于设置日志的格式和详细程度。</font>

# 夹具fixture
```python
import pytest
from datetime import datetime


@pytest.fixture(scope="session")
def ff():
    print('我也是fixture，但是也被使用')


@pytest.fixture(autouse=True, scope="session")
def f(ff):
    print(datetime.now(), '用例开始执行')
    # 前置操作
    yield 123  # 开始执行(只能写一个)
    # 后置操作
    print(datetime.now(), '用例执行完毕')
```

```python
# 夹具fixture（装饰器），添加yield关键字
# 在用例的参数列表中，加入fixture名字即可
# 夹具fixture方便复用
# 默认共享范围只有一个函数
# 在conftest.py文件里面定义的夹具全局共享


def test_1(f):
    print('收到fixture传递的是数据', f)


def test_2():
    pass

```

**<font style="color:rgb(0, 0, 0) !important;background-color:rgb(249, 250, 251);">解释</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">：</font>

+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">@pytest.fixture</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">用于定义夹具，</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">scope</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">参数指定夹具的共享范围，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">session</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">、</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">module</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">、</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">class</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">、</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">function</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">autouse=True</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">表示该夹具会自动应用到所有测试用例中。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">yield</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> 关键字用于分隔前置操作和后置操作。</font>

```python
scope参数选项：
function: 每个测试函数执行一次（默认）
class: 每个测试类执行一次
module: 每个模块执行一次
package: 每个包执行一次
session: 整个测试会话只执行一次
```

# pytest插件的使用
```python
# 安装pytest-html
pip install pytest-html -i https://pypi.tuna.tsinghua.edu.cn/simple
# 将测试结果输出为html格式
pytest --html=report.html --self-contained-html 
```

```python
pip install pytest-xdist
pytest -n 4  # 使用 4 个进程并行执行测试
pytest -n auto  # 自动检测并使用所有可用 CPU 核心
```

```python
 pip install pytest-logging
pytest-rerunfailures
```

# allure框架
**使用allure框架的时候，要先下载allure，编辑好环境变量**，再去安装allure-pytest，否则的话就会报错  


```python
pip install allure-pytest -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## allure的使用
```python
import allure
import pytest


@allure.epic('自动化测试') # 史诗（最大分组）
@allure.feature('框架测试') # 主题 模块
@allure.story('mark标记和筛选') # 故事 功能
@allure.title('实现筛选的用例') # 标题 用例
@pytest.mark.ut
def test_a():
    pass


@allure.epic('自动化测试') # 史诗（最大分组）
@allure.feature('框架测试') # 主题 模块
@allure.story('前置和后置') # 故事 功能
@allure.title('使用fixure的用例') # 标题 用例
@pytest.mark.ut
def test_b():
    pass
```

```python
[pytest]

addopts = --alluredir=temps --clean-alluredir -m ut
markers =
    api: 接口测试
    web: UI测试
    ut: 单元测试
    login: 登陆相关
    pay: 支付相关
    ddt: 数据驱动测试

log_file = ./logs/pytest.log
log_file_level = info
log_file_format = %(levelname)-8s %(asctime)s [%(name)s:%(lineno)s] : %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S

result_log_enable = 1
result_log_separator = 1
result_log_level_separator = warning
result_log_level_verbose = info
```

```python
import pytest
import os


pytest.main()

os.system("allure generate -o report -c temps")
```

**<font style="color:rgb(0, 0, 0) !important;background-color:rgb(249, 250, 251);">解释</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">：</font>

+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">pytest.main()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);">用于执行所有测试用例。</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">allure generate -o report -c temps</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> 用于生成 Allure 报告，</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">-o</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> 指定报告输出目录，</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">-c</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;background-color:rgb(249, 250, 251);"> 表示清除之前的报告。</font>

执行main.py之后，会生成logs，report，temps文件夹，在report文件夹中的index.html，就可以直观的看到测试的用例，以及执行结果

![](https://cdn.nlark.com/yuque/0/2025/png/39247472/1753089124090-95969c70-3c70-410e-9144-403e3cd14248.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39247472/1753088930937-72859707-da7b-4d6c-b046-e8a1b618a1c2.png)

