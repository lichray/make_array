<!-- maruku -o make_array.html make_array.md -->

<style type="text/css">
pre code { display: block; margin-left: 2em; }
div { display: block; margin-left: 2em; }
ins { text-decoration: none; font-weight: bold; background-color: #A0FFA0 }
del { text-decoration: line-through; background-color: #FFA0A0 }
</style>

<table><tbody>
<tr><th>Doc. no.:</th>	<td>Nnnnn</td></tr>
<tr><th>Date:</th>	<td>2013-10-13</td></tr>
<tr><th>Project:</th>	<td>Programming Language C++, Library Evolution Working Group</td></tr>
<tr><th>Reply-to:</th>	<td>Zhihao Yuan &lt;zy at miator dot net&gt;</td></tr>
</tbody></table>

# make_array

## Motivation

## Design Decisions

## Examples

    auto a1 = make_array(2, 3L);  // array<long, 2>
    // auto ax = make_array(2, 3U);  // narrowing
    auto a2 = make_array("foo");  // array<char, 4>

## Wording

## Sample Implementation

A sample implementation is available at
<https://gist.github.com/lichray/6034753>.

## Acknowledgments
