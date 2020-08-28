---
title: Quickstart - Implemente a sua primeira aplicação Azure Spring Cloud
description: Neste quickstart, implementamos uma aplicação spring cloud olá para a Nuvem de primavera Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: e4813f5c8c156fdf381a55ae450cf0ee35cedfa7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048211"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud

Este quickstart explica como implementar uma aplicação simples de microserviço Azure Spring Cloud para funcionar no Azure. 

O código de aplicação utilizado neste tutorial é uma aplicação simples construída com o Initializr primavera. Quando tiver concluído este exemplo, a aplicação será acessível online e poderá ser gerida através do portal Azure.

Este quickstart explica como:

> [!div class="checklist"]
> * Gerar um projeto básico de Nuvem de primavera
> * Prestação de uma instância de serviço
> * Construa e implemente a app com um ponto final público
> * Registos de fluxo em tempo real

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-hello-project"></a>Gere um projeto de olá Cloud Spring Cloud

Comece com [o Initializr de primavera](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) para gerar um projeto de amostra com dependências recomendadas para Azure Spring Cloud. A imagem a seguir mostra o Initializr criado para este projeto de amostra.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Página inicializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Clique **em Gerar** quando todas as dependências estiverem definidas. Faça o download e desembale o pacote e, em seguida, adicione a seguinte dependência ao ficheiro de `pom.xml` aplicação.

    ```xml
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
            <version>2.3.0</version>
        </dependency>
    ```

1. Crie um controlador web para uma simples aplicação web adicionando `src/main/java/com/example/hellospring/HelloController.java` o seguinte:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar um exemplo de Azure Spring Cloud

O procedimento a seguir cria uma instância de Azure Spring Cloud usando o portal Azure.

1. Num novo separador, abra o [portal Azure](https://ms.portal.azure.com/). 

2. A partir da caixa de pesquisa superior, procure *a Azure Spring Cloud.*

3. Selecione *Azure Spring Cloud* a partir dos resultados.

    ![Início do ícone ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar**.

    ![Adicionar ícone ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:
    - **Subscrição**: Selecione a subscrição que pretende ser faturada para este recurso.
    - **Grupo de recursos**: Criar novos grupos de recursos para novos recursos é uma boa prática. Isto será usado em etapas posteriores como **\<resource group name\>** .
    - **Dados de serviço/Nome**: Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização**: Selecione a região para a sua instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Rever e criar**.

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
O procedimento a seguir constrói e implementa a aplicação utilizando o Azure CLI. Execute o seguinte comando na raiz do projeto.

1. Construa o projeto usando Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Se ainda não o instalou) Instale a extensão Azure Spring Cloud para o Azure CLI:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Crie a app com o ponto final público atribuído:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Implementar o ficheiro Jar para a aplicação:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Demora alguns minutos a terminar a implementação da aplicação. Para confirmar que foi implementado, vá à lâmina **apps** no portal Azure. Devia ver o estado do pedido.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

O procedimento a seguir utiliza o plug-in IntelliJ para a Azure Spring Cloud para implementar a aplicação da amostra no IntelliJ IDEA.  

### <a name="import-hello-project"></a>Projeto importe olá

1. Abra o diálogo IntelliJ **Welcome** e selecione **Import Project** para abrir o assistente de importação.
1. Selecione `hellospring` a pasta.

    ![Projeto de Importação](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Implementar a aplicação
Para poder implementar no Azure tem de iniciar sôms com a sua conta Azure e escolher a sua subscrição.  Para obter informações de insusição, consulte [a Instalação e a inscrição.](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

1. Clique com o botão direito no seu projeto no explorador de projetos IntelliJ e selecione **Azure**  ->  **Deploy to Azure Spring Cloud**.

    ![Desdobre-se para Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure.png)

1. Aceite o nome para aplicação no campo **Nome.** **O nome** refere-se à configuração, não ao nome da aplicação. Os utilizadores normalmente não precisam de alterá-lo.
1. Na caixa de texto **do Artefacto,** selecione *hellospring-0.0.1-SNAPSHOT.jar*.
1. Na caixa de texto **de assinatura,** verifique a sua subscrição.
1. Na caixa de texto **da Nuvem de primavera,** selecione a instância de Azure Spring Cloud que criou no [exemplo de Provision Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Na **App:** caixa de texto, selecione **Criar app...**.
1. Introduza *a primavera de hellospring*e, em seguida, clique **em OK**.

    ![Implementar para Azure OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)

1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** O plug-in executará o comando `mvn package` na `hellospring` aplicação e implantará o frasco gerado pelo `package` comando.
---

Uma vez concluída a implementação, pode aceder à aplicação em `https://<service instance name>-hellospring.azuremicroservices.io/` .

  ![Aplicativo de acesso a partir do navegador](media/spring-cloud-quickstart-java/access-app-browser.png)

## <a name="streaming-logs-in-real-time"></a>Registos de streaming em tempo real

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Utilize o seguinte comando para obter registos em tempo real da App.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Os registos aparecem nos resultados:

[Registos de ![ ](media/spring-cloud-quickstart-java/streaming-logs.png) streaming ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selecione **Azure Explorer,** em **seguida, Cloud primavera**.
1. Clique com o botão direito na aplicação de execução.
1. Selecione **'Registos** de streaming' da lista de drop-down.

    ![Selecione registos de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecione a instância.

    ![Selecione a instância](media/spring-cloud-intellij-howto/select-instance.png)

1. O registo de streaming será visível na janela de saída.

    ![Saída de registo de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)
---

Para funcionalidades avançadas de análise de registos, visite o **separador Registos** no menu do [portal Azure.](https://portal.azure.com/) Os troncos aqui têm uma latência de alguns minutos.

[![Registos Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos
Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando na Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Gere um projeto básico de Azure Spring Cloud
> * Prestação de uma instância de serviço
> * Construa e implemente a app com um ponto final público
> * Registos de streaming em tempo real
## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construir e Executar Microserviços](spring-cloud-quickstart-sample-app-introduction.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
