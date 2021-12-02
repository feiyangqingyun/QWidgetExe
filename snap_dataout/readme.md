## 一、注意事项
编译好以后记得将file目录下（切记是file目录下而不是file目录）的所有文件复制到可执行文件同一目录。
如果导出的excel文件双击打开以后会弹出警告提示，请先用管理员身份运行excel禁止提示.reg，只要运行一次即可。
- 简单示例用于演示最简单的参数设置导出数据到csv、xls、pdf和打印数据，直接传入的QTableView、QTableWidget控件。
- 导入导出用于演示导出导入csv格式的数据，数据源为数据库表，相当于将数据库表信息导出到csv文件，导入的csv文件会更新对应数据库数据。
- 初级示例用于演示最基础的直接从数据库数据导出到xls、pdf和打印数据，支持数据校验、随机背景、自动拉伸末尾列。
- 中级示例在初级示例上增加了子标题，同时校验规则改成了对数值类型进行过滤突出颜色显示。
- 高级示例用于演示分组导出数据，比如表中的某个设备的所有数据放一块，每个分组带有自定义信息内容用于展示统计的多条信息。
- 线程示例用于演示多线程导出数据到xls、pdf文件，可以指定行数列数，比如指定10个字段100W条数据，导出只用21s。
- 后期增加查询示例用于演示根据用户选择的查询条件对查询出来的记录进行导出到xls、pdf和打印数据。
- 图文混排用于演示如何自定义图文报表，导出到pdf及打印。

## 二、功能特点
1. 组件同时集成了导出数据到csv、xls、pdf和打印数据。
2. 所有操作全部提供静态方法无需new，数据和属性等各种参数设置采用结构体数据，极为方便。
3. 同时支持QTableView、QTableWidget、QStandardItemModel、QSqlTableModel等数据源。
4. 提供静态方法直接传入QTableView、QTableWidget控件，自动识别列名、列宽和数据内容。
5. 每组功能都提供单独的完整的示例，注释详细，非常适合各阶段Qter程序员。
6. 原创导出数据机制，不依赖任何office组件或者操作系统等第三方库，支持嵌入式linux。
7. 速度超快，9个字段10万行数据只需要2秒钟完成。
8. 只需要四个步骤即可开始急速导出海量数据比如100W条记录到Excel。
9. 同时提供直接写入数据接口和多线程写入数据接口，不卡主界面。
10. 可设置标题、副标题、表名。
11. 可设置导出数据的字段名、列名、列宽。
12. 可设置末尾列自动拉伸填充，默认拉伸更美观。
13. 可设置是否启用校验过滤数据，启用后符合规则的数据特殊颜色显示。
14. 可指定校验的列、校验规则、校验值、校验值数据类型。
15. 校验规则支持 精确等于==、大于>、大于等于>=、小于<、小于等于<=、不等于!=、包含contains。
16. 校验值数据类型支持 整型int、浮点型float、双精度型double，默认文本字符串类型。
17. 可设置随机背景颜色及需要随机背景色的列集合。
18. 支持分组输出数据，比如按照设备分组输出数据，方便查看。
19. 可设置csv分隔符、行内容分隔符、子内容分隔符。
20. 可设置边框宽度、自动填数据类型，默认自动数据类型开启。
21. 可设置是否开启数据单元格样式，默认不开启，不开启可以节约大概30%的文件体积。
22. 可设置横向排版、纸张边距等，比如导出到pdf以及打印数据。
23. 支持图文混排导出数据到pdf以及打印数据，自动分页。
24. 灵活性超高，可自由更改源码设置对齐方式、文字颜色、背景颜色等。
25. 支持任意excel表格软件，包括但不限于excel2003-2021、wps、openoffice等。
26. 纯Qt编写，支持任意Qt版本+任意编译器+任意系统。

## 三、使用方法
### 3.1 第一步：引入组件
- 组件中所有代码文件是一个整体，不支持单个代码文件拆分使用，因为很多通用的方法都放在一个代码文件中，复用很多代码。
- datehead是本组件用到的头文件以及通用的数据结构体。
- datahelper是通用的校验列函数和数据导出打印函数。
- datacreat用于创建html格式的数据，用来导出到pdf和打印数据。
- datacsv用于导入导出文件csv格式。
- dataxls是数据导出到xls的核心。
- dataprint是数据导出到pdf和打印数据。

