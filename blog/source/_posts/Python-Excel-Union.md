---
title: Python Excel Union
date: 2020-02-28 10:03:02
tags: [python tools]
description: Python Excel Union
---

#### 写在前面
由于受不了每次将一堆$excel$整合在一个$excel$中，所以就有了这篇文章

## 依赖库

```python
import xlrd
import xlsxwriter
```

$xlrd$是$python$中读取$exce$l数据的

$xlsxwriter$是$python$中向$excel$写入数据的

## 代码

```python
import os
import datetime
import xlrd
import xlsxwriter

# 遍历的文件目录
file_path = 'test'

# 合并后的excel存储目录
tar_file = file_path + '/mimxed.xlsx'


# 遍历目标目录下的所有文件名
def all_path(dirname):
    result = []
    # 当前主目录,当前主目录下的所有目录,当前主目录下的所有文件
    for maindir, subdir, file_name_list in os.walk(dirname):
        for filename in file_name_list:
            if filename.endswith('xls') or filename.endswith('xlsx'):
                apath = os.path.join(maindir, filename)  # 合并成一个完整路径
                result.append(apath)
    return result


# 获取excel文件的内容数据
def concat_and_insert(fdir):
    records = []
    if len(fdir) > 0:
        for dir in fdir:
            # 读取文件
            data = xlrd.open_workbook(dir)
            # 第一个sheet的名字
            first_sheet = data.sheet_by_index(0).name
            # print(dir, '>' * 10, first_sheet)
            # 获取sheet页的名称
            sheet = data.sheet_by_name(first_sheet)

            # 获取表的数目
            nrows = sheet.nrows
            for i in range(nrows):  # 参数可根据实际情况调整
                if i < 2:
                    continue
                if sheet.row_values(i)[2] == '':
                    continue
                records.append(sheet.row_values(i))
    return records


# 写入数据
def insert_file(alist, tarfile):
    # 新建目标文件
    wh = xlsxwriter.Workbook(tarfile)
    wadd = wh.add_worksheet('total')
    if len(alist) > 0:
        for row_num, row_data in enumerate(alist):
            wadd.write_row(row_num + 1, 0, row_data)
    wh.close()


def main():
    start = datetime.datetime.now()
    print(start)
    filename = all_path(file_path)
    # print(filename)

    # 获取数据
    records = concat_and_insert(filename)

    # 写入文件
    insert_file(records, tar_file)

    end = datetime.datetime.now()
    print(end)
    print("持续时间{}".format(end - start))
    print('ok')


main()
```

