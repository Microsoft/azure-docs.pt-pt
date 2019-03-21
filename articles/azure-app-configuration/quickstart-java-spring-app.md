---
title: Início rápido para saber como utilizar a configuração de aplicações do Azure | Documentos da Microsoft
description: Guia de introdução para utilizar a configuração de aplicações do Azure com aplicações de Java Spring.
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
ms.openlocfilehash: 050a7a3718cb7c9eb864b7ed6ea0787c079e31b9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226322"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Início rápido: Criar uma aplicação de Java Spring com a configuração de aplicações

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Pode usá-lo facilmente armazenar e gerir todas as suas definições de aplicação num único local que é separada a partir do código. Este guia de introdução mostra-lhe como incorporar o serviço uma aplicação Java Spring.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale um suportados [Kit de desenvolvimento Java (JDK)](https://aka.ms/azure-jdks) com a versão 8 e [Apache Maven](https://maven.apache.org/) com a versão 3.0 ou superior.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

1. Para criar um novo arquivo de configuração de aplicação, inicie sessão para o [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, selecione **+ criar um recurso**. Na **pesquisar no Marketplace** , introduza **configuração de aplicações** e prima Enter.

    ![Pesquisa de configuração de aplicações](./media/quickstarts/azure-app-configuration-new.png)

2. Selecione **configuração da aplicação** partir da linha de resultados da pesquisa e, em seguida, selecione **criar**.

3. Sobre o **configuração da aplicação** > **criar** página, introduza as seguintes definições.

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Introduza um nome de recurso exclusiva para utilizar para o recurso de arquivo de configuração de aplicações. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o `-` caráter e consecutivas `-` carateres não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para testar a configuração de aplicações. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e o **subscrição** não for apresentada a lista pendente. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso de arquivo de configuração de aplicação. Este grupo é útil para organizar os vários recursos que pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. central* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes da sua aplicação. |

    ![Criar um arquivo de configuração de aplicação](./media/quickstarts/azure-app-configuration-create.png)

4. Selecione **Criar**. A implementação pode demorar alguns minutos a concluir.

5. Depois de concluída a implementação, selecione **configurações** > **chaves de acesso**. Tome nota da cadeia de caracteres de só de leitura ou primária de leitura-escrita ligação da chave primária. Utilize esta cadeia de ligação mais tarde para configurar a sua aplicação para comunicar com o armazenamento de configuração de aplicação que criou. A cadeia de ligação tem o seguinte formato:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Utilize toda a cadeia na sua aplicação.

6. Selecione **Explorador de chave/valor** > **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | /application/config.message | Olá |

    Deixe **rótulo** e **tipo de conteúdo** branco por agora.

## <a name="create-a-spring-boot-app"></a>Criar uma aplicação Spring Boot aplicação

Utilizar o [Spring Initializr](https://start.spring.io/) para criar um novo projeto de Spring Boot.

1. Navegue para <https://start.spring.io/>.

2. Especifique as seguintes opções:

   * Gerar um **Maven** do projeto com **Java**.
   * Especifique um **Spring Boot** versão igual ou superior a 2.0.
   * Especifique a **grupo** e **artefacto** nomes para a sua aplicação.
   * Adicionar a **Web** dependência.

3. Depois de especificar as opções anteriores, selecione **projeto gerar**. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicação

1. Depois de extrair os ficheiros no sistema local, a simple aplicação de Spring Boot está pronta para edição. Localize a *pom* ficheiro no diretório de raiz da sua aplicação.

2. Abra o *pom. XML* ficheiro num editor de texto e adicione o starter Spring Cloud Azure Config à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M1</version>
    </dependency>
    ```

3. Crie um novo ficheiro de Java com o nome *MessageProperties.java* no diretório do pacote da sua aplicação. Adicione as seguintes linhas:

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

4. Crie um novo ficheiro de Java com o nome *HelloController.java* no diretório do pacote da sua aplicação. Adicione as seguintes linhas:

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

5. Abra o ficheiro de Java do aplicativo principal e adicione `@EnableConfigurationProperties` para ativar esta funcionalidade.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Crie um novo ficheiro designado `bootstrap.properties` sob o diretório de recursos da sua aplicação e adicione as seguintes linhas ao ficheiro. Substitua os valores de exemplo com as propriedades adequadas para seu armazenamento de configuração de aplicação.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Criar a sua aplicação de Spring Boot com o Maven e executá-lo, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Depois da aplicação está em execução, utilize *curl* para testar seu aplicativo, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Verá a mensagem que introduziu no arquivo de configuração de aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação Java Spring. Para obter mais informações, consulte [Spring no Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)