1. 将core_dataout整个目录拷贝到你的项目文件夹同级目录。
2. 打开你的项目的pro文件，引入组件。
```cpp
INCLUDEPATH += $$PWD/../core_dataout
include ($$PWD/../core_dataout/core_dataout.pri)
```
3. 此时在项目树状结构图中可以看到 core_dataout 组件代码。

### 3.2 第二步：获取数据
- 无论是导出到xls还是pdf或者打印，前提是都要拿到需要处理的数据集合。
- 拿到数据的方式基本上两种，一种是数据库查询比如QSqlTableModel，一种是界面控件取数据比如QTableView、QTableWidget、QStandardItemModel。
- QTableView一般有两种数据源，一个是QSqlTableModel，一个是QStandardItemModel。
- 数据集合按照一行行数据来，中间用英文的 分号 ; 隔开，最后统一放到QStringList中。

#### 3.2.1 示例QSqlTableModel
```cpp
void frmDataOut2::on_btnLoad_clicked()
{
    model->setTable("MsgInfo");
    model->select();
    ui->tableView->setModel(model);
    for (int i = 0; i < columnCount; i++) {
        model->setHeaderData(i, Qt::Horizontal, columnNames.at(i));
        ui->tableView->setColumnWidth(i, columnWidths.at(i));
    }
}

QStringList frmDataOut2::getContent()
{
    QStringList content;
    QString sql = QString("select * from MsgInfo limit %1").arg(100);
    QSqlQuery query;
    if (!query.exec(sql)) {
        return content;
    }

    //循环遍历数据
    while (query.next()) {
        QStringList list;
        for (int i = 0; i < column; i++) {
            list << query.value(i).toString();
        }
        content << list.join(";");
    }
    return content;
}
```

#### 3.2.2 示例QStandardItemModel
```cpp
void frmSimple::on_btnLoad_clicked()
{
    //清空数据
    model->clear();    
    //设置列数及列标题和列宽
    model->setColumnCount(column);
    for (int i = 0; i < column; ++i) {
        model->setHeaderData(i, Qt::Horizontal, columnNames.at(i));
        ui->tableView->setColumnWidth(i, columnWidths.at(i));
    }

    //循环添加行数据
    for (int i = 0; i < row; ++i) {
        //循环添加一行的列
        QList<QStandardItem *> items;
        for (int j = 0; j < column; ++j) {
            QStandardItem *item = new QStandardItem;
            item->setText(QString("行%1_列%2").arg(i + 1).arg(j + 1));
            items << item;
        }
        model->appendRow(items);
    }
    ui->tableView->setModel(model);
}

QStringList frmSimple::getContent()
{
    //从 QTableView 获取数据
    QStringList content;
    for (int i = 0; i < row; ++i) {
        QStringList list;
        for (int j = 0; j < column; ++j) {
            list << model->item(i, j)->text();
        }
        //每行数据作为一个整体字符串分割存入
        content << list.join(";");
    }
    return content;
}
```

#### 3.2.3 示例QTableWidget
```cpp
void frmDataOut1::on_btnLoad_clicked()
{
    QStringList list;
    list << "防区上线" << "防区离线" << "防区旁路" << "防区报警" << "防区故障";

    ui->tableWidget->clearContents();
    for (int i = 0; i < rowCount; i++) {
        //随机生成告警内容
        int index = rand() % 4;
        QTableWidgetItem *item1 = new QTableWidgetItem(QString::number(i + 1));
        QTableWidgetItem *item2 = new QTableWidgetItem("防区" + QString::number(i + 1));
        QTableWidgetItem *item3 = new QTableWidgetItem("主机上报");
        QTableWidgetItem *item4 = new QTableWidgetItem(list.at(index));
        QTableWidgetItem *item5 = new QTableWidgetItem(DATETIME);
        item5->setTextAlignment(Qt::AlignCenter);

        ui->tableWidget->setItem(i, 0, item1);
        ui->tableWidget->setItem(i, 1, item2);
        ui->tableWidget->setItem(i, 2, item3);
        ui->tableWidget->setItem(i, 3, item4);
        ui->tableWidget->setItem(i, 4, item5);
    }
}

QStringList frmDataOut1::getContent()
{
    QStringList content;
    for (int i = 0; i < row; i++) {
        QStringList list;
        for (int j = 0; j < column; j++) {
            list << ui->tableWidget->item(i, j)->text();
        }
        content << list.join(";");
    }
    return content;
}
```

