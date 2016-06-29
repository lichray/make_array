<!-- maruku -o make_array_IS.html make_array_IS.md -->

<style type="text/css">
pre code { display: block; margin-left: 2em; }
div { display: block; margin-left: 2em; }
ins { text-decoration: none; font-weight: bold; background-color: #A0FFA0 }
del { text-decoration: line-through; background-color: #FFA0A0 }
</style>

<table><tbody>
<tr><th>Doc. no.:</th>	<td>PxxxxR0</td></tr>
<tr><th>Date:</th>	<td>2016-03-31</td></tr>
<tr><th>Audience:</th>	<td>Library Evolution Working Group</td></tr>
<tr><th>Reply-to:</th>	<td>Zhihao Yuan &lt;zy at miator dot net&gt;</td></tr>
</tbody></table>

# Propose to adopt make_array in C++17

## Proposal

This paper proposes to adopt the "Array creation functions"`[1]` in Library
Fundamentals v2 into the C++17 working paper, for the following reasons:

 1. This facility is useful, and has been replicated/reinvented in many
    places/codebases, also been an LWG issue since 2008`[2]`, while the design
    adopted in TSv2 is of the least controversial among those.

 2. The functionality is stable since the initial `make_array` paper`[3]`.

 3. This facility is a pure addition.
 
A note about whether this facility may be superseded by the deduced
constructors: the presence of the constructors are contradictory to the
concept of aggregate types.  This have been discussed at
the first meeting when the initial paper was presented.

## Wording

Copy the section 9.2.2 \[container.array.creation\]`[1]` from
Library Fundamentals v2 to the IS working paper, as a new section
23.3.2.9 between \[array.zero\] and \[array.tuple\], and adjust the `<array>`
synopsis in 23.3.1/2 \[sequences.general\].

Alternatively, apply the wording in the adopted paper`[4]`, which contains
the wording with the same content but relative to the IS working paper.

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
