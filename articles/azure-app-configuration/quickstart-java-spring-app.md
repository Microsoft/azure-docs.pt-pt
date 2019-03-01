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
ms.openlocfilehash: c5e172410c7cb60888107e37064c2429caf6df9e
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195494"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Início rápido: Criar uma aplicação de Java Spring com a configuração de aplicações

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Permite-lhe armazenar e gerir todas as suas definições de aplicação num único local que é separada do seu código facilmente. Este guia de introdução mostra-lhe como incorporar o serviço uma aplicação Java Spring.

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, instale um suportados [Kit de desenvolvimento Java (JDK)](https://aka.ms/azure-jdks) com a versão 8 e [Apache Maven](http://maven.apache.org/) com a versão 3.0 ou superior.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

1. Para criar um novo arquivo de configuração de aplicação, primeiro inicie sessão para o [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na **pesquisar no Marketplace** caixa de texto, tipo **configuração de aplicações** e pressione **Enter**.

    ![Pesquisa de configuração de aplicações](./media/quickstarts/azure-app-configuration-new.png)

2. Clique em **configuração da aplicação** nos resultados da pesquisa e, em seguida **criar**.

3. Na **configuração da aplicação** > **criar** página, introduza as seguintes definições:

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Introduza um nome de recurso exclusiva para utilizar para o recurso de arquivo de configuração de aplicações. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o caráter `-` e os carateres `-` consecutivos não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para testar a configuração de aplicações. Se a sua conta tiver apenas uma subscrição, esta é selecionada automaticamente e não é apresentado o menu pendente **Subscrição**. |
    | **Grupo de Recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso de arquivo de configuração de aplicação. Este grupo é útil para organizar os vários recursos que pretende eliminar em simultâneo, ao eliminar o grupo de recursos. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. central* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |

    ![Criar um arquivo de configuração de aplicação](./media/quickstarts/azure-app-configuration-create.png)

4. Clique em **Criar**. A implementação pode demorar alguns minutos.

5. Assim que a implementação estiver concluída, clique em **configurações** > **chaves de acesso**. Anote a cadeia só de leitura ou primária de leitura-escrita ligação da chave primária. O usará posteriormente para configurar a sua aplicação para comunicar com o armazenamento de configuração de aplicação que acabou de criar. A cadeia de ligação tem o seguinte formato:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Terá de utilizar toda a cadeia na sua aplicação.

6. Clique em **Explorador de chave/valor** e **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | /application/config.message | Olá |

    Irá sair **rótulo** e **tipo de conteúdo** branco por agora.

## <a name="create-a-spring-boot-app"></a>Criar uma aplicação Spring Boot aplicação

Irá utilizar o [Spring Initializr](https://start.spring.io/) para criar um novo projeto de Spring Boot.

1. Navegue para <https://start.spring.io/>.

2. Especifique as seguintes opções:

   * Gerar um **Maven** do projeto com **Java**.
   * Especifique um **Spring Boot** versão igual ou superior a 2.0.
   * Especifique a **grupo** e **artefacto** nomes para a sua aplicação.
   * Adicionar a **Web** dependência.

3. Quando especificar as opções listadas acima, clique em **projeto gerar**. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

## <a name="connect-to-app-configuration-store"></a>Ligar ao arquivo de configuração de aplicação

1. Depois de ter de extrair os ficheiros no sistema local, a simple aplicação de Spring Boot estará pronta para edição. Localize a *pom* ficheiro no diretório de raiz da sua aplicação.

2. Abra o *pom. XML* ficheiro num editor de texto e adicione o starter Spring Cloud Azure Config à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M1</version>
    </dependency>
    ```

3. Crie um novo ficheiro de Java com o nome *MessageProperties.java* no diretório do pacote da sua aplicação. Adicione o seguinte linhas.

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

4. Crie um novo ficheiro de Java com o nome *HelloController.java* no diretório do pacote da sua aplicação. Adicione o seguinte linhas.

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

6. Crie um novo ficheiro designado `bootstrap.properties` sob o diretório de recursos da sua aplicação, adicione as seguintes linhas ao ficheiro e substitua os valores de exemplo com as propriedades adequadas para seu armazenamento de configuração de aplicação.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Criar a sua aplicação de Spring Boot com o Maven e executá-lo. Por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Quando seu aplicativo estiver em execução, pode usar *curl* para testar a aplicação; por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Deverá ver a mensagem que introduziu para o arquivo de configuração de aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação Java Spring. Visite [Spring na home page do Azure](https://docs.microsoft.com/java/azure/spring-framework/) para obter mais informações.

Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)