<!-- maruku -o make_array_IS.html make_array_IS.md -->

<style type="text/css">
pre { margin: 0; }
pre code { display: block; margin-left: 2em; }
div { display: block; margin-left: 2em; }
ins { text-decoration: none; font-weight: bold; background-color: #A0FFA0 }
del { text-decoration: line-through; background-color: #FFA0A0 }
</style>

<table><tbody>
<tr><th>Doc. no.:</th>	<td>P0325R1</td></tr>
<tr><th>Date:</th>	<td>2016-06-29</td></tr>
<tr><th>Audience:</th>	<td>Library Working Group</td></tr>
<tr><th>Reply-to:</th>	<td>Zhihao Yuan &lt;zy at miator dot net&gt;</td></tr>
</tbody></table>

# Propose to adopt make_array into the IS

## Proposal

This paper proposes to adopt the "Array creation functions"`[1]` in Library
Fundamentals v2 into the C++ working paper, for the following reasons:

 1. These facilities are useful, and have been replicated/reinvented in many
    places/codebases, also been an LWG issue since 2008`[2]`, while the design
    adopted in TSv2 is of the least controversial among those.

 2. The functionality is stable since the initial `make_array` paper`[3]`.

## Wording

The wording is relative to N4594.

Copy the section 9.2.2 \[container.array.creation\]`[1]` from
Library Fundamentals v2 to the IS working paper, as a new section
23.3.7.9 \[array.creation\] between \[array.zero\] and \[array.tuple\],
and adjust 23.3.2 \[array.syn\] accordingly (see the adopted paper`[4]`
for reference).

Amend the examples in 23.3.7.9 with the following:

> *\[Example:*

        int i = 1; int& ri = i;
        auto a1 = make_array(i, ri);         // a1 is of type array<int, 2>
        auto a2 = make_array(i, ri, 42L);    // a2 is of type array<long, 3>
        auto a3 = make_array<long>(i, ri);   // a3 is of type array<long, 2>
        auto a4 = make_array<long>();        // a4 is of type array<long, 0>
        auto a5 = make_array();              // ill-formed

<div><ins><tt style="white-space: pre">   auto a6 = make_array&lt;double&gt;(1, 2);  // ill-formed: might narrow</tt></ins></div>

> *--end example\]*

## References

`[1]` Array creation functions. N4564 _Programming Languages -- C++ Extensions
      for Library Fundamentals, Version 2_.
      <http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2015/n4564.pdf#page=104&zoom=auto,0,590>

`[2]` LWG 851 _simplified array construction_.
      <http://cplusplus.github.io/LWG/lwg-active.html#851>

`[3]` N3824 _make\_array_.
      <http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2014/n3824.htm>

`[4]` N4391 _make\_array, revision 4_.
      <http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2015/n4391.html>
