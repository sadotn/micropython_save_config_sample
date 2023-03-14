# micropython_save_config_sample
Source: https://stackabuse.com/reading-and-writing-json-to-a-file-in-python/

<div class="content">
Following some examples that I found on line for future reference:<br>
<br>
Write data to a file:
<div class="codebox"><pre><code>import ujson as js

config = {'key1': 'value1', 'key2': 'value2'}

f = open('config.json', 'w')
f.write(js.dumps(config))
f.close()</code></pre></div>

Read data from a file:
<div class="codebox"><pre><code>import json as js

f = open('config.json', 'r')
c = js.loads(f.read())

#edit the data
config['key3'] = 'value3'

#write it back to the file
f = open('config.json', 'w')
f.write(js.dumps(config))
f.close() </code></pre></div>

Some more info about nested dictionaries:
 <div class="codebox"><pre><code>&gt;&gt;&gt; d = {}
&gt;&gt;&gt; d['dict1'] ={}
&gt;&gt;&gt; d['dict2'] ={}
&gt;&gt;&gt; d['dict1']['innerkey1'] = 'value1'
&gt;&gt;&gt; d['dict2']['innerkey2'] = 'value2'
&gt;&gt;&gt; d
{'dict2': {'innerkey2': 'value2'}, 'dict1': {'innerkey1': 'value1'}}
&gt;&gt;&gt; </code></pre></div>

 
<div class="content"><noscript><style>.lazyload-placeholder { display: none; }</style></noscript><h3 id="introduction">Introduction</h3>
<blockquote>
<p>In this guide, we'll take a look at how to read and write JSON data from and to a file in Python, using the <code>json</code> module.</p>
</blockquote>
<p><a rel="nofollow" target="_blank" href="https://en.wikipedia.org/wiki/JSON"><em>JSON (JavaScript Object Notation)</em></a> is an extremely popular format for data serialization, given how generally applicable and lightweight it is - while also being fairly human-friendly. Most notably, it's extensively used in the web development world, where you'll likely encounter JSON-serialized objects being sent from <em>REST APIs</em>, application configuration, or even simple data storage.</p>

<p><strong>Advice:</strong> If you'd like to read more about creating REST APIs with Python, read our <a target="_blank" href="https://stackabuse.com/creating-a-rest-api-in-python-with-django/"><em>"Creating a REST API in Python with Django"</em></a> and <a target="_blank" href="https://stackabuse.com/creating-a-rest-api-with-django-rest-framework/"><em>"Creating a REST API with Django REST Framework"</em></a>!</p>

 <p>Given its prevalence, reading and parsing JSON files (or strings) is pretty common, and writing JSON to be sent off is equally as common. In this guide - we'll take a look at how to leverage the <code>json</code> module to read and write JSON in Python.</p>
 
<h3 id="writingjsontoafilewithpythonwithjsondumpandjsondumps">Writing JSON to a File with Python with json.dump() and json.dumps()</h3>
<p>To write JSON contents to a file in Python - we can use <code>json.dump()</code> and <code>json.dumps()</code>. These are <em>separate methods</em> and achieve different result:</p>
<ul>
<li><code>json.dumps()</code> - Serializes an object into a JSON-formatted <em>string</em></li>
<li><code>json.dump()</code> - Serialized an object into a JSON stream for  saving into files or sockets</li>
</ul>
<div class="alert alert-note">
    <div class="flex">
        <p><strong>Note:</strong> The "s" in "dumps" is actually short for <em>"dump string"</em>.</p>
    </div>
</div>
 
<div id="ad-snigel-1"><div class=" my-4 flex justify-center" style="min-height:250px"><div id="adngin-incontent1-0"></div></div></div><p>JSON's natural format is similar to a <em>map</em> in computer science - a map of <code>key-value</code> pairs. In Python, a <em>dictionary</em> is a map implementation, so we'll naturally be able to represent JSON faithfully through a <code>dict</code>. A dictionary can contain other nested dictionaries, arrays, booleans, or other primitive types like integers and strings.</p>
<p>:::</p>
<p><strong>Note:</strong> The built-in <a rel="nofollow noopener noreferrer" target="_blank" href="https://docs.python.org/3/library/json.html">json</a> package offers several convenience methods that allows us to convert between JSON and dictionaries.</p>
<p>:::</p>
<p>That being said, let's import the <code>json</code> module, define a dictionary with some data and then convert it into JSON before saving to a file:</p>
<pre><code class="hljs"><span class="hljs-keyword">import</span> json

