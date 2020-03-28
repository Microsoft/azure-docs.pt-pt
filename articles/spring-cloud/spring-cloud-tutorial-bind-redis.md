---
title: Tutorial - Bind Azure Cache for Redis para a sua aplicação Azure Spring Cloud
description: Este tutorial mostra-lhe como ligar o Cache Azure para redis à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277510"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Bind Azure Cache para Redis para a sua aplicação Azure Spring Cloud 

Em vez de configurar manualmente as suas aplicações spring boot, pode ligar automaticamente os serviços Azure às suas aplicações utilizando a Nuvem de primavera Azure. Este artigo mostra como ligar a sua aplicação ao Azure Cache for Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância de Nuvem de primavera Azure implantada
* Um Cache Azure para a instância de serviço Redis
* A extensão da Nuvem de primavera Azure para o Azure CLI

Se não tiver uma instância de Nuvem de primavera Azure implantada, siga os passos no [arranque rápido da implementação de uma aplicação Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Bind Azure Cache para Redis

1. Adicione a seguinte dependência ao ficheiro pom.xml do seu projeto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remova `spring.redis.*` quaisquer `application.properties` propriedades do ficheiro

1. Atualize a `az spring-cloud app update` implementação atual utilizando `az spring-cloud app deployment create`ou crie uma nova implementação utilizando .

1. Vá à sua página de serviço Azure Spring Cloud no portal Azure. Vá ao **Painel de Aplicações** e selecione a aplicação para ligar ao Azure Cache for Redis. Esta aplicação é a mesma que atualizou ou implementou no passo anterior.

1. Selecione **a ligação do serviço** e selecione Criar a **ligação do serviço**. Preencha o formulário, tendo a certeza de selecionar o valor de **ligação** Do tipo **Azure Cache para Redis,** o seu Cache Azure para o servidor Redis e a opção chave **Primária.**

1. Reinicie a aplicação. A encadernação deve agora funcionar.

1. Para garantir que a ligação do serviço está correta, selecione o nome de ligação e verifique os seus dados. O `property` campo deve ser assim:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar a sua aplicação Azure Spring Cloud ao Azure Cache for Redis. Para saber mais sobre serviços vinculativos à sua aplicação, continue a ser tutorial sobre vincular uma aplicação a uma base de dados Azure para instância MySQL.

> [!div class="nextstepaction"]
> [Saiba como ligar-se a uma Base de Dados Azure para a instância MySQL](spring-cloud-tutorial-bind-mysql.md)