### 3.3 第三步：设置数据
- 无论是导出到xls还是pdf或者打印，都需要设置数据结构体，传入列名、列宽、数据集合等信息。
- 如果是导出到xls还需要设置文件名及表名。
- 如果是导出到pdf需要设置文件名。
- 打印不需要设置文件名和表名。

```cpp
void frmSimple::on_btnXls_clicked()
{
    //设置结构体数据
    DataContent dataContent;
    //填充内容
    dataContent.content = getContent();
    //设置列名列宽
    dataContent.columnNames = columnNames;
    dataContent.columnWidths = columnWidths;
    //设置文件名
    dataContent.fileName = "d:/0.xls";
    //设置表名
    dataContent.sheetName = "测试信息";

    //调用静态函数保存
    DataXls::saveXls(dataContent);
    //打开刚才导出的文件
    QUIHelper::openFile(dataContent.fileName, "导出测试信息");
}

void frmSimple::on_btnPdf_clicked()
{
    //设置结构体数据
    DataContent dataContent;
    //填充内容
    dataContent.content = getContent();
    //设置列名列宽
    dataContent.columnNames = columnNames;
    dataContent.columnWidths = columnWidths;
    //设置文件名
    dataContent.fileName = "d:/0.pdf";

    //调用静态函数保存
    DataPrint::savePdf(dataContent);
    //打开刚才导出的文件
    QUIHelper::openFile(dataContent.fileName, "导出测试信息");
}

void frmSimple::on_btnPrint_clicked()
{
    //设置结构体数据
    DataContent dataContent;
    //填充内容
    dataContent.content = getContent();
    //设置列名列宽
    dataContent.columnNames = columnNames;
    dataContent.columnWidths = columnWidths;

    //调用静态函数打印
    DataPrint::print(dataContent);
}
```

### 3.4 第四步：执行操作
- 数据导入导出组件dataout提供了统一的数据结构体用来设置数据和其他参数。
- 封装了统一的静态函数DataXls::saveXls用来导出到xls、DataPrint::savePdf用来导出到pdf、DataPrint::print用来打印数据。
- 支持各种详细复杂的参数设置以及海量数据导出多线程操作，具体参见详细使用demo。

```cpp
//调用静态函数保存到xls
DataXls::saveXls(dataContent);
//调用静态函数保存到pdf
DataPrint::savePdf(dataContent);
//调用静态函数打印数据
DataPrint::print(dataContent);
```

## 四、最简方法
为了方便入门级的Qter（Qt程序员）也能直接上手使用，比如很多时候已经把数据加载到了QTableView、QTableWIgdte中，只想直接传入这个控件及数据模型，就给我导出数据到csv/xls/pdf和打印数据就行，甚至可以不传入文件名称，弹出对话框让用户选择。立即安排。
```cpp
void frmSimple::on_btnCsv1_clicked()
{
    QString file = QUIHelper::appPath() + "/db/dataout_tableview.csv";
    DataHelper::DataOut(ui->tableView, model, 0, file, "测试标题", "测试信息");
    //打开刚才导出的文件
    QUIHelper::openFile(file, "导出测试信息");
}

void frmSimple::on_btnXls1_clicked()
{
    //通用函数直接传入控件
#if 1
    QString file = QUIHelper::appPath() + "/db/dataout_tableview.xls";
    DataHelper::DataOut(ui->tableView, model, 1, file, "测试标题", "测试信息");
#else
    //不传入文件名则会弹出对话框选择保存文件名
    QString file = DataHelper::DataOut(ui->tableView, model, 1);
#endif
    //打开刚才导出的文件
    QUIHelper::openFile(file, "导出测试信息");
}

void frmSimple::on_btnPdf1_clicked()
{
    //通用函数直接传入控件
    QString file = QUIHelper::appPath() + "/db/dataout_tableview.pdf";
    DataHelper::DataOut(ui->tableView, model, 2, file, "测试标题", "测试信息");
    //打开刚才导出的文件
    QUIHelper::openFile(file, "导出测试信息");
}

void frmSimple::on_btnPrint1_clicked()
{
    //通用函数直接传入控件
    DataHelper::DataOut(ui->tableView, model, 3, "", "测试标题");
}
```