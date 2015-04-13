<!-- maruku -o make_array.html make_array.md -->

<style type="text/css">
pre code { display: block; margin-left: 2em; }
div { display: block; margin-left: 2em; }
ins { text-decoration: none; font-weight: bold; background-color: #A0FFA0 }
del { text-decoration: line-through; background-color: #FFA0A0 }
</style>

<table><tbody>
<tr><th>Doc. no.:</th>	<td>N4391</td></tr>
<tr><th>Date:</th>	<td>2015-02-26</td></tr>
<tr><th>Project:</th>	<td>Programming Language C++, Library Working Group</td></tr>
<tr><th>Revises:</th>	<td>N4315</td></tr>
<tr><th>Reply-to:</th>	<td>Zhihao Yuan &lt;zy at miator dot net&gt;</td></tr>
</tbody></table>

# make_array, revision 4

## Changes since N4315

- Ill-form the program if the input contains `reference_wrapper`.
- Drop _see below_ in the synopsis.
- Fix typos and formatting.

## Changes since N4065

- Merge `make_array` and `array_of` into one.
- Cleanup notes for presentation.

## Changes since N4031

- `make_array<D>` renamed to `array_of<D>`.
- Fixed _cv_-`reference_wrapper<T>` detection.

## Changes since N3824

- Editorial wording updates.
- Comments updates.

## Motivation

We have `make_tuple`, `make_pair`, but not `make_array`, while `std::array`
creation can also benefit from this "semiofficial" tuple-like interface to
deduce both element type and array bound.

## Scope

[LWG 851](http://cplusplus.github.io/LWG/lwg-closed.html#851) intended
to provide a replacement syntax to

    array<T, N> a = { E1, E2, ... };

, so the following

    auto a = make_array(42u, 3.14);

is well-formed (with additional `static_cast`s applied inside) because

    array<double, 2> = { 42u, 3.14 };

is well-formed.

This paper intends to provide a set of `std::array` creation interfaces
which are
comprehensive from both `tuple`'s point of view and `array`'s point of
view, so narrowing is just naturally banned.  See more details
driven by this direction in [Design Decisions](#design_decisions).

## Examples

    auto a1 = make_array(2, 3L);        // array<long, 2>
    auto ax = make_array(2, 3U);        // error: narrowing

    auto a2 = make_array<long>(2, 3U);      // explicit destination type
    auto ax = make_array<unsigned>(2, 3U);  // error: narrowing

    auto a3 = make_array("foo");        // array<char const*, 1>, decayed
    auto a4 = to_array("foo");          // array<char, 4>

## Design Decisions

- Provide both `make_tuple`-like, type-deduced interface and raw array
  style bound-deduced interface.

- Ban `reference_wrapper` in the `make_tuple`-like interface.  `make_tuple`
  and `make_pair` have special handling
  of `reference_wrapper`, then user might expect that the expression

<div><div><tt>make_array(ref(a), ref(b))</tt></div></div>

> also results in a tuple-like object storing `T&`.  However, `std::array`
> does not store "real" references, and any attempts to workaround this
> break the interfaces in different ways.
> Note that "doing nothing" is not an option since, for example,
> `common_type_t<reference_wrapper<int>, reference_wrapper<long>>` is
> `long`, not reference or `reference_wrapper`.

- A saperated interface to perform constructing from raw array instead of
  array-to-pointer conversion.  `make_tuple` and `make_pair`
  unconditionally decay, but such a behavior, when being applied to
  `make_array`,

<div><div><tt>make_array("raw array")&nbsp;
// got array&lt;char const&#42;, 1&gt;</tt></div></div>

> is inexplicable.  However, to keep the interfaces consistent, I decide
> to name a new utility differently instead of to ban this conversion.

## Wording

This wording is relative to N4296.

Add to 23.3.1/2 &#91;sequences.general&#93;, `<array>` synopsis:

    namespace std {
      template <class T, size_t N > struct array;
> ...

      template <class T, size_t N >
        void swap(array<T,N>& x, array<T,N>& y) noexcept(noexcept(x.swap(y)));
<div><ins>
<tt>template &lt;class D = void, class... Types&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr array&lt;<i>VT</i>, sizeof...(Types)&gt;
make_array(Types&amp;&amp;...);</tt></br>
<tt>template &lt;class T, size_t N&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr array&lt;remove_cv_t&lt;T&gt;, N&gt;
to_array(T (&amp;a)&#91;N&#93;);</tt></br>
</ins></div>

> ...

    }

New section 23.3.2.9 &#91;array.creation&#93; (between &#91;array.zero&#93;
and &#91;array.tuple&#93;, which was 23.3.2.9):

> #### 23.3.2.9 Array creation functions &#91;array.creation&#93;

<div>
<tt>template &lt;class D = void, class... Types&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr array&lt;<i>VT</i>, sizeof...(Types)&gt;
make_array(Types&amp;&amp;...);</tt></br>
</div>

> Let _Ui_ be `decay_t<`_Ti_`>` for each _Ti_ in `Types`.

> *Remarks:* The program is ill-formed if `D` is `void`
> and at least one _Ui_ is a specialization of `reference_wrapper`.

> *Returns:* `array<`_VT_`, sizeof...(Types)>{ std::forward<Types>(t)... }`,
> where _VT_ is `common_type_t<Types...>` if `D` is `void`, otherwise _VT_
> is `D`.

> *\[Example:*

        int i = 1; int& ri = i;
        auto a1 = make_array(i, ri);         // a1 is of type array<int, 2>
        auto a2 = make_array(i, ri, 42L);    // a2 is of type array<long, 3>
        auto a3 = make_array<long>(i, ri);   // a3 is of type array<long, 2>
        auto a4 = make_array<long>();        // a4 is of type array<long, 0>
        auto a5 = make_array();              // ill-formed

> *--end example\]*

*\[Editorial note:*
The non-code text in the example above, such as "is of type", "ill-formed",
should be in italic face.
*\]*

<div>
<tt>template &lt;class T, size_t N&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr array&lt;remove_cv_t&lt;T&gt;, N&gt;
to_array(T (&amp;a)&#91;N&#93;);</tt></br>
</div>

> *Returns:* An `array<remove_cv_t<T>, N>` such that each element is
> copy-initialized
> with the corresponding element of `a`.

## Sample Implementation

A sample implementation is available at
<https://gist.github.com/lichray/6034753>.

## Acknowledgments

Jonathan Wakely, who showed me how `index_sequence` helps initializing
`std::array` from a raw array.

Daniel Krügler, who explained why an explicit destination type is essential.

Ville Voutilainen and other people who reviewed this paper.

Stephan T. Lavavej, who pointed out the ambiguity issue of the two make_array
overloads.
