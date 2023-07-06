# Object-oriented Programming

在软硬件环境日趋复杂多变的情况下，怎么使软件更好的维护和迭代成了重要的问题。传统的程序设计是一种`面向过程`的思想，这种思想把程序看作一个个函数，计算机按需调用这些函数来达到计算目的。每当出现一个新的需求时，便需要编写对应的函数，而新编写的函数往往不能复用现有的函数。

为了提高软件的重用性、灵活性和扩展性，便出现了`面向对象程序设计`。`面向对象`的核心概念是`类(class)`和`对象(object)`。`类`是由某种特定的`元数据`所组成的`内聚`的包。`对象`则指的是`类`（class）的实例。文字表述可能比较让人困惑，以苹果来做个形象的理解，苹果具有`颜色`、`质量`和`甜度`等属性，根据这些就可以建立一个`apple`类：

```java
public class Apple {
	String color;
  double weight;
  int sweet;
}
```

而具体到一个特定的苹果（比如一个红色、重200g、甜度5的苹果），也就是apple类的一个实例，就可以看作是一个`对象`。

这就是面向对象的基本思想，它把万事万物都抽象成不同的类，并根据属性示例化，程序中所有的行为都是对对象的操作。比如吃一口苹果、把苹果卖掉、榨苹果汁等。

## 重用性、灵活性和扩展性

2022年笔者曾为某奢侈品品牌中国区编写过一个销售/库存报表处理程序。程序一共会处理五种Excel表，分别为`当前周分地区销售||库存数据`（分中国大陆、中国香港、中国台湾3*2张表）、`当前周中国销售&&库存数据`、`过往周销售&&库存数据`，`去年同期数据`和`今年至今销售&&库存数据`。具体处理流程如下：

1. 将`当前周分地区销售数据`对应复制进`当前周中国销售&&库存数据`；
2. 将`当前周分地区库存数据`对应复制进`当前周中国销售&&库存数据`；
3. 将`去年同期数据`对应复制进`今年至今销售&&库存数据`；
4. 结合`当前周中国销售&&库存数据`、`过往周销售&&库存数据`计算数据并更新至`今年至今销售&&库存数据`。

笔者编写的第一版代码采用了面向过程思想，`main`流程代码如下：

```python
if __name__ == '__main__':
    # 打开Excel程序，默认设置：程序不可见，只打开不新建工作薄，屏幕更新关闭
    app = xw.App(visible=False, add_book=False)
    app.display_alerts = False
    app.screen_updating = False

    work_space = input("数据表文件名(默认work_space.xlsx):")
    if work_space == '':
        work_space = 'work_space.xlsx'
    book = app.books.open(work_space)

    week_num = input("当前周数:")
    sheet = book.sheets['DB_wk' + week_num]
    # 获取sku所在列
    titles = sheet.range('A9').expand('right').value
    sku_index = chr(titles.index('SKU') + ord('A'))
    print("sku_index:" + sku_index)

    rng = sheet.range(sku_index + '10').expand('down')
    rows = rng.rows.count
    week_sku_list = sheet.range(sku_index + '10' + ':' + sku_index + str(rows + 9)).value
    # print('sku_list:' + str(sku_list))

    print("开始处理stock...")
    stock_processor(sheet, week_sku_list)
    print("stock处理完成！")

    print("开始处理sales performance...")
    sales_performance_processor(sheet, week_sku_list)
    print("sales performance处理完成！")

    print("开始处理去年同期数据...")
    last_year_processor(sheet, week_sku_list, week_num)
    print("去年同期数据处理完成！")

    print("开始处理YTD...")
    ytd_processor(book, week_sku_list, week_num)
    print("YTD处理完成！")

    print("正在保存...")
    book.save()
    app.quit()
    input('保存成功！')

```

`xxx_processor(arg...)`方法主要是先根据`week_sku_list`顺序对数据排序，然后按`列`复制进指定的sheet。由于每张表的数据不同，笔者写了不同的processor。同时为了维护复制时Excel表`列`的对应关系，笔者手动维护了多个`mapper`。

这导致后续每次新增sku或者Excel结构变动，都得更新代码。

2023年笔者采用面向对象的思想重构了上述代码，重构后的`main`方法如下：

```python
import time

import service.process_excel as process_excel
from constants.base import INDEX_OF_QUIT, MAIN_VALID_OPTIONS

def print_options():
    print("\033[32m/****** Options ******/")
    print("1--Process excel")
    print("2--Add mall")
    print("3--Modify columns")
    print("4--Modify excel config")
    print("5--User guide")
    print("0--Exit")
    print("/*********************/\033[0m")


def process():
    process_excel.process()


def add_mall():
    print("\033[34m待开发\033[0m")
    pass


def modify_columns():
    print("\033[34m待开发\033[0m")
    pass


def modify_excel_config():
    print("\033[34m待开发\033[0m")
    pass

  
def print_user_guide():
    f = open("src/README.txt")
    user_guide = f.read()
    print(user_guide)
    f.close()
    input("\033[31mPress ENTER to continue:\033[0m")

    
def switch_choice(option_choice):
    match option_choice:
        case "1":
            process()
        case "2":
            add_mall()
        case "3":
            modify_columns()
        case "4":
            modify_excel_config()
        case "5":
            print_user_guide()

            
if __name__ == '__main__':
    while True:
        print_options()
        choice = input("\033[31mPress the number of option:\033[0m")
        if choice == INDEX_OF_QUIT:
            print("\033[33mHave a nice day!\033[0m")
            break
        elif choice not in MAIN_VALID_OPTIONS:
            print("\033[31mUnknown option!\033[0m")
        else:
            switch_choice(choice)
        time.sleep(0.5)
```

`\033[31m`、`\033[33m`等为调整控制台字体代码。

`process_excel.process()`中执行处理逻辑的代码如下：

```
def switch_choice(option_choice, app):
    match option_choice:
        case "1":
            process_all(app)
        case "2":
            process_stock_data(app, None, week)
        case "3":
            process_sales_performance(app, None, week)
        case "4":
            process_last_year(app, None, week)
        case "5":
            process_year_to_date(app, None, week)
```

## 面向对象的特征

### 封装

### 继承

### 多态

