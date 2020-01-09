---
title: Tutorial – associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud
description: Este tutorial mostra como associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 662d36f8a25f2f0a21d800b7b1a25e94b13908a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461505"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Associar o cache do Azure para Redis ao seu aplicativo Azure Spring Cloud 

Em vez de configurar manualmente seus aplicativos Spring boot, você pode associar automaticamente os serviços do Azure selecionados aos seus aplicativos usando o Azure Spring Cloud. Este artigo mostra como associar seu aplicativo ao cache do Azure para Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Um cache do Azure para instância do serviço Redis
* A extensão de nuvem Spring do Azure para o CLI do Azure

Se você não tiver uma instância do Azure Spring Cloud implantada, siga as etapas no guia de [início rápido sobre como implantar um aplicativo do Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Associar o cache do Azure para Redis

1. Adicione a seguinte dependência ao arquivo pom. XML do seu projeto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remover todas as propriedades de `spring.redis.*` do arquivo de `application.properties`

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma nova implantação usando `az spring-cloud app deployment create`.

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Vá para o **painel do aplicativo** e selecione o aplicativo a ser associado ao cache do Azure para Redis. Esse aplicativo é o mesmo que você atualizou ou implantou na etapa anterior.

1. Selecione **Associação de serviço** e selecione **criar Associação de serviço**. Preencha o formulário, certifique-se de selecionar o valor do **tipo de associação** **cache do Azure para Redis**, seu cache do Azure para servidor Redis e a opção **Primary** Key.

1. Reinicie a aplicação. A Associação agora deve funcionar.

1. Para garantir que a associação de serviço esteja correta, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ter a seguinte aparência:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure ao cache do Azure para Redis. Para saber mais sobre como associar serviços ao seu aplicativo, continue no tutorial sobre como associar um aplicativo a uma instância do banco de dados do Azure para MySQL.

> [!div class="nextstepaction"]
> [Saiba como associar a uma instância do banco de dados do Azure para MySQL](spring-cloud-tutorial-bind-mysql.md)
