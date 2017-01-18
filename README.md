#Qt_GeoShapes
## 功能  
使用Qt的QPolygonF类进行扩展，支持对地理多边形、地理圆形和地理矩形，共同的基类为MyQtGeoShapeBase。  
当从西向东运动的时候，经度值逐渐增大，直到东经180度后突变为-180度（西经用复数表示），然后再逐渐增长到-179度，-178度，以此类推。当一个空间区域跨东经180度的时候，需要特殊处理，判断覆盖范围。下图是地球上的经度划分：  

![经度](http://git.oschina.net/uploads/images/2017/0118/220228_c52e8930_854788.jpeg "经度划分")  

如下图所示，是一个横跨180度经度线的多边形区域，绿色点是海上的目标。通过提供的GeoShapes库，可以生成多边形空间对象，判断某个坐标点是否在该多边形内。  

![横跨国际日期变更线的多边形区域](http://git.oschina.net/uploads/images/2017/0118/215338_69f64e39_854788.png "多边形区域")  


## 基类接口  
```C++  
virtual bool containsPoint(QGeoCoordinate geoCoordinate, Qt::FillRule fillRule=Qt::OddEvenFill) =0;
qint32 getGeoShapObjectID() const;
void setGeoShapObjectID(const qint32 &value);  
```  
## 多边形类接口  
```C++  
// use x to represent longitude, y to represent latitude.
// Note the difference from QGeoCoordinate: first parameter is latitude, second parameter is longitude.
explicit MyQtGeoPolygon(QVector<QPointF> paramGeoPointsInDegreesBeforeTranslate, bool *ok,QObject *parent = 0);
virtual bool containsPoint(QGeoCoordinate geoCoordinate, Qt::FillRule fillRule=Qt::OddEvenFill) ;
QPolygonF getPolygonFTranslated() const;
QVector<QPointF> getGeoPointsInDegreesBeforeTranslate() const;
QVector<QPointF> getGeoPointsInDegreeAfterTranslate() const;
```  

## 矩形类接口  
```C++  
explicit MyQtGeoRectangle(const QGeoCoordinate &bottomLeft, const QGeoCoordinate &topRight, bool *ok, QObject *parent = 0);
virtual bool containsPoint(QGeoCoordinate geoCoordinate, Qt::FillRule fillRule=Qt::OddEvenFill) ;
QGeoRectangle getGeoRectangle() const;
void setGeoRectangle(const QGeoRectangle &value);
```  

## 圆形类接口  
```C++  
explicit MyQtGeoCircle(const QGeoCoordinate &center, qreal radiusInMeters,bool *ok,QObject *parent = 0);
virtual bool containsPoint(QGeoCoordinate geoCoordinate, Qt::FillRule fillRule=Qt::OddEvenFill) ;
QGeoCircle getGeoCircle() const;
void setGeoCircle(const QGeoCircle &value);
```  

## 使用示例  
构造一个跨过东经180度线的矩形，然后判断(20,180)这一个点是否在区域内。
```C++
QGeoCoordinate coorBottomLeft(10,160); //第一个参数是纬度，第二个参数是经度
QGeoCoordinate coorUpperRight(40,-160);
MyQtGeoRectangle *myGeoRect=new MyQtGeoRectangle (coorBottomLeft,coorUpperRight,&ok);
qDebug()<<myGeoRect->getGeoRectangle().bottomLeft()<<myGeoRect->getGeoRectangle().bottomRight();
MyQtGeoShapeBase *myShape=myGeoRect;
QGeoCoordinate pFTestb(20,180);
qDebug()<<myShape->containsPoint(pFTestb);
```