---
title: Quickstart - Lance uma aplicação azure cloud existente utilizando o portal Azure
description: Neste arranque rápido, implemente uma aplicação Spring Cloud para a Nuvem de primavera Azure utilizando o portal Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: a215fe2305b320fe27ef9d868d060f3e9cb14c1c
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431364"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Quickstart: Lançar uma aplicação azure cloud existente usando o portal Azure

Este quickstart mostra-lhe como implementar uma aplicação de Nuvem de primavera existente para o Azure. A Nuvem de primavera Azure permite-lhe executar facilmente aplicações de microserviço baseadas em Nuvem de primavera no Azure. 

Pode encontrar o código de aplicação da amostra utilizado neste tutorial no nosso [repositório de amostras GitHub](https://github.com/Azure-Samples/PiggyMetrics). Quando terminar, a aplicação de amostra fornecida estará acessível online e pronta para ser gerida através do portal Azure.

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Provisão de uma instância de serviço
> * Definir um servidor de configuração, por exemplo
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribuir ponto final público para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> A Nuvem de primavera Azure é atualmente oferecida como uma pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.  Para mais informações sobre suporte durante as pré-visualizações, por favor reveja as nossas [FAQ](https://azure.microsoft.com/support/faq/) ou preencha um pedido de [Suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Tem ferramentas azure comuns pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se estiver ligado à sua subscrição Azure, lance o seu [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell lendo a [nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instale o Git](https://git-scm.com/)
2. [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instale Maven 3.0 ou acima](https://maven.apache.org/download.cgi)
4. [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisão de uma instância de serviço no portal Azure

1. Num novo separador, abra o [portal Azure.](https://ms.portal.azure.com/) 

2. A partir da caixa de pesquisa superior, procure **azure Spring Cloud**.

3. Selecione **Azure Spring Cloud** a partir dos resultados.

 ![Ícone do ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar**.

 ![Ícone do ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring **Criar.**  Considere as seguintes orientações:
    - **Subscrição**: Selecione a subscrição que pretende ser faturada para este recurso.  Certifique-se de que esta subscrição foi adicionada à nossa lista de autorizações para a Azure Spring Cloud.
    - **Grupo de recursos**: Criar novos grupos de recursos para novos recursos é uma boa prática.
    - **Detalhes do serviço/nome**: Especifique o nome da sua instância de serviço.  O nome deve ter entre 4 e 32 caracteres de comprimento e só pode conter letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último personagem deve ser uma letra ou um número.
    - **Localização**: Selecione a localização para a sua instância de serviço. Atualmente, as localizações apoiadas incluem Os EUA Orientais, OESTE DOS 2, Europa Ocidental e Sudeste Asiático.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique no separador **Definição** de Diagnóstico para abrir o seguinte diálogo.

7. Pode definir **os registos de ativação** para *sim* ou *não* de acordo com os seus requisitos.

    ![Ativar registos](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Clique no separador **Tracing.**

9. Pode definir **o rastreio de ativação** para *sim* ou *não* de acordo com os seus requisitos.  Se definir **O rastreio de ativação** para sim, também selecione uma visão de aplicação existente, ou crie uma nova. Sem a especificação de Insights de **Aplicação** haverá um erro de validação.


    ![Rastreio](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Clique em **Rever e criar**.

11. Verifique as suas especificações e clique em **Criar**.

Leva cerca de 5 minutos para o serviço ser implantado.  Uma vez implantado, aparecerá a página **'Overview'** para a instância de serviço.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Configurar o seu servidor de configuração

1. Vá à página de **visão geral** do serviço e selecione **Config Server**.

2. Na secção de **repositório Predefinido,** coloque **URI** para "https://github.com/Azure-Samples/piggymetrics-config".

3. Selecione **Aplicar** para guardar as suas alterações.

    ![Screenshot do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Construir e implementar aplicações de microserviços

1. Abra uma [Casca de Nuvem Azure](https://shell.azure.com) e clone o repositório da aplicação de amostra saquea da sua máquina local.  Aqui, criamos primeiro um diretório temporário chamado `source-code` antes de clonar a app.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Construa o pacote clonado.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```
3. Atribua nomes ao seu grupo de recursos e ao seu serviço. Certifique-se de substituir os espaços reservados abaixo com o nome de grupo de recursos e nome de serviço que você disponibilizou anteriormente neste tutorial.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Crie a aplicação `gateway` e implemente o ficheiro JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Seguindo o mesmo padrão, crie as aplicações `account-service` e `auth-service` e implemente os seus ficheiros JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Leva alguns minutos para terminar de implementar as aplicações. Para confirmar que foram implantados, vá à **lâmina** apps no portal Azure. Devia ver uma linha cada uma das três aplicações.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto final público ao gateway

1. Abra o separador **Apps** no menu à esquerda.

2. Selecione a aplicação `gateway` para mostrar a página **'Overview'.**

3. **Selecione Designado ponto final** para atribuir um ponto final público ao gateway. Isto pode levar alguns minutos.

    ![Screenshot do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Introduza o ponto final do público atribuído **(URL**rotulado) no seu navegador para ver a sua aplicação de execução.

    ![Screenshot do portal ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Provisão de uma instância de serviço
> * Definir um servidor de configuração, por exemplo
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribuir ponto final público para o seu gateway de candidatura

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implementação](spring-cloud-tutorial-prepare-app-deployment.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
