---
title: Início rápido para aprender a usar a configuração de Azure App
description: Um guia de início rápido para usar Azure App configuração com aplicativos Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 172fe646b294ca511a22128094c56172c4268018
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750305"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Início rápido: criar um aplicativo Spring Java com a configuração Azure App

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo Spring Java para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Um [JDK (Java Development Kit)](https://docs.microsoft.com/java/azure/jdk) com suporte com a versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versão 3,0 ou superior.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | /application/config.message | Olá |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring boot

Use o [Spring Initializr](https://start.spring.io/) para criar um novo projeto Spring boot.

1. Navegue para <https://start.spring.io/>.

2. Especifique as seguintes opções:

   * Gere um projeto **Maven** com **Java**.
   * Especifique uma versão do **Spring boot** que seja igual ou maior que 2,0.
   * Especifique os nomes de **grupo** e **artefato** para seu aplicativo.
   * Adicione a dependência **da Web Spring** .

3. Depois de especificar as opções anteriores, selecione **gerar projeto**. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Depois de extrair os arquivos no sistema local, seu aplicativo Spring boot simples estará pronto para edição. Localize o arquivo *pom. xml* no diretório raiz do seu aplicativo.

2. Abra o arquivo *pom. xml* em um editor de texto e adicione o iniciador de configuração do Spring Cloud do Azure à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0</version>
    </dependency>
    ```

3. Crie um novo arquivo Java chamado *MessageProperties. java* no diretório do pacote do seu aplicativo. Adicione as seguintes linhas:

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

4. Crie um novo arquivo Java chamado *HelloController. java* no diretório do pacote do seu aplicativo. Adicione as seguintes linhas:

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

5. Abra o arquivo Java do aplicativo principal e adicione `@EnableConfigurationProperties` para habilitar esse recurso.

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

6. Crie um novo arquivo chamado `bootstrap.properties` no diretório de recursos do seu aplicativo e adicione as linhas a seguir ao arquivo. Substitua os valores de exemplo pelas propriedades apropriadas para seu repositório de configuração de aplicativo.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Crie seu aplicativo Spring boot com o Maven e execute-o, por exemplo:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```

2. Depois que o aplicativo estiver em execução, use a *rotação* para testar seu aplicativo, por exemplo:

      ```CLI
      curl -X GET http://localhost:8080/
      ```

    Você verá a mensagem inserida no repositório de configuração de aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo Spring Java. Para obter mais informações, consulte [Spring no Azure](https://docs.microsoft.com/java/azure/spring-framework/). Para saber como usar uma identidade gerenciada do Azure para simplificar o acesso à configuração do aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
