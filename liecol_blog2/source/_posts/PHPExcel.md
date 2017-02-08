---
title: PHPExcel的使用
date: 2016-12-20 20:50:00
tags: [php,phpexcel]
categories:
- php
---

下载文件
**PHPExcel.php(file)**
**PHPExcel(dir)**
```php
include 'PHPExcel.php';
include 'PHPExcel/Writer/Excel2007.php';
```
以thinkphp为例贴上一个可抄作业的代码(非tp可删减对应代码即可)：
其他单元格细节请自行搜索，没什么太大难度。
<!--more-->
```php
    //导出excel核心代码
    public function exportExcel($fileName,$expTitle,$expCellName,$expTableData){
        // vendor('PHPExcel.PHPExcel'); /-----Start
        vendor("PHPExcel.PHPExcel");
        vendor("PHPExcel.PHPExcel.Writer.Excel5");
        vendor("PHPExcel.PHPExcel.IOFactory.php");
        $PHPExcel = new \PHPExcel();    //-----End(这一块可以自行更改)
        $xlsTitle = iconv('utf-8', 'gb2312', $expTitle);//文件名称
//      $fileName = $_SESSION['loginAccount'].date('_YmdHis');//or $xlsTitle 文件名称可根据自己情况设定

        $cellNum = count($expCellName);
        $dataNum = count($expTableData);
        
        $objPHPExcel = $PHPExcel;
        $cellName = array('A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD','AE','AF','AG','AH','AI','AJ','AK','AL','AM','AN','AO','AP','AQ','AR','AS','AT','AU','AV','AW','AX','AY','AZ');
    
        if($cellNum >0){
            $objPHPExcel->getActiveSheet(0)->mergeCells('A1:'.$cellName[$cellNum-1].'1');//合并单元格
        }else{
            $objPHPExcel->getActiveSheet(0)->mergeCells('A1:'.$cellName[10].'1');//合并单元格
        }
        $objPHPExcel->setActiveSheetIndex(0)->setCellValue('A1', $expTitle.'  Export time:'.date('Y-m-d H:i:s'));
        for($i=0;$i<$cellNum;$i++){
            $objPHPExcel->setActiveSheetIndex(0)->setCellValue($cellName[$i].'2', $expCellName[$i][1]);
        }
        // Miscellaneous glyphs, UTF-8
        for($i=0;$i<$dataNum;$i++){
            for($j=0;$j<$cellNum;$j++){
                $XS_SJ = $expTableData[$i][$expCellName[$j][0]];
                $bar_code = (string)$expTableData[$i][$expCellName[0][0]];
                if((int)($cellNum-1)==$j && $bar_code){
                    $objDrawing = new \PHPExcel_Worksheet_Drawing();
                    $bar_path = "./Public/Bar_code/".$bar_code.".png";
                    $objDrawing->setPath($bar_path);
                    $objDrawing->setHeight(30);
                    $objDrawing->setCoordinates($cellName[$j].($i+3), ' '.$XS_SJ);
                    $objDrawing->setOffsetX(20);
                    $objDrawing->setRotation(12);
                    $objDrawing->getShadow()->setVisible(true);
                    $objDrawing->getShadow()->setDirection(45);
                    $objDrawing->setWorksheet($objPHPExcel->getActiveSheet());
                }
                //用' ' 处理数字为文本,以免excel中数字太大时科学计数法导致错误
                $objPHPExcel->getActiveSheet(0)->setCellValue($cellName[$j].($i+3), ' '.$XS_SJ);//控制不能显示科学计数数字。
                $objPHPExcel->getActiveSheet(0)->getColumnDimension($cellName[$j])->setWidth(20);
                $objPHPExcel->getActiveSheet()->getDefaultRowDimension($i)->setRowHeight(22);

            }
        }
    
        header('pragma:public');
        header("Expires: 0");
        header("Cache-Control:must-revalidate,post-check=0,pre-check=0");
        header("Content-Type:application/force-download");
        header("Content-Type:application/octet-stream");
        header("Content-Type:application/download");
        header('Content-type:application/vnd.ms-excel;charset=utf-8;name="'.$xlsTitle.'.xls"');
        header("Content-Disposition:attachment;filename=$fileName.xls");//attachment新窗口打印inline本窗口打印
        $objWriter = \PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel5');
        $objWriter->save('php://output');
        // $objPHPExcel = PHPExcel_IOFactory::load($fileName);
        exit;
    }
```
调用：
```php
	public function export_to_excel(){

        $expTitle = 'liecol快递' ; //设置文件名
        $fileName = 'liecol快递' . date('Ymd'); //设置文件名
        
        $expCellName  = array(
                array('tracking_number','分单号'),
                array('sender_name','发件人'),
                array('sender_tel','发件人电话'),
                array('sender_address','发件人地址'),
                array('sender_country','发件人国家'),
                array('recipients_name','收件人姓名'),
                array('recipients_address','收件人地址'),
                array('recipients_code','收件人邮编'),
                array('recipients_tel','收件人电话'),
                array('recipients_id','证件号'),
                array('update_time','订单日期'),
                array('bar_code','条形码')
        );
        
        $expTableData = D('Order')->field('tracking_number,sender_name,sender_tel,sender_address,sender_country,recipients_name,recipients_address,recipients_code,recipients_tel,recipients_id,update_time')->select();
        $this->exportExcel($fileName,$expTitle,$expCellName,$expTableData);

    }
```
create By 
liecol-晓斌 
2016-12-20