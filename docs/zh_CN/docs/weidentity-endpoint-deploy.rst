
WeIdentity Endpoint Service部署指引
-------------------------------------

为了实现基于RPC的远程调用和转发机制，WeIdentity Endpoint Service分成两部分：代理端（也即RestService这一侧）和服务端（也即Java SDK这一侧）。

其中，代理端负责直接接收HTTP/HTTPS请求，解析、进行Endpoint查询，并转发给对应的服务端。服务端负责接收请求，并直接转发给预先注册的Endpoint实现。因此，您需要按照以下顺序，分别部署Endpoint Service的服务端和代理端。

1. 代理端（RestService）配置
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Endpoint Service在代理端是内建于Rest Service的，环境要求也与其一致，因此您需要首先按照 \ `Rest Service 部署文档 <./weidentity-rest-deploy.html>`_\ 的步骤对其进行部署。

部署完毕后，在 ``dist/conf`` 目录下，修改 ``application.properties`` 文件中的主机端口列表（``server.hostport.list``）这一项。您需要在此处以逗号分隔所有需要由Rest Service连接的远程服务端，指明其主机IP及端口。这样，Endpoint Service就会在后台以您配置的时间间隔（此处的 ``fetch.period.seconds`` ）去远程拉取注册在服务端的Endpoint。

.. code-block:: bash

    # 向服务端周期拉取Endpoint配置的时间间隔，单位为秒
    fetch.period.seconds=60
    # 服务端所有主机端口列表
    server.hostport.list=127.0.0.1:6010,127.0.0.2:6011

2. 在服务端（Java SDK）注册您的Endpoint
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Rest Service代理端，仅仅是一个收集来自各方端点请求并进行中转的角色，它不包含具体的Endpoint的处理逻辑和方法实现。每个主机仍然需要在自己的Java SDK侧注册Endpoint，并暴露之前在代理端声明的对外端口，以供代理端中转调用。您需要在您集成Java-SDK时执行以下步骤：

- 为您需要注册的每个Endpoint，实现一个对应的 ``EndpointFunctor`` 接口。此接口包括两个方法： ``execute()`` 和 ``getDescription()`` 
    - 实现 ``execute()`` ，用来决定具体当代理端的RPC请求发送过来时需要进行的操作
    - 实现 ``getDescription()`` ，用来提供一段对此接口功能的描述
- 在实现完成之后，通过 ``registerEndpoint()`` 方法向此EndpointHandler内注册您的Endpoint
- 服务端会自动将您的Endpoint写入本地Endpoint数据配置项中
- 本地Endpoint数据配置项会被周期性地同步到代理端
- 为了确保代理端能够正确地获取这些Endpoint，您需要启动 ``RpcServer`` 的 ``main()`` 进程

详细的相关实现，可以参考源代码的 `EndpointSample.java <https://github.com/WeBankFinTech/WeIdentity/blob/master/src/main/java/com/webank/weid/suite/endpoint/EndpointSample.java>`_ 。

3. 启动服务端
^^^^^^^^^^^^^^^^^^^

Endpoint Service在服务端依托于WeIdentity-Java-SDK，环境要求也与其一致，请见 `Java-SDK 部署环境要求 <./weidentity-installation.html>`_。

在 ``src/main/resources`` 目录下，修改 ``weidentity.properties`` 文件中的 ``rpc.listener.port`` 这一项内容，以具体确定在哪个端口上进行监听。

.. code-block:: bash

    # RPC服务端的监听端口
    rpc.listener.port=6010

随后，您需要执行您在上一步骤中注册Endpoint的方法。以 ``EndpointSample.java`` 为例，执行：

.. code-block:: bash

    java -cp <$your class path> com.webank.weid.suite.endpoint.EndpointSample

您也可以在IDE环境里执行您的注册方法。当您看到以下提示，表示RPC Server已经启动完毕：

.. code-block:: text

    Trying to receive incoming traffic at Port: 6010

4. 在代理端调用您的Endpoint
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

当您注册Endpoint完毕之后，等待您之前所设定的拉取时间间隔（默认为60秒），以保证此Endpoint被代理端正确地拉取到。随后，您就可以在代理端调用此接口了。

您可以参见\ `REST API文档 <./weidentity-endpoint-deploy.html>`_\ 中的“WeIdentity Endpoint Service API”一节，了解如何获取和调用注册的Endpoint。