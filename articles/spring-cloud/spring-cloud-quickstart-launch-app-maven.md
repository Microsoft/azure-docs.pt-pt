---
title: Início rápido – iniciar um aplicativo usando o Maven com o Azure Spring Cloud
description: Neste guia de início rápido, inicie um aplicativo de exemplo usando o Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: a8ccda88239a150a24c9abaf6ebb172f0fd01dba
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278932"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Início rápido: iniciar um aplicativo de nuvem Spring do Azure usando o plug-in do Maven

Usando o plug-in do Azure Spring Cloud Maven, você pode criar e atualizar facilmente seus aplicativos de nuvem Spring do Azure. Predefinindo uma configuração, você pode implantar aplicativos em seu serviço de nuvem do Azure Spring existente. Neste artigo, você usa um aplicativo de exemplo chamado PiggyMetrics para demonstrar esse recurso.

Seguindo este guia de início rápido, você aprenderá a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Configurar um servidor de configuração para uma instância
> * Clonar e compilar o aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

>[!Note]
> Atualmente, o Azure Spring Cloud é oferecido como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações sobre o suporte durante as visualizações, leia nossas [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre um [solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.


>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar os comandos neste artigo. Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, o JDK (Java Development Kit), o Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com). Para obter mais informações, consulte [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para concluir este guia de início rápido:

1. [Instale o Git](https://git-scm.com/).
2. [Instale o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Instale o Maven 3,0 ou posterior](https://maven.apache.org/download.cgi).
4. [Inscreva-se para uma assinatura gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra [este link para o Azure Spring Cloud no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)e entre na sua conta.

1. Forneça os **detalhes do projeto** para o aplicativo de exemplo da seguinte maneira:

    1. Selecione a **assinatura** com a qual o aplicativo será associado.
    1. Selecione ou crie um grupo de recursos para o aplicativo. É recomendável criar um novo grupo de recursos.  O exemplo a seguir mostra um novo grupo de recursos chamado `myspringservice`.
    1. Forneça um nome para o novo serviço de nuvem do Azure Spring.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hifens. O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.  O serviço no exemplo a seguir tem o nome `contosospringcloud`.
    1. Selecione um local para seu aplicativo nas opções fornecidas.  Neste exemplo, selecionamos `East US`.
    1. Selecione **revisão + criar** para examinar um resumo do novo serviço.  Se tudo parecer correto, selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![selecione revisar + criar](media/maven-qs-review-create.jpg)

Leva cerca de 5 minutos para que o serviço seja implantado. Depois que o serviço for implantado, selecione **ir para recurso** e a página **visão geral** da instância de serviço será exibida.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Configurar o servidor de configuração

1. Na página **visão geral** do serviço, selecione **servidor de configuração**.
1. Na seção **repositório padrão** , defina **URI** como **https://github.com/Azure-Samples/piggymetrics** , defina **rótulo** como **configuração**e, em seguida, selecione **aplicar** para salvar as alterações.

    > [!div class="mx-imgBorder"]
    > ![definir e aplicar configurações de configuração](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Clonar e compilar o repositório de aplicativos de exemplo

1. Inicie o [Azure cloud Shell](https://shell.azure.com).

1. Clone o repositório git executando o seguinte comando:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e compile o projeto executando o seguinte comando:

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gerar configurações e implantar na nuvem do Azure Spring

1. Gere configurações executando o seguinte comando na pasta raiz de PiggyMetrics que contém o POM pai:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Selecione os módulos `gateway`,`auth-service`e `account-service`.

    b. Selecione sua assinatura e o cluster do serviço de nuvem do Azure Spring.

    c. Na lista de projetos fornecidos, insira o número que corresponde a `gateway` para dar acesso público.
    
    d. Confirme a configuração.

1. O POM agora contém as dependências e configurações do plug-in. Implante os aplicativos usando o seguinte comando:

   ```azurecli
   mvn azure-spring-cloud:deploy
   ```

1. Após a conclusão da implantação, você pode acessar o PiggyMetrics usando a URL fornecida na saída do comando anterior.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um aplicativo Spring Cloud de um repositório Maven. Para saber mais sobre o Azure Spring Cloud, continue no tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Prepare seu aplicativo Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
> [saiba mais sobre os plug-ins do Maven para o Azure](https://github.com/microsoft/azure-maven-plugin)

Mais exemplos estão disponíveis no GitHub: [exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
