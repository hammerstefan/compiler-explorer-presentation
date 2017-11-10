<pre style="font-size:20px">
<code class="c++" data-noescape>
class <span style="font-size:80px">COMPILER EXPLORER</span> 
{
public:
    std::string author = "<span style="font-size:50px">Stefan Hammer</span>";<br>
    std::string date   = "<span style="font-size:50px">November 10, 2017</span>";

private:
    virtual void show_godbolt() noexcept;
    virtual void teach_asm() noexcept;
    virtual void live_demo(); //throws
<span style="display:inline-block;position:relative;background:url(/data/images/squig.gif) bottom repeat-x;">}</span>
</code>
</pre>

<small><a href="http://shammer-linux.adtran.com:8001"/>shammer-linux.adtran.com:8001</small>



## What is Compiler Explorer
Notes: CppCon2017 talk. Matt Godbolt.


## What is Compiler Explorer
### The Godbolt 			<!--.element: class="fragment"-->
* Compiler 						<!--.element: class="fragment"-->
* Investigation tool 	<!--.element: class="fragment"-->
* Learning aid 				<!--.element: class="fragment"-->

[http://godbolt.org](http://godbolt.org)
Notes:
CE is a web based tool that wraps compiler, investigation tool, and learning aid into one package.


## Compiler
Notes: 
gcc, cppx, d, swift, haskell, go, ispc.<br>
all available versions, historical, beta, nightly


## Investigation tool
Notes:
Ever wondered about the difference between two algorithms?
Been concerned about the overhead in new C++ features?
Curious about alternative compilers (CLang vs GCC)?


## Learning
Notes:
Learn ASM. Learn how compilers work. Learn C++ features.<br>
More on this later.
