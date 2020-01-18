---
title: Transmitir logs de aplicativo do Spring Cloud do Azure em tempo real
description: Como usar o streaming de log para exibir logs de aplicativo instantaneamente
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264004"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Transmitir logs de aplicativo do Spring Cloud do Azure em tempo real
O Azure Spring Cloud permite o streaming de log no CLI do Azure para obter os logs do console do aplicativo em tempo real para solução de problemas. Você também pode [analisar logs e métricas com configurações de diagnóstico](./diagnostic-services.md).

## <a name="prerequisites"></a>Pré-requisitos

* Instalar a [extensão de CLI do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) para Spring Cloud, versão mínima 0.2.0.
* Uma instância do **Azure Spring Cloud** com um aplicativo em execução, por exemplo, [aplicativo Spring Cloud](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Usar a CLI para os logs de cauda

Para evitar especificar repetidamente o seu grupo de recursos e o nome da instância de serviço, defina o nome do grupo de recursos padrão e o nome do cluster.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Nos exemplos a seguir, o grupo de recursos e o nome do serviço serão omitidos nos comandos.

### <a name="tail-log-for-app-with-single-instance"></a>Log final do aplicativo com instância única
Se um aplicativo chamado auth-Service tiver apenas uma instância, você poderá exibir o log da instância do aplicativo com o seguinte comando:
```
az spring-cloud app log tail -n auth-service
```
Isso retornará logs:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Log final do aplicativo com várias instâncias
Se existirem várias instâncias para o aplicativo chamado `auth-service`, você poderá exibir o log da instância usando a opção `-i/--instance`. 

Primeiro, você pode obter os nomes de instância do aplicativo com o comando a seguir.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Com resultados:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Em seguida, você pode transmitir logs de uma instância de aplicativo com a opção `-i/--instance` opção:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Você também pode obter detalhes das instâncias do aplicativo do portal do Azure.  Depois de selecionar **aplicativos** no painel de navegação esquerdo do serviço de nuvem do Azure Spring, selecione **instâncias do aplicativo**.

### <a name="continuously-stream-new-logs"></a>Transmitir continuamente novos logs
Por padrão, `az spring-cloud ap log tail` imprime somente os logs existentes transmitidos para o console do aplicativo e, em seguida, são encerrados. Se você quiser transmitir novos logs, adicione-f (--follow):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Para verificar todas as opções de log com suporte:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Passos seguintes

* [Analisar logs e métricas com configurações de diagnóstico](./diagnostic-services.md)

 





