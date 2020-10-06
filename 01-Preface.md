# Preface

C++ 最佳实践：一个可复制的代码标准文档


本文档旨在对 C++ 的最佳实践进行商讨。 它对 *Effective C ++*（Meyers）和 *C++编码标准*（Alexandrescu，Sutter）等书作了补充。 我们填补了一些他们没有讨论的较低级别的详细信息，并提供了具体的样式建议，同时还讨论了如何确保整体的代码质量。

在所有情况下，简洁都是首选。 为了举例说明为什么一个选项比另一个选项更可取，示例是优选的。 如有必要，将使一些用文字。


<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">C++ Best Practices</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://cppbestpractices.com" property="cc:attributionName" rel="cc:attributionURL">Jason Turner</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

*免责声明*

本文档基于我的个人经验。 您不应100％同意。 它作为一本书存在于[GitHub](https://github.com/lefticus/cppbestpractices)上，以便您可以复制用于自己的用途或提交建议的更改供所有人共享。

本书激发了奥莱理（O'Reilly）视频的灵感：[学习C ++最佳实践](http://shop.oreilly.com/product/0636920049814.do)

*翻译声明*

基于个人兴趣翻译 C++ 最佳实践，原文版权归于作者 Jason Turner，本人根据自己的理解将其翻译为中文，并未逐字逐句遵循原文，个别地方为便于理解，有所更改。

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type"> C++ 最佳实践中文版</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://cppbestpractices.com" property="cc:attributionName" rel="cc:attributionURL">Walter Fan </a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">知识共享署名-非商业性使用 4.0 国际许可协议</a>进行许可。