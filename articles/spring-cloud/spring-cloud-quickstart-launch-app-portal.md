---
title: Quickstart - Lançar uma aplicação Azure Spring Cloud existente utilizando o portal Azure
description: Neste arranque rápido, implemente uma aplicação Cloud spring para a Nuvem de primavera Azure usando o portal Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 664984581ffdfa319ebb121a3475256ec5a2692c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260582"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Quickstart: Lançar uma aplicação Azure Spring Cloud existente utilizando o portal Azure

Este quickstart mostra-lhe como implementar uma aplicação de Cloud spring existente para Azure. O Azure Spring Cloud permite-lhe executar facilmente aplicações de microserviços baseados em Spring Cloud no Azure. 

Antes de executar este exemplo, pode experimentar o [arranque básico](spring-cloud-quickstart.md).

Pode encontrar o código de aplicação da amostra utilizado neste tutorial no nosso [repositório de amostras GitHub.](https://github.com/Azure-Samples/PiggyMetrics) Quando terminar, a aplicação de amostra fornecida estará acessível online e pronta para ser gerida através do portal Azure.

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Desa parte para um servidor de configuração para uma instância
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribua o ponto final público para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Tem ferramentas Azure comuns pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver iniciado sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar Maven 3.0 ou superior](https://maven.apache.org/download.cgi)
4. [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Prestação de uma instância de serviço no portal Azure

1. Num novo separador, abra o [portal Azure](https://ms.portal.azure.com/). 

2. A partir da caixa de pesquisa superior, procure **a Azure Spring Cloud.**

3. Selecione **Azure Spring Cloud** a partir dos resultados.

 ![Início asc](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar**.

 ![ASC adicionar](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:
    - **Subscrição**: Selecione a subscrição que pretende ser faturada para este recurso.  Certifique-se de que esta subscrição foi adicionada à nossa lista de admissões para Azure Spring Cloud.
    - **Grupo de recursos**: Criar novos grupos de recursos para novos recursos é uma boa prática.
    - **Detalhes de serviço/Nome**: Especifique o nome da sua instância de serviço.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização**: Selecione a localização para a sua instância de serviço. Locais atualmente apoiados incluem Os EUA Orientais, EUA Ocidentais 2, Europa Ocidental e Sudeste Asiático.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique no **separador Definição de Diagnóstico** para abrir o seguinte diálogo.

7. Pode **configurar Iniciar registos** para *sim* ou *não* de acordo com os seus requisitos.

    ![Ativar registos](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Clique no **separador Rastreio.**

9. Pode definir **Ativar o rastreio** para *sim* ou *não* de acordo com os seus requisitos.  Se definir **Ativar o rastreio** para sim, selecione também uma visão de aplicação existente ou crie uma nova. Sem a especificação **De Insights de Aplicação** haverá um erro de validação.


    ![Vista de rastreio](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Clique em **Rever e criar**.

11. Verifique as suas especificações e clique em **Criar**.

Leva cerca de 5 minutos para o serviço ser implantado.  Uma vez implantado, aparecerá a página **'Visão Geral'** para a instância de serviço.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Configurar o seu servidor de configuração

1. Aceda à página **de visão geral** do serviço e selecione **Config Server**.

2. Na secção **de repositório padrão,** desafine o **URI** para https://github.com/Azure-Samples/piggymetrics-config " .

3. Selecione **Aplicar** para guardar as alterações.

    ![Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Construir e implementar aplicações de microserviços

1. Abra uma [Concha Azure Cloud](https://shell.azure.com) ou a sua concha local com O Azure CLI instalado. Aqui, criamos primeiro um diretório temporário chamado `source-code` antes de clonar a aplicação da amostra.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Construa o pacote clonado.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Instale a extensão Azure Spring Cloud para o CLI Azure utilizando o seguinte comando

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Atribua nomes ao seu grupo de recursos e ao seu serviço. Não se esqueça de substituir os espaços reservados abaixo pelo nome do grupo de recursos e nome de serviço que aprovisionou anteriormente neste tutorial.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Crie a `gateway` aplicação e desloque o ficheiro JAR.

    Utilizando a extensão Cloud spring, crie a aplicação:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Seguindo o mesmo padrão, crie as `account-service` aplicações e `auth-service` implemente os seus ficheiros JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Demora alguns minutos a terminar a implementação das aplicações. Para confirmar que foram implementados, aceda à lâmina **apps** no portal Azure. Devia ver uma linha de cada uma das três aplicações.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto final público ao gateway

1. Abra o **separador Apps** no menu à esquerda.

2. Selecione a `gateway` aplicação para mostrar a página **'Vista Geral'.**

3. Selecione **Atribuir Ponto final** para atribuir um ponto final público ao gateway. A implementação pode demorar alguns minutos.

    ![Ponto final do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Introduza o ponto final público designado **(URL**com rótulo) no seu navegador para ver a sua aplicação de execução.

    ![App de amostra de portal ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Desa parte para um servidor de configuração para uma instância
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribua o ponto final público para o seu gateway de aplicação

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
