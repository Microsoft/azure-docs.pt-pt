---
title: 'Início rápido: Iniciar um aplicativo usando o Maven – Azure Spring Cloud'
description: Iniciar um aplicativo de exemplo usando o Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166522"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Início rápido: Iniciar um aplicativo de nuvem Spring do Azure usando o plug-in Maven

O plug-in Maven do Azure Spring Cloud permite que você crie e atualize facilmente seus aplicativos de serviço de nuvem do Azure Spring. Ao definir previamente uma configuração, você pode implantar aplicativos em seu serviço de nuvem do Azure Spring existente. Neste artigo, usamos um aplicativo de exemplo chamado PiggyMetrics para demonstrar esse recurso.

>[!Note]
> Antes de iniciar este guia de início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Como um serviço de visualização, solicitamos que os clientes entrem em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se você quiser explorar os recursos do Azure Spring Cloud, [fill este formulário @ no__t-1.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o comando a seguir

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com)e entre em sua conta.

1. Pesquise a **nuvem do Azure Spring** e selecione-a para ir para a página Visão geral. Selecione o botão **criar** para começar.

1. Preencha o formulário, considerando as seguintes diretrizes:
    - Nome do serviço: Especifique o nome da sua instância de serviço.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - Subscrição: Selecione a assinatura que você deseja que seja cobrada para este recurso.  Verifique se essa assinatura foi adicionada à nossa lista de permissões para o Azure Spring Cloud.
    - Grupo de recursos: A criação de novos grupos de recursos para novos recursos é uma prática recomendada.
    - Local Selecione o local para sua instância de serviço. Os locais com suporte no momento incluem leste dos EUA, oeste dos EUA 2, Europa Ocidental e sudeste asiático.
    
Leva cerca de 5 minutos para que o serviço seja implantado.  Após a implantação, a página **visão geral** da instância do serviço será exibida.

## <a name="set-up-your-configuration-server"></a>Configurar o servidor de configuração

1. Vá para a página **visão geral** do serviço e selecione **servidor de configuração**.
1. Na seção **repositório padrão** , defina **URI** como "https://github.com/Azure-Samples/piggymetrics", defina **rótulo** como "configuração" e selecione **aplicar** para salvar as alterações.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonar e compilar o repositório de aplicativos de exemplo

1. Clone o repositório git executando o comando a seguir.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e compile o projeto executando o comando a seguir.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Gerar e implantar a configuração de nuvem do Azure Spring

1. Adicione o seguinte ao seu `pom.xml` ou `settings.xml` para permitir que o Maven funcione com o Azure Spring Cloud.

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

1. Gere uma configuração executando o comando a seguir.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Selecione os módulos `gateway`, `auth-service` e `account-service`.

    1. Selecione sua assinatura e o cluster do serviço de nuvem do Azure Spring.

    1. Na lista de projetos fornecida, insira o número que corresponde a `gateway` para dar acesso público.
    
    1. Confirme a configuração.

1. Implante os aplicativos usando o seguinte comando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Você pode acessar o PiggyMetrics usando a URL fornecida na saída do comando anterior.

## <a name="next-steps"></a>Próximos Passos

Neste guia de início rápido, você implantou um aplicativo Spring Cloud de um repositório Maven.  Para saber mais sobre o Azure Spring Cloud, continue no tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
