# 第八章 新型数字带通调制技术 #

## 8-2 ##

设发送数字序列为 +1 -1 -1 -1 -1 -1 +1 ，试画出相应的 MSK 信号相位 [^1]
变化图。若码元 速率为1000Baud，载频为3000Hz，试画出此 MSK 信号的波形。

[^1]: 应该是附加相位吧。

$$
\begin{aligned}
	f_1 & = f_\mathrm{c} + \frac{\Delta f}{2}\\
	& = f_c + \frac{R_B}{4}\\
	& = 3250Hz\\
	f_{-1} & = f_\mathrm{c} - \frac{\Delta f}{2}\\
	& = 2750Hz\\
	e(t) & = \sum_{i = -\infty}^\infty \mathrm{rect}\left(\frac{t}{T_B} -
	i\right)\cos\omega_{a_i}t\\
	& = \sum_{i = -\infty}^\infty \chi_{a_i = 1}\mathrm{rect}
	\left(\frac{t}{T_B} - i\right)\cos\omega_1t + \sum_{i = -\infty}^\infty
	\chi_{a_i = -1}\mathrm{rect} \left(\frac{t}{T_B} -
	i\right)\cos\omega_{-1}t
\end{aligned}
$$

![MSK 相位](img/8_2_1.pdf "MSK 相位")

![MSK 波形](img/8_2_2.pdf "MSK 波形")

```{.octave}
%% in
tic;
clc;
clear;
close all;
pkg load signal;

%% process
A = [+1 -1 -1 -1 -1 -1 +1];
B = zeros(1, length(A));
for i = 1:length(A)
	B(i) = pi / 2 * sum(A(1:i));
end
B = [0 B];
pl1 = plot(0:length(A), B);
xlabel('$t/T_B$');
ylabel('$\theta(t)/\mathrm{rad}$')
hold on;
for i = 1:length(A) - 1
	plot([i i], [2 -8], ':');
end
print -dpdflatexstandalone '/tmp/8_2_1.tex';
system 'latexmk -cd -pvc- /tmp/8_2_1.tex';
system 'cp /tmp/8_2_1.pdf img/8_2_1.pdf';
figure;
f0 = 2750;
f1 = 3250;
fb = 1000;
tb = 1 / fb;
w1 = 2 * pi * f1;
w0 = 2 * pi * f0;
Phi = [pi 0 0 0 0 0 0];
fn_w = @(x) w1 * (x == 1) + w0 * (x == -1);
fn_Rect = @(t) rectpuls(t / tb + 1 / 2 - (1:length(A)));
fn_e = @(t) sum(fn_Rect(t) .* cos(fn_w(A) * t + Phi));
dt = tb / 100;
tm = tb * length(A);
X = 0:dt:tm;
Y = zeros(1, length(X));
for i = 1:length(X)
	Y(i) = fn_e(X(i));
end
pl2 = plot(X, Y);
hold on;
for i = 1:length(A) - 1
	plot([i * tb i * tb], [-1 1], ':');
end
xlabel('$t/\mathrm{s}$');
ylabel('$u(t)/\mathrm{V}$')
print -dpdflatexstandalone '/tmp/8_2_2.tex';
system 'latexmk -cd -pvc- /tmp/8_2_2.tex';
system 'cp /tmp/8_2_2.pdf img/8_2_2.pdf';

%% output
toc;
```

## 8-3 ##

设有一 MSK 信号，其码元速率为1000 Baud，分别用频率 $f_1$ 和 $f_0$
表示码元“1”和“0”。若 $f_1 = 1250Hz$ ，试确定 $f_0$ 的值，并画出三个码元“101”的波形。

$$
\begin{aligned}
	f_0 & = f_1 - \frac{R_B}{2}\\
	& = 750Hz
\end{aligned}
$$

![101](img/8_3.pdf "101")

```{.octave}
%% in
tic;
clc;
clear;
close all;
pkg load signal;

%% process
A = [1 0 1];
f0 = 750;
f1 = 1250;
fb = 1000;
tb = 1 / fb;
w1 = 2 * pi * f1;
w0 = 2 * pi * f0;
Phi = [pi 0 0];
fn_w = @(x) w1 * (x == 1) + w0 * (x == 0);
fn_Rect = @(t) rectpuls(t / tb + 1 / 2 - (1:length(A)));
fn_e = @(t) sum(fn_Rect(t) .* cos(fn_w(A) * t + Phi));
dt = tb / 100;
tm = tb * length(A);
X = 0:dt:tm;
Y = zeros(1, length(X));
for i = 1:length(X)
	Y(i) = fn_e(X(i));
end
pl2 = plot(X, Y);
hold on;
for i = 1:length(A) - 1
	plot([i * tb i * tb], [-1 1], ':');
end
xlabel('$t/\mathrm{s}$');
ylabel('$u(t)/\mathrm{V}$')
print -dpdflatexstandalone '/tmp/8_3.tex';
system 'latexmk -cd -pvc- /tmp/8_3.tex';
system 'cp /tmp/8_3.pdf img/8_3.pdf';

%% output
toc;
```

