---
title: Bind Azure Cache para Redis para a sua aplicação Azure Spring Cloud
description: Saiba como ligar a Azure Cache para Redis à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 414f7dc64027b286d8a963452d2b86126b3c5818
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878553"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Bind Azure Cache para Redis para a sua aplicação Azure Spring Cloud 

**Este artigo aplica-se a:** ✔️ Java

Em vez de configurar manualmente as suas aplicações Boot Spring Boot, pode ligar automaticamente os serviços Azure às suas aplicações utilizando a Azure Spring Cloud. Este artigo mostra como ligar a sua aplicação ao Azure Cache para Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Um exemplo de Nuvem de primavera de Azure implantado
* Um Cache Azure para a instância de serviço redis
* A extensão da Nuvem de primavera Azure para o CLI Azure

Se não tiver uma instância Azure Spring Cloud implantada, siga os passos no [arranque rápido ao implementar uma aplicação Azure Spring Cloud](spring-cloud-quickstart.md).

## <a name="bind-azure-cache-for-redis"></a>Bind Azure Cache para Redis

1. Adicione a seguinte dependência ao ficheiro pom.xml do seu projeto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remova quaisquer `spring.redis.*` propriedades do `application.properties` ficheiro

1. Atualize a implementação atual utilizando `az spring-cloud app update` ou crie uma nova implementação utilizando `az spring-cloud app deployment create` .

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure. Vá ao **Painel de Aplicações** e selecione a aplicação para ligar ao Cache Azure para Redis. Esta aplicação é a mesma que atualizou ou implementou no passo anterior.

1. Selecione **a ligação de serviço** e selecione Criar **ligação de serviço**. Preencha o formulário, tendo a certeza de selecionar o valor **de ligação** **Azure Cache para Redis,** a sua Cache Azure para o servidor Redis e a opção chave **primária.**

1. Reinicie a aplicação. A ligação deve agora funcionar.

1. Para garantir que a ligação de serviço está correta, selecione o nome de encadernação e verifique os seus detalhes. O `property` campo deve ser assim:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar a sua aplicação Azure Spring Cloud ao Azure Cache para Redis. Para saber mais sobre serviços vinculativos à sua aplicação, consulte [a Bind to a Azure Database for MySQL instance](spring-cloud-howto-bind-mysql.md).
