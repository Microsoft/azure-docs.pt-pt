---
title: Transmitir em fluxo registos de aplicação do Azure Spring Cloud em tempo real
description: Como utilizar o streaming de registos para visualizar os registos de aplicações instantaneamente
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192205"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Transmitir em fluxo registos de aplicação do Azure Spring Cloud em tempo real
A Azure Spring Cloud permite que o streaming de log no Azure CLI obtenha registos de consolas de aplicações em tempo real para resolução de problemas. Também pode [analisar registos e métricas com definições](./diagnostic-services.md)de diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [a extensão Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) para spring cloud, versão mínima 0.2.0 .
* Uma instância de **Azure Spring Cloud** com uma aplicação em execução, por exemplo, [aplicação Spring Cloud](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  A extensão CLI ASC é atualizada da versão 0.2.0 para 0.2.1. Esta alteração afeta a sintaxe do comando para o streaming de registos: `az spring-cloud app log tail`, que é substituída por: `az spring-cloud app logs`. O comando: `az spring-cloud app log tail` será depreciado numa futura libertação. Se tiver usado a versão 0.2.0, pode fazer o upgrade para 0.2.1. Primeiro, retire a versão antiga com o comando: `az extension remove -n spring-cloud`.  Em seguida, instale 0.2.1 pelo comando: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Use CLI para troncos de cauda

Para evitar especificar repetidamente o nome do seu grupo de recursos e da instância de serviço, detete o nome do grupo de recursos predefinido e o nome do cluster.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Nos exemplos seguintes, o grupo de recursos e o nome do serviço serão omitidos nos comandos.

### <a name="tail-log-for-app-with-single-instance"></a>Log de cauda para app com instância única
Se uma aplicação chamada auth-service tiver apenas uma instância, pode ver o registo da instância da aplicação com o seguinte comando:
```
az spring-cloud app logs -n auth-service
```
Isto devolverá os registos:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Log de cauda para app com múltiplas instâncias
Se existirem várias instâncias para a aplicação denominada `auth-service`, pode ver o registo da instância utilizando a opção `-i/--instance`. 

Em primeiro lugar, pode obter os nomes da instância da aplicação com o seguinte comando.

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
Em seguida, pode transmitir registos de uma instância de aplicação com a opção `-i/--instance` opção:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Também pode obter detalhes de instâncias de aplicações do portal Azure.  Depois de selecionar **Apps** no painel de navegação esquerdo do seu serviço Azure Spring Cloud, selecione **App Instances**.

### <a name="continuously-stream-new-logs"></a>Stream continuamente novos troncos
Por padrão, `az spring-cloud ap log tail` impressões apenas registos existentes transmitidos para a consola da aplicação e depois saem. Se quiser transmitir novos registos, adicione -f (-follow):  

```
az spring-cloud app logs -n auth-service -f
``` 
Para verificar todas as opções de exploração de madeira suportadas:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Passos seguintes

* [Analisar registos e métricas com definições de diagnóstico](./diagnostic-services.md)

 





