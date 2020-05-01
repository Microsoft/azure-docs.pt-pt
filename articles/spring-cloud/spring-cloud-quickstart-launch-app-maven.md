---
title: Quickstart - Lançar uma aplicação usando Maven com nuvem de primavera azure
description: Neste arranque rápido, lance uma aplicação de amostra usando Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: f374e0611b887e2a2c787569d9fe58bc1a5c84e9
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594071"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Quickstart: Lançar uma aplicação Azure Spring Cloud utilizando o plug-in Maven

Utilizando o plug-in Azure Spring Cloud Maven, pode facilmente criar e atualizar as suas aplicações Azure Spring Cloud. Ao definir uma configuração, pode implementar aplicações para o serviço Azure Spring Cloud existente. Neste artigo, você usa uma aplicação de amostra chamada PiggyMetrics para demonstrar esta funcionalidade.

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Provisão de uma instância de serviço
> * Configurar um servidor de configuração, por exemplo,
> * Clone e construa aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribuir um ponto final público para a sua aplicação

>[!Note]
> A Nuvem de primavera Azure é atualmente oferecida como uma pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.  Para mais informações sobre suporte durante as pré-visualizações, por favor reveja as nossas [FAQ](https://azure.microsoft.com/support/faq/) ou preencha um pedido de [Suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.


>[!TIP]
> Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar os comandos neste artigo. Tem ferramentas azure comuns pré-instaladas, incluindo as versões mais recentes de Git, o Java Development Kit (JDK), Maven e o Azure CLI. Se tiver assinado a sua assinatura Azure, lance [a Azure Cloud Shell](https://shell.azure.com). Para mais informações, consulte [a visão geral da Casca de Nuvem Azure](../cloud-shell/overview.md).

Para concluir este guia de início rápido:

1. [Instale Git](https://git-scm.com/).
2. [Instale jDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Instale maven 3.0 ou mais tarde.](https://maven.apache.org/download.cgi)
4. [Inscreva-se para uma subscrição azure gratuita.](https://azure.microsoft.com/free/)

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisão de uma instância de serviço no portal Azure

1. Num navegador web, abra [este link para Azure Spring Cloud no portal Azure](https://ms.portal.azure.com/#create/Microsoft.AppPlatform), e inscreva-se na sua conta.

1. Forneça os Detalhes do **Projeto** para a aplicação da amostra da seguinte forma:

    1. Selecione a **Subscrição** com a qual a aplicação será associada.
    1. Selecione ou crie um grupo de recursos para a aplicação. Recomendamos a criação de um novo grupo de recursos.  O exemplo abaixo mostra um `myspringservice`novo grupo de recursos chamado .
    1. Forneça um nome para o novo serviço Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres de comprimento e só pode conter letras minúsculas, números e hífenes. O primeiro carácter do nome de serviço deve ser uma letra e o último personagem deve ser uma letra ou um número.  O serviço no exemplo abaixo `contosospringcloud`tem o nome .
    1. Selecione um local para a sua aplicação a partir das opções fornecidas.  Neste exemplo, selecionamos `East US`.
    1. Selecione **Review + crie** para rever um resumo do seu novo serviço.  Se tudo parecer correto, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Selecione Review + criar](media/maven-qs-review-create.jpg)

Leva cerca de 5 minutos para o serviço ser implantado. Depois de o serviço ser implementado, selecione **Ir para o recurso** e aparece a página de visão **geral** para a instância de serviço.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Configurar o seu servidor de configuração

1. Na página **de visão geral** do serviço, selecione **Config Server**.
1. Na secção de **reposição Predefinido,** coloque **uri** para **https://github.com/Azure-Samples/piggymetrics-config**, e, em seguida, selecione **Aplicar** para guardar as suas alterações.

    > [!div class="mx-imgBorder"]
    > ![Definir e aplicar definições de config](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Clone e construa o repositório de aplicação da amostra

1. Lance a [Casca de Nuvem Azure.](https://shell.azure.com)

1. Clone o repositório Git executando o seguinte comando:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Alterar o diretório e construir o projeto executando o seguinte comando:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gere configurações e desdobre-se para a Nuvem de primavera Azure

1. Gere configurações executando o seguinte comando na pasta raiz da PiggyMetrics contendo o POM-mãe:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Selecione `gateway`os`auth-service`módulos, e `account-service`.

    b. Selecione a sua subscrição e o cluster de serviço Azure Spring Cloud.

    c. Na lista de projetos fornecidos, insira o número que corresponde `gateway` para lhe dar acesso público.
    
    d. Confirme a configuração.

1. O POM agora contém as dependências e configurações do plugin. Implementar as aplicações utilizando o seguinte comando:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Depois de concluída a implantação, pode aceder à PiggyMetrics utilizando o URL fornecido na saída a partir do comando anterior.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou uma aplicação da Nuvem de primavera de um repositório Maven. Para saber mais sobre o Azure Spring Cloud, continue a ser o tutorial sobre a preparação da sua aplicação para implementação.

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implementação](spring-cloud-tutorial-prepare-app-deployment.md)
> [Saiba mais sobre plug-ins Maven para Azure](https://github.com/microsoft/azure-maven-plugins)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
