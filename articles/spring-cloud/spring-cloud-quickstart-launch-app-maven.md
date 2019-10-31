---
title: 'Início rápido: iniciar um aplicativo usando o Maven – Azure Spring Cloud'
description: Iniciar um aplicativo de exemplo usando o Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: ce07d43a289cf527664b120dd832cf832fb2b05e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161409"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Início rápido: iniciar um aplicativo de nuvem Spring do Azure usando o plug-in Maven

Com o plug-in do Azure Spring Cloud Maven, você pode criar e atualizar facilmente seus aplicativos de serviço de nuvem Spring do Azure. Predefinindo uma configuração, você pode implantar aplicativos em seu serviço de nuvem do Azure Spring existente. Neste artigo, você usa um aplicativo de exemplo chamado PiggyMetrics para demonstrar esse recurso.

>[!Note]
> Antes de começar este guia de início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud. Como serviço de visualização, convidamos você a entrar em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões. Se você quiser explorar os recursos do Azure Spring Cloud, preencha e envie o formulário de [interesse do Azure Spring Cloud (visualização particular)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u). Embora o Azure Spring Cloud esteja em versão prévia, a Microsoft oferece suporte limitado sem um SLA.  Para obter mais informações sobre o suporte durante as visualizações, consulte este [FAQ de suporte](https://azure.microsoft.com/support/faq/).

>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar os comandos neste artigo. Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, o JDK (Java Development Kit), o Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com). Para obter mais informações, consulte [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/).
2. [Instale o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Instale o Maven 3,0 ou posterior](https://maven.apache.org/download.cgi).
4. [Instale o CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Inscreva-se para uma assinatura gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o seguinte comando:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com)e entre em sua conta.

1. Pesquise e selecione **Azure Spring Cloud**. 
1. Na página Visão geral, selecione **criar**e, em seguida, faça o seguinte:  

    a. Na caixa **nome do serviço** , especifique o nome da sua instância de serviço. O nome deve ter de 4 a 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hifens. O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.  

    b. Na lista suspensa **assinatura** , selecione a assinatura que você deseja que seja cobrada para esse recurso. Verifique se essa assinatura foi adicionada à nossa lista de permissões para o Azure Spring Cloud.  

    c. Na caixa **grupo de recursos** , crie um novo grupo de recursos. A criação de grupos de recursos para novos recursos é uma prática recomendada.  

    d. Na lista suspensa **local** , selecione o local para sua instância de serviço. Os locais com suporte no momento incluem leste dos EUA, oeste dos EUA 2, Europa Ocidental e sudeste asiático.
    
Leva cerca de 5 minutos para que o serviço seja implantado. Depois que o serviço for implantado, a página **visão geral** da instância de serviço será exibida.

## <a name="set-up-your-configuration-server"></a>Configurar o servidor de configuração

1. Na página **visão geral** do serviço, selecione **servidor de configuração**.
1. Na seção **repositório padrão** , defina **URI** como **https://github.com/Azure-Samples/piggymetrics** , defina **rótulo** como **configuração**e, em seguida, selecione **aplicar** para salvar as alterações.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonar e compilar o repositório de aplicativos de exemplo

1. Clone o repositório git executando o seguinte comando:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e compile o projeto executando o seguinte comando:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Gerar e implantar a configuração de nuvem do Azure Spring

1. Para permitir que o Maven funcione com o Azure Spring Cloud, adicione o código a seguir ao arquivo *pom. xml* ou *Settings. xml* .

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Gere uma configuração executando o seguinte comando:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Selecione os módulos `gateway`, `auth-service` e `account-service`.

    b. Selecione sua assinatura e o cluster do serviço de nuvem do Azure Spring.

    c. Na lista de projetos fornecidos, insira o número que corresponde a `gateway` para dar acesso público.
    
    d. Confirme a configuração.

1. Implante os aplicativos usando o seguinte comando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Você pode acessar o PiggyMetrics usando a URL fornecida na saída do comando anterior.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um aplicativo Spring Cloud de um repositório Maven. Para saber mais sobre o Azure Spring Cloud, continue no tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
