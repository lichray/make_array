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
view, so narrowing is just naturally baned.  See more details
driven by this direction in [Design Decisions](#design_decisions).

## Examples

    auto a1 = make_array(2, 3L);  // array<long, 2>
    // auto ax = make_array(2, 3U);  // no narrowing
    auto a2 = make_array("foo");  // array<char, 4>

## Design Decisions

- No array-to-pointer conversion.  `make_tuple` and `make_pair`
  unconditionally decay, but such a behavior, if being applied to `make_array`,

<div><div><tt>make_array("raw array")&nbsp;
// got array&lt;char const&#42;, 1&gt;</tt></div></div>

> is unexpected and inexplicable.

- Ban `reference_wrapper`.  `make_tuple` and `make_pair` have special handling
  of `reference_wrapper`, then user might expect that the expression

<div><div><tt>make_array(ref(a), ref(b))</tt></div></div>

> also results in a tuple-like object storing `T&`.  However, `std::array`
> does not store "real" references, and any attempts to workaround this
> break the interfaces in different ways.

- Yield the array-to-pointer interface to constructing from raw array.  So
  user get the expected result when "making an array from an array".

## Wording

This wording is relative to N3797, which contains
the resolution of
[LWG 2141](http://cplusplus.github.io/LWG/lwg-defects.html#2141).

Add to 23.3.1/2 &#91;sequences.general&#93;, `<array>` synopsis:

    namespace std {
      template <class T, size_t N > struct array;
> ...

      template <class T, size_t N >
        void swap(array<T,N>& x, array<T,N>& y) noexcept(noexcept(x.swap(y)));
<div><ins>
<tt>template &lt;class... Types&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr <i>see below</i> make_array(Types&amp;&amp;...);</tt></br>
<tt>template &lt;class T, size_t N&gt;</tt></br>
<tt>&nbsp;&nbsp;constexpr <i>see below</i> make_array(T (&amp;a)&#91;N&#93;);</tt></br>
</ins></div>

> ...

    }

New section 23.3.2.9 &#91;array.creation&#93; (between &#91;array.zero&#93;
and &#91;array.tuple&#93;, which was 23.3.2.9):

> #### 23.3.2.9 Array creation functions &#91;array.creation&#93;

    template <class... Types>
      constexpr array<CT, sizeof...(Types)> make_array(Types&&...);

> Let _`Ui`_ be `remove_reference<`_`Ti`_`>::type` for each _`Ti`_ in `Types`.

> *Remarks:* This function shall not participate in overload resolution
> unless each _`Ui`_ is neither an array nor `reference_wrapper<`_`Ti`_`>`.

*\[Editorial note:* These two cases are banned for genericity reasons, so
use SFINAE to allow users to handle them in generic code.  *--end note\]*

> *Returns:* An `array<CT, sizeof...(Types)>` initialized with
> `{ std::forward<Types>(t))... }`,
> where `CT` is `common_type<Types...>::type`.

> *\[Example:*

        int i = 1; int& ri = i;
        make_array(i, ri, 42L)

>  creates an `array` of type

        array<long, 3>

> *--end example\]*

    template <class T, size_t N>
      constexpr array<V, N> make_array(T (&a)[N]);

> *Returns:* An `array<V, N>` such that each element is copy-initialized
> with the corresponding element of `a`, where `V` is `remove_cv<T>::type`.

*\[Editorial note:* The `remove_cv` here functionally performs decay, while
 intentionally kills constructing from multidimensional array with a hard
error, because `std::array` is not aware of multidimensional array (yet), and
I don't want user to try anything may silently break their code in the future.
*--end note\]*

## Sample Implementation

A sample implementation is available at
<https://gist.github.com/lichray/6034753>.

## Acknowledgments

Jonathan Wakely, who showed me how to use `index_sequence` to initialize
`std::array` from a raw array.
