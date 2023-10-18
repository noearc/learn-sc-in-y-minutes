
SuperCollider is a platform for audio synthesis and algorithmic composition, used by musicians, artists and researchers working with sound.

And sclang is an object-oriented language for sound synthesis and digital signal processing (DSP). 
The sclang part is a sophisticated programming language with nice features for building GUIs (Graphical User Interfaces); and the server part is a lean, mean, efficient UNIX command line application (meaning it runs without any GUI representation).

They communicate by a protocol called OSC (Open Sound Control), over either UDP (User Datagram Protocol) or TCP (Transmission Control Protocol


# Essential keystrokes to get started
Ctrl-Enter
Shift-Enter
编辑器快捷键：

Ctrl + Enter (Windows) / Cmd + Enter (Mac)：运行选中的代码块或当前行。

Ctrl + D (Windows) / Cmd + D (Mac)：停止所有音频处理和服务器。

Ctrl + . (Windows) / Cmd + . (Mac)：中断运行中的代码。

代码编辑快捷键：

Ctrl + / (Windows) / Cmd + / (Mac)：注释/取消注释选中的行。

Ctrl + I (Windows) / Cmd + I (Mac)：自动缩进选中的代码块。

Ctrl + [ (Windows) / Cmd + [ (Mac)：向左缩进选中的代码块。

Ctrl + ] (Windows) / Cmd + ] (Mac)：向右缩进选中的代码块。

帮助和文档快捷键：

Ctrl + H (Windows) / Cmd + H (Mac)：打开 Help 浏览器。

Ctrl + Shift + D (Windows) / Cmd + Shift + D (Mac)：显示函数文档。

Ctrl + Shift + B (Windows) / Cmd + Shift + B (Mac)：在浏览器中查找当前选中的文本。

# Basic Syntax

``` supercollider
// this is a comment
/*
this is a multi-line comment
*/

// statements must end with semicolumn
{ [SinOsc.ar(440, 0, 0.2), SinOsc.ar(442, 0, 0.2)] }.play;

// variable declaration: use camelCase and start with ~
// single letter variable are fine but aviod s (reserved for server)
(
    a = 4;
    a = 4.squared; // predeclared in 'global' (actually environment) namespace 
    ~num = 4;
    ~num = ~num.squared;
)

```

# methods
``` supercollider
~square.value(4);
value(~square, 4);

``` 

# Data types and operaters

``` supercollider
1.class; => Integer

1.0.class; => Float

true.class; => Boolean

"my string".class; => String // used for out IO

'my string'.class; => Symbol // same name, same object, used as tags and names
\mySymbol.class; => Symbol // same name, same object, used as tags and names

3+3*7 => 42 // no operater precedence!
3+(3*7) => 24 // bracket to the rescue

true.not; // not is a method.
3 < 4 || 3.1 > -1; => error 
3 < 4 || (3.1 > -1); => true
(3 < 4) && (3.1 < -1); => false

a = [1,2,3]; // Basic array, fixed size
a.maxSize; // always a power of 2
b = #[1,2,3]; // Immutable array, more efficient.
c = List[1,2,3]; // List, more flexible, but more overhead  
d = a++b;
e = a++c;

```

# Contorl flow

``` supercollider

// if statements: if (expr, trueFunc, falseFunc);
a = 2
z = if (a<5, {100},{200});
=> z = 100

// While loops: while({<test function>}, {<body function>})
i = 0;
while({i < 4}, {i.postln; i = i + 1});

// for loops: for(startValue, endValue, {<body function>})
// forBy loops: forBy ( startValue, endValue, stepValue, function );
for(3, 6, {arg x; x.postln;}); => 3,4,5,6 //endValue inclusive
forBy(3, 6, 2, {arg x; x.postln;}); => 3,5 //endValue inclusive

// do block: do ( collection, function )
// or more commonly: collection.do(function)

[ 1, 2, "abc", (3@4) ].do({ arg item, i; [i, item].postln; });
(8..20).do({ arg item; item.postln }); // iterates from eight to twenty
(8,10..20).do({ arg item; item.postln }); // iterates from eight to twenty, with stepsize two

// switch

// case

``` 
# Function

``` supercollider
// ues value method(?) to access function return value
f = {2+3};
f.value;

// arguments in function
g = {arg a, b; a.value + b.value};
// or this syntactic suger, quite like ruby(?)
g = {|a, b| a.value + b.value}

// two style of function application
g.value(3,4);
g.value(b: 4, a: 3);

// pass funtion into functions 
h = {3.0.rand;};
g.value(h,h);

// variables's scope is the function where it is declared
f = { var foo; foo = 3; foo; };
f.value;
foo;            // this will cause an error as 'foo' is only valid within f.

// functions's scope is a () region
(
var myFunc;
myFunc = { |input| input.postln; };
myFunc.value("foo");    // arg is a String
myFunc.value("bar");
)

myFunc;            // throws an error

// make a factorial function and call it
(
~fact = {
	|x|
	if (x == 0, {1}, {x * ~fact.value(x-1)});
};

a = ~fact.value(6);
)

```

# UGens
s.quit;
s.boot;
# Synth and SynthDef
``` supercollider
SynthDef.new(\bpfsaw,{
    arg atk=2,sus=0,rel=3,c1=1,c2=(-1),
    freq=500,detune=0.2,pan=0,amp=1,out=0,cfmin=500,cfmax=5000,rqmin=0.1,rqmax=0.2,lsf=200,ldb=0,cfhzmin=0.1,cfhzmax=0.3;
    var sig , env;
    env = EnvGen.kr(Env([0,1,1,0],[atk,sus,rel],[c1,0,c2]),doneAction:2);
    sig = Saw.ar(freq*{LFNoise1.kr(0.5,detune).midiratio}!2);
    sig = BPF.ar(
        sig,
        {LFNoise1.kr(
            LFNoise1.kr(4).exprange(cfhzmin,cfhzmax)
        ).exprange(cfmin,cfmax)}!2,
        {LFNoise1.kr(0.2).exprange(rqmin,rqmax)}!2
    );
    sig = BLowShelf.ar(sig,lsf,0.5,ldb);
    sig = Balance2.ar(sig[0],sig[1],pan);
    sig = sig * env * amp;
    Out.ar(out,sig);
}).add;

Synth(\bpfsaw,[,])
```

# Pattern



# Resources
[Sccode](https://sccode.org/)
[Wiki](https://github.com/supercollider/supercollider/wiki)
[Forum](https://scsynth.org/)
[Slack](https://scsynth.slack.com/)
[Documentation](https://doc.sccode.org/)
[SC tweets - tunes under 140 characters](https://twitter.com/search?q=supercollider+play)
[Rosetta Code](http://rosettacode.org/wiki/Category:SuperCollider)
