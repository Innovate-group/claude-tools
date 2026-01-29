# Twig Quick Reference

## Tags

| Tag | Purpose | Example |
|-----|---------|---------|
| `{% if %}` | Conditional | `{% if x %}...{% endif %}` |
| `{% for %}` | Loop | `{% for i in items %}...{% endfor %}` |
| `{% set %}` | Variable | `{% set x = 1 %}` |
| `{% block %}` | Define block | `{% block name %}...{% endblock %}` |
| `{% extends %}` | Inherit template | `{% extends 'base.tpl' %}` |
| `{% include %}` | Include template | `{% include 'file.tpl' %}` |
| `{% embed %}` | Include + override | `{% embed 'file.tpl' %}...{% endembed %}` |
| `{% macro %}` | Define function | `{% macro name() %}...{% endmacro %}` |
| `{% import %}` | Import macros | `{% import 'macros.tpl' as m %}` |
| `{% from %}` | Import specific | `{% from 'macros.tpl' import x %}` |
| `{% use %}` | Import blocks | `{% use 'blocks.tpl' %}` |
| `{% apply %}` | Apply filter | `{% apply upper %}...{% endapply %}` |
| `{% autoescape %}` | Control escaping | `{% autoescape false %}...{% endautoescape %}` |
| `{% verbatim %}` | Raw output | `{% verbatim %}{{ literal }}{% endverbatim %}` |
| `{% do %}` | Execute without output | `{% do items.append(x) %}` |
| `{% flush %}` | Flush output | `{% flush %}` |

## Output

```twig
{{ variable }}              {# Print variable #}
{{ variable | filter }}     {# Print with filter #}
{{ function() }}            {# Print function result #}
{# This is a comment #}     {# Comment (not rendered) #}
```

## Whitespace Control

```twig
{{- variable -}}    {# Trim both sides #}
{%- tag -%}         {# Trim both sides #}
{{- variable }}     {# Trim left only #}
{{ variable -}}     {# Trim right only #}
```

## Common Filters

| Filter | Purpose | Example |
|--------|---------|---------|
| `escape` / `e` | HTML escape | `{{ x \| e }}` |
| `raw` | No escape | `{{ html \| raw }}` |
| `default` | Default value | `{{ x \| default('N/A') }}` |
| `join` | Join array | `{{ arr \| join(', ') }}` |
| `split` | Split string | `{{ str \| split(',') }}` |
| `length` | Count | `{{ arr \| length }}` |
| `first` / `last` | First/last item | `{{ arr \| first }}` |
| `slice` | Substring/subarray | `{{ arr \| slice(0, 3) }}` |
| `merge` | Merge arrays | `{{ arr \| merge([x]) }}` |
| `keys` | Get keys | `{{ obj \| keys }}` |
| `sort` | Sort array | `{{ arr \| sort }}` |
| `reverse` | Reverse | `{{ arr \| reverse }}` |
| `upper` / `lower` | Case change | `{{ str \| upper }}` |
| `title` / `capitalize` | Title case | `{{ str \| title }}` |
| `trim` | Trim whitespace | `{{ str \| trim }}` |
| `striptags` | Remove HTML | `{{ html \| striptags }}` |
| `nl2br` | Newline to `<br>` | `{{ text \| nl2br }}` |
| `replace` | Replace | `{{ str \| replace({'a': 'b'}) }}` |
| `date` | Format date | `{{ d \| date('Y-m-d') }}` |
| `number_format` | Format number | `{{ n \| number_format(2) }}` |
| `json_encode` | To JSON | `{{ obj \| json_encode }}` |
| `batch` | Group items | `{{ arr \| batch(3) }}` |
| `column` | Extract column | `{{ arr \| column('name') }}` |
| `filter` | Filter items | `{{ arr \| filter(x => x > 0) }}` |
| `map` | Transform items | `{{ arr \| map(x => x * 2) }}` |
| `reduce` | Reduce to value | `{{ arr \| reduce((c,x) => c+x, 0) }}` |

## Tests

| Test | Purpose | Example |
|------|---------|---------|
| `defined` | Is defined | `{% if x is defined %}` |
| `null` / `none` | Is null | `{% if x is null %}` |
| `empty` | Is empty | `{% if x is empty %}` |
| `iterable` | Is iterable | `{% if x is iterable %}` |
| `even` / `odd` | Number parity | `{% if n is even %}` |
| `divisible by` | Divisibility | `{% if n is divisible by(3) %}` |
| `same as` | Strict equality | `{% if x is same as(y) %}` |

