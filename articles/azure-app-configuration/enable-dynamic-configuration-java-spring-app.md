---
title: Use configuração dinâmica numa aplicação De Arranque de primavera
titleSuffix: Azure App Configuration
description: Saiba como atualizar dinamicamente os dados de configuração para aplicações spring boot
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929162"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Use configuração dinâmica numa aplicação java primavera

A biblioteca do cliente de Configuração de Configuração de Aplicação mola suporta a atualização de um conjunto de configurações a pedido, sem causar o reinício de uma aplicação. A biblioteca do cliente caches cada configuração para evitar demasiadas chamadas para a loja de configuração. A operação de atualização não atualiza o valor até que o valor em cache tenha expirado, mesmo quando o valor tenha sido alterado na loja de configuração. O prazo de validade por defeito para cada pedido é de 30 segundos. Pode ser ultrapassado, se necessário.

Pode verificar se há definições atualizadas a pedido, chamando `AppConfigurationRefresh` o método do `refreshConfigurations()` método.

Em alternativa, pode utilizar o `spring-cloud-azure-appconfiguration-config-web` pacote, que requer uma dependência `spring-web` para lidar com a atualização automatizada.

## <a name="use-automated-refresh"></a>Use atualização automatizada

Para utilizar a atualização automatizada, comece com uma aplicação Spring Boot que utiliza a Configuração da Aplicação, como a aplicação que cria seguindo o [arranque rápido da Bota de primavera para configuração de aplicações.](quickstart-java-spring-app.md)

Em seguida, abra o ficheiro *pom.xml* num editor de texto e adicione um `<dependency>` para `spring-cloud-azure-appconfiguration-config-web` .

**Nuvem de primavera 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Nuvem de primavera 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Executar e testar a aplicação localmente

1. Crie a aplicação Spring Boot com o Maven e execute-a.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra uma janela do navegador e vá para o URL: `http://localhost:8080` .  Verá a mensagem associada à sua chave. 

    Também pode usar *caracóis* para testar a sua aplicação, por exemplo: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Para testar a configuração dinâmica, abra o portal de Configuração de Aplicações Azure associado à sua aplicação. Selecione **Configuration Explorer** e atualize o valor da sua chave apresentada, por exemplo:
    | Chave | Valor |
    |---|---|
    | aplicação/config.message | Olá - Atualizado |

1. Refresque a página do navegador para ver a nova mensagem exibida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação Boot de primavera atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
