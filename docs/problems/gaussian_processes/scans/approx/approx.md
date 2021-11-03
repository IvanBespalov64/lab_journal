---
layout: page
title: Аппроксимация
permalink: problems/gaussian_processes/scans/approx
---

Использовал scipy.optimize.curve_fit

Чтобы сходилось лучше нужно использовать key p0 = []

Крайние точки выбиваются, т.к. амплитуда по краям больше, чем в середине. Можно откинуть правые и левые две точки.

Пример скрипта:

```python
rom scipy.optimize import curve_fit
import matplotlib.pyplot as plt

def func(x, a, b, c, d, e, f, g):
    return a * np.sin(b * x + c) + d * np.sin(e * x + f) + g


degree, energy = np.array([]), np.array([])
with open("ccccl.relaxscanact.dat", "r") as file:
    for line in file:
        deg, en = map(float, line.split())
        degree = np.append(degree, deg)
        energy = np.append(energy, en)
    
    xfine = np.linspace(degree.min(), degree.max(), 360)
    degree = degree[2:-2]
    energy = energy[2:-2]
    
		#p0 подобран для CCCCl
    popt, pcov = curve_fit(func, degree, energy, p0 = [-0.003, 0.05, -2.09, 0.0001, 2.31, -2.55, -578.67])
    
    plt.plot(degree, energy, '.')
    plt.plot(xfine, func(xfine, popt[0], popt[1], popt[2], popt[3], popt[4], popt[5], popt[6]))
    plt.show()
```

![ccccl_scipy](ccccl_scipy.png)

### Оказалось, что использовать scipy не очень удобно.

Было решено использовать линейную регрессию для многопеременной функции на R.

```R
library(ggplot2)
library(dplyr)
library(latex2exp)

X <- c(0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0, 70.0, 80.0, 90.0, 100.0, 110.0, 120.0, 130.0, 140.0, 150.0, 160.0, 170.0, 180.0, 190.0, 200.0, 210.0, 220.0, 230.0, 240.0, 250.0, 260.0, 270.0, 280.0, 290.0, 300.0, 310.0, 320.0, 330.0, 340.0, 350.0, 360.0)
Y <- c(-578.66423977, -578.66487175, -578.66634903, -578.66839944, -578.67055639, -578.67233366, -578.67333832, -578.67331535, -578.67232917, -578.67068289, -578.66891244, -578.667569, -578.66705619, -578.66748202, -578.66867255, -578.67024551, -578.67175431, -578.67282044, -578.67320023, -578.67282113, -578.67175821, -578.67024784, -578.66867911, -578.66749292, -578.66706328, -578.66758042, -578.66891456, -578.67067637, -578.67232218, -578.67331682, -578.6733297, -578.6723393, -578.67055564, -578.66839191, -578.66634192, -578.66487277, -578.66433893)


X <- X*pi/180 # To radians

df <- base::data.frame(X,Y) 

X1 <- cos(X)
X2 <- cos(2*X)
X3 <- cos(3*X)

model <- summary(lm(Y ~ X1 + X2 + X3)) # Linear regression 

f <- function(x){
  return(model$coefficients[1] + model$coefficients[2]*X1 + model$coefficients[3]*X2 + model$coefficients[4]*X3)
}

df <- df %>% mutate(A = f(X)) # Adding predicted values
ggplot(df, aes(x = X * 180 / pi, y = Y)) + 
  geom_point() + # Dots 
  geom_line(aes(x = X * 180 / pi, y = A)) + # Predicted
  labs(x = TeX("$\\theta, radians$"),
       y = TeX("$Energy, hartri$"),
       title = "CCCF torsion angle") + # labels
  scale_x_continuous(breaks = seq(from = 0, to = 360, by = 60),
                     expand = c(0, 0))
 
# Calculating RMSE
RSS <- c(crossprod(model$residuals))
MSE <- RSS/length(model$residuals)
RMSE <- sqrt(MSE)
RMSE
```

