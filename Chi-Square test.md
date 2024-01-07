## 卡方检验用法
```
m2=data.frame(rbind(c(762, 327), c(484, 239)))
dimnames(m2) <- list(gender = c("F", "M"),party = c("Democrat","Independent"))                 
Xsq <- chisq.test(m2)
Xsq$observed
```


### 1. 可以进行单样本卡方检验
- https://www.sohu.com/a/407711662_354986

### 2. 卡方检验要求
```
2.3 R×C列联表检验注意事项 
R×C列联表检验要求理论频数不宜太小，不宜有1/5以上格子的理论频数小于5，也不宜有一个理论频数小于1，否则有可能产生
偏性。如果出现理论频数不满足此要求，可考虑选择如下方法处理：
(1) 增加样本含量；
(2) 结合专业知识将该格所在行或列与别的行或列合并（例如将上面的A、B、O、AB变成A、B、其他）；
(3) Fisher确切概率法，借助软件实现。
```

### 3. 如果出现下面的提示

```
当卡方检验时出现错误
Warning message:
In chisq.test(a) : Chi-squared approximation may be incorrect
解决办法：
加入参数，相当于Fisher确切概率法
chisq.test(a, simulate.p.value = TRUE)
```
- https://stats.stackexchange.com/questions/81483/warning-in-r-chi-squared-approximation-may-be-incorrect

```
当fisher.test检验时出现错误
Error in fisher.test(df1) : 
  FEXACT[f3xact()] error: hash key 7e+10 > INT_MAX, kyy=273, it[i (= nco = 6)]= 0.
Rather set 'simulate.p.value=TRUE'
解决办法：
加入参数
fisher.test(a, simulate.p.value = TRUE)
```
