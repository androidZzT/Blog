---
title: 不定积分基本公式与推导
date: 2022-05-03 15:28:33
tags: 高数
categories: 考研
katex: true
---
不定积分基本公式与推导
---

##### 1
{% katex %}
\int\ a^x\,dx=\dfrac{a^x}{lna} + C,\quad ({a^x})' = a^x lna
{% endkatex %}


##### 2
{% katex %}
\int\tan x\,dx=\int\dfrac{sinx}{cosx}\,dx=-\int\dfrac{1}{cosx}\,d(cosx)=-\ln{|\cos{x}|} + C
{% endkatex %}

{% katex %}
\int\cot x\,dx=\int\dfrac{cosx}{sinx}\,dx=-\int\dfrac{1}{sinx}\,d(sinx)=\ln{|\sin{x}|} + C
{% endkatex %}

##### 3
{% katex %}
\int\dfrac{1}{cosx}\,dx =\int\sec{x}\,dx\\
=\int\dfrac{cosx}{(cosx)^2}\,dx
=\int\dfrac{cosx}{1-(sinx)^2}\,dx \\
=\int\dfrac{1}{1-(sinx)^2}\,d(sinx)=\int\dfrac{1}{(1+sinx)(1-sinx)}\,d(sinx) \\
=\dfrac{1}{2}[\int\dfrac{1}{1+sinx}\,d(sinx) - \int\dfrac{1}{1-sinx}\,d(sinx)] \\
=\dfrac{1}{2}ln|\dfrac{1+sinx}{1-sinx}| + C \\
=\dfrac{1}{2}ln|\dfrac{(1+sinx)^2}{(cosx)^2}|=ln|\dfrac{1+sinx}{cosx}| \\
=ln|secx + tanx| + C
{% endkatex %}

{% katex %}
\int\dfrac{1}{sinx}\,dx =\int\csc{x}\,dx \\
=\int\dfrac{sinx}{(sinx)^2}\,dx
=\int\dfrac{sinx}{1-(cosx)^2}\,dx \\
=-\int\dfrac{1}{1-(cosx)^2}\,d(cosx)=-\int\dfrac{1}{(1+cosx)(1-cosx)}\,d(cosx) \\
=-\dfrac{1}{2}[\int\dfrac{1}{1+cosx}\,d(cosx+1) - \int\dfrac{1}{1-cosx}\,d(1-cosx)] \\
=-\dfrac{1}{2}ln|\dfrac{1+cosx}{1-cosx}| \\
=-\dfrac{1}{2}ln\dfrac{(sinx)^2}{(1-cosx)^2} \\
=ln|\dfrac{1-cosx}{sinx}| \\
=ln|cscx + cotx| + C
{% endkatex %}

##### 4
{% katex %}
\int (secx)^2\,dx=tanx + C,\quad tanx'=(secx)^2
{% endkatex %}
{% katex %}
\int (cscx)^2\,dx=-cotx +C,\quad cotx'=-(cscx)^2
{% endkatex %}

##### 5
{% katex %}
\int secx \; tanx\, dx= \int \dfrac{sinx}{(cosx)^2}\,dx=\int-\dfrac{1}{(cosx)^2}\,d(cosx)=secx+C, \quad (secx)'=secx\;tanx
{% endkatex %}
{% katex %}
\int cscx \; cotx \,dx = \int\dfrac{cosx}{(sinx)^2}\,dx=\int\dfrac{1}{(sinx)^2}\,d(sinx)=-cscx + C, \quad (cscx)' = cscx - cotx
{% endkatex %}

##### 6

{% katex %}
\int\dfrac{1}{a^2 + x^2}\,dx=\dfrac{1}{a}arctan\dfrac{x}{a} + C
{% endkatex %}
{% katex %}
\int\dfrac{1}{\sqrt{a^2 - x^2}}\,dx=arcsin\dfrac{x}{a} + C
{% endkatex %}

##### 7
**三角函数换元**
{% katex %}
\int\dfrac{1}{\sqrt{x^2 - a^2}}\,dx \\
令\; x=asect, \quad dx=a\;sect \; tant \; dt \\
=\int \dfrac{1}{atant} \; asect \; tant\, dt \\
=\int sect\, dt \\
=ln|sect + tant| + C \\
回代 \; x sect=\dfrac{x}{a} ,\quad tant=\dfrac{\sqrt{x^2 - a^2}}{a} \\
=ln | x + \sqrt{x^2 - a^2}| - lna + C_1 \\
=ln | x + \sqrt{x^2 - a^2}| + C 
{% endkatex %}

{% katex %}
\int\dfrac{1}{\sqrt{x^2 + a^2}}\,dx \\
令\; x=atant, \quad dx=a\;(sect)^2 \; dt \\
=\int \dfrac{1}{asect} \; a(sect)^2, dt \\
=\int sect\, dt \\
=ln|sect + tant| + C  \\
回代 \; sect = \dfrac{\sqrt{x^2 + a^2}}{a} \; tant=\dfrac{x}{a} \\
=ln|x + \sqrt{x^2 + a^2}| + C 
{% endkatex %}

{% katex %}
\int\sqrt{a^2 - x^2}\, dx \\
令\; x=asint \quad dx=acostdt \\
=\int a^2 (cost)^2\, dt \\
=a^2 \int \dfrac{cos2t+1}{2}\, dt \\
=a^2(\dfrac{sin2t}{4}+\dfrac{t}{2})+ C \\
回代 \; sin2t=2sintcost=\dfrac{2x\sqrt{a^2-x^2}}{a^2} \quad t=arcsin\dfrac{x}{a} \\
=\dfrac{a^2}{2}arcsin\dfrac{x}{a} + \dfrac{x}{2}\sqrt{a^2 - x^2} + C
{% endkatex %}

##### 8
**平方差**
{% katex %}
\int \dfrac{1}{x^2-a^2}\, dx \\
=\int \dfrac{1}{(x+a)(x-a)}\, dx \\
=\dfrac{1}{2a}[\int \dfrac{1}{x+a}\,dx - \int \dfrac{1}{x-a}\, dx]\\
=\dfrac{1}{2a}ln\dfrac{x+a}{x-a} + C
{% endkatex %}

##### 9
**三角函数变换**
{% katex %}
\int (cosx)^2\, dx=\int \dfrac{cos2x+1}{2}\,dx=\dfrac{sin2x}{4}+\dfrac{x}{2}+ C
{% endkatex %}
{% katex %}
\int (sinx)^2\, dx=\int \dfrac{1-cos2x}{2}\, dx=\dfrac{x}{2} - \dfrac{sin2x}{4} + C
{% endkatex %}
{% katex %}
\int (tanx)^2\, dx=\int (secx)^2 - 1\, dx=tanx - x + C
{% endkatex %}
{% katex %}
\int (cotx)^2\, dx=\int (cscx)^2 - 1\, dx=-cotx - x + C
{% endkatex %}
