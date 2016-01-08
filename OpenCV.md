# OpenCV
计算机视觉库，装起来比较麻烦

## 安装
### OS X
```
brew tap homebrew/science
brew install opencv3
brew install opencv3 --with-contrib --with-python3
```
需要用python的需要根据自己python的环境来link一下，我这里是
```
ln -s /usr/local/opt/opencv3/lib/python3.5/site-packages/cv2.cpython-35m-darwin.so /usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5/lib/python3.5/cv2.cpython-35m-darwin.so
```

### CentOS
默默地自己build吧
按照[官方文档](http://docs.opencv.org/master/dd/dd5/tutorial_py_setup_in_fedora.html#gsc.tab=0)的步骤来


