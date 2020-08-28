---
title: Quickstart - Lançar uma aplicação usando Maven com Azure Spring Cloud
description: Neste quickstart, lance uma aplicação de amostra usando Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 95ae9c596742b075b5d78d22b925b3adf9285a7d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048062"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Quickstart: Lançar uma aplicação Azure Spring Cloud utilizando o plug-in Maven

Utilizando o plug-in Azure Spring Cloud Maven, pode facilmente criar e atualizar as suas aplicações Azure Spring Cloud. Ao prédefinir uma configuração, pode implementar aplicações para o seu serviço Azure Spring Cloud existente. Neste artigo, utiliza-se uma aplicação de amostra chamada PiggyMetrics para demonstrar esta funcionalidade.

Antes de executar este exemplo, pode experimentar o [arranque básico](spring-cloud-quickstart.md).

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Configurar um servidor de configuração para uma instância
> * Clone e construa aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribua um ponto final público para a sua aplicação

>[!Note]
> Azure Spring Cloud é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem que os clientes experimentem novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para mais informações sobre o suporte durante as pré-visualizações, por favor reveja as nossas [PERGUNTAS Frequentes](https://azure.microsoft.com/support/faq/) ou preencha um [pedido de Apoio](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.


>[!TIP]
> Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar os comandos neste artigo. Tem ferramentas Azure comuns pré-instaladas, incluindo as versões mais recentes do Git, o Java Development Kit (JDK), Maven e o Azure CLI. Se você está inscrito na sua subscrição Azure, lance [Azure Cloud Shell](https://shell.azure.com). Para mais informações, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md).

Para concluir este guia de início rápido:

1. [Instale git](https://git-scm.com/).
2. [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Instale o Maven 3.0 ou mais tarde](https://maven.apache.org/download.cgi).
4. [Inscreva-se para uma subscrição gratuita do Azure.](https://azure.microsoft.com/free/)

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Prestação de uma instância de serviço no portal Azure

1. Num navegador web, abra [este link para Azure Spring Cloud no portal Azure](https://ms.portal.azure.com/#create/Microsoft.AppPlatform), e inscreva-se na sua conta.

1. Fornecer os Detalhes do **Projeto** para a aplicação da amostra da seguinte forma:

    1. Selecione a **Subscrição** com a qual a aplicação será associada.
    1. Selecione ou crie um grupo de recursos para a aplicação. Recomendamos a criação de um novo grupo de recursos.  O exemplo abaixo mostra um novo grupo de recursos chamado `myspringservice` .
    1. Forneça um nome para o novo serviço Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes. O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.  O serviço no exemplo abaixo tem o nome `contosospringcloud` .
    1. Selecione um local para a sua aplicação a partir das opções fornecidas.  Neste exemplo, selecionamos `East US` .
    1. Selecione **Review + crie** para rever um resumo do seu novo serviço.  Se tudo parecer correto, **selecione Criar**.

    > [!div class="mx-imgBorder"]
    > ![Selecione Review + criar](media/maven-qs-review-create.jpg)

Leva cerca de 5 minutos para o serviço ser implantado. Depois de o serviço ser implementado, selecione **Ir para** o recurso e aparecer a página **'Vista Geral'** para a instância de serviço.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Configurar o seu servidor de configuração

1. Na página **'Vista Geral'** do serviço, selecione **Config Server**.
1. Na secção **de repositório predefinido,** desafine **o URI** **https://github.com/Azure-Samples/piggymetrics-config** e, em seguida, **selecione Aplicar** para guardar as suas alterações.

    > [!div class="mx-imgBorder"]
    > ![Definir e aplicar configurações config](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Clone e construa o repositório de aplicação de amostra

1. Lançar a [Azure Cloud Shell](https://shell.azure.com).

1. Clone o repositório git executando o seguinte comando:

    ```console
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Alterar o diretório e construir o projeto executando o seguinte comando:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gerei configurações e implementa para a Nuvem de primavera do Azure

1. Gerar configurações executando o seguinte comando na pasta raiz da PiggyMetrics contendo o POM principal:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Selecione os módulos `gateway` `auth-service` , e `account-service` .

    b. Selecione a sua subscrição e o cluster de serviço Azure Spring Cloud.

    c. Na lista de projetos fornecidos, insira o número que corresponde `gateway` para lhe dar acesso público.
    
    d. Confirme a configuração.

1. O POM agora contém as dependências e configurações de plugin. Implementar as aplicações utilizando o seguinte comando:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Depois de concluída a implementação, pode aceder à PiggyMetrics utilizando o URL fornecido na saída do comando anterior.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, lançou uma aplicação "Spring Cloud" a partir de um repositório de Maven. Para saber mais sobre a Azure Spring Cloud, continue o tutorial sobre a preparação da sua app para implementação.

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md) 
>  [Saiba mais sobre os plug-ins da Maven para o Azure](https://github.com/microsoft/azure-maven-plugins)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
