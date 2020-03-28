---
title: Implementar aplicações no Azure Spring Cloud através do Jenkins e da CLI do Azure
description: Aprenda a utilizar o Azure CLI num oleoduto de integração e implantação contínuo para implantar microserviços para o serviço Azure Spring Cloud
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75734979"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Tutorial: Implementar aplicativos para Azure Spring Cloud usando Jenkins e o Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) é um desenvolvimento de microserviço totalmente gerido com descoberta de serviço incorporado e gestão de configuração. O serviço facilita a implementação de aplicações de microserviço baseadas em Boot spring para o Azure. Este tutorial demonstra como pode utilizar o Azure CLI em Jenkins para automatizar a integração e entrega contínuas (CI/CD) para a Azure Spring Cloud.

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Providenciar uma instância de serviço e lançar uma aplicação Java Spring
> * Prepare o seu servidor Jenkins
> * Use o Azure CLI num oleoduto Jenkins para construir e implementar as aplicações de microserviço 

Este tutorial assume conhecimento intermédio dos serviços core Azure, Azure Spring Cloud, [oleodutos](https://jenkins.io/doc/book/pipeline/) Jenkins e plug-ins, e GitHub.

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> A Nuvem de primavera Azure é atualmente oferecida como uma pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.  Para mais informações sobre suporte durante as pré-visualizações, por favor reveja as nossas [FAQ](https://azure.microsoft.com/support/faq/) ou preencha um pedido de [Suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para saber mais.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Uma conta do GitHub. Se não tiver uma conta GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* Um servidor mestre do Jenkins. Se ainda não tem um mestre Jenkins, desloque [jenkins](https://aka.ms/jenkins-on-azure) no Azure seguindo os passos neste [arranque rápido.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) São necessários os seguintes no nó/agente Jenkins (por exemplo. construir servidor):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 ou acima](https://maven.apache.org/download.cgi)
    * [Azure CLI instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.67 ou superior

    >[!TIP]
    > Ferramentas como Git, JDK, Az CLI e Azure plug-ius estão incluídas por padrão no modelo de solução Azure Marketplace [Microsoft Jenkins.](https://aka.ms/jenkins-on-azure) 
    
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Providenciar uma instância de serviço e lançar uma aplicação Java Spring

Utilizamos a [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) como aplicação de serviço da Microsoft e seguimos os mesmos passos em [Quickstart: Lançar uma aplicação Java Spring utilizando o Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para fornecer a instância de serviço e configurar as aplicações. Se já passou pelo mesmo processo, pode saltar para a secção seguinte. Caso contrário, incluídos nos comandos Azure CLI. Consulte [o Quickstart: Lance uma aplicação Java Spring utilizando o Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para obter informações adicionais sobre o fundo.

A sua máquina local precisa de cumprir o mesmo pré-requisito que o servidor de construção jenkins. Certifique-se de que os seguintes estão instalados para construir e implementar as aplicações de microserviço:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 ou acima](https://maven.apache.org/download.cgi)
    * [Azure CLI instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.67 ou superior

1. Instale a extensão azure Spring Cloud:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Crie um grupo de recursos para conter o seu serviço Azure Spring Cloud:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Provisão de uma instância de Nuvem de primavera Azure:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Bifurque o [piggy Metrics](https://github.com/Azure-Samples/piggymetrics) repo para a sua própria conta GitHub. Na sua máquina local, clone o seu `source-code`repo num diretório chamado:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Configura risque o seu servidor de configuração. Certifique-se &lt;de que substitui&gt; o seu id GitHub pelo valor correto.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Construir o projeto:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Criar os três microserviços: **gateway,** **auth-service,** e **serviço de conta:**

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Implementar as aplicações: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Atribuir ponto final público ao portal:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Consultar a aplicação de gateway para obter o url para que possa verificar se a aplicação está em execução.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Navegue para o URL fornecido pelo comando anterior para executar a aplicação PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Preparar servidor Jenkins

Nesta secção, prepara-se o servidor Jenkins para executar uma construção, o que é bom para testes. No entanto, devido a implicações de segurança, deve utilizar um [agente Azure VM](https://plugins.jenkins.io/azure-vm-agents) ou [um agente de contentores Azure](https://plugins.jenkins.io/azure-container-agents) para criar um agente em Azure para executar as suas construções. Para obter mais informações, veja o artigo do Jenkins sobre as [implicações em termos de segurança da compilação no servidor Mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Instalar plug-ins

1. Inscreva-se no seu servidor Jenkins. Escolha **gerir jenkins > gerir plugins**.
2. No separador **Disponível,** selecione os seguintes plug-ins:
    * [Integração GitHub](https://plugins.jenkins.io/github-pullrequest)
    * [Credencial Azure](https://plugins.jenkins.io/azure-credentials)

    Se estes plug-ins não aparecerem na lista, verifique o separador **Instalado** para ver se já estão instalados.

3. Para instalar os plug-ins, escolha **O download agora e instale-o após o reinício**.

4. Reinicie o seu servidor Jenkins para completar a instalação.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Adicione a sua credencial principal de serviço Azure na loja de credenciais Jenkins

1. Precisa de um Diretor de Serviço Azure para implantar em Azure. Para mais informações, consulte a secção [principal](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) do serviço Create no tutorial do Serviço de Aplicações Azure. A saída `az ad sp create-for-rbac` é mais ou menos assim:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. No painel jenkins, selecione**Sistema** **de Credenciais** > . Em seguida, selecione **Global credentials(unrestricted)** (Credenciais globais (sem restrições).

3. **Selecione Adicionar Credenciais**. 

4. Selecione O **Diretor de Serviço microsoft Azure** como amável.

5. Valores de fornecimento para: * ID de subscrição: `appId` use o `password` id de subscrição do Azure * ID do cliente: use * Segredo do Cliente: use * ID do inquilino: use `tenant` * Ambiente Azure: selecione um valor pré-definido. Por exemplo, utilize **o Azure** para o Azure Global * ID: definido como **azure_service_principal**. Usamos este ID num passo posterior neste artigo * Descrição: é um campo opcional. Recomendamos que forneça um valor significativo aqui.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Instale extensão de nuvem de mola Maven e Az CLI

O gasoduto de amostra utiliza a Maven para construir e o Az CLI para se deslocar para a instância de serviço. Quando o Jenkins é instalado, cria uma conta de administração chamada *Jenkins.* Certifique-se de que o utilizador *jenkins* tem permissão para executar a extensão da nuvem de mola.

1. Ligue-se ao mestre Jenkins via SSH. 

2. Instalar Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Instale a CLI do Azure. Para mais informações, consulte [Instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). O Azure CLI é instalado por padrão se utilizar [o Jenkins Master no Azure](https://aka.ms/jenkins-on-azure).

4. Mude para `jenkins` o utilizador:

    ```bash
        sudo su jenkins
    ```

5. Adicione a extensão **da nuvem de mola:**

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Criar um Jenkinsfile
1. No seu própriohttps://github.com/&ltrepo (o&gt;seu id /piggymetrics GitHub), crie um **Jenkinsfile** na raiz.

2. Atualize o ficheiro da seguinte forma. Certifique-se de que substitui os valores do nome do grupo de ** \<recursos>** e ** \<o nome **do serviço>. Substitua **azure_service_principal** com o ID certo se utilizar um valor diferente quando adicionar a credencial em Jenkins. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Poupe e cometa a mudança.

## <a name="create-the-job"></a>Criar o trabalho

1. No painel jenkins, clique **em New Item**.

2. Forneça um nome, *Deploy-PiggyMetrics* para o trabalho e selecione **Pipeline**. Clique em OK.

3. Em seguida, clique no separador **Pipeline**.

4. Em **Definition** (Definição), selecione **Pipeline script from SCM** (Script de pipeline do SCM).

5. Em **SCM**, selecione **Git**.

6. Introduza o URL GitHub para o seu repo bifurcado: ** https://github.com/&lt.o seu&gt;id GitHub /piggymetrics.git**

7. Certifique-se de que o **especificador de ramo (preto para 'qualquer')** é ***/Azure**

8. Mantenha **o caminho do Script** como **Jenkinsfile**

7. Clique em **Guardar**

## <a name="validate-and-run-the-job"></a>Validar e executar o trabalho

Antes de executar o trabalho, vamos atualizar o texto na caixa de entrada de login para introduzir o ID de **login**.

1. No seu próprio repo, abra `index.html` em **/gateway/src/main/resources/static/**

2. Procure "insira o seu login" e atualize para "introduzir o ID de login"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Comprometa as alterações

4. Executar o trabalho em Jenkins manualmente. No painel jenkins, clique no trabalho *Deploy-PiggyMetrics* e, em seguida, **Build Now**.

Depois de concluída a função, navegue para o IP público da aplicação **gateway** e verifique se a sua candidatura foi atualizada. 

![Métricas de porquinho atualizados](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o Azure CLI em Jenkins para automatizar a integração e entrega contínuas (CI/CD) para a Azure Spring Cloud.

Para saber mais sobre o fornecedor Azure Jenkins, consulte o site do Jenkins no site azure.

> [!div class="nextstepaction"]
> [Jenkins em Azure](/azure/jenkins/)
