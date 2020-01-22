---
title: Início rápido para adição de sinalizadores de recurso ao Spring boot-configuração de Azure App | Microsoft Docs
description: Um guia de início rápido para adicionar sinalizadores de recursos a aplicativos Spring boot e gerenciá-los na configuração do Azure App
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 3e82354116969b01743700485b5c2dd75b4887e4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310072"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Início rápido: Adicionar sinalizadores de recurso a um aplicativo Spring boot

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo Web Spring boot para criar uma implementação de ponta a ponta do gerenciamento de recursos. Você pode usar o serviço de configuração de aplicativo para armazenar centralmente todos os sinalizadores de recursos e controlar seus Estados.

As bibliotecas de gerenciamento de recursos do Spring boot estendem a estrutura com suporte abrangente ao sinalizador de recursos. Essas bibliotecas **não** têm uma dependência em nenhuma biblioteca do Azure. Eles se integram perfeitamente com a configuração do aplicativo por meio de seu provedor de configuração do Spring boot.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Um [SDK do Java Development Kit](https://docs.microsoft.com/java/azure/jdk) com suporte com a versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versão 3,0 ou superior.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de recursos** >  **+ criar** para adicionar os seguintes sinalizadores de recurso:

    | Chave | Estado |
    |---|---|
    | Beta | Desativado |

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring boot

Você usa o [Spring Initializr](https://start.spring.io/) para criar um novo projeto Spring boot.

1. Navegue para <https://start.spring.io/>.

2. Especifique as seguintes opções:

   - Gere um projeto **Maven** com **Java**.
   - Especifique uma versão do **Spring boot** que seja igual ou maior que 2,0.
   - Especifique os nomes de **grupo** e **artefato** para seu aplicativo.
   - Adicione a dependência **da Web Spring** .

3. Depois de especificar as opções anteriores, selecione **gerar projeto**. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="add-feature-management"></a>Adicionar gerenciamento de recursos

1. Depois de extrair os arquivos no sistema local, seu aplicativo Spring boot simples estará pronto para edição. Localize o arquivo *pom. xml* no diretório raiz do seu aplicativo.

2. Abra o arquivo *pom. xml* em um editor de texto e adicione o iniciador de configuração do Spring Cloud do Azure e o gerenciamento de recursos à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Há uma biblioteca de gerenciamento de recursos que não é da Web que não tem uma dependência no Spring Web. Consulte os [documentos](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) adicionais para obter diferenças. Além disso, quando não estiver usando a configuração de aplicativo, consulte [declaração de sinalizador de recurso](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration).

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Abra _bootstrap. Properties_ no diretório _Resources_ do seu aplicativo. Se _bootstrap. Properties_ não existir, crie-a. Adicione a seguinte linha ao arquivo.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. No portal de configuração de aplicativo para seu repositório de configuração, acesse chaves de acesso. Selecione a guia chaves somente leitura. Nesta guia, copie o valor de uma das cadeias de conexão e adicione-a como uma nova variável de ambiente com o nome de variável de `APP_CONFIGURATION_CONNECTION_STRING`.

1. Abra o arquivo Java do aplicativo principal e adicione `@EnableConfigurationProperties` para habilitar esse recurso.

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

1. Crie um novo arquivo Java chamado *MessageProperties. java* no diretório do pacote do seu aplicativo. Adicione as seguintes linhas:

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

1. Crie um novo arquivo Java chamado *HelloController. java* no diretório do pacote do seu aplicativo. Adicione as seguintes linhas:

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

1. Crie um novo arquivo HTML chamado *Welcome. html* no diretório modelos do seu aplicativo. Adicione as seguintes linhas:

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Crie uma nova pasta chamada CSS em estática e dentro dela um novo arquivo CSS chamado *Main. css*. Adicione as seguintes linhas:

    ```css
    html {
    position: relative;
    min-height: 100%;
    }
    body {
    margin-bottom: 60px;
    }
    .footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    line-height: 60px;
    background-color: #f5f5f5;
    }

    body > .container {
    padding: 60px 15px 0;
    }

    .footer > .container {
    padding-right: 15px;
    padding-left: 15px;
    }

    code {
    font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Crie seu aplicativo Spring boot com o Maven e execute-o, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Abra uma janela do navegador e vá para `https://localhost:8080`, que é a URL padrão para o aplicativo Web hospedado localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. No portal de configuração do aplicativo, selecione **Gerenciador de recursos**e altere o estado da chave **beta** para **ativado**:

    | Chave | Estado |
    |---|---|
    | Beta | Ativado |

4. Atualize a página do navegador para ver as novas definições de configuração.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou para gerenciar recursos em um aplicativo Web Spring boot por meio das [bibliotecas de gerenciamento de recursos](https://go.microsoft.com/fwlink/?linkid=2074664).

- Saiba mais sobre o [Gerenciamento de recursos](./concept-feature-management.md).
- [Gerenciar sinalizadores de recurso](./manage-feature-flags.md).
- [Use os sinalizadores de recurso em um aplicativo Spring boot Core](./use-feature-flags-spring-boot.md).
