# MapReduce


### How Many Steps?

<p class="fragment">Map</p>
<p class="fragment">Reduce</p>
<p class="fragment">Partition</p>
<p class="fragment">Comparison</p>
<p class="fragment">Input</p>
<p class="fragment">Output</p>


Let's ignore Input / Output, and combine Partition and Comparison into "Shuffle"


### Map

Input -> KV


### Shuffle (Partition + Comparison)

[K<sub>1</sub>V<sub>1</sub>, K<sub>2</sub>V<sub>2</sub>, K<sub>1</sub>V<sub>3</sub>] -> [K<sub>1</sub>V<sub>1</sub>,  K<sub>1</sub>V<sub>3</sub>], [K<sub>2</sub>V<sub>2</sub>]


### Reduce

[K<sub>1</sub>V<sub>1</sub>,  K<sub>1</sub>V<sub>3</sub>] -> K<sub>1</sub>R<sub>1</sub>


But that doesn't really explain it for most people

Let's make the variables mean something


# Word Count


### Problem

For a given text, how many times does each word appear?


### Corpus
<pre>
<code class="lang-text" style="text-align:center;">
Work It Harder, Make It Better
Do It Faster, Makes Us Stronger
More Than Ever Hour After
Our Work Is Never Over
</code>
</pre>


### Map

Text comes in

<pre>
<code class="lang-text" style="text-align:center;">
(work,1)
(it,1)
(harder,1)
(make,1)
(it,1)
...
</code>
</pre>

comes out


### Shuffle

<pre>
<code class="lang-text" style="text-align:center;">
(after,1)
(do,1)
(ever,1)
(hour,1)
(it,1),(it,1),(it,1)
(is,1)
...
</code>
</pre>


### Reduce

<pre>
<code class="lang-text" style="text-align:center;">
(after,1)
(do,1)
(ever,1)
(hour,1)
(it,3)
(is,1)
...
</code>
</pre>
