---
title: Arranque rápido para aprender a usar a configuração da app Azure
description: Neste arranque rápido, crie uma aplicação Java Spring com Configuração de Aplicação Azure para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932035"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação Java Spring com Configuração de Aplicação Azure

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação Java Spring para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Um Kit de [Desenvolvimento java suportado (JDK)](/java/azure/jdk) com a versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versão 3.0 ou superior.

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **+ Crie** valor  >  **chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | /application/config.message | Hello |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="create-a-spring-boot-app"></a>Criar uma aplicação de Boot de primavera

Utilize o [Initializr primavera](https://start.spring.io/) para criar um novo projeto de Bota de primavera.

1. Navegue para <https://start.spring.io/>.

1. Especifique as seguintes opções:

   - Gere um projeto do **Maven** com **Java**.
   - Especifique uma versão **de Boot de mola** igual ou superior a 2.0.
   - Especifique os nomes do **Grupo** e do **Artefacto** da aplicação.
   - Adicione a dependência da **Web da primavera.**

1. Depois de especificar as opções anteriores, **selecione Gerar Projeto**. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Depois de extrair os ficheiros do seu sistema local, a sua aplicação de Bota de Mola simples está pronta para edição. Localize o ficheiro *pom.xml* no diretório de raiz da sua aplicação.

1. Abra o ficheiro *pom.xml* num editor de texto e adicione o arranque Spring Cloud Azure Config à lista `<dependencies>` de:

    **Nuvem de primavera 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Nuvem de primavera 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
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

1. Abra o ficheiro Java da aplicação principal e adicione `@EnableConfigurationProperties` para ativar esta funcionalidade.

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

1. Crie um novo ficheiro nomeado `bootstrap.properties` no diretório de recursos da sua app e adicione as seguintes linhas ao ficheiro. Substitua os valores da amostra pelas propriedades apropriadas para a sua loja de Configuração de Aplicações.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Desaprote uma variável ambiental chamada **APP_CONFIGURATION_CONNECTION_STRING**, e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Na linha de comando, executar o seguinte comando e reiniciar o pedido de comando para permitir que a alteração entre em vigor:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Construa a sua aplicação Boot de primavera com a Maven e execute-a, por exemplo:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Depois da sua aplicação estar em execução, use *o curl* para testar a sua aplicação, por exemplo:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Vê a mensagem que inseriu na loja de Configuração de Aplicações.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a com uma aplicação Java Spring. Para mais informações, consulte [primavera em Azure.](/java/azure/spring-framework/) Para aprender como ativar a sua aplicação Java Spring para atualizar dinamicamente as configurações de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-java-spring-app.md)