data = {
    <span class="hljs-string">'employees'</span> : [
        {
            <span class="hljs-string">'name'</span> : <span class="hljs-string">'John Doe'</span>,
            <span class="hljs-string">'department'</span> : <span class="hljs-string">'Marketing'</span>,
            <span class="hljs-string">'place'</span> : <span class="hljs-string">'Remote'</span>
        },
        {
            <span class="hljs-string">'name'</span> : <span class="hljs-string">'Jane Doe'</span>,
            <span class="hljs-string">'department'</span> : <span class="hljs-string">'Software Engineering'</span>,
            <span class="hljs-string">'place'</span> : <span class="hljs-string">'Remote'</span>
        },
        {
            <span class="hljs-string">'name'</span> : <span class="hljs-string">'Don Joe'</span>,
            <span class="hljs-string">'department'</span> : <span class="hljs-string">'Software Engineering'</span>,
            <span class="hljs-string">'place'</span> : <span class="hljs-string">'Office'</span>
        }
    ]
}

<span class="hljs-comment"># .dumps() as a string</span>
json_string = json.dumps(data)
<span class="hljs-built_in">print</span>(json_string)
</code></pre>
<p>This results in:</p>
<pre><code class="hljs">{'employees': [{'name': 'John Doe', 'department': 'Marketing', 'place': 'Remote'}, {'name': 'Jane Doe', 'department': 'Software Engineering', 'place': 'Remote'}, {'name': 'Don Joe', 'department': 'Software Engineering', 'place': 'Office'}]}
</code></pre>
<p>Here, we have a simple dictionary with a few <code>employees</code>, each of which has a <code>name</code>, <code>department</code> and <code>place</code>. The <code>dumps()</code> function of the <code>json</code> module <em>dumps</em> a dictionary into JSON contents, and returns a <em>JSON string</em>.</p>
<p>Once serialized, you may decide to send it off to another service that'll deserialize it, or, say, store it. To store this JSON string into a file, we'll simply open a file in write mode, and write it down. If you don't want to extract the data into an independent variable for later use and would just like to dump it into a file, you can skip the <code>dumps()</code> function and use <code>dump()</code> instad:</p>
<div id="ad-snigel-2"><div class=" my-4 flex justify-center" style="min-height:250px"><div id="adngin-incontent2-0"></div></div></div><pre><code class="hljs"><span class="hljs-comment"># Using a JSON string</span>
<span class="hljs-keyword">with</span> <span class="hljs-built_in">open</span>(<span class="hljs-string">'json_data.json'</span>, <span class="hljs-string">'w'</span>) <span class="hljs-keyword">as</span> outfile:
    outfile.write(json_string)

<span class="hljs-comment"># Directly from dictionary</span>
<span class="hljs-keyword">with</span> <span class="hljs-built_in">open</span>(<span class="hljs-string">'json_data.json'</span>, <span class="hljs-string">'w'</span>) <span class="hljs-keyword">as</span> outfile:
    json.dump(json_string, outfile)
</code></pre>
<p>Any file-like object can be passed to the second argument of the <code>dump()</code> function, even if it isn't an actual file. A good example of this would be a socket, which can be opened, closed, and written to much like a file.</p>
<h3 id="readingjsonfromafilewithpythonwithjsonloadandjsonloads">Reading JSON from a File with Python with json.load() and json.loads()</h3>
<p>The mapping between dictionary contents and a JSON string is straightforward, so it's easy to convert between the two. The same logic as with <code>dump()</code> and <code>dumps()</code> is applied to <code>load()</code> and <code>loads()</code>. Much like <code>json.dumps()</code>, the <code>json.loads()</code> function accepts a JSON string and converts it into a dictionary, while <code>json.load()</code> lets you load in a file:</p>
<pre><code class="hljs"><span class="hljs-keyword">import</span> json

