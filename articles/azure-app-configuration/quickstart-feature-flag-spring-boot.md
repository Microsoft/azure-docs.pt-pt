---
title: Quickstart para adicionar bandeiras de recurso à Bota de primavera com configuração de app Azure
description: Adicione bandeiras de funcionalidade sabotadas e gerencie-as usando a configuração da app Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: a0d3c23f8f53b8ddfbd3fbd1cb1744a47664ce08
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774011"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação spring boot

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação web Spring Boot para criar uma implementação final da gestão de funcionalidades. Pode utilizar o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados.

As bibliotecas de Gestão de Recursos de Arranque de primavera alargam o quadro com um suporte abrangente de bandeira de recurso. Estas bibliotecas **não** têm dependência de nenhuma biblioteca Azure. Integram-se perfeitamente com a Configuração da App através do seu fornecedor de configuração de Botas de primavera.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Um Kit de Desenvolvimento Java suportado [SDK](https://docs.microsoft.com/java/azure/jdk) com a versão 8.
* [Apache Maven](https://maven.apache.org/download.cgi) versão 3.0 ou superior.

## <a name="create-an-app-configuration-instance"></a>Criar uma instância de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione Gestor de **Funcionalidades**  >  **+Adicione** para adicionar uma bandeira de recurso chamada `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe `label` indefinido por enquanto.

## <a name="create-a-spring-boot-app"></a>Crie uma aplicação spring boot

Use o [Initializr de primavera](https://start.spring.io/) para criar um novo projeto spring boot.

1. Navegue para <https://start.spring.io/>.

1. Especifique as seguintes opções:

   * Gere um projeto do **Maven** com **Java**.
   * Especifique uma versão **Spring Boot** igual ou superior a 2.0.
   * Especifique os nomes do **Grupo** e do **Artefacto** da aplicação.  Este artigo usa `com.example` e `demo` .
   * Adicione a dependência da **Web da primavera.**

1. Depois de especificar as opções anteriores, selecione **Generate Project**. Quando solicitado, descarregue o projeto para o seu computador local.

## <a name="add-feature-management"></a>Adicionar gestão de recursos

1. Depois de extrair os ficheiros do seu sistema local, a sua aplicação Spring Boot está pronta para ser editada. Localize *pom.xml* no diretório raiz da sua aplicação.

1. Abra o ficheiro *pom.xml* num editor de texto e adicione o seguinte à lista `<dependencies>` de:

    **Nuvem de primavera 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Nuvem de primavera 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Há uma biblioteca de gestão de recursos não web que não tem uma dependência na web da primavera. Consulte a [documentação](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) do GitHub para as diferenças.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Navegue para o `resources` diretório da sua app e `bootstrap.properties` abra.  Se o ficheiro não existir, crie-o. Adicione a seguinte linha ao ficheiro.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. No portal de configuração da aplicação para a sua loja de config, selecione `Access keys` a partir da barra lateral. Selecione o separador de teclas apenas para leitura. Copiar o valor da cadeia de ligação primária.

1. Adicione a cadeia de ligação primária como uma variável ambiental utilizando o nome variável `APP_CONFIGURATION_CONNECTION_STRING` .

1. Abra o ficheiro Java da aplicação principal e adicione `@EnableConfigurationProperties` para ativar esta funcionalidade.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Crie um novo ficheiro Java chamado *MessageProperties.java* no diretório de pacotes da sua aplicação.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Crie um novo ficheiro Java chamado *HelloController.java* no diretório de pacotes da sua aplicação.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. Crie um novo ficheiro HTML chamado *welcome.html* no diretório de modelos da sua aplicação.

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

1. Crie uma nova pasta chamada CSS dentro `static` e no seu interior um novo ficheiro CSS chamado *main.css*.

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

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Crie a aplicação Spring Boot com o Maven e execute-a.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra uma janela do navegador e vá ao URL: `http://localhost:8080/welcome` .

    ![Lançamento de app Quickstart local](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. No portal de configuração da aplicação selecione Gestor de **Funcionalidades**, e altere o estado da chave **Beta** para **On:**

    | Chave | Estado |
    |---|---|
    | Beta | Ativado |

1. Refresque a página do navegador para ver as novas configurações de configuração.

    ![Lançamento de app Quickstart local](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de configuração de aplicações e utilizou-a para gerir funcionalidades numa aplicação web spring boot através das [bibliotecas de Gestão de Recursos.](https://go.microsoft.com/fwlink/?linkid=2074664)

* Saiba mais sobre gestão de [recursos.](./concept-feature-management.md)
* [Gerir as bandeiras de recurso.](./manage-feature-flags.md)
* [Utilize bandeiras de recurso numa aplicação Spring Boot Core](./use-feature-flags-spring-boot.md).
