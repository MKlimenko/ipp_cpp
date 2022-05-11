# Intel® Integrated Performance Primitives C++ wrapper

This is a small header-only library used to provide C++ wrappers for the IPP library functions.

> :warning: Disclaimer: Although I'm an Intel employee, this library was developed in my own free time and has nothing to do with my employer and/or the IPP team.

## Library structure

All of the functions in this wrapper library are located under the `ipp_cpp` namespace and then subdivided into the domain-related inline namespaces.

Every set of functions is split into multiple function overloads with the same name but different signatures, for example:

```cpp
inline auto AddC(Ipp8u val, Ipp8u* pSrcDst, int len, int scaleFactor) {
    return ippsAddC_8u_ISfs(val, pSrcDst, len, scaleFactor);
}

inline auto AddC(const Ipp8u* pSrc, Ipp8u val, Ipp8u* pDst, int len, int scaleFactor) {
    return ippsAddC_8u_Sfs(pSrc, val, pDst, len, scaleFactor);
}

inline auto AddC_16s(Ipp16s val, Ipp16s* pSrcDst, int len) {
    return ippsAddC_16s_I(val, pSrcDst, len);
}

inline auto AddC(Ipp16s val, Ipp16s* pSrcDst, int len, int scaleFactor) {
    return ippsAddC_16s_ISfs(val, pSrcDst, len, scaleFactor);
}
```

The same approach is used to distinguish between the inplace and non-inplace versions of the functions.

For the functions that perform some actions based on the type, but don't have that type in their arguments (like IIR initialization) you have to provide the type manually. However, this still can be used in generic code by some `decltype`-related stuff:

```cpp
template <typename T> auto IIRGetStateSize(int order, int* pBufferSize) { static_assert(sizeof(T) == 0, "Unexpected overload"); }

template <>
inline auto IIRGetStateSize<Ipp32f>(int order, int* pBufferSize) {
    return ippsIIRGetStateSize_32f(order, pBufferSize);
}

template <>
inline auto IIRGetStateSize<Ipp32fc>(int order, int* pBufferSize) {
    return ippsIIRGetStateSize_32fc(order, pBufferSize);
}
```

In case you provide an invalid template specialization, `static_assert` will be triggered.

## Room for improvement

There are three major ways to improve this library, if you have any time/wish to assist me, your help will be highly appreciated.

- [ ] `Ipp24u/s` 
- [ ] `Ipp16f`
- [ ] Better tests

### `Ipp24u/s`

There are a couple of functions inside the IPP library that operate on the `Ipp24u/s` datatype. Currently, it's presented as a struct with an array of three `Ipp8u`-s, but for the sake of processing outside of the IPP library, this type should be implemented properly.

### `Ipp16f`

The same stands for the `Ipp16f` datatype, which was aliased to the `Ipp16s` in the library and is currently widely used in the deep learning-related applications

### Better tests

Currently, the tests for this library are implemented as a compilation check. Since the quality of the library is very high, I don't think we should reinvent the tests, but we might spot some ambiguous overload resolution stuff, so there's something worth implementing someday. 
