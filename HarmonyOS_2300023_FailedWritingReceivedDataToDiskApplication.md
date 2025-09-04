# Netowork Kit throw 2300023 "Failed writing received data to disk/application" error | HarmonyOS | ArkTS

```
{"code":2300023,"message":"Failed writing received data to disk/application"}
```

maxLimit in [HttpRequestOptions](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-http#httprequestoptions) was 5MB by default.  
And max maxLimit is 100MB. There is a hard limitation on HTTP response size.

You could adjust maxLimit to bypass this issue.  
If we do need to download large response, we need to split resource into multiple HTTP request, which requires changes in both client side and server side.

## References

[加载或者传输超大图片遇到错误码2300023该如何处理 | developer.huawei.com](https://developer.huawei.com/consumer/cn/doc/architecture-guides/insurance-v1_2-ts_219-0000002392863680)
