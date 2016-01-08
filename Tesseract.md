# Tesseract
开源的OCR库
## 安装
软件仓库里一般都有

### Mac
```
brew install tesseract
```
### CentOS
```
sudo yum install tesseract
```

## Python wrapper
目前用的这个

* [pytesseract](https://github.com/madmaze/pytesseract) 有个缺点是非ascii字符有可能出现UnicodeDecodeError的异常，因为它open输出结果的时候没有指定字符集，于是就跟着系统走用ascii了，我自己[fork](https://github.com/Windfarer/pytesseract)了一个，改好了自己用

## 添加语言数据
[官方仓库](https://github.com/tesseract-ocr/tessdata)中可以下载已经训练好的语言数据放入相应的目录中，并在运行命令时指定参数，如```-l chi_sim```即可

### Mac路径(由brew安装)

```
/usr/local/share/tessdata/
```

### CentOS下路径

```
/usr/share/tesseract/tessdata
```

## 模型训练
TODO
