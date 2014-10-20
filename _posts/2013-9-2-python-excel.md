---
layout: default
---

使用python**简单**的读写excel主要可以使用两个类库，一个是xlrd，一个是xlwt

xlrd
=====
https://pypi.python.org/pypi/xlrd

	import xlrd
	
	#open
	data = xlrd.oepn_workbook('xxx.xls')
	
	#查看有哪些sheet
	data.sheet_names()
	
	#获取sheet有三种方式
	sheet = data.sheets()[0]
	sheet = data.sheet_by_index(0)
	sheet = data.sheet_by_name(u'sheet0')
	
	#行 列
	rows_num = sheet.nrows
	cols_num = sheet.ncols
	
	#取值 整行 整列 
	sheet.row_values(index)
	sheet.col_values(index)
	
	#cell
	cell = sheet.cell(x, y).value
	cell = sheet.row(x)[y].value
	
	简单的写入
	row = 0
	col = 0
	ctype = 1 # 类型 0 empty,1 string, 2 number, 3 date, 4 boolean, 5 error
	value = 'hello'
	xf = 0 # 扩展的格式化 (默认是0)
	sheet.put_cell(row, col, ctype, value, xf)
	sheet.cell(0,0) # u'hello'
	sheet.cell(0,0).value # 'hello'
	

xlwt
=====
https://pypi.python.org/pypi/xlwt

可能容易遇到的问题是

* 写中文，用ms office打开后乱码
* 日期的读写
* 重复写cell报错

下面都给了解决的方法

	import xlwt
	
	#新建
	workbook = xlwt.Workbook(encoding = 'utf-8')
	sheet = workbook.add_sheet('sheet0')
	
	#写
	sheet.write(x,y,'test')
	
	#重复写同一个单元格会抛出一个Error
	#需要在打开sheet的时候加上一个参数
	sheet = workbook.add_sheet('sheet0',cell_overwrite_ok=True)
	
	#保存
	workbook.save('test.xls')
	
	#使用style
	style = xlwt.XFStyle()
	font = xlwt.Font()
	font.name = 'SimSun' #一般使用宋体，这样用MS office打开的时候就不会出现乱码
	style.font = font
	
	sheet.write(x, y, "你好啊", style)
	

在xlwt的源码中可以找到一个目录，下面放了一些例子

* dates.py 使用不同的日期格式
* hyperlinks.py 超链接
* merged.py 合并单元格
* row_styles.py 对一整行使用样式

下面是一个写日期的例子

	from datetime import datetime
	import xlwt
 
	wbk = xlwt.Workbook()
	sheet = wbk.add_sheet('sheet1')
 
	# style
	excel_date_fmt = 'M/D/YY h:mm'
	style = xlwt.XFStyle()
	style.num_format_str = excel_date_fmt
 
	now = datetime.datetime.now()
 
    # Write the data, using the style defined above.
    sheet.write(x,y,now, style)
     
	wbk.save('test.xls') 
