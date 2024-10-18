# kube-proxy


## Start Up
```
cmd/kube-proxy/proxy.go:main()
cmd/kube-proxy/app/server.go:Run()
    cmd/kube-proxy/app/server_windows.go:NewProxyServer()
        createClients()
        utilnode.GetHostname()
        detectNodeIP()
        getProxyMode()
        getDualStackMode()
cmd/kube-proxy/app/server.go:runLoop()
```