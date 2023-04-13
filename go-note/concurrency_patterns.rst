.. _go_concurrency_patterns:

Go 并发设计模式
=====================================================================

屏障模式(Barrier Mode)
--------------------------------------------------
屏障模式(Barrier Mode)，用来阻塞goroutine直到聚合所有goroutine返回结果，可以用通道实现。使用场景：

1. 多个网络请求并发，聚合结果
2. 粗粒度任务拆分并发执行，聚合结果

.. code-block:: go

    package main

    import (
        "fmt"
        "io/ioutil"
        "net/http"
        "time"
    )

    // 封装屏障模式响应的结构体(一般就是返回值和错误)
    type BarrierResponse struct {
        Err    error
        Resp   string
        Status int
    }

    // 执行单个请求，结果放入 channel
    func doRequest(out chan<- BarrierResponse, url string) {
        res := BarrierResponse{}
        client := http.Client{Timeout: time.Duration(3 * time.Second)}
        resp, err := client.Get(url)
        if resp != nil {
            res.Status = resp.StatusCode
        }
        if err != nil {
            res.Err = err
            out <- res
            return
        }

        b, err := ioutil.ReadAll(resp.Body)
        defer resp.Body.Close()
        if err != nil {
            res.Err = err
            out <- res
            return
        }
        res.Resp = string(b)
        out <- res //  结果放入通道
    }

    // 并发请求并聚合结果
    func Barrier(urls ...string) {
        n := len(urls)
        in := make(chan BarrierResponse, n)
        response := make([]BarrierResponse, n)
        defer close(in)

        for _, url := range urls {
            go doRequest(in, url)
        }

        var hasError bool
        for i := 0; i < n; i++ {
            resp := <-in
            if resp.Err != nil {
                fmt.Println("Error: ", resp.Err, resp.Status)
                hasError = true
            }
            response[i] = resp
        }
        if !hasError {
            for _, resp := range response {
                fmt.Println(resp.Status)
            }
        }
    }

    func main() {
        urls := []string{
            "https://www.baidu.com",
            "https://www.weibo.com",
            "https://www.zhihu.com",
        }
        Barrier(urls...)
    }

未来模式(Future Mode)
--------------------------------------------------
Future模式(也称为Promise Mode)。使用 `fire-and-forget` 方式，主进程不等子进程执行完就直接返回，然后等到未来执行完的时候
再去获取结果。
