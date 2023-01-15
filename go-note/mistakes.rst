.. _gomistakes:

Go 常见错误
=====================================================================

100 Go Mistakes and How to Avoid Them
---------------------------------------------------------------
《100 Go Mistakes and How to Avoid Them》这本书的一些重要内容笔记。

2 Code and project organization
--------------------------------------------------

2.1 #1: Unintended variable shadowing
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

	var client *http.Client // 以下代码运行后，client 依然还是 nil
	if tracing {
		// Declares a client variable
		client, err := createClientWithTracing() // 新创建的同名变量覆盖外层同名的 client
		if err != nil {
			return err
		}
		log.Println(client)
	} else {
		client, err := createDefaultClient()
		if err != nil {
			return err
		}
		log.Println(client)
	}

    // 解决方式：不要使用 := 赋值一个新变量，需要声明一下 client 和 err
	var client *http.Client
	var err error // Declares an err variable
	if tracing {
		client, err = createClientWithTracing()
		if err != nil {
			return err
		}
	} else {
		// Same logic
	}

2.5 #5: Interface pollution
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
When to use interfaces:

- Common behavior
- Decoupling
- Restricting behavior
