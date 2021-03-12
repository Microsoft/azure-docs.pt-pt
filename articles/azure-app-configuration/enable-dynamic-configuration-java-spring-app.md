---
title: Use configuração dinâmica numa aplicação De Arranque de primavera
titleSuffix: Azure App Configuration
description: Saiba como atualizar dinamicamente os dados de configuração para aplicações spring boot
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 076ab0bb7dbc85a31b626a24d977e6fea558143e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636543"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Use configuração dinâmica numa aplicação java primavera

A Configuração de Aplicativos tem duas bibliotecas para a primavera. `spring-cloud-azure-appconfiguration-config` requer Bota de primavera e tem uma dependência de `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` requer Web primavera juntamente com a Bota de primavera. Ambas as bibliotecas suportam o detonador manual para verificar se há valores de configuração atualizados. `spring-cloud-azure-appconfiguration-config-web` também adiciona suporte para verificação automática da atualização de configuração.

A atualização permite-lhe refrescar os valores de configuração sem ter de reiniciar a sua aplicação, embora faça com que todos os feijões sejam `@RefreshScope` recriados. A biblioteca do cliente cache um id de haxixe das configurações atualmente carregadas para evitar demasiadas chamadas para a loja de configuração. A operação de atualização não atualiza o valor até que o valor em cache tenha expirado, mesmo quando o valor tenha sido alterado na loja de configuração. O prazo de validade por defeito para cada pedido é de 30 segundos. Pode ser ultrapassado, se necessário.

`spring-cloud-azure-appconfiguration-config-web`'atualização automatizada é desencadeada com base na atividade, especificamente a da Spring `ServletRequestHandledEvent` Web. Se `ServletRequestHandledEvent` a aa não for ativada, `spring-cloud-azure-appconfiguration-config-web` a atualização automatizada não desencadeará uma atualização, mesmo que o tempo de validade da cache tenha expirado.

## <a name="use-manual-refresh"></a>Use atualização manual

A Configuração da Aplicação expõe `AppConfigurationRefresh` o que pode ser usado para verificar se a cache está expirada e se é expirada desencadear uma atualização.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh`'s `refreshConfigurations()` devolve um que é verdade se uma `Future` atualização foi desencadeada, e falso se não. Falso significa que ou o tempo de validade da cache não expirou, não houve alteração, ou outro fio está atualmente a verificar uma atualização.

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
