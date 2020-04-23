---
title: Quickstart para aprender a usar a configuração da app Azure
description: Um arranque rápido para usar a Configuração de Aplicações Azure com aplicações Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: 69b9733f4d79f5fc42fe86ca0f8a97305205aeef
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085064"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Quickstart: Crie uma app Java Spring com configuração de aplicações azure

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação Java Spring para centralizar o armazenamento e a gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Um Kit de Desenvolvimento Java suportado [(JDK)](https://docs.microsoft.com/java/azure/jdk) com a versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versão 3.0 ou superior.

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Select Configuration Explorer** > **+ Criar** > o**valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | /aplicação/config.mensagem | Hello |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

7. Selecione **Aplicar**.

## <a name="create-a-spring-boot-app"></a>Crie uma aplicação spring boot

Use o [Initializr de primavera](https://start.spring.io/) para criar um novo projeto spring boot.

1. Navegue para <https://start.spring.io/>.

1. Especifique as seguintes opções:

   - Gere um projeto do **Maven** com **Java**.
   - Especifique uma versão **Spring Boot** igual ou superior a 2.0.
   - Especifique os nomes do **Grupo** e do **Artefacto** da aplicação.
   - Adicione a dependência da **Web da primavera.**

1. Depois de especificar as opções anteriores, selecione **Generate Project**. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Depois de extrair os ficheiros do seu sistema local, a sua simples aplicação Spring Boot está pronta para ser editada. Localize o ficheiro *pom.xml* no diretório raiz da sua aplicação.

1. Abra o ficheiro *pom.xml* num editor de texto e adicione o arranque `<dependencies>`da Spring Cloud Azure Config à lista de:

    **Nuvem de primavera 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Nuvem de primavera 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Crie um novo ficheiro Java chamado *MessageProperties.java* no diretório de pacotes da sua aplicação. Adicione as seguintes linhas:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Crie um novo ficheiro Java chamado *HelloController.java* no diretório de pacotes da sua aplicação. Adicione as seguintes linhas:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Abra o ficheiro Java da `@EnableConfigurationProperties` aplicação principal e adicione para ativar esta funcionalidade.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Crie um `bootstrap.properties` novo ficheiro nomeado no diretório de recursos da sua app e adicione as seguintes linhas ao ficheiro. Substitua os valores da amostra pelas propriedades apropriadas para a sua loja de configuração de aplicações.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Detete uma variável ambiental chamada **APP_CONFIGURATION_CONNECTION_STRING,** e deite-a na chave de acesso à sua loja de configuração de aplicações. Na linha de comando, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração faça efeito:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, execute o seguinte comando:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar macOS ou Linux, execute o seguinte comando:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Construa a sua aplicação Spring Boot com a Maven e execute-a, por exemplo:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Depois de a sua aplicação estar em execução, utilize *caracóis* para testar a sua aplicação, por exemplo:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Vê a mensagem que inseriu na loja de configuração de aplicações.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação Java Spring. Para mais informações, consulte [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). Para aprender como permitir que a sua aplicação Java Spring refresque dinamicamente as configurações de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-java-spring-app.md)
