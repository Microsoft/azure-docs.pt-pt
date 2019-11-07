---
title: Como associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Saiba como associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607576"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Tutorial: associar os serviços do Azure ao seu aplicativo Azure Spring Cloud: cache do Azure para Redis

O Azure Spring Cloud permite associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente o aplicativo Spring boot. Este artigo demonstra como associar seu aplicativo ao cache do Azure para Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Um cache do Azure para instância do serviço Redis
* Extensão do Azure Spring Cloud para o CLI do Azure

Se você não tiver uma instância do Azure Spring Cloud implantada, siga as etapas neste guia de [início rápido](spring-cloud-quickstart-launch-app-portal.md) para implantar seu primeiro aplicativo Spring Cloud.

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

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Localize o **painel do aplicativo** e selecione o aplicativo a ser associado ao cache do Azure para Redis.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Em seguida, escolha `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, não se esqueça de selecionar o **tipo de associação** `Azure Cache for Redis`, o servidor Redis e a opção chave primária. 

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