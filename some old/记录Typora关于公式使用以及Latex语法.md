# 记录Typora关于公式部分内容

## 设置
typora行内公式默认是没有打开的，如果需要使用需要手动进行开启。
文化 -> 偏好设置 -> 内联公式
![FHVCxP.png](https://s2.ax1x.com/2019/01/06/FHVCxP.png)

## 简单使用
1. 公式块
    两侧分别使用两个`$$`进行包裹。![FHVkqS.md.png](https://s2.ax1x.com/2019/01/06/FHVkqS.md.png)
2. 行内公式
    两侧分别使用两个`$`进行包裹，注意需要首先确保已经开启内联公式功能。
    ![FHVZ5j.png](https://s2.ax1x.com/2019/01/06/FHVZ5j.png)

## 公式语法（Tex指令的使用，支持LaTax）

### 函数、符号及特殊字符

#### 声调

| 语法         | 效果                                                         | 语法         | 效果                                                         | 语法           | 效果                                                         |
| ------------ | ------------------------------------------------------------ | ------------ | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| \bar{x}      | ![\bar{x}](http://upload.wikimedia.org/wikipedia/zh/math/c/9/4/c947a5bcc06592f37f6b1c4f2ed57dea.png) | \acute{\eta} | ![\acute{\eta}](http://upload.wikimedia.org/wikipedia/zh/math/8/f/f/8ff055145396a2ffefacea0b18ec3fda.png) | \check{\alpha} | ![\check{\alpha}](http://upload.wikimedia.org/wikipedia/zh/math/a/c/4/ac4a31ee5874dc5044590eedae7a48c4.png) |
| \grave{\eta} | ![\grave{\eta}](http://upload.wikimedia.org/wikipedia/zh/math/9/1/f/91f1cf98709ee5bd4380b05d04ce260a.png) | \breve{a}    | ![\breve{a}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/5/58565c69fa6e895ea11e57ffdbe7d4cd.png) | \ddot{y}       | ![\ddot{y}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/7/58779ff5e3932c43545a8d8114384dd6.png) |
| \dot{x}      | ![\dot{x}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/4/584bdd6bbf3b22901631c94c12f09332.png) | \hat{\alpha} | ![\hat{\alpha}](http://upload.wikimedia.org/wikipedia/zh/math/3/f/e/3fe9b211473065676e1b8048e21b9743.png) | \tilde{\iota}  | ![\tilde{\iota}](http://upload.wikimedia.org/wikipedia/zh/math/5/d/b/5db129ecac03423d19e453d9ada8a0e8.png) |

#### 函数

| 语法                                                         | 效果                                                         | 语法                  | 效果                                                         | 语法                  | 效果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------- | ------------------------------------------------------------ | --------------------- | ------------------------------------------------------------ |
| \sin\theta                                                   | ![\sin\!\theta](http://upload.wikimedia.org/wikipedia/zh/math/0/9/3/09370ac60fb75f6555aad6ce6fa51284.png) | \cos\theta            | ![\cos\!\theta](http://upload.wikimedia.org/wikipedia/zh/math/3/9/2/3922f2c5f59efde904a8e70433e3c014.png) | \tan\theta            | ![\tan\!\theta](http://upload.wikimedia.org/wikipedia/zh/math/8/9/e/89eb6b1450d9b26d2071d1f64a567aa4.png) |
| \arcsin\frac{L}{r}                                           | ![\arcsin\frac{L}{r}](http://upload.wikimedia.org/wikipedia/zh/math/5/9/c/59c47fbda47ed6da13841e777d49dd4e.png) | \arccos\frac{T}{r}    | ![\arccos\frac{T}{r}](http://upload.wikimedia.org/wikipedia/zh/math/7/9/b/79bed5fd8aa7405d98f639dd1dfbb427.png) | \arctan\frac{L}{T}    | ![\arctan\frac{L}{T}](http://upload.wikimedia.org/wikipedia/zh/math/9/7/1/97143846d4543215119d569364979c2a.png) |
| \sinh g                                                      | ![\sinh\!g](http://upload.wikimedia.org/wikipedia/zh/math/6/6/2/662b4f90832dea02dcee532c226df3bf.png) | \cosh h               | ![\cosh\!h](http://upload.wikimedia.org/wikipedia/zh/math/0/b/9/0b95b10be34e368fea4697a333f14c5d.png) | \tanh i               | ![\tanh\!i](http://upload.wikimedia.org/wikipedia/zh/math/d/7/7/d779acc41d8d9e1b708644807d2b61a1.png) |
| \operatorname{sh}j                                           | ![\operatorname{sh}j](http://upload.wikimedia.org/wikipedia/zh/math/d/9/a/d9a5b98974c7360119bf868fc1c5c544.png) | \operatorname{argsh}k | ![\operatorname{argsh}k](http://upload.wikimedia.org/wikipedia/zh/math/c/4/4/c44c117b43a78dba43cbd0d47f9ae9e7.png) | \operatorname{ch}h    | ![\operatorname{ch}h](http://upload.wikimedia.org/wikipedia/zh/math/9/b/e/9becb1baaf3ac62c35e5ecd33c689cee.png) |
| \operatorname{argch}l                                        | ![\operatorname{argch}l](http://upload.wikimedia.org/wikipedia/zh/math/e/1/3/e13362fecb2c9c535e6370bed2355f20.png) | \operatorname{th}i    | ![\operatorname{th}i](http://upload.wikimedia.org/wikipedia/zh/math/8/1/4/8146bd5925b931e7ffe878c83762a945.png) | \operatorname{argth}m | ![\operatorname{argth}m](http://upload.wikimedia.org/wikipedia/zh/math/5/5/3/5536490854b0c2592644c44455cfd43a.png) |
| k'(x)=\lim_{\Delta x\to 0}\frac{k(x)-k(x-\Delta x)}{\Deltax} | ![k'(x)=\lim_{\Delta x\to0}\!\frac{k(x)-k(x-\Delta x)}{\Delta x}](http://upload.wikimedia.org/wikipedia/zh/math/a/3/5/a35afcec7be6f2e0e49964f3997eeac6.png) | \limsup S             | ![\limsup S](http://upload.wikimedia.org/wikipedia/zh/math/c/3/a/c3ac735451511bdf8de1d5da58206a8a.png) | \liminf I             | ![\liminf I](http://upload.wikimedia.org/wikipedia/zh/math/f/8/5/f8508b1afb27fe30fd5be91984938df4.png) |
| \max H                                                       | ![\max\!H](http://upload.wikimedia.org/wikipedia/zh/math/9/9/c/99ced9d4b2ceaa766c3c5d68bac86979.png) | \min L                | ![\min\!L](http://upload.wikimedia.org/wikipedia/zh/math/0/7/d/07ddfdd950e34d4440b5b67678ad8bfa.png) | \inf s                | ![\inf s](http://upload.wikimedia.org/wikipedia/zh/math/7/4/8/7487ba38d7669bd75d98d64352f0e180.png) |
| \sup t                                                       | ![\sup t](http://upload.wikimedia.org/wikipedia/zh/math/6/5/3/653854c1aad8cfa165b5a8c39b5fb969.png) | \exp\!t               | ![\exp\!t](http://upload.wikimedia.org/wikipedia/zh/math/c/8/e/c8e52278a8bc8c608afa285f7c4c28df.png) | \ln X                 | ![\ln\!X](http://upload.wikimedia.org/wikipedia/zh/math/9/f/6/9f6784ddb3f4b9555612f1de74098116.png) |
| \lg X                                                        | ![\lg\!X](http://upload.wikimedia.org/wikipedia/zh/math/3/2/6/3267adf708693ca43b8fc4dace597be0.png) | \log X                | ![\log\!X](http://upload.wikimedia.org/wikipedia/zh/math/1/6/d/16d14ffacc5feb0b4f30de46aea931bf.png) | \log_\alpha X         | ![\log_\alpha\!X](http://upload.wikimedia.org/wikipedia/zh/math/b/b/6/bb6db979b913638a0d0f30a4ac32bd42.png) |
| \ker x                                                       | ![\ker x](http://upload.wikimedia.org/wikipedia/zh/math/c/a/0/ca051c4eda4d55ca99bd3b918bd7ead5.png) | \deg x                | ![\deg\!x](http://upload.wikimedia.org/wikipedia/zh/math/f/b/5/fb5d0a3b0f1fec9639c04e92b252fee0.png) | \gcd(T,U,V,W,X)       | ![\!\gcd(T,U,V,W,X)](http://upload.wikimedia.org/wikipedia/zh/math/e/d/8/ed810992e880412f54389f581b3e4f3c.png) |
| \Pr x                                                        | ![\Pr x](http://upload.wikimedia.org/wikipedia/zh/math/1/f/1/1f1c9533b66b3af1a2540bfa41ca9dff.png) | \det x                | ![\det\!x](http://upload.wikimedia.org/wikipedia/zh/math/f/0/1/f011b7de0181e313c4e36293419bc4da.png) | \hom x                | ![\hom x](http://upload.wikimedia.org/wikipedia/zh/math/9/1/9/919603d21a66d6909953cf095d8ce8c7.png) |
| \arg x                                                       | ![\arg x](http://upload.wikimedia.org/wikipedia/zh/math/4/2/8/428ef2e44e64d5de3773f591de3d5e06.png) | \dim x                | ![\dim x](http://upload.wikimedia.org/wikipedia/zh/math/f/1/b/f1b92c71934d2d660f68777740560aa9.png) | \lim_{t\to n}T        | ![\lim_{t\to n}T](http://upload.wikimedia.org/wikipedia/zh/math/7/a/b/7ab3a88998975fc6c02f395eb7a677cc.png) |

#### 同余

| 语法     | 效果                                                         | 语法      | 效果                                                         |
| -------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ |
| \pmod{m} | ![\pmod{m}](http://upload.wikimedia.org/wikipedia/zh/math/1/6/8/1680ea379fd6694b28ae04a34e77d8e6.png) | a \bmod b | ![a \bmod b](http://upload.wikimedia.org/wikipedia/zh/math/5/4/e/54efbc7c212108a4011e84c69cdd91f8.png) |

#### 微分

| 语法   | 效果                                                         | 语法       | 效果                                                         | 语法        | 效果                                                         |
| ------ | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| \nabla | ![\nabla](http://upload.wikimedia.org/wikipedia/zh/math/f/e/3/fe3a83e41074834731743ab803cd4936.png) | \partial x | ![\partial x](http://upload.wikimedia.org/wikipedia/zh/math/2/8/d/28ddb3e82115d069796faf6356e2dbf6.png) | \mathrm{d}x | ![\mathrm{d}x\ ](http://upload.wikimedia.org/wikipedia/zh/math/3/d/7/3d7524d2b2372a1188d73b20b9ba4b31.png) |
| \dot x | ![\dot x](http://upload.wikimedia.org/wikipedia/zh/math/3/a/b/3abf2ac2fcc14362b32b0411d43cbf48.png) | \ddot y    | ![\ddot y](http://upload.wikimedia.org/wikipedia/zh/math/0/d/2/0d23f3a6391ae79fac3f4b30ec914a84.png) |             |                                                              |

#### 集合

| 语法      | 效果                                                         | 语法        | 效果                                                         | 语法      | 效果                                                         | 语法      | 效果                                                         | 语法        | 效果                                                         |
| --------- | ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| \forall   | ![\forall](http://upload.wikimedia.org/wikipedia/zh/math/d/4/d/d4d49bead125261b226eaa867bd016ce.png) | \exists     | ![\exists](http://upload.wikimedia.org/wikipedia/zh/math/9/3/e/93ebe8636e1f8d60004fe33d1321674e.png) | \empty    | ![\empty](http://upload.wikimedia.org/wikipedia/zh/math/4/d/f/4df085f70a97244c977b6ff20b1952b4.png) | \emptyset | ![\emptyset](http://upload.wikimedia.org/wikipedia/zh/math/4/d/f/4df085f70a97244c977b6ff20b1952b4.png) | \varnothing | ![\varnothing](http://upload.wikimedia.org/wikipedia/zh/math/d/0/9/d096fc15d57854ec89d746709b02e52e.png) |
| \in       | ![\in](http://upload.wikimedia.org/wikipedia/zh/math/8/c/2/8c20c78b364ed5dbadd49e5b997aa1cc.png) | \ni         | ![\ni](http://upload.wikimedia.org/wikipedia/zh/math/7/c/3/7c34bf8e7b41952d024ef2180330b308.png) | \not\in   | ![\not\in](http://upload.wikimedia.org/wikipedia/zh/math/5/2/5/525f6840d78cdc4bcb177f06c7964022.png) | \notin    | ![\notin](http://upload.wikimedia.org/wikipedia/zh/math/6/9/2/692614f72e231db0d2313050ac29e113.png) | \subset     | ![\subset](http://upload.wikimedia.org/wikipedia/zh/math/7/a/f/7afa88c2877d79e6a8a190b360edfcd6.png) |
| \subseteq | ![\subseteq](http://upload.wikimedia.org/wikipedia/zh/math/a/0/7/a07903a0504f50f27e2a85c27e47fbd5.png) | \supset     | ![\supset](http://upload.wikimedia.org/wikipedia/zh/math/0/f/2/0f2c04f82a1eb8e3e371366214579f5b.png) | \supseteq | ![\supseteq](http://upload.wikimedia.org/wikipedia/zh/math/5/5/f/55fef34436f901c3e488cbaa41050df8.png) | \cap      | ![\cap](http://upload.wikimedia.org/wikipedia/zh/math/e/7/8/e78f632038354aae583f795a73d4e6b8.png) | \bigcap     | ![\bigcap](http://upload.wikimedia.org/wikipedia/zh/math/d/d/f/ddf86464dcc2190258c76ddc1da7032a.png) |
| \cup      | ![\cup](http://upload.wikimedia.org/wikipedia/zh/math/4/3/2/432c1df69e11aba7c5c5070e7578609f.png) | \bigcup     | ![\bigcup](http://upload.wikimedia.org/wikipedia/zh/math/8/d/5/8d50523fd64ca129ae09c7ae73e1385f.png) | \biguplus | ![\biguplus](http://upload.wikimedia.org/wikipedia/zh/math/9/0/2/902effd4844ae7dbd71f9f3cecec25f0.png) | \sqsubset | ![\sqsubset](http://upload.wikimedia.org/wikipedia/zh/math/f/2/2/f22614b2c39be672bfda11afddd15c18.png) | \sqsubseteq | ![\sqsubseteq](http://upload.wikimedia.org/wikipedia/zh/math/b/1/b/b1be6c28d38e18390ea808e56c4e1d93.png) |
| \sqsupset | ![\sqsupset](http://upload.wikimedia.org/wikipedia/zh/math/f/e/4/fe47401f278fa0892559bad5b9373ebe.png) | \sqsupseteq | ![\sqsupseteq](http://upload.wikimedia.org/wikipedia/zh/math/0/9/a/09a87c18c45c26e284e6160bc344ebe9.png) | \sqcap    | ![\sqcap](http://upload.wikimedia.org/wikipedia/zh/math/5/9/5/59553260bed5e399db651cf2a3f703cf.png) | \sqcup    | ![\sqcup](http://upload.wikimedia.org/wikipedia/zh/math/2/d/1/2d130618c4d29b0f1f52f248ff359341.png) | \bigsqcup   | ![\bigsqcup](http://upload.wikimedia.org/wikipedia/zh/math/b/f/6/bf60615860b12ad8b31cc27645fc5224.png) |

#### 逻辑

| 语法          | 效果                                                         | 语法   | 效果                                                         | 语法      | 效果                                                         | 语法           | 效果                                                         |
| ------------- | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| p             | ![p](http://upload.wikimedia.org/wikipedia/zh/math/8/3/8/83878c91171338902e0fe0fb97a8c47a.png) | \land  | ![\land](http://upload.wikimedia.org/wikipedia/zh/math/9/c/a/9cae4437756a15b8e44ec23e07fb1f65.png) | \wedge    | ![\wedge](http://upload.wikimedia.org/wikipedia/zh/math/1/b/a/1ba4f06f68614e5da79a8ebd378d532a.png) | \bigwedge      | ![\bigwedge](http://upload.wikimedia.org/wikipedia/zh/math/6/c/1/6c1357f344d79b14bbd6a43c112f36db.png) |
| \bar{q} \to p | ![\pagecolor{White} \bar{q} \to p](http://upload.wikimedia.org/wikipedia/zh/math/6/c/c/6cc8a80363dc8031c53a0f4ae4704c91.png) | \lor   | ![\lor](http://upload.wikimedia.org/wikipedia/zh/math/5/a/d/5addb134385e47a2efa484f6306e75a1.png) | \vee      | ![\vee](http://upload.wikimedia.org/wikipedia/zh/math/7/2/7/727ea4c8c49862411edae46adf506e3e.png) | \bigvee        | ![\bigvee](http://upload.wikimedia.org/wikipedia/zh/math/a/f/2/af2beebc69c64f6a9a59db61113e14a0.png) |
| \lnot         | ![\lnot](http://upload.wikimedia.org/wikipedia/zh/math/8/e/f/8efca960b209402104b448a5ad9486a8.png) | \neg q | ![\pagecolor{White} \neg q](http://upload.wikimedia.org/wikipedia/zh/math/d/8/a/d8a41edda094745ef7850fe97ceaa5ca.png) | \setminus | ![\setminus](http://upload.wikimedia.org/wikipedia/zh/math/1/7/d/17d233e7f25e7e6108e6e8135fc5bf65.png) | \smallsetminus | ![\pagecolor{White} \smallsetminus](http://upload.wikimedia.org/wikipedia/zh/math/7/2/9/7290ff3fe4f3ef8927caf56dc3c2d131.png) |

#### 根号

| 语法     | 效果                                                         | 语法        | 效果                                                         |
| -------- | ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| \sqrt{3} | ![\sqrt{3}](http://upload.wikimedia.org/wikipedia/zh/math/1/7/c/17c68e294ba0dc1356295bac633bf868.png) | \sqrt[n]{3} | ![\pagecolor{White}\sqrt[n]{3}](http://upload.wikimedia.org/wikipedia/zh/math/f/7/7/f77bdd840c6d77e25f5c88640bf0ffbf.png) |

#### 关系符号

| 语法                                                         | 效果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `\Delta ABC\sim\Delta XYZ`                                   | ![\Delta ABC\sim\Delta XYZ\!](http://upload.wikimedia.org/wikipedia/zh/math/6/a/3/6a3e77cae79f0fdcc1727b5061411221.png) |
| `\sqrt{3}\approx1.732050808\ldots`                           | ![\sqrt{3}\approx1.732050808\ldots](http://upload.wikimedia.org/wikipedia/zh/math/d/a/7/da73a49927110231f6021c6adb08a43d.png) |
| \simeq                                                       | ![\simeq](http://upload.wikimedia.org/wikipedia/zh/math/8/e/3/8e3648e8286c414a11b8f10a833d5596.png) |
| \cong                                                        | ![\cong](http://upload.wikimedia.org/wikipedia/zh/math/f/1/6/f16a7bd80bb5648a755eb58221d03546.png) |
| \dot=                                                        | ![\dot=](http://upload.wikimedia.org/wikipedia/zh/math/e/0/1/e011dac23072797e8fc73cf575709782.png) |
| `\ggg`                                                       | ![\ggg](http://upload.wikimedia.org/wikipedia/zh/math/f/b/7/fb7d1cf9a5e65f98089b6b2cb1570a3c.png) |
| `\gg`                                                        | ![\gg](http://upload.wikimedia.org/wikipedia/zh/math/3/3/e/33e3502dfbd3f875dcbdd2f9a40eedb4.png) |
| `>`                                                          | ![>\,](http://upload.wikimedia.org/wikipedia/zh/math/1/1/4/114529aa81593e4dfb2279a9b594c779.png) |
| `\ge`                                                        | ![\ge](http://upload.wikimedia.org/wikipedia/zh/math/8/f/b/8fbe2a506fe3db0835548e1b648ec977.png) |
| `\geqq`                                                      | ![\geqq](http://upload.wikimedia.org/wikipedia/zh/math/2/3/e/23e68b97e8ddf443cdeed5ce45e04abe.png) |
| `=`                                                          | ![=\,](http://upload.wikimedia.org/wikipedia/zh/math/4/4/8/4488ba5f7e82e2d8c136b559d95283d5.png) |
| `\leq`                                                       | ![\leq](http://upload.wikimedia.org/wikipedia/zh/math/4/9/d/49dc1443f33cf63082d6e193dd2af78f.png) |
| `\leqq`                                                      | ![\leqq](http://upload.wikimedia.org/wikipedia/zh/math/8/8/5/885f9db7c38c45758c0f7b07b823bc64.png) |
| `<`                                                          | ![<\,](http://upload.wikimedia.org/wikipedia/zh/math/0/1/6/0164cc2c31c906d9d775037410cc195c.png) |
| `\ll`                                                        | ![\ll](http://upload.wikimedia.org/wikipedia/zh/math/9/4/3/9439146cc81e763e0563ccbbcb523314.png) |
| `\lll`                                                       | ![\lll](http://upload.wikimedia.org/wikipedia/zh/math/4/e/8/4e8dc43a1f5be9ea3d8d27fdea882497.png) |
| `\begin{align}
\because\begin{cases}
\acute{a}x^2+bx^2+c\gtrless0\gtrless\grave{a}x^2+bx^2+c\\
\acute{a}>0>\grave{a}
\end{cases}\\
\therefore\frac{-b\pm\sqrt{b^2-4\acute{a}c}}{2\acute{a}}{}_\lessgtr^\gtrlessx_\lessgtr^\gtrless\frac{-b\pm\sqrt{b^2-4\grave{a}c}}{2\grave{a}}
\end{align}` | ![](http://upload.wikimedia.org/wikipedia/zh/math/8/1/0/8105aed2a6a0372d348af577ea8652d8.png) |
| `(x-y)^2\equiv(-x+y)^2\equiv x^2-2xy+y^2`                    | ![(x-y)^2\equiv(-x+y)^2\equiv x^2-2xy+y^2](http://upload.wikimedia.org/wikipedia/zh/math/6/c/9/6c9a2bfd0d3b9a5a74f8682479733cf9.png) |
| x\not\equiv N                                                | ![x\not\equiv N](http://upload.wikimedia.org/wikipedia/zh/math/1/b/0/1b05bef638197b56e7298f7ce316ad13.png) |
| x\ne A                                                       | ![x\ne A](http://upload.wikimedia.org/wikipedia/zh/math/3/1/0/3104de4e8ef189be10d43d6ca8e9444a.png) |
| x\neq C                                                      | ![x\neq C](http://upload.wikimedia.org/wikipedia/zh/math/7/0/1/7016f46da82d8566a65d93a0ed082874.png) |
| t\propto v                                                   | ![t\propto v](http://upload.wikimedia.org/wikipedia/zh/math/c/f/f/cfff18dbfebc64e8d6080fa3731249c1.png) |
| \pm                                                          | ![\pm](http://upload.wikimedia.org/wikipedia/zh/math/5/7/2/5722e2f6169308b8be3542900c6d6553.png) |
| \mp                                                          | $ \mp $                                                      |

#### 几何符号

| 特征                                                    | 语法                                                         | 效果                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 菱形                                                    | \Diamond                                                     | ![\Diamond](http://upload.wikimedia.org/wikipedia/zh/math/6/a/2/6a2084de787fb2d1c3e408d926425904.png) |
| 正方形                                                  | \Box                                                         | ![\Box](http://upload.wikimedia.org/wikipedia/zh/math/9/d/8/9d8f6d53ad070771a6910933810023c4.png) |
| 三角形(Delta)                                           | `\Delta`                                                     | ![\Delta\!](http://upload.wikimedia.org/wikipedia/zh/math/5/a/b/5abc9a983cecde2f902ff5e4de34ec1f.png) |
| 三角形(图型)                                            | `\triangle`                                                  | ![\triangle](http://upload.wikimedia.org/wikipedia/zh/math/f/5/d/f5d889f32d6794e1bc2ed394e9688c76.png) |
| 角名                                                    | `\angle\Alpha\Beta\Gamma`                                    | ![\angle\Alpha\Beta\Gamma](http://upload.wikimedia.org/wikipedia/zh/math/3/8/5/38501930aadb2f2d724c193f209f9578.png) |
| [角度](http://zh.wikipedia.org/wiki/%E8%A7%92%E5%BA%A6) | `\sin\!\frac{\pi}{3}=\sin60^\operatorname{\omicron}=\frac{\sqrt{3}}{2}` | ![\sin\!\frac{\pi}{3}=\sin60^\operatorname{\omicron}=\frac{\sqrt{3}}{2}](http://upload.wikimedia.org/wikipedia/zh/math/d/a/0/da0804e5be1869c583e761bb38e2f007.png) |
| [垂直](http://zh.wikipedia.org/wiki/%E5%9E%82%E7%9B%B4) | \perp                                                        | ![\perp](http://upload.wikimedia.org/wikipedia/zh/math/d/2/1/d21e3d53f78769beee8ff7b6c4a9a92c.png) |

#### 箭头符号

| 语法            | 效果                                                         | 语法            | 效果                                                         | 语法           | 效果                                                         |
| --------------- | ------------------------------------------------------------ | --------------- | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| \leftarrow      | ![\leftarrow](http://upload.wikimedia.org/wikipedia/zh/math/a/6/4/a6465c0244621c63e7e1e96eb55aad7a.png) | \gets           | ![\gets](http://upload.wikimedia.org/wikipedia/zh/math/c/e/9/ce9bfd92570656019fed79d092b6ae74.png) | \rightarrow    | ![\rightarrow](http://upload.wikimedia.org/wikipedia/zh/math/8/3/e/83e37b7246fdfcb99b2754210ebeae27.png) |
| \to             | ![\to](http://upload.wikimedia.org/wikipedia/zh/math/d/a/5/da558173e1f2ddfeb273751d481f9a52.png) | \leftrightarrow | ![\leftrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/1/b/1/1b18a4c4fc578ef4cfd1cc0eb0daa473.png) | \longleftarrow | ![\longleftarrow](http://upload.wikimedia.org/wikipedia/zh/math/5/7/f/57f18f6115ac560cb869f55d691ab334.png) |
| \longrightarrow | ![\longrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/b/7/8/b78286a473d99ba959caf406cea7e493.png) | \mapsto         | ![\mapsto](http://upload.wikimedia.org/wikipedia/zh/math/9/4/5/9450612598aafa1f56196840d7f83ff9.png) | \longmapsto    | ![\longmapsto](http://upload.wikimedia.org/wikipedia/zh/math/b/6/8/b68c87df100d6a59aea8e716bb3a97ce.png) |
| \hookrightarrow | ![\hookrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/7/2/9/7294bafcf0fbf24fa305a03ddb7bd367.png) | \hookleftarrow  | ![\hookleftarrow](http://upload.wikimedia.org/wikipedia/zh/math/f/d/4/fd458e10f8ceba4e35d89bafa86905de.png) | \nearrow       | ![\nearrow](http://upload.wikimedia.org/wikipedia/zh/math/3/0/f/30f1696dc98c430e93a6139c456dbd5d.png) |
| \searrow        | ![\searrow](http://upload.wikimedia.org/wikipedia/zh/math/7/5/2/75269e7e572c9cd78ae150d34a71b7d5.png) | \swarrow        | ![\swarrow](http://upload.wikimedia.org/wikipedia/zh/math/5/f/b/5fb8d3e625fff8c3b4d6450236bad8eb.png) | \nwarrow       | ![\nwarrow](http://upload.wikimedia.org/wikipedia/zh/math/2/3/a/23ac812cbd7c1de7762b501b2575798a.png) |
| \uparrow        | ![\uparrow](http://upload.wikimedia.org/wikipedia/zh/math/f/0/4/f045028b3e31841b22efbbb9a0911dc0.png) | \downarrow      | ![\downarrow](http://upload.wikimedia.org/wikipedia/zh/math/4/2/f/42f4ac9a26f75eda6a7716993026a6c8.png) | \updownarrow   | ![\updownarrow](http://upload.wikimedia.org/wikipedia/zh/math/4/5/7/457ca7d9f0425e9b1dc99132e119dbce.png) |

| 语法            | 效果                                                         | 语法              | 效果                                                         | 语法             | 效果                                                         | 语法              | 效果                                                         |
| --------------- | ------------------------------------------------------------ | ----------------- | ------------------------------------------------------------ | ---------------- | ------------------------------------------------------------ | ----------------- | ------------------------------------------------------------ |
| \rightharpoonup | ![\rightharpoonup](http://upload.wikimedia.org/wikipedia/zh/math/8/8/3/8837df45e6cc70b20f1c232847a27d71.png) | \rightharpoondown | ![\rightharpoondown ](http://upload.wikimedia.org/wikipedia/zh/math/a/7/9/a79d180f700de8efbe9c9e44a53ad372.png) | \leftharpoonup   | ![\leftharpoonup](http://upload.wikimedia.org/wikipedia/zh/math/4/8/9/4898039d7b0702b29079e5e9ca3c4513.png) | \leftharpoondown  | ![\leftharpoondown ](http://upload.wikimedia.org/wikipedia/zh/math/9/b/0/9b05a226682efc86dc1d66ef026ae2e3.png) |
| \upharpoonleft  | ![\upharpoonleft](http://upload.wikimedia.org/wikipedia/zh/math/c/6/e/c6e4de1cefacd371ebbf050f78786be6.png) | \upharpoonright   | ![\upharpoonright ](http://upload.wikimedia.org/wikipedia/zh/math/d/f/5/df58255d87a189b5c496160f06212765.png) | \downharpoonleft | ![\downharpoonleft](http://upload.wikimedia.org/wikipedia/zh/math/4/a/4/4a406771aef37db53572e672e9fcd360.png) | \downharpoonright | ![\downharpoonright](http://upload.wikimedia.org/wikipedia/zh/math/3/7/4/374d7e005ee1a76c99ac68f71028cbdc.png) |

| 语法           | 效果                                                         | 语法            | 效果                                                         | 语法                          | 效果                                                         |
| -------------- | ------------------------------------------------------------ | --------------- | ------------------------------------------------------------ | ----------------------------- | ------------------------------------------------------------ |
| \Leftarrow     | ![\Leftarrow](http://upload.wikimedia.org/wikipedia/zh/math/1/c/f/1cfb61f8b7206229c3ad6452e0d83674.png) | \Rightarrow     | ![\Rightarrow](http://upload.wikimedia.org/wikipedia/zh/math/d/f/0/df09aea884019cb88a2957126faba316.png) | \Leftrightarrow               | ![\Leftrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/0/1/4/014cced2b73c22eb88cdc6901afb0c9d.png) |
| \Longleftarrow | ![\Longleftarrow](http://upload.wikimedia.org/wikipedia/zh/math/7/2/1/7218138eb9cfb7959782d4e553a9105e.png) | \Longrightarrow | ![\Longrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/7/5/8/7585765360506206cb893e03b9517acc.png) | \Longleftrightarrow (or \iff) | ![\Longleftrightarrow](http://upload.wikimedia.org/wikipedia/zh/math/3/5/c/35c86324cc425d53964bba41b40bbe99.png) |
| \Uparrow       | ![\Uparrow](http://upload.wikimedia.org/wikipedia/zh/math/b/2/6/b267b68b9e6b5c95dc1853c82be33b00.png) | \Downarrow      | ![\Downarrow](http://upload.wikimedia.org/wikipedia/zh/math/a/5/8/a5855048483c40e8ffeac0646476f178.png) | \Updownarrow                  | ![\Updownarrow](http://upload.wikimedia.org/wikipedia/zh/math/7/1/8/7185c13dd289c8bde5d575f846e9d521.png) |

#### 特殊符号

| 语法  | 效果                                                         | 语法 | 效果                                                         | 语法   | 效果                                                         | 语法   | 效果                                                         | 语法    | 效果                                                         | 语法     | 效果                                                         |
| ----- | ------------------------------------------------------------ | ---- | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ | ------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| \eth  | ![\eth](http://upload.wikimedia.org/wikipedia/zh/math/2/d/a/2dacecd4fffb3d4fdea52ed937b50c81.png) | \S   | ![\S](http://upload.wikimedia.org/wikipedia/zh/math/5/d/6/5d60d7c05a80628104ef1bd0718eb3fc.png) | \P     | ![\P](http://upload.wikimedia.org/wikipedia/zh/math/8/9/9/899edd97aab272d0a8c8987321b24408.png) | \%     | ![\%](http://upload.wikimedia.org/wikipedia/zh/math/2/8/f/28f423e28b5eb397034d51aaf59b708b.png) | \dagger | ![\dagger](http://upload.wikimedia.org/wikipedia/zh/math/5/0/f/50f39cb506b81c04fb18893469de5be5.png) | \ddagger | ![\ddagger](http://upload.wikimedia.org/wikipedia/zh/math/f/d/6/fd6bb9c23860e78a827828a896063b5b.png) |
| \star | ![\star](http://upload.wikimedia.org/wikipedia/zh/math/2/3/d/23d64887f9c2add7a296e5b99acbbdfb.png) | *    | ![*](http://upload.wikimedia.org/wikipedia/zh/math/3/3/8/3389dae361af79b04c9c8e7057f60cc6.png) | \ldots | ![\ldots](http://upload.wikimedia.org/wikipedia/zh/math/0/6/5/065775997fdbcb83ddbe8936e67e4f2c.png) | \smile | ![\smile](http://upload.wikimedia.org/wikipedia/zh/math/e/c/9/ec97f8d0dbf2f26b882bb6d671d2e535.png) | \frown  | ![\frown](http://upload.wikimedia.org/wikipedia/zh/math/4/f/8/4f8881ccda31a4b84283529b47d30c95.png) | \wr      | ![\wr](http://upload.wikimedia.org/wikipedia/zh/math/b/5/c/b5ce93cb24555a53656581ff87bc2caf.png) |

| 语法       | 效果                                                         | 语法      | 效果                                                         | 语法     | 效果                                                         |
| ---------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| \oplus     | ![\oplus](http://upload.wikimedia.org/wikipedia/zh/math/b/7/1/b71edd70fcad670e99a9912ba5e55d77.png) | \bigoplus | ![\bigoplus](http://upload.wikimedia.org/wikipedia/zh/math/1/7/e/17e0badeeb2a6501df77203521a6501d.png) | \otimes  | ![\otimes](http://upload.wikimedia.org/wikipedia/zh/math/e/9/d/e9dd9013ec300ceba41484dfc2c9a876.png) |
| \bigotimes | ![\bigotimes](http://upload.wikimedia.org/wikipedia/zh/math/9/f/3/9f3cab49d684e7498a965b3e9d3c1524.png) | \times    | ![\times](http://upload.wikimedia.org/wikipedia/zh/math/9/e/e/9eedd61e32f7a8e70e171028a7e5dc08.png) | \cdot    | ![\cdot](http://upload.wikimedia.org/wikipedia/zh/math/3/6/f/36f8ae4c86b69d52d037a6802d91cc4a.png) |
| \div       | ![\div](http://upload.wikimedia.org/wikipedia/zh/math/a/3/9/a3933e4fee514b1dfa4cea09af6ae53e.png) | \circ     | ![\circ](http://upload.wikimedia.org/wikipedia/zh/math/1/0/c/10c3e97d2a3eda0d182b81d48f231b62.png) | \bullet  | ![\bullet](http://upload.wikimedia.org/wikipedia/zh/math/b/f/5/bf588c17a2f1ba670dd67abd8ef6b8c6.png) |
| \bigodot   | ![\bigodot](http://upload.wikimedia.org/wikipedia/zh/math/9/6/a/96a7b9d0ce387adfe7c6f3b873a0b76a.png) | \boxtimes | ![\boxtimes](http://upload.wikimedia.org/wikipedia/zh/math/e/2/2/e2211456bb3ab339c66e0ae2e2a813a4.png) | \boxplus | ![\boxplus](http://upload.wikimedia.org/wikipedia/zh/math/f/8/e/f8e648d94a8e0c86827c1605310274c2.png) |

| 语法          | 效果                                                         | 语法           | 效果                                                         | 语法   | 效果                                                         | 语法   | 效果                                                         |
| ------------- | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ |
| \triangleleft | ![\triangleleft](http://upload.wikimedia.org/wikipedia/zh/math/2/d/0/2d08cb5c3db4b928ee78ebf5799272c3.png) | \triangleright | ![\triangleright](http://upload.wikimedia.org/wikipedia/zh/math/6/f/8/6f8e04d60521ae7f16ba977acc75a5be.png) | \infty | ![\infty](http://upload.wikimedia.org/wikipedia/zh/math/d/2/4/d245777abca64ece2d5d7ca0d19fddb6.png) | \bot   | ![\bot](http://upload.wikimedia.org/wikipedia/zh/math/2/4/6/2462ef886428ee7d949936d41281f003.png) |
| \top          | ![\top](http://upload.wikimedia.org/wikipedia/zh/math/0/9/c/09c35e480ff978d5868d6e09f324f913.png) | \vdash         | ![\vdash](http://upload.wikimedia.org/wikipedia/zh/math/b/f/7/bf73c9341a48c47c84a48dad635ff940.png) | \vDash | ![\vDash](http://upload.wikimedia.org/wikipedia/zh/math/c/1/1/c11b72dc7a3b789b717a21f90be06c47.png) | \Vdash | ![\Vdash](http://upload.wikimedia.org/wikipedia/zh/math/f/a/b/fab53e7da58e75e9eb1c8b5719631e0b.png) |
| \models       | ![\models](http://upload.wikimedia.org/wikipedia/zh/math/e/7/6/e766ce0de4bbe899d7ea2ebe40b3e0ee.png) | \lVert         | ![\lVert](http://upload.wikimedia.org/wikipedia/zh/math/d/8/6/d864e6c7ca350ed86ee64288afbd178a.png) | \rVert | ![\rVert](http://upload.wikimedia.org/wikipedia/zh/math/f/7/8/f7898b1a0e380641f53fc4d5c2218b68.png) |        |                                                              |

| 语法   | 效果                                                         | 语法  | 效果                                                         | 语法        | 效果                                                         |
| ------ | ------------------------------------------------------------ | ----- | ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| \imath | ![\imath](http://upload.wikimedia.org/wikipedia/zh/math/1/b/6/1b6f7d5292201038835cfccdf34c96e3.png) | \hbar | ![\hbar](http://upload.wikimedia.org/wikipedia/zh/math/9/d/f/9dfd055ef1683b053f1b5bf9ed6dbbb4.png) | \ell        | ![\ell](http://upload.wikimedia.org/wikipedia/zh/math/3/3/4/334ce9eb79df1178b0380461c9eaa09e.png) |
| \mho   | ![\mho](http://upload.wikimedia.org/wikipedia/zh/math/f/f/c/ffcb76daff70c2bd4e0f4624bed32a2f.png) | \Finv | ![\Finv](http://upload.wikimedia.org/wikipedia/zh/math/6/a/9/6a984c8c20e99df30c065327cfce2b55.png) | \Re         | ![\Re](http://upload.wikimedia.org/wikipedia/zh/math/6/e/0/6e0372ee3e41f8ed7bba429e0ccdc96e.png) |
| \Im    | ![\Im](http://upload.wikimedia.org/wikipedia/zh/math/e/f/8/ef81859e9fa4549ecefd51647be8224a.png) | \wp   | ![\wp](http://upload.wikimedia.org/wikipedia/zh/math/5/9/2/59234eec7b76260bbbfcf81979887b2c.png) | \complement | ![\complement](http://upload.wikimedia.org/wikipedia/zh/math/8/9/f/89fb291e5e64322950296e45369d3285.png) |

| 语法         | 效果                                                         | 语法       | 效果                                                         | 语法      | 效果                                                         | 语法       | 效果                                                         |
| ------------ | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| \diamondsuit | ![\diamondsuit](http://upload.wikimedia.org/wikipedia/zh/math/f/b/2/fb24ec60ac1d9b437a64b2edcb5450f4.png) | \heartsuit | ![\heartsuit](http://upload.wikimedia.org/wikipedia/zh/math/7/b/d/7bd6a6158c45891182ef5783234c2c5a.png) | \clubsuit | ![\clubsuit](http://upload.wikimedia.org/wikipedia/zh/math/f/2/4/f243a5ff32cd1dd4a28521b225bb76b5.png) | \spadesuit | ![\spadesuit](http://upload.wikimedia.org/wikipedia/zh/math/8/a/2/8a2e30359d1821a30aff595e4ff18375.png) |
| \Game        | ![\Game](http://upload.wikimedia.org/wikipedia/zh/math/4/f/6/4f67c5c3f8e2624b0e62de0ee8d38650.png) | \flat      | ![\flat](http://upload.wikimedia.org/wikipedia/zh/math/8/3/8/8380b268482c1a8d9fd36dc88e8a127a.png) | \natural  | ![\natural](http://upload.wikimedia.org/wikipedia/zh/math/3/1/7/3178537e3f3a2bc507823259218904ad.png) | \sharp     | ![\sharp](http://upload.wikimedia.org/wikipedia/zh/math/7/8/1/7818bf5748ed97579507cd14a1d5bb65.png) |

#### 上标、下标及[积分](http://zh.wikipedia.org/wiki/%E7%A7%AF%E5%88%86)等

| 功能                                                         | 语法                                                         | 效果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 上标                                                         | `a^2`                                                        | ![\pagecolor{White} a^2](http://upload.wikimedia.org/wikipedia/zh/math/f/1/d/f1d0a7eeee296f457681b906e2cdacac.png) |
| 下标                                                         | `a_2`                                                        | ![\pagecolor{White} a_2](http://upload.wikimedia.org/wikipedia/zh/math/8/e/2/8e2d2291a5593af94e996a65b6f726f8.png) |
| 组合                                                         | `a^{2+2}`                                                    | ![\pagecolor{White} a^{2+2}](http://upload.wikimedia.org/wikipedia/zh/math/0/6/8/0683bba81494fa4172f1052cec9f2eed.png) |
| `a_{i,j}`                                                    | ![\pagecolor{White} a_{i,j}](http://upload.wikimedia.org/wikipedia/zh/math/b/d/9/bd93d6a6976568f768d4ef2a91bb2151.png) |                                                              |
| 结合上下标                                                   | `x_2^3`                                                      | ![\pagecolor{White} x_2^3](http://upload.wikimedia.org/wikipedia/zh/math/f/7/f/f7ffd853543b9b2461e72eb28f8dec3c.png) |
| 前置上下标                                                   | `{}_1^2\!X_3^4`                                              | ![\pagecolor{White} {}_1^2\!X_3^4](http://upload.wikimedia.org/wikipedia/zh/math/2/4/9/2491f82bb48b549a2aaf289dbb3a7bed.png) |
| [导数](http://zh.wikipedia.org/wiki/%E5%AF%BC%E6%95%B0) （**HTML**） | `x'`                                                         | ![\pagecolor{White} x'](http://upload.wikimedia.org/wikipedia/zh/math/2/1/e/21ef9651f881246acc18a8b92368aabf.png) |
| 导数 （**PNG**）                                             | `x^\prime`                                                   | ![\pagecolor{White} x^\prime](http://upload.wikimedia.org/wikipedia/zh/math/e/3/d/e3dbdd462fde1f1fb890aed48df66440.png) |
| 导数 （**错误**）                                            | `x\prime`                                                    | ![\pagecolor{White} x\prime](http://upload.wikimedia.org/wikipedia/zh/math/e/5/2/e5269181cb3851f57f684d621a9f1fca.png) |
| 上划线                                                       | `\overline{h i j}`                                           | ![\pagecolor{White} \overline{h i j}](http://upload.wikimedia.org/wikipedia/zh/math/5/b/1/5b143339bea2ab5becbbb7a3bdaba555.png) |
| 下划线                                                       | `\underline{k l m}`                                          | ![\pagecolor{White} \underline{k l m}](http://upload.wikimedia.org/wikipedia/zh/math/1/4/7/147943fe9e7a222b850132ddd7882dce.png) |
| [双重积分](http://zh.wikipedia.org/wiki/%E5%8F%8C%E9%87%8D%E7%A7%AF%E5%88%86) | `\iint_{D}^{W} \, dx\,dy`                                    | ![\pagecolor{White} \iint_{D}^{W} \, dx\,dy](http://upload.wikimedia.org/wikipedia/zh/math/4/4/e/44e3f99f1f29b9ee5de871e820cec5f3.png) |
| 三重积分                                                     | `\iiint_{E}^{V} \, dx\,dy\,dz`                               | ![\pagecolor{White} \iiint_{E}^{V} \, dx\,dy\,dz](http://upload.wikimedia.org/wikipedia/zh/math/8/c/0/8c050c5bd1690dd42a9f175ec124cb97.png) |
| 四重积分                                                     | `\iiiint_{F}^{U} \, dx\,dy\,dz\,dt`                          | ![\pagecolor{White} \iiiint_{F}^{U} \, dx\,dy\,dz\,dt](http://upload.wikimedia.org/wikipedia/zh/math/d/b/5/db51c71f79d301a689b3231927a0dfaa.png) |
| 闭合的[曲线](http://zh.wikipedia.org/wiki/%E8%B7%AF%E5%BE%84%E7%A7%AF%E5%88%86)、[曲面积分](http://zh.wikipedia.org/wiki/%E6%9B%B2%E9%9D%A2%E7%A7%AF%E5%88%86) | \oint_{C} x^3\, dx + 4y^2\, dy                               | ![\pagecolor{White} \oint_{C} x^3\, dx + 4y^2\, dy](http://upload.wikimedia.org/wikipedia/zh/math/e/5/7/e5791a184359ab19ff7e372b15ec0d1b.png) |
| [交集](http://zh.wikipedia.org/wiki/%E4%BA%A4%E9%9B%86)      | `\bigcap_1^{n} p`                                            | ![\pagecolor{White} \bigcap_1^{n} p](http://upload.wikimedia.org/wikipedia/zh/math/4/6/3/463d4e4bd315c0c6030354a540d85829.png) |
| [并集](http://zh.wikipedia.org/wiki/%E5%B9%B6%E9%9B%86)      | `\bigcup_1^{k} p`                                            | ![\pagecolor{White} \bigcup_1^{k} p](http://upload.wikimedia.org/wikipedia/zh/math/8/0/0/80059f454f87af13803b327c202dcec1.png) |

![FHZfh9.md.png](https://s2.ax1x.com/2019/01/06/FHZfh9.md.png)

![上下标、积分等](https://s2.ax1x.com/2019/01/06/FHZ4pR.png)

#### [分数](http://zh.wikipedia.org/wiki/%E5%88%86%E6%95%B0)、[矩阵](http://zh.wikipedia.org/wiki/%E7%9F%A9%E9%98%B5)和多行列式

| 功能                                                         | 语法                                                         | 效果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 分数                                                         | `\frac{2}{4}=0.5`                                            | ![\frac{2}{4}=0.5](http://upload.wikimedia.org/wikipedia/zh/math/4/6/d/46dc0b34e0ab4e944a437720a4431d6c.png) |
| 小型分数                                                     | `\tfrac{2}{4} = 0.5`                                         | ![\tfrac{2}{4} = 0.5](http://upload.wikimedia.org/wikipedia/zh/math/2/8/4/284667fc4a92790093aa59b61b3667a0.png) |
| 大型分数（嵌套）                                             | `\cfrac{2}{c + \cfrac{2}{d + \cfrac{2}{4}}} =a`              | ![\cfrac{2}{c + \cfrac{2}{d + \cfrac{2}{4}}} = a](http://upload.wikimedia.org/wikipedia/zh/math/6/d/0/6d099c02b3faf73f9320656217415906.png) |
| 大型分数（不嵌套）                                           | `\dfrac{2}{4} = 0.5 \qquad \dfrac{2}{c + \dfrac{2}{d +\dfrac{2}{4}}} = a` | ![\dfrac{2}{4} = 0.5 \qquad \dfrac{2}{c + \dfrac{2}{d + \dfrac{2}{4}}} = a](http://upload.wikimedia.org/wikipedia/zh/math/5/a/3/5a37ae94a95c7dd603c20cd4fbe8d9e9.png) |
| [二项式](http://zh.wikipedia.org/wiki/%E4%BA%8C%E9%A1%B9%E5%BC%8F)系数 | `\dbinom{n}{r}=\binom{n}{n-r}=C^n_r=C^n_{n-r}`               | ![\dbinom{n}{r}=\binom{n}{n-r}=C^n_r=C^n_{n-r}](http://upload.wikimedia.org/wikipedia/zh/math/6/a/5/6a5373cd8c6503567a3c2a69deda71f3.png) |
| 小型[二项式](http://zh.wikipedia.org/wiki/%E4%BA%8C%E9%A1%B9%E5%BC%8F)系数 | `\tbinom{n}{r}=\tbinom{n}{n-r}=C^n_r=C^n_{n-r}`              | ![\tbinom{n}{r}=\tbinom{n}{n-r}=C^n_r=C^n_{n-r}](http://upload.wikimedia.org/wikipedia/zh/math/0/b/1/0b1188af9ee1a12bc62ca56cf83f268c.png) |
| 大型[二项式](http://zh.wikipedia.org/wiki/%E4%BA%8C%E9%A1%B9%E5%BC%8F)系数 | `\binom{n}{r}=\dbinom{n}{n-r}=C^n_r=C^n_{n-r}`               | ![\binom{n}{r}=\dbinom{n}{n-r}=C^n_r=C^n_{n-r}](http://upload.wikimedia.org/wikipedia/zh/math/b/6/9/b69b21e3b047a91cd1127a498b3267f6.png) |
| 条件定义                                                     | `f(n) = \begin{cases}  n/2,  & \mbox{if }n\mbox{ is even} \\ 3n+1, & \mbox{if }n\mbox{ is odd} \end{cases} ` | ![f(n) = \begin{cases} n/2, & \mbox{if }n\mbox{ is even} \ 3n+1, & \mbox{if }n\mbox{ is odd} \end{cases} ](http://upload.wikimedia.org/wikipedia/zh/math/e/3/a/e3aebe50364cfe498fa9ca99c0036010.png) |
| 多行等式                                                     | `\begin{align} f(x) & = (m+n)^2 \\ & = m^2+2mn+n^2 \\ \end{align} ` | ![ \begin{align} f(x) & = (m+n)^2 \ & = m^2+2mn+n^2 \ \end{align} ](http://upload.wikimedia.org/wikipedia/zh/math/1/d/9/1d9f576360e949d08e0fac7cacbbd25c.png) |
| `\begin{alignat}{2} f(x) & = (m-n)^2 \\ f(x) & = (-m+n)^2 \\ & = m^2-2mn+n^2 \\ \end{alignat} ` | ![ \begin{alignat}{2} f(x) & = (m-n)^2 \ f(x) & = (-m+n)^2 \ & = m^2-2mn+n^2 \ \end{alignat} ](http://upload.wikimedia.org/wikipedia/zh/math/1/8/d/18d4bad0865b57fc1d5b383abe2da1b0.png) |                                                              |
| 多行等式（左对齐）                                           | `\begin{array}{lcl} z        & = & a \\ f(x,y,z) & = & x + y + z  \end{array} ` | ![\begin{array}{lcl} z & = & a \ f(x,y,z) & = & x + y + z \end{array}](http://upload.wikimedia.org/wikipedia/zh/math/9/b/f/9bf19115bb27237fa997ca93b94ad217.png) |
| 多行等式（右对齐）                                           | `\begin{array}{lcr} z        & = & a \\ f(x,y,z) & = & x + y + z     \end{array} ` | ![\begin{array}{lcr} z & = & a \ f(x,y,z) & = & x + y + z \end{array}](http://upload.wikimedia.org/wikipedia/zh/math/0/2/a/02ae32735e1e21ba3b05984289fd2763.png) |
| 长公式换行                                                   | `<math>f(x) \,\!</math> <math>= \sum_{n=0}^\infty a_n x^n </math> <math>= a_0+a_1x+a_2x^2+\cdots</math>  ` | ![f(x) \,\!](http://upload.wikimedia.org/wikipedia/zh/math/8/d/f/8dfae20000a042d8e9047aad1d7e171e.png)![= \sum_{n=0}^\infty a_n x^n ](http://upload.wikimedia.org/wikipedia/zh/math/6/6/3/6633d51d63b35281d030755a6b0aebb1.png)![= a_0 +a_1x+a_2x^2+\cdots](http://upload.wikimedia.org/wikipedia/zh/math/f/e/3/fe3e268382fd486e8572daf895bd4c9d.png) |
| [方程组](http://zh.wikipedia.org/wiki/%E6%96%B9%E7%A8%8B%E7%BB%84) | `\begin{cases} 3x + 5y +  z \\ 7x - 2y + 4z \\ -6x + 3y + 2z \end{cases} ` | ![\begin{cases} 3x + 5y + z \\ 7x - 2y + 4z \\ -6x + 3y + 2z \end{cases}](http://upload.wikimedia.org/wikipedia/zh/math/6/3/4/6349be04b3562fc215c7a4e130422a96.png) |
| 数组                                                         | `\begin{array}{|c|c||c|} a & b & S \\ \hline 0&0&1\\ 0&1&1\\ 1&0&1\\ 1&1&0\\ \end{array} ` | ![ \begin{array}{\|c\|c\|\|c\|} a & b & S \ \hline 0&0&1\ 0&1&1\ 1&0&1\ 1&1&0\ \end{array} ](http://upload.wikimedia.org/wikipedia/zh/math/9/1/5/9151e94ef2bb52c18176dbe4c11921ed.png) |

矩阵相关：

| `\begin{matrix} x & y \\ z & v \end{matrix} `                | ![\begin{matrix} x & y \\ z & v \end{matrix}](http://upload.wikimedia.org/wikipedia/zh/math/b/9/9/b99890966e1b997497211428f8e3419d.png) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `\begin{vmatrix} x & y \\ z & v \end{vmatrix} `              | ![\begin{vmatrix} x & y \\ z & v \end{vmatrix}](http://upload.wikimedia.org/wikipedia/zh/math/9/2/b/92b8f0e57848a80b4babd2ba93775370.png) |
| `\begin{Vmatrix} x & y \\ z & v \end{Vmatrix} `              | ![\begin{Vmatrix} x & y \\ z & v \end{Vmatrix}](http://upload.wikimedia.org/wikipedia/zh/math/b/b/a/bba5bfd11057dbb202307584eed8f2dc.png) |
| `\begin{bmatrix} 0      & \cdots & 0      \\ \vdots & \ddots & \vdots \\ 0      & \cdots & 0 \end{bmatrix} ` | ![\begin{bmatrix} 0 & \cdots & 0 \\ \vdots & \ddots & \vdots \\ 0 & \cdots & 0\end{bmatrix} ](http://upload.wikimedia.org/wikipedia/zh/math/8/1/a/81a12a09ac84853e3d25323b8643c630.png) |
| `\begin{Bmatrix} x & y \\ z & v \end{Bmatrix} `              | ![\begin{Bmatrix} x & y \\ z & v \end{Bmatrix}](http://upload.wikimedia.org/wikipedia/zh/math/b/f/7/bf7244e2842c8a7d55892e229560d5c1.png) |
| `\begin{pmatrix} x & y \\ z & v \end{pmatrix} `              | ![\begin{pmatrix} x & y \\ z & v \end{pmatrix}](http://upload.wikimedia.org/wikipedia/zh/math/4/4/4/444df88e616def4e275b4e920c7b872e.png) |
| `\bigl( \begin{smallmatrix} a&b\\ c&d \end{smallmatrix} \bigr) ` | ![ \bigl( \begin{smallmatrix} a&b\\ c&d \end{smallmatrix} \bigr) ](http://upload.wikimedia.org/wikipedia/zh/math/c/d/4/cd49bbc188dce0f93fef57312af5a106.png) |

### 字体

#### [希腊字母](http://zh.wikipedia.org/wiki/%E5%B8%8C%E8%85%8A%E5%AD%97%E6%AF%8D)

斜体小写希腊字母一般用于在方程中显示变量。

![正体希腊字母](https://s2.ax1x.com/2019/01/06/FHeSjP.png)

![粗体希腊字母](https://s2.ax1x.com/2019/01/06/FHePHS.png)

#### 黑板粗体

- 语法

  `\mathbb{ABCDEFGHIJKLMNOPQRSTUVWXYZ}`

- 效果

  ![\pagecolor{White}\mathbb{ABCDEFGHIJKLMNOPQRSTUVWXYZ}](http://upload.wikimedia.org/wikipedia/zh/math/c/e/4/ce44c3481a2b5d78785b0c920f5a4614.png)

黑板粗体（[Blackboardbold](http://en.wikipedia.org/wiki/Blackboard_bold)）一般用于表示数学和物理学中的向量或集合的符号。 

备注：

1. ![\{ \,](http://upload.wikimedia.org/wikipedia/zh/math/6/9/b/69b3f95ebb53ab73dc89a39639392f86.png)花括号![\} \,](http://upload.wikimedia.org/wikipedia/zh/math/b/7/5/b752132764734448b12a5a3dd2cda51c.png)中只有使用大写拉丁字母才能正常显示，使用小写字母或数字会得到其他符号。

#### 正粗体

- 语法

  `\mathbf{012…abc…ABC…}`

- 效果

  ![\pagecolor{White}\mathbf{0 \ 1 \ 2 \ 3 \ 4 \ 5 \ 6 \ 7 \ 8 \ 9}](http://upload.wikimedia.org/wikipedia/zh/math/7/7/6/77638faf12d57998f2e9b73e943de27c.png)

  ![\pagecolor{White}\mathbf{a \ b \ c \ d \ e \ f \ g \ h \ i \ j \ k \ l \ m \ n \ o \ p \ q \ r \ s \ t \ u \ v \ w \ x \ y \ z}](http://upload.wikimedia.org/wikipedia/zh/math/1/6/6/1662b633eb414e7b094e8f34e4108f3a.png)

  ![\pagecolor{White}\mathbf{A \ B \ C \ D \ E \ F \ G \ H \ I \ J \ K \ L \ M \ N \ O \ P \ Q \ R \ S \ T \ U \ V \ W \ X \ Y \ Z}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/0/580fd155c65a7dc6f92cfa4dfb973ed5.png)

- 备注

  花括号{}内只能使用拉丁字母和数字，不能使用希腊字母如\alpha等。
#### 斜粗体
- 语法
  `\boldsymbol{012…abc…ABC…\alpha \beta\gamma…}`
- 效果
  ![\pagecolor{White}\boldsymbol{0 \ 1 \ 2 \ 3 \ 4 \ 5 \ 6 \ 7 \ 8 \ 9}](http://upload.wikimedia.org/wikipedia/zh/math/a/9/0/a90278c14f5690a74ed5a6a8302c9082.png)

  ![\pagecolor{White}\boldsymbol{a \ b \ c \ d \ e \ f \ g \ h \ i \ j \ k \ l \ m \ n \ o \ p \ q \ r \ s \ t \ u \ v \ w \ x \ y \ z}](http://upload.wikimedia.org/wikipedia/zh/math/a/d/e/aded24ff1434a136f279a7bef76514e7.png)

  ![\pagecolor{White}\boldsymbol{A \ B \ C \ D \ E \ F \ G \ H \ I \ J \ K \ L \ M \ N \ O \ P \ Q \ R \ S \ T \ U \ V \ W \ X \ Y \ Z}](http://upload.wikimedia.org/wikipedia/zh/math/6/a/5/6a53c91894a6ffb4be339e91549a8423.png)

  ![\pagecolor{White}\boldsymbol{\alpha \ \beta \ \gamma \ \delta \ \epsilon \ \zeta \ \eta \ \theta \ \iota \ \kappa \ \lambda \ \mu \ \nu \ \xi \ o \ \pi \ \rho \ \sigma \ \tau \ \upsilon \ \phi \ \chi \ \psi \ \omega}](http://upload.wikimedia.org/wikipedia/zh/math/2/5/b/25ba644b7c4d6ea15e453246149a5fc7.png)

- 备注

  使用`\boldsymbol{}`可以加粗所有合法的符号。

#### 斜体数字

- 语法

  `\mathit{0123456789}`

- 效果

  ![\mathit{0123456789}\!](http://upload.wikimedia.org/wikipedia/zh/math/9/6/8/96846c8042557a593245c9adbfadcf67.png)

#### 罗马体

- 语法

  `\mathrm{012…abc…ABC…}或\mbox{}或\operatorname{}`

- 效果

  ![\mathrm{0123456789}\ ](http://upload.wikimedia.org/wikipedia/zh/math/1/5/4/15472007a8874b45b1dc0b446928e792.png)

  ![\mathrm{ABCDEFGHIJKLMNOPQRSTUVWXYZ}\ ](http://upload.wikimedia.org/wikipedia/zh/math/3/6/a/36ab31702c21e576eeb46bcb3af9492d.png)

  ![\mathrm{abcdefghijklmnopqrstuvwxyz}\ ](http://upload.wikimedia.org/wikipedia/zh/math/6/2/5/6253a68230884ac36ac5705cc943cf35.png)

- 备注

  罗马体可以使用数字和[拉丁字母](http://zh.wikipedia.org/wiki/%E6%8B%89%E4%B8%81%E5%AD%97%E6%AF%8D)。

#### 哥特体

- 语法

  `\mathfrak{012…abc…ABC…}`

- 效果

  ![\pagecolor{White} \mathfrak{0 \ 1 \ 2 \ 3 \ 4 \ 5 \ 6 \ 7 \ 8 \ 9}](http://upload.wikimedia.org/wikipedia/zh/math/8/9/7/8978258d44abd5fa142106d7d0d5cfdb.png)

  ![\pagecolor{White} \mathfrak{a \ b \ c \ d \ e \ f \ g \ h \ i \ j \ k \ l \ m \ n \ o \ p \ q \ r \ s \ t \ u \ v \ w \ x \ y \ z}](http://upload.wikimedia.org/wikipedia/zh/math/0/3/0/0304e047f276ed327b84d340e393b7d4.png)

  ![\pagecolor{White} \mathfrak{A \ B \ C \ D \ E \ F \ G \ H \ I \ J \ K \ L \ M \ N \ O \ P \ Q \ R \ S \ T \ U \ V \ W \ X \ Y \ Z}](http://upload.wikimedia.org/wikipedia/zh/math/e/a/9/ea949c322ae53aaf0a0de51dc465cf3f.png)

- 备注

  哥特体可以使用数字和拉丁字母。

#### 手写体

- 语法

  `\mathcal{ABC…}`

- 效果

  ![\mathcal{ABCDEFGHIJKLMNOPSTUVWXYZ}](http://upload.wikimedia.org/wikipedia/zh/math/4/a/8/4a8cadf2764770249d0cc7bb1d8c5b94.png)

- 备注

  手写体仅对大写拉丁字母有效。

#### 希伯来字母

- 语法

  `\aleph\beth\gimel\daleth`

- 效果

  ![\aleph\beth\gimel\daleth](http://upload.wikimedia.org/wikipedia/zh/math/8/b/9/8b995d7f3c72ab2bfffec5fe0bdb5df1.png)

### 括号

| 功能   | 语法                       | 显示                                                         |
| ------ | -------------------------- | ------------------------------------------------------------ |
| 不好看 | ( \frac{1}{2} )            | ![( \frac{1}{2} )](http://upload.wikimedia.org/wikipedia/zh/math/4/0/a/40ad9d3d1fc9a61e16d22d7e3f854fec.png) |
| 好看了 | \left( \frac{1}{2} \right) | ![\left ( \frac{1}{2} \right )](http://upload.wikimedia.org/wikipedia/zh/math/2/8/b/28bcd5b82ce0e92b25e8a0b4bd5be215.png) |

您可以使用 `\left` 和 `\right` 来显示不同的括号：

| 功能                         | 语法                                                      | 显示                                                         |
| ---------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| 圆括号，小括号               | \left**(** \frac{a}{b} \right**)**                        | ![\left( \frac{a}{b} \right)](http://upload.wikimedia.org/wikipedia/zh/math/2/9/0/2905969500b40b2f2c7078206e7e0e81.png) |
| 方括号，中括号               | \left**[** \frac{a}{b} \right**]**                        | ![\left[ \frac{a}{b} \right]](http://upload.wikimedia.org/wikipedia/zh/math/8/5/8/8585c96f355f7e301fd5143bea32efaf.png) |
| 花括号，大括号               | \left**\{** \frac{a}{b} \right**\}**                      | ![\left\{ \frac{a}{b} \right\}](http://upload.wikimedia.org/wikipedia/zh/math/c/4/d/c4d4af6bab9a0e6532dddd50e7d27158.png) |
| 角括号                       | \left **\langle** \frac{a}{b} \right **\rangle**          | ![\left\langle \frac{a}{b} \right \rangle](http://upload.wikimedia.org/wikipedia/zh/math/d/0/6/d06e733ce705ed26a7e048dbd2945371.png) |
| 单竖线，绝对值               | \left**\|** \frac{a}{b} \right**\|**                      | ![\left\| \frac{a}{b} \right\|](http://upload.wikimedia.org/wikipedia/zh/math/4/0/d/40d6c8253b08e8801a01b3f6e5069a62.png) |
| 双竖线，范                   | \left **\|** \frac{a}{b} \right **\|**                    | ![\left \| \frac{a}{b} \right \|](http://upload.wikimedia.org/wikipedia/zh/math/f/3/0/f30a5c412d1e4b4e7c6195ff5d47e947.png) |
| 取整函数 （Floor function）  | \left **\lfloor** \frac{a}{b} \right **\rfloor**          | ![\left \lfloor \frac{a}{b} \right \rfloor](http://upload.wikimedia.org/wikipedia/zh/math/c/0/7/c07e1fc7c0150828e55da4efe37e8a3f.png) |
| 取顶函数 （Ceiling function) | \left **\lceil** \frac{c}{d} \right **\rceil**            | ![\left \lceil \frac{c}{d} \right \rceil](http://upload.wikimedia.org/wikipedia/zh/math/8/6/8/868c1e52c339e01204aa1a77d44e3c71.png) |
| 斜线与反斜线                 | \left **/** \frac{a}{b} \right **\backslash**             | ![\left / \frac{a}{b} \right \backslash ](http://upload.wikimedia.org/wikipedia/zh/math/2/f/3/2f3c5907c0a4fc4fda69eb71890ce952.png) |
| 上下箭头                     | \left **\uparrow** \frac{a}{b} \right **\downarrow**      | ![\pagecolor{White}\left \uparrow \frac{a}{b} \right \downarrow ](http://upload.wikimedia.org/wikipedia/zh/math/b/f/8/bf8dd6b753cb6aeb801ea23de51ad5bc.png) |
| 上下箭头                     | \left **\Uparrow** \frac{a}{b} \right **\Downarrow**      | ![\pagecolor{White}\left \Uparrow \frac{a}{b} \right \Downarrow ](http://upload.wikimedia.org/wikipedia/zh/math/3/d/e/3de7822465115ade10b47634c22b6b7d.png) |
| 上下箭头                     | \left **\updownarrow** \frac{a}{b} \right**\Updownarrow** | ![\pagecolor{White}\left \updownarrow \frac{a}{b} \right \Updownarrow](http://upload.wikimedia.org/wikipedia/zh/math/0/6/b/06b15cbba4d935fe84a1a503603e4eb0.png) |
| 混合括号                     | \left [ 0,1 \right ) \left \langle \psi \right \|         | ![\left [ 0,1 \right )](http://upload.wikimedia.org/wikipedia/zh/math/a/3/8/a38771eae1778d0e214f6596a8dc1337.png) ![\left \langle \psi \right \|](http://upload.wikimedia.org/wikipedia/zh/math/d/a/2/da25fc177fd4c53a2c3399c25685dd4c.png) |
| 单左括号                     | \left \{ \frac{a}{b} **\right .**                         | ![\left \{ \frac{a}{b} \right .](http://upload.wikimedia.org/wikipedia/zh/math/c/e/d/ced2a2fb558fe49fa56018b9f8fd69d5.png) |
| 单右括号                     | **\left .** \frac{a}{b} \right \}                         | ![\left . \frac{a}{b} \right \}](http://upload.wikimedia.org/wikipedia/zh/math/9/a/c/9ac9b3c6d21c56f5b2b474b0ea1c4b8a.png) |

备注：

- 可以使用 `\big, \Big, \bigg, \Bigg` 控制括号的大小，比如代码

  **\Bigg** ( **\bigg** [ **\Big** \{**\big**\langle \left | \| \frac{a}{b} \| \right | **\big** \rangle**\Big**\}**\bigg** ] **\Bigg** )

　显示︰

![](http://upload.wikimedia.org/wikipedia/zh/math/9/0/9/90905682eff0186bd5d70a201f4e4538.png)

### 空格

注意TEX能够自动处理大多数的空格，但是您有时候需要自己来控制。

| 功能        | 语法                | 显示                                                         | 宽度                                                         |
| ----------- | ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2个quad空格 | `\alpha\qquad\beta` | ![\alpha\qquad\beta](http://upload.wikimedia.org/wikipedia/zh/math/4/3/b/43ba5910626e8cdd1e7c87f87457bc68.png) | ![2m\ ](http://upload.wikimedia.org/wikipedia/zh/math/d/8/a/d8a3700be4ced531e5618e8bebfece25.png) |
| quad空格    | `\alpha\quad\beta`  | ![\alpha\quad\beta](http://upload.wikimedia.org/wikipedia/zh/math/b/d/b/bdbaa56ab92dbec191da654efcf15f31.png) | ![m\ ](http://upload.wikimedia.org/wikipedia/zh/math/d/4/8/d482d61667a2a8f3a40b25f2626b6d16.png) |
| 大空格      | `\alpha\ \beta`     | ![\alpha\ \beta](http://upload.wikimedia.org/wikipedia/zh/math/8/c/c/8cc37fcc9fc3729b33095484307b65e9.png) | ![\frac{m}{3}](http://upload.wikimedia.org/wikipedia/zh/math/8/b/d/8bd0331723b650fa4e57c0c97ea74bdb.png) |
| 中等空格    | `\alpha\;\beta`     | ![\alpha\;\beta](http://upload.wikimedia.org/wikipedia/zh/math/1/9/a/19aad8cbec4cda349710592cddcbae8a.png) | ![\frac{2m}{7}](http://upload.wikimedia.org/wikipedia/zh/math/d/8/6/d86c7e706d3844153628c5344a0d43c3.png) |
| 小空格      | `\alpha\,\beta`     | ![\alpha\,\beta](http://upload.wikimedia.org/wikipedia/zh/math/5/8/0/580e640ac8bd1b0b421e62a48f9d4815.png) | ![\frac{m}{6}](http://upload.wikimedia.org/wikipedia/zh/math/2/b/2/2b25f9317e1ebccefa69c899bb87f655.png) |
| 没有空格    | `\alpha\beta`       | ![\alpha\beta\ ](http://upload.wikimedia.org/wikipedia/zh/math/c/7/6/c761464c4ea7b0a18e7bd830bc80fc62.png) | ![0\ ](http://upload.wikimedia.org/wikipedia/zh/math/5/7/1/571e19a3fb35a2f712cd608e89b85dc5.png) |
| 紧贴        | `\alpha\!\beta`     | ![\alpha\!\beta](http://upload.wikimedia.org/wikipedia/zh/math/6/d/3/6d331458bfd8a10d0639514187a1eb42.png) | ![-\frac{m}{6}](http://upload.wikimedia.org/wikipedia/zh/math/1/a/4/1a40f481cfa743830b2c80b87a4acccc.png) |

### 颜色

- 语法

- 字体颜色︰`{\color{色调}表达式}`
- 背景颜色︰`{\pagecolor{色调}表达式}`

- 支援色调表

| ![\color{Apricot}\text{Apricot}](http://upload.wikimedia.org/wikipedia/zh/math/b/8/9/b8948aeb7bdca5bd4e18d613ac6c5696.png) | ![\color{Aquamarine}\text{Aquamarine}](http://upload.wikimedia.org/wikipedia/zh/math/f/c/4/fc435c38d6cd34147f1b0562b0e580c0.png) | ![\color{Bittersweet}\text{Bittersweet}](http://upload.wikimedia.org/wikipedia/zh/math/d/6/7/d67b10dd93c2300ee8d13b5099078d1b.png) | ![\color{Black}\text{Black}](http://upload.wikimedia.org/wikipedia/zh/math/3/6/4/364fc160f6c30914ad3d70a6bb551dc6.png) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![\color{Blue}\text{Blue}](http://upload.wikimedia.org/wikipedia/zh/math/5/f/7/5f795126f5d16b97c60578f01b368cd6.png) | ![\color{BlueGreen}\text{BlueGreen}](http://upload.wikimedia.org/wikipedia/zh/math/3/0/2/302ea2ab02b2998679c1f973dfb17395.png) | ![\color{BlueViolet}\text{BlueViolet}](http://upload.wikimedia.org/wikipedia/zh/math/f/7/d/f7d3a6b44f64ec4d9b289bf8ac436d92.png) | ![\color{BrickRed}\text{BrickRed}](http://upload.wikimedia.org/wikipedia/zh/math/a/2/f/a2f94714d1809cb3f71016db0e8c2315.png) |
| ![\color{Brown}\text{Brown}](http://upload.wikimedia.org/wikipedia/zh/math/9/9/c/99cfd151aa2998fb6b309c8c50393c32.png) | ![\color{BurntOrange}\text{BurntOrange}](http://upload.wikimedia.org/wikipedia/zh/math/3/e/3/3e3b04676ace992e28aaa5608455a289.png) | ![\color{CadetBlue}\text{CadetBlue}](http://upload.wikimedia.org/wikipedia/zh/math/f/d/3/fd392c22a7bb76e6203788f0a5e6584b.png) | ![\color{CarnationPink}\text{CarnationPink}](http://upload.wikimedia.org/wikipedia/zh/math/6/0/7/6079cf2eacc794bf3e99bc9fc233e2d0.png) |
| ![\color{Cerulean}\text{Cerulean}](http://upload.wikimedia.org/wikipedia/zh/math/9/7/5/9759c3640f8f5a2cfa5cfa5c4bc64e2f.png) | ![\color{CornflowerBlue}\text{CornflowerBlue}](http://upload.wikimedia.org/wikipedia/zh/math/0/7/2/072ea0cddb81b6996a86c5c60042fc8c.png) | ![\color{Cyan}\text{Cyan}](http://upload.wikimedia.org/wikipedia/zh/math/3/2/1/321ecf031772dbe95758cab0dfaa6f27.png) | ![\color{Dandelion}\text{Dandelion}](http://upload.wikimedia.org/wikipedia/zh/math/7/8/6/78686b75a31528404d2e9b365f892142.png) |
| ![\color{DarkOrchid}\text{DarkOrchid}](http://upload.wikimedia.org/wikipedia/zh/math/1/9/b/19bc495f720e6bb920eed9545880e383.png) | ![\color{Emerald}\text{Emerald}](http://upload.wikimedia.org/wikipedia/zh/math/b/4/3/b4310eecc8d70893a71a728574dc9f0f.png) | ![\color{ForestGreen}\text{ForestGreen}](http://upload.wikimedia.org/wikipedia/zh/math/b/8/9/b89859eb7faadeb40830600590478e6e.png) | ![\color{Fuchsia}\text{Fuchsia}](http://upload.wikimedia.org/wikipedia/zh/math/3/0/7/3073bfb913846b8b74d221b3de291348.png) |
| ![\color{Goldenrod}\text{Goldenrod}](http://upload.wikimedia.org/wikipedia/zh/math/a/f/6/af66a8061a03abb89bc4d205503d437f.png) | ![\color{Gray}\text{Gray}](http://upload.wikimedia.org/wikipedia/zh/math/1/e/5/1e5478f23b28143107d25266b55ef78a.png) | ![\color{Green}\text{Green}](http://upload.wikimedia.org/wikipedia/zh/math/9/4/7/9474b1edd45b5aefe4533543fe85bbbd.png) | ![\pagecolor{Gray}\color{GreenYellow}\text{GreenYellow}](http://upload.wikimedia.org/wikipedia/zh/math/8/f/1/8f10b1195e9646f9ca5fbf15001a5b12.png) |
| ![\color{JungleGreen}\text{JungleGreen}](http://upload.wikimedia.org/wikipedia/zh/math/f/7/2/f72158890930502ffd7dae256812f7e4.png) | ![\color{Lavender}\text{Lavender}](http://upload.wikimedia.org/wikipedia/zh/math/f/2/f/f2fa7339ac0b50f73409f1e05eb77800.png) | ![\color{LimeGreen}\text{LimeGreen}](http://upload.wikimedia.org/wikipedia/zh/math/c/4/c/c4c5d14dea2c682d5f4148eab87e332f.png) | ![\color{Magenta}\text{Magenta}](http://upload.wikimedia.org/wikipedia/zh/math/d/9/b/d9bfd6c63b5b8c21f53e52a74a75eb97.png) |
| ![\color{Mahogany}\text{Mahogany}](http://upload.wikimedia.org/wikipedia/zh/math/d/b/b/dbb2ef205ba8d4d3586b1b9785c54c25.png) | ![\color{Maroon}\text{Maroon}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/6/5861b59a922bda9d96cf03cb8a184a8a.png) | ![\color{Melon}\text{Melon}](http://upload.wikimedia.org/wikipedia/zh/math/e/9/b/e9b605ab8c6a1135ac9bb24e540e645b.png) | ![\color{MidnightBlue}\text{MidnightBlue}](http://upload.wikimedia.org/wikipedia/zh/math/3/c/1/3c196c0de1592080c250b05208cb29c1.png) |
| ![\color{Mulberry}\text{Mulberry}](http://upload.wikimedia.org/wikipedia/zh/math/7/8/f/78fe49c7ffa31d0309ecad4e17c8533b.png) | ![\color{NavyBlue}\text{NavyBlue}](http://upload.wikimedia.org/wikipedia/zh/math/b/5/7/b57ac6d698f2a553d1de298b8ae86f55.png) | ![\color{OliveGreen}\text{OliveGreen}](http://upload.wikimedia.org/wikipedia/zh/math/1/f/f/1ff90d0c4e6d6901579206062701309a.png) | ![\color{Orange}\text{Orange}](http://upload.wikimedia.org/wikipedia/zh/math/1/d/d/1dd73f756801b262f01f87912b369339.png) |
| ![\color{OrangeRed}\text{OrangeRed}](http://upload.wikimedia.org/wikipedia/zh/math/6/d/f/6df4aca479f5fa8acae9c21141636557.png) | ![\color{Orchid}\text{Orchid}](http://upload.wikimedia.org/wikipedia/zh/math/e/0/3/e03e079ac7c0138cc85bb20894e42c7d.png) | ![\color{Peach}\text{Peach}](http://upload.wikimedia.org/wikipedia/zh/math/1/6/a/16a4afaaa911b78f102f2e088c596715.png) | ![\color{Periwinkle}\text{Periwinkle}](http://upload.wikimedia.org/wikipedia/zh/math/1/0/4/104bee7d6969d0403571f7aa65390384.png) |
| ![\color{PineGreen}\text{PineGreen}](http://upload.wikimedia.org/wikipedia/zh/math/5/8/2/5821d738015d4bae29a90be43c9dc760.png) | ![\color{Plum}\text{Plum}](http://upload.wikimedia.org/wikipedia/zh/math/d/e/3/de3328ac78da89a5e86e1917ec8fb87b.png) | ![\color{ProcessBlue}\text{ProcessBlue}](http://upload.wikimedia.org/wikipedia/zh/math/e/2/0/e20ab4232d3130d086b8de76eee6b53c.png) | ![\color{Purple}\text{Purple}](http://upload.wikimedia.org/wikipedia/zh/math/f/e/f/fefd1c1377e3d29213e81e866583adad.png) |
| ![\color{RawSienna}\text{RawSienna}](http://upload.wikimedia.org/wikipedia/zh/math/7/4/5/745d3d1a6a79b318a497e6fbcb57dc02.png) | ![\color{Red}\text{Red}](http://upload.wikimedia.org/wikipedia/zh/math/9/e/2/9e2052c4c91b5216205fe642a06c5ac1.png) | ![\color{RedOrange}\text{RedOrange}](http://upload.wikimedia.org/wikipedia/zh/math/2/a/0/2a026699e64707b449d7c3811d752725.png) | ![\color{RedViolet}\text{RedViolet}](http://upload.wikimedia.org/wikipedia/zh/math/2/a/c/2ac9ad9fbd882591f7971ff477880fe6.png) |
| ![\color{Rhodamine}\text{Rhodamine}](http://upload.wikimedia.org/wikipedia/zh/math/2/7/d/27d615add22f24e5689271903afd2ea8.png) | ![\color{RoyalBlue}\text{RoyalBlue}](http://upload.wikimedia.org/wikipedia/zh/math/6/e/2/6e26c2a826a4d55150b804f2e71444af.png) | ![\color{RoyalPurple}\text{RoyalPurple}](http://upload.wikimedia.org/wikipedia/zh/math/d/d/4/dd4a6069922baf4c048592b1bccef491.png) | ![\color{RubineRed}\text{RubineRed}](http://upload.wikimedia.org/wikipedia/zh/math/7/7/1/771e441e86b10ef3db7b7cb90d9570d1.png) |
| ![\color{Salmon}\text{Salmon}](http://upload.wikimedia.org/wikipedia/zh/math/1/2/0/1204c3c0547f50b71bda5357deba7948.png) | ![\color{SeaGreen}\text{SeaGreen}](http://upload.wikimedia.org/wikipedia/zh/math/e/8/9/e897b90beb4e669c01a63c3d2ac2d954.png) | ![\color{Sepia}\text{Sepia}](http://upload.wikimedia.org/wikipedia/zh/math/e/3/b/e3b0037782599bf00cec26b758627e4b.png) | ![\color{SkyBlue}\text{SkyBlue}](http://upload.wikimedia.org/wikipedia/zh/math/0/3/b/03bc1de1505a991d0f8c2db1a9211740.png) |
| ![\pagecolor{Gray}\color{SpringGreen}\text{SpringGreen}](http://upload.wikimedia.org/wikipedia/zh/math/d/7/4/d747177fd9554cc703043bf3dfeaad7d.png) | ![\color{Tan}\text{Tan}](http://upload.wikimedia.org/wikipedia/zh/math/6/9/7/6975e0f90106c5e304d39dfebc6ad1d0.png) | ![\color{TealBlue}\text{TealBlue}](http://upload.wikimedia.org/wikipedia/zh/math/2/b/1/2b19a41a6ca9691cdbd5fa9f15665d5a.png) | ![\color{Thistle}\text{Thistle}](http://upload.wikimedia.org/wikipedia/zh/math/8/2/8/828c123619d3d3e078aa28bbb362c389.png) |
| ![\color{Turquoise}\text{Turquoise}](http://upload.wikimedia.org/wikipedia/zh/math/e/d/b/edbea9eb14e35cbadb5e2df41afae369.png) | ![\color{Violet}\text{Violet}](http://upload.wikimedia.org/wikipedia/zh/math/8/5/d/85da72dd0a892dd3364fefd94a14cf7c.png) | ![\color{VioletRed}\text{VioletRed}](http://upload.wikimedia.org/wikipedia/zh/math/9/b/5/9b5d2430fd995e45f7974583ab86db0c.png) | ![\pagecolor{Black}\color{White}\text{White}](http://upload.wikimedia.org/wikipedia/zh/math/a/e/b/aebc9cd09371478a17387abbc3b09c82.png) |
| ![\color{WildStrawberry}\text{WildStrawberry}](http://upload.wikimedia.org/wikipedia/zh/math/0/9/6/0962e3794c0315fd26b9668555ebff1c.png) | ![\pagecolor{Gray}\color{Yellow}\text{Yellow}](http://upload.wikimedia.org/wikipedia/zh/math/6/f/0/6f08fd94fdd641d1e7398d6762ae2545.png) | ![\color{YellowGreen}\text{YellowGreen}](http://upload.wikimedia.org/wikipedia/zh/math/7/2/a/72a3756fa95c0da850b33ccb7b3e3900.png) | ![\color{YellowOrange}\text{YellowOrange}](http://upload.wikimedia.org/wikipedia/zh/math/1/1/9/119eb093f2ffcbd3d77a13f55f185f52.png) |

＊注︰输入时第一个字母必需以大写输入，如`\color{OliveGreen}`。

- 例子

- `{\color{Blue}x^2}+{\color{Brown}2x} -{\color{OliveGreen}1}`

![](http://upload.wikimedia.org/wikipedia/zh/math/5/9/7/5974e571c8ac33bee928deb354b8b8ae.png)

- `x_{\color{Maroon}1,2}=\frac{-b\pm\sqrt{{\color{Maroon}b^2-4ac}}}{2a}`

![](http://upload.wikimedia.org/wikipedia/zh/math/8/b/f/8bf49316253beb52d292f7a6d4c075f8.png)



## 小型数学公式

行内公式：![f(x)=5+\frac{1}{5}](http://upload.wikimedia.org/wikipedia/zh/math/2/d/6/2d6f9a27a5f153f3e748db0e38afc071.png) 

- ![✗](http://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/X_mark.svg/20px-X_mark.svg.png)并不好看（太大了，不协调）。

行内公式：![\begin{smallmatrix} f(x)=5+\frac{1}{5} \end{smallmatrix} ](http://upload.wikimedia.org/wikipedia/zh/math/2/b/3/2b3879de51a23d29ce31650594c91cb6.png) 是2。

- ![✓](http://upload.wikimedia.org/wikipedia/commons/thumb/f/fb/Yes_check.svg/20px-Yes_check.svg.png)好看些了。

可以使用

```
   \begin{smallmatrix}...\end{smallmatrix}
```

或直接使用{{[Smallmath](http://zh.wikipedia.org/wiki/Template:Smallmath)}}模板。

```
   {{Smallmath|f=  f(x)=5+\frac{1}{5} }}
```

## 强制使用PNG

假设我们现在需要一个[PNG](http://zh.wikipedia.org/wiki/PNG)图的数学公式。
若输入 `2x=1` 的话︰

![](http://upload.wikimedia.org/wikipedia/zh/math/e/2/4/e24b7cbea4ac6526c7bf9687d7e61516.png)

 　**↑** 这并不是我们想要的。

若你需要强制输出一个PNG图的数学公式的话，你可于公式的最后加上`\,`（小空格，但于公式的最后是不会显示出来）。

![](http://upload.wikimedia.org/wikipedia/zh/math/2/3/7/237b4b59d46cd7db3d26d6c700550bbe.png)

 　**↑** 以PNG图输出。

你也可以使用 `\,\!`，这个亦能强制使用PNG图像。

　　阅读更多︰[Help:Displayinga formula#Forced PNG rendering](http://en.wikipedia.org/wiki/Help:Displaying_a_formula#Forced_PNG_rendering)