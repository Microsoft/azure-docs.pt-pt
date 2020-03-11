---
title: Use configuração dinâmica numa aplicação Spring Boot
titleSuffix: Azure App Configuration
description: Saiba como atualizar dinamicamente os dados de configuração para aplicações de Boot de primavera
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 6445b9707273d273c562b7d643da34f5ba26e1fc
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967489"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Use a configuração dinâmica numa aplicação Java Spring

A biblioteca de clientes da Bota de Arranque de Configuração da Aplicação suporta a atualização de um conjunto de configurações a pedido, sem causar o reinício de uma aplicação. A biblioteca do cliente caches cada configuração para evitar muitas chamadas para a loja de configuração. A operação de atualização não atualiza o valor até que o valor em cache tenha expirado, mesmo quando o valor tenha mudado na loja de configuração. O tempo de validade predefinido para cada pedido é de 30 segundos. Pode ser ultrapassado, se necessário.

Pode verificar se estão as configurações atualizadas a pedido, ligando para `AppConfigurationRefresh`método de `refreshConfigurations()`.

Em alternativa, pode utilizar o pacote `spring-cloud-azure-appconfiguration-config-web`, que requer uma dependência de `spring-web` para lidar com a atualização automatizada.

## <a name="use-automated-refresh"></a>Use atualização automatizada

Para utilizar uma atualização automatizada, comece com uma aplicação Spring Boot que utiliza a Configuração de Aplicações, como a aplicação que cria seguindo o [quickstart spring boot para configuração](quickstart-java-spring-app.md)de aplicações .

Em seguida, abra o ficheiro *pom.xml* num editor de texto e adicione um `<dependency>` para `spring-cloud-azure-appconfiguration-config-web`.

**Nuvem de primavera 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Nuvem de primavera 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Guarde o ficheiro e, em seguida, construa e execute a sua aplicação como de costume.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou a sua aplicação Spring Boot para atualizar dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para mais informações, consulte [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/).