## Operators

```twig
{# Math #}
+ - * / // %  ** 

{# Logic #}
and  or  not  ()

{# Comparison #}
==  !=  <  >  <=  >=  <=>

{# Containment #}
in  not in

{# String #}
~   {# Concatenation #}

{# Ternary #}
{{ x ? 'yes' : 'no' }}
{{ x ?: 'default' }}     {# If x falsy, use default #}
{{ x ?? 'default' }}     {# If x null/undefined, use default #}

{# Range #}
1..5   {# [1, 2, 3, 4, 5] #}
'a'..'e'  {# ['a', 'b', 'c', 'd', 'e'] #}
```

## For Loop

```twig
{% for item in items %}
  {{ loop.index }}       {# 1-indexed #}
  {{ loop.index0 }}      {# 0-indexed #}
  {{ loop.first }}       {# true if first #}
  {{ loop.last }}        {# true if last #}
  {{ loop.length }}      {# total items #}
  {{ loop.revindex }}    {# reverse 1-indexed #}
  {{ loop.parent }}      {# parent loop context #}
{% else %}
  {# No items #}
{% endfor %}

{# Key-value #}
{% for key, value in items %}
  {{ key }}: {{ value }}
{% endfor %}
```

## Conditionals

```twig
{% if condition %}
  ...
{% elseif other %}
  ...
{% else %}
  ...
{% endif %}

{# Inline #}
{{ condition ? 'yes' : 'no' }}
```

## Variables

```twig
{# Set single #}
{% set x = 1 %}

{# Set multiple #}
{% set x, y = 1, 2 %}

{# Capture block #}
{% set x %}
  <div>Content</div>
{% endset %}

{# Array #}
{% set arr = [1, 2, 3] %}

{# Object/Hash #}
{% set obj = { name: 'John', age: 30 } %}

{# Append to array #}
{% set arr = arr | merge([4]) %}
```

## Include Options

```twig
{% include 'file.tpl' %}
{% include 'file.tpl' with { var: value } %}
{% include 'file.tpl' with { var: value } only %}
{% include 'file.tpl' ignore missing %}
{% include ['file1.tpl', 'file2.tpl'] %}
```

## Embed Syntax

```twig
{% embed 'component.tpl' %}
  {% block blockname %}
    Override content
  {% endblock %}
{% endembed %}

{% embed 'component.tpl' with { var: value } %}
  ...
{% endembed %}

{% embed 'component.tpl' with { var: value } only %}
  ...
{% endembed %}
```

## Macro Syntax

```twig
{# Define #}
{% macro name(arg1, arg2 = 'default') %}
  <div>{{ arg1 }} - {{ arg2 }}</div>
{% endmacro %}

{# Call in same file #}
{{ _self.name('value') }}

{# Import and call #}
{% import 'macros.tpl' as macros %}
{{ macros.name('value') }}

{# Import specific #}
{% from 'macros.tpl' import name %}
{{ name('value') }}
```

## Block Functions

```twig
{# Render block #}
{{ block('blockname') }}

{# Check if defined #}
{% if block('blockname') is defined %}

{# Parent content #}
{{ parent() }}

{# Block from another template #}
{{ block('blockname', 'template.tpl') }}
```

## Arrow Functions

```twig
{# Single argument #}
x => x + 1

{# Multiple arguments #}
(a, b) => a + b

{# With key #}
(value, key) => key ~ ': ' ~ value

{# Usage in filters #}
{{ items | filter(x => x > 0) }}
{{ items | map(x => x * 2) }}
{{ items | sort((a, b) => a.name <=> b.name) }}
{{ items | reduce((carry, x) => carry + x, 0) }}
```

## Escape Strategies

```twig
{{ var | e }}            {# HTML (default) #}
{{ var | e('html') }}    {# HTML explicit #}
{{ var | e('js') }}      {# JavaScript #}
{{ var | e('css') }}     {# CSS #}
{{ var | e('url') }}     {# URL #}
{{ var | e('html_attr') }} {# HTML attribute #}
```

## Special Variables

```twig
_self       {# Current template (for macros) #}
_context    {# Current context as array #}
_charset    {# Current charset #}
```
