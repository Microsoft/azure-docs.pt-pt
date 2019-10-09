---
title: Como associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Saiba como associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038945"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Tutorial: Associe os serviços do Azure ao seu aplicativo Azure Spring Cloud: Cache do Azure para Redis

O Azure Spring Cloud permite associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente o aplicativo Spring boot. Este artigo demonstra como associar seu aplicativo ao cache do Azure para Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Um cache do Azure para instância do serviço Redis
* Extensão do Azure Spring Cloud para o CLI do Azure

Se necessário, instale a extensão do Azure Spring Cloud para o CLI do Azure usando o seguinte comando:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="bind-azure-cache-for-redis"></a>Associar o cache do Azure para Redis

1. Adicione a seguinte dependência na `pom.xml` do seu projeto

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remover propriedades `spring.redis.*`, se houver, no arquivo `application.properties`

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma nova implantação usando `az spring-cloud app deployment create`.

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Localize o **painel do aplicativo** e selecione o aplicativo a ser associado ao cache do Azure para Redis.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Em seguida, escolha `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, certifique-se de selecionar **tipo de associação** `Azure Cache for Redis`, seu servidor Redis e a opção chave primária. 

1. Reinicie o aplicativo e essa associação deve funcionar agora.

1. Para garantir que a associação de serviço esteja correta, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ter a seguinte aparência:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure a um cache Redis do Azure.  Para saber mais sobre como associar serviços ao seu aplicativo, continue no tutorial para associar um aplicativo a um banco de BD MySQL.

> [!div class="nextstepaction"]
> [Saiba como associar um serviço mysql do Azure ao serviço de nuvem Spring do Azure](spring-cloud-tutorial-bind-mysql.md).