---
layout: page
title: Mean function
permalink: problems/gaussian_processes/mean_func
usemathjax: true
---

По всей видимости входные данные при обобщении становтяся n-мерными - все нормально, в конеченом счете просто не для всех один и тот же $A * sin(B * x)$, а для каждого угла свой.

Сейчас смог сделать для мономерного случая с обновлением 2 коэффициентов, третий не выходит. Если $f(x) = Asin(B * x) + C$, то:
$$
\frac{\delta f}{\delta A} = sin(Bx) \\ \frac{\delta f}{\delta B} = A x\sin(Bx) \\ \frac{\delta f}{\delta C} = 1
$$

### Ссылка на репозиторий с кодом:

https://github.com/IvanBespalov64/gaussian_processes

Нашел ноутбук с возмыжными библиотеками:

https://github.com/yeahrmek/gp/blob/master/GaussianProcesses.ipynb