<span class="hljs-keyword">with</span> <span class="hljs-built_in">open</span>(<span class="hljs-string">'json_data.json'</span>) <span class="hljs-keyword">as</span> json_file:
    data = json.load(json_file)
    <span class="hljs-built_in">print</span>(data)
</code></pre>
<p>This results in:</p>
<pre><code class="hljs">{'employees': [{'name': 'John Doe', 'department': 'Marketing', 'place': 'Remote'}, {'name': 'Jane Doe', 'department': 'Software Engineering', 'place': 'Remote'}, {'name': 'Don Joe', 'department': 'Software Engineering', 'place': 'Office'}]}
</code></pre>
<p>Alternatively, let's read a JSON string into a dictionary:</p>
<pre><code class="hljs"><span class="hljs-keyword">import</span> json

python_dictionary = json.loads(json_string)
<span class="hljs-built_in">print</span>(python_dictionary)
</code></pre>
<p>Which also results in:</p>
<pre><code class="hljs">{'employees': [{'name': 'John Doe', 'department': 'Marketing', 'place': 'Remote'}, {'name': 'Jane Doe', 'department': 'Software Engineering', 'place': 'Remote'}, {'name': 'Don Joe', 'department': 'Software Engineering', 'place': 'Office'}]}
</code></pre>
<p>This one is especially useful for parsing REST API responses that send JSON. This data comes to you as a string, which you can then pass to <code>json.loads()</code> directly, and you have a much more manageable dictionary to work with!</p>
<h3 id="sortingprettyprintingseparatorsandencoding">Sorting, Pretty-Printing, Separators and Encoding</h3>
<p>When serializing your data to JSON with Python, the standard format aiming to minimize the required memory to transmit messages is not very readable since whitespaces are eliminated. While this is the ideal behavior for data transfer (computers don't care for readability, but do care about size) - sometimes you may need to make small changes, like adding whitespace to make it human readable.</p>

 <div class="alert alert-note">
     <div class="flex">
        <p><strong>Note:</strong> <code>json.dump()</code>/<code>json.dumps()</code> and <code>json.load()</code>/<code>json.loads()</code> all provide a few options formatting.</p>
     </div>
</div>
<h4 id="prettyprintingjsoninpython">Pretty-Printing JSON in Python</h4>
<p>Making JSON human readable (aka <em>"pretty-printing"</em>) is as easy as passing an integer value for the <code>indent</code> parameter:</p>
<pre><code class="hljs"><span class="hljs-keyword">import</span> json
data = {<span class="hljs-string">'people'</span>:[{<span class="hljs-string">'name'</span>: <span class="hljs-string">'Scott'</span>, <span class="hljs-string">'website'</span>: <span class="hljs-string">'stackabuse.com'</span>, <span class="hljs-string">'from'</span>: <span class="hljs-string">'Nebraska'</span>}]}
<span class="hljs-built_in">print</span>(json.dumps(data, indent=<span class="hljs-number">4</span>))
</code></pre>
<p>This creases a 4-space indentation on each new logical block:</p>
<pre><code class="hljs">{
    "people": [
        {
            "website": "stackabuse.com", 
            "from": "Nebraska", 
            "name": "Scott"
        }
    ]
}
</code></pre>
<p>Another option is to use the command line tool - <code>json.tool</code>. With it, you can pretty-print the JSON in the command line without affecting the transmitted string, and just impacting how it's displayed on the standard output pipe:</p>
<pre><code class="hljs"><span class="hljs-meta">$</span><span class="bash"> <span class="hljs-built_in">echo</span> <span class="hljs-string">'{"people":[{"name":"Scott", "website":"stackabuse.com", "from":"Nebraska"}]}'</span> | python -m json.tool</span>
{
    "people": [
        {
            "name": "Scott",
            "website": "stackabuse.com"
            "from": "Nebraska",
        }
    ]
}
</code></pre>
<h4 id="sortingjsonobjectsbykeys">Sorting JSON Objects by Keys</h4>
<p>In JSON terms:</p>
<blockquote>
<p>"An object is an unordered set of name/value pairs."</p>
</blockquote>
<p>The key order isn't guaranteed, but it's possible that you may need to enforce key order. To achieve ordering, you can pass <code>True</code> to the <code>sort_keys</code> option when using <code>json.dump()</code> or <code>json.dumps()</code>:</p>
<pre><code class="hljs"><span class="hljs-keyword">import</span> json
data = {<span class="hljs-string">'people'</span>:[{<span class="hljs-string">'name'</span>: <span class="hljs-string">'Scott'</span>, <span class="hljs-string">'website'</span>: <span class="hljs-string">'stackabuse.com'</span>, <span class="hljs-string">'from'</span>: <span class="hljs-string">'Nebraska'</span>}]}
<span class="hljs-built_in">print</span>(json.dumps(data, sort_keys=<span class="hljs-literal">True</span>, indent=<span class="hljs-number">4</span>))
</code></pre>
<p>This results in:</p>
<pre><code class="hljs">{
    "people": [
        {
            "from": "Nebraska",
            "name": "Scott",
            "website": "stackabuse.com"
        }
    ]
}
</code></pre>
<h4 id="asciitextandencoding">ASCII Text and Encoding</h4>
<p>By default, <code>json.dump()</code> and <code>json.dumps()</code> will ensure that text in the given Python dictionary is ASCII-encoded. If non-ASCII characters are present, then they're automatically escaped, as shown in the following example:</p>
<div id="ad-snigel-3"><div class=" my-4 flex justify-center" style="min-height:250px"><div id="adngin-incontent3-0"></div></div></div><pre><code class="hljs"><span class="hljs-keyword">import</span> json
data = {<span class="hljs-string">'item'</span>: <span class="hljs-string">'Beer'</span>, <span class="hljs-string">'cost'</span>:<span class="hljs-string">'£4.00'</span>}
jstr = json.dumps(data, indent=<span class="hljs-number">4</span>)
<span class="hljs-built_in">print</span>(jstr)
</code></pre>
<pre><code class="hljs">{
    "item": "Beer",
    "cost": "\u00a34.00"
}
</code></pre>
<p>This isn't always acceptable, and in many cases you may want to keep your Unicode characters unchanged. To do this, set the <code>ensure_ascii</code> option to <code>False</code>:</p>
<pre><code class="hljs">jstr = json.dumps(data, ensure_ascii=<span class="hljs-literal">False</span>, indent=<span class="hljs-number">4</span>)
<span class="hljs-built_in">print</span>(jstr)
</code></pre>
<pre><code class="hljs">{
    "item": "Beer",
    "cost": "£4.00"
}
</code></pre>
<h4 id="skipcustomkeydatatypes">Skip Custom Key Data Types</h4>
<p>If a key in your dictionary is of a non-primitive type (<code>str</code>, <code>int</code>, <code>float</code>, <code>bool</code> or <code>None</code>), a <code>TypeError</code> is raised when you try dumping JSON contents into a file. You can skip these keys via the <code>skipkeys</code> argument:</p>
<pre><code class="hljs">jstr = json.dumps(data, skipkeys=<span class="hljs-literal">True</span>)
</code></pre>
<h4 id="enablinganddisablingcircularcheck">Enabling and Disabling Circular Check</h4>
<p>If a property of a JSON object references itself, or another object that references back the parent object - an infinitely recursive JSON is created. Infinite recursion typically results in memory being allocated rapidly until a device runs out of memory, and in the case of dumping JSON, a <code>RecursionError</code> is raised and the dumping is halted.</p>
<p>This is regulated by the <code>check_circular</code> flag, which is <code>True</code> by default, and prevents possible issues when writing circular dependencies. To turn it off, you can set it to `False:</p>
<pre><code class="hljs">jstr = json.dumps(data, check_circular=<span class="hljs-literal">False</span>)
</code></pre>
<p>Do note, however, that this is <em>highly</em> not recommended.</p>
<h4 id="enablinganddisablingnans">Enabling and Disabling NaNs</h4>
<p>NaN-values, such as <code>-inf</code>, <code>inf</code> and <code>nan</code> may creep into objects that you want to serialize or deserialize. JSON standard <em>doesn't allow</em> for NaN values, but they still carry logical value that you might want to transmit in a message. On another hand - you may want to enforce that NaN values <em>aren't</em> transmitted, and raise an exception instead. The <code>allow_nan</code> flag is set to <code>True</code> by default, and allows you to serialize and deserialize NaN values, replacing them with the JavaScript equivalents (<code>Inifinity</code>, <code>-Infinity</code> and <code>NaN</code>).</p>
<p>If you set the flag to <code>False</code> instead - you'll switch to a strictly JSON-standardized format, which raises a <code>ValueError</code> if your objects contain attributes with these values:</p>
<div id="ad-snigel-4"><div class=" my-4 flex justify-center" style="min-height:250px"><div id="adngin-incontent4-0"></div></div></div><pre><code class="hljs">jstr = json.dumps(data, allow_nan=<span class="hljs-literal">False</span>)
</code></pre>
<h4 id="changingseparators">Changing Separators</h4>
<p>In JSON, the keys are separated from values with colons (<code>:</code>) and the items are separated from each other with commas (<code>,</code>):</p>
<pre><code class="hljs">key1:value1,
key2:value2
</code></pre>
<p>The default separators for reading and writing JSON in Python is <code>(', ', ': ')</code> with <em>whitespaces</em> after the commas and colons. You can alter these to skip the whitespaces and thus make the JSON a bit more compact, or fully change the separators with other special characters for a different representation:</p>
<pre><code class="hljs"><span class="hljs-comment"># Updated to not contain whitespaces after separators</span>
jstr = json.dumps(data, separators=(<span class="hljs-string">','</span>, <span class="hljs-string">':'</span>))
</code></pre>
<h3 id="compatibilityissueswithpython2">Compatibility Issues with Python 2</h3>
<p>If you're using an older version of Python (2.x) - you may run into a <code>TypeError</code> while trying to dump JSON contents into a file. Namely, if the contents contain a non-ASCII character, a <code>TypeError</code> is raised, <em>even if</em> you pass the encoding argument, when using the <code>json.dump()</code> method:</p>
<pre><code class="hljs"><span class="hljs-comment"># Python 2.x</span>
<span class="hljs-keyword">with</span> <span class="hljs-built_in">open</span>(<span class="hljs-string">'json_data.json'</span>, <span class="hljs-string">'w'</span>, encoding=<span class="hljs-string">'utf-8'</span>) <span class="hljs-keyword">as</span> outfile:
    json.dump(json_string, outfile, ensure_ascii=<span class="hljs-literal">False</span>)
</code></pre>
<p>If you encounter this edge-case, which has since been fixed in subsequent Python versions - try using <code>json.dumps()</code> instead, and write the string contents into a file instead of streaming the contents directly into a file.</p>
<h3 id="conclusion">Conclusion</h3>
<p>In this guide, we introduced you to the <code>json.dump()</code>, <code>json.dumps()</code>, <code>json.load()</code>, and <code>json.loads()</code> methods, which help in serializing and deserializing JSON strings.</p>
<p>We've then taken a look at how you can sort JSON objects, pretty-print them, change the encoding, skip custom key data types, enable or disable circular checks and whether NaNs are allowed, as well as how to change the separators for serialization and deserialization.</p>
<p>With JSON having being one of the most popular ways to serialize structured data, you'll likely have to interact with it pretty frequently, especially when working on web applications. Python's <code>json</code> module is a great way to get started, although you'll probably find that <a rel="nofollow" target="_blank" href="https://github.com/simplejson/simplejson">simplejson</a> is another great alternative that is much less strict on JSON syntax.</p>
</div>
