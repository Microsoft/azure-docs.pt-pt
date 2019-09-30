---
title: Início rápido para aprender a usar a configuração de Azure App | Microsoft Docs
description: Um guia de início rápido para usar Azure App configuração com aplicativos Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: f4ebbd4f37422c5aa2fea07a243eb624ec9e2961
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687029"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Início rápido: Criar um aplicativo Spring Java com configuração Azure App

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo Spring Java para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Um [JDK (Java Development Kit)](https://docs.microsoft.com/java/azure/jdk) com suporte com a versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versão 3,0 ou superior.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | /application/config.message | Olá |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring boot

Você usa o [Spring Initializr](https://start.spring.io/) para criar um novo projeto Spring boot.

1. Navegue para <https://start.spring.io/>.

2. Especifique as seguintes opções:

   * Gere um projeto **Maven** com **Java**.
   * Especifique uma versão do **Spring boot** que seja igual ou maior que 2,0.
   * Especifique os nomes de **grupo** e **artefato** para seu aplicativo.
   * Adicione a dependência **da Web** .

3. Depois de especificar as opções anteriores, selecione **gerar projeto**. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Depois de extrair os arquivos no sistema local, seu aplicativo Spring boot simples estará pronto para edição. Localize o arquivo *pom. xml* no diretório raiz do seu aplicativo.

2. Abra o arquivo *pom. xml* em um editor de texto e adicione o iniciador de configuração do Spring Cloud do Azure à `<dependencies>`lista de:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    ```

3. Crie um novo arquivo Java chamado *MessageProperties. java* no diretório do pacote do seu aplicativo. Adicione as seguintes linhas:

    ```java
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
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Crie um novo arquivo chamado `bootstrap.properties` no diretório de recursos do seu aplicativo e adicione as linhas a seguir ao arquivo. Substitua os valores de exemplo pelas propriedades apropriadas para seu repositório de configuração de aplicativo.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Crie seu aplicativo Spring boot com o Maven e execute-o, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Depois que o aplicativo estiver em execução, use a *rotação* para testar seu aplicativo, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Você verá a mensagem inserida no repositório de configuração de aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo Spring Java. Para obter mais informações, consulte [Spring no Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Para saber mais sobre como usar a configuração de aplicativo, prossiga para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
