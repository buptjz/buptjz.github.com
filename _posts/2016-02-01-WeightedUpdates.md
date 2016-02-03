<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

<script type="text/x-mathjax-config">MathJax.Hub.Config({TeX: {equationNumbers:{autoNumber:"AMS"}}});</script>


### Weighted Marjority Algorithm

我们想要投资一个股票，为了简洁起见，我们认为股票只有涨和跌两个动作。
每天早上，我们要判断这个股票今天是涨还是跌，如果判断错了，我们损失1$。

现在我们手头上呢，有n个专家同志，我们将要介绍的这个算法呢，就是能够基本上做到和最好的专家的水平差不多。（难点在于：我们只有到最后才知道谁是最好的专家，而我们的预测确实一直持续的）

废话不多说，直接上算法：


---
**Weighted Marjority Algorithm**

初始化：固定一个\\(\epsilon \leq \frac{1}{2} \\).每一个专家i，给一个权重\\( \omega_i^{(1)}:=1 \\)

for t=1,2,...,T:

1. 根据专家预测，选出累加权重大的那个预测值作为最后的预测
2. 对于预测错误的专家i，降低它的权重：

\begin{equation}
\omega_i^{(t+1)} = \omega _i^{(t)}  (1-\epsilon)
\label{eq:omegaupdate}
\end{equation}

---

怎么能够说明这个算法是可行的呢？只要我们正明，这个算法犯错的机率和最好的专家的犯错几率差不了太多，就能说明这个算法还OK。所以我们希望正明一个不等式成立：

\begin{equation}
m^{(T)} \leq f(n) + bm_i^{(T)} 
\end{equation}

其中\\(m^{(T)}\\)是我们算法在T轮之后预测错误的次数，\\(m _i^{(T)}\\)是最好的专家（说是任意一个专家也行）在T轮之后预测错误的次数。

根据公式\eqref{eq:omegaupdate}，我们有\\(\omega_i^{(t+1)}=(1-\epsilon)^{ \omega _i^{(t)}}\\)。令 \\(\Phi^{(t)}=\sum _i \omega _i ^{(t)}\\). 有\\(\Phi^{(1)}=n\\).

我们每做出一个错误判断，说明至少有一半权重以上的专家也预测错了，所以一半以上权重会衰减，所以\\(\Phi^{(t)}\\)衰减了至少\\((1-\epsilon/2)\\):

\begin{equation}
\Phi^{(t+1)} \leq \Phi{(t)}\left(
\frac{1}{2}+\frac{1}{2}(1-\epsilon)
\right)=\Phi{(t)}(1-\epsilon/2)
\end{equation}

因此，我们可以递推出：

\begin{equation}
\Phi^{(t+1)} \leq n(1-\epsilon/2)^{m^{(T)}}
\label{eq:induction}
\end{equation}

又因为对于任意一个\\(i\\)而言，我们有

\begin{equation}
\Phi^{(T+1)} \geq \omega _i^{(T+1)}
\label{eq:obvious}
\end{equation}

根据公式\eqref{eq:induction}和公式\eqref{eq:obvious}，再加上\\(-\ln{(1-\epsilon)} \leq \epsilon + \epsilon^2 \\)  since \\(\epsilon \lt \frac{1}{2}\\)，就可以推导出：

\begin{equation}
m^{(T)} \leq \frac{2\ln{n}}{\epsilon} + 2(1+\epsilon)m _i^{(T)}.
\end{equation}