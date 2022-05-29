#### nomogram参考网站
- https://zhuanlan.zhihu.com/p/67200975
- https://zhuanlan.zhihu.com/p/72570554
#### DCA决策曲线
- https://www.jianshu.com/p/5f3dda814cb8

## 导入数据，并且做前处理
示例数据
[test.csv](https://github.com/171909771/Statistics/files/8793007/test.csv)
dat4=read.csv("test.csv",row.names = 1)

## 执行logic回归,"."就是加入所有的自变量
### 逐步法的理解,用双向模式
- https://www.jianshu.com/p/5b921ed0d500
```r
fit1 <- glm(Group~., data = dat4,family = "binomial")
fit2 <- step(fit1,direction = "both")
#### 选P值有意义的进行后续分析
summary(fit2)
### 计算OR值，相当于是该因素存在，是多少倍发生
1/exp(coef(fit2))
```
## 绘图nomogram
```r
library(rms)
### 执行lrm需要执行下面2步
dd <- datadist(dat4)
options(datadist="dd")
f1 <- lrm(Group ~ PTGS2,dat4,x = TRUE,y = TRUE)
### 绘制logisitc回归的风险预测值的nomogram图
nom <- nomogram(f1, fun= function(x)1/(1+exp(-x)), # or fun=plogis
                fun.at=c(.001, .01, .05, seq(.1,.9, by=.1), .95, .99, .999), lp=F, funlabel="Risk")
plot(nom)
```

![1](https://user-images.githubusercontent.com/41554601/170861325-28cca281-b61a-428f-bdb2-233597d5164b.png)

## 绘制内部ROC曲线
```r
fit4 <- glm(Group ~ PTGS2,dat4,family = "binomial")
summary(fit4)

pre <- predict(fit4,type='response')
pre <- predict(fit4)
library(pROC)
plot.roc(dat4$Group, pre,
           main="ROC Curve", percent=TRUE,
           print.auc=TRUE,
           ci=TRUE, of="thresholds",
           thresholds="best",
           print.thres="best")
### 得到置信区间
rocplot1 <- roc(dat4$Group,pre)
ci.auc(rocplot1)
```
![2](https://user-images.githubusercontent.com/41554601/170861433-c64acc80-445a-438b-9d3c-032c0a021062.png)

## Calibration 校正曲线
### f1是lrm函数生成
```r
cal <- calibrate(f1,  method = "boot", B = 1000)
plot(cal, xlab = "Nomogram Predicted Survival", ylab = "Actual Survival",main = "Calibration Curve")
```
![3](https://user-images.githubusercontent.com/41554601/170861457-ac7376da-5038-4c45-8b11-967cca61ba40.png)


## 外部验证
```r
dat5=data.frame(Group=sample(0:1,20,replace=T),PTGS2=runif(20,8,12))
pre1 <- predict(fit4,newdata = dat5)
plot.roc(dat5$Group, pre1,
           main="ROC Curve", percent=TRUE,
           print.auc=TRUE,
           ci=TRUE, of="thresholds",
           thresholds="best",
           print.thres="best")
### Calibration 校正曲线
fit5 <- lrm(dat5$Group ~ pre1,x = TRUE,y = TRUE)
cal2 <- calibrate(fit5,  method = "boot", B = 1000)
plot(cal2, xlab = "Nomogram Predicted Survival", ylab = "Actual Survival",main = "Calibration Curve")
```

## DCA决策曲线
```r
library(ggDCA)
library(rms)
### f1是lrm函数生成
data  <- dca(f1)
ggplot(data)
```
#### 离这2根线越远的线是最佳的方法
![4](https://user-images.githubusercontent.com/41554601/170861467-581093e4-5ae4-4e05-a964-21342d91faad.png)
