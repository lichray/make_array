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

We have `make_tuple`, `make_pair`, but not `make_array`, while `std::array`
creation can also benefit from this "semiofficial" tuple-like interface to
deduce both element type and array bound.

## Scope

LWG issue [851](http://cplusplus.github.io/LWG/lwg-closed.html#851) intended
to provide a replacement syntax to

    array<T, N> a = { E1, E2, ... };

, so the following

    auto a = make_array(42u, 3.14);

is well-formed (with additional `static_cast`s applied inside) because

    array<double, 2> = { 42u, 3.14 };

is well-formed.

This paper intends to provide `std::array` creation interfaces which are
comprehensive from both `tuple`'s point of view and `array`'s point of
view, so narrowing is just naturally baned.  You can find more details
driven by this direction in [Design Decisions](#design_decisions).

## Examples

    auto a1 = make_array(2, 3L);  // array<long, 2>
    // auto ax = make_array(2, 3U);  // no narrowing
    auto a2 = make_array("foo");  // array<char, 4>

## Design Decisions

## Wording

## Sample Implementation

A sample implementation is available at
<https://gist.github.com/lichray/6034753>.

## Acknowledgments
