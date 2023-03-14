# micropython_save_config_sample

<div class="content">
Following some examples that I found on line for future reference:<br>
<br>
Write data to a file:
<div class="codebox"><pre><code>import json

config = {'key1': 'value1', 'key2': 'value2'}

f = open('config.json', 'w')
f.write(ujson.dumps(config))
f.close()
</code></pre></div>

Read data from a file:
<div class="codebox"><pre><code>import json

f = open('config.json', 'r')
c = ujson.loads(f.readall())

#edit the data
config['key3'] = 'value3'

#write it back to the file
f = open('config.json', 'w')
f.write(ujson.dumps(config))
f.close()
 </code></pre></div>

Some more info about nested dictionaries:
 <div class="codebox"><pre><code>&gt;&gt;&gt; d = {}
&gt;&gt;&gt; d['dict1'] ={}
&gt;&gt;&gt; d['dict2'] ={}
&gt;&gt;&gt; d['dict1']['innerkey1'] = 'value1'
&gt;&gt;&gt; d['dict2']['innerkey2'] = 'value2'
&gt;&gt;&gt; d
{'dict2': {'innerkey2': 'value2'}, 'dict1': {'innerkey1': 'value1'}}
&gt;&gt;&gt; 
</code></pre></div>
