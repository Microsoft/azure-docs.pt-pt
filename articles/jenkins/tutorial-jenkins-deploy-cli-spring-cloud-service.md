---
title: Implantar aplicativos no Azure Spring Cloud usando o Jenkins e o CLI do Azure
description: Saiba como usar CLI do Azure em um pipeline de implantação e integração contínua para implantar os microserviços no serviço de nuvem Spring do Azure
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734979"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Tutorial: implantar aplicativos no Azure Spring Cloud usando o Jenkins e o CLI do Azure

O [Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) é um desenvolvimento de microserviço totalmente gerenciado com descoberta de serviços e gerenciamento de configuração internos. O serviço facilita a implantação de aplicativos de microatendimento baseados em Spring boot no Azure. Este tutorial demonstra como você pode usar o CLI do Azure no Jenkins para automatizar a integração e a entrega contínuas (CI/CD) para o Azure Spring Cloud.

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Provisionar uma instância de serviço e iniciar um aplicativo Spring Java
> * Preparar o servidor Jenkins
> * Usar o CLI do Azure em um pipeline Jenkins para compilar e implantar os aplicativos de microatendimento 

Este tutorial assume o conhecimento intermediário dos principais serviços do Azure, do Azure Spring Cloud, dos [pipelines](https://jenkins.io/doc/book/pipeline/) Jenkins e dos plug-ins e do github.

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Atualmente, o Azure Spring Cloud é oferecido como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações sobre o suporte durante as visualizações, leia nossas [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre um [solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para saber mais.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Uma conta do GitHub. Se você não tiver uma conta do GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* Um servidor mestre do Jenkins. Se você ainda não tiver um mestre do Jenkins, implante o [Jenkins](https://aka.ms/jenkins-on-azure) no Azure seguindo as etapas neste guia de [início rápido](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Os itens a seguir são necessários no nó/agente do Jenkins (por exemplo, servidor de compilação):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
    * [CLI do Azure instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.67 ou superior

    >[!TIP]
    > Ferramentas como git, JDK, AZ CLI e Azure plug-ius são incluídas por padrão no modelo de solução [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) do Azure Marketplace.
    
* [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Provisionar uma instância de serviço e iniciar um aplicativo Spring Java

Usamos as [métricas de transportado](https://github.com/Azure-Samples/piggymetrics) como o aplicativo de serviço da Microsoft de exemplo e seguem as mesmas etapas no [início rápido: iniciar um aplicativo Spring Java usando o CLI do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para provisionar a instância do serviço e configurar os aplicativos. Se você já passou pelo mesmo processo, pode pular para a próxima seção. Caso contrário, CLI do Azure estão incluídos nos comandos a seguir. Consulte [início rápido: iniciar um aplicativo Spring Java usando o CLI do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para obter informações adicionais sobre o plano de fundo.

Seu computador local precisa atender ao mesmo pré-requisito que o servidor de Build Jenkins. Verifique se os itens a seguir estão instalados para compilar e implantar os aplicativos de microserviço:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
    * [CLI do Azure instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.67 ou superior

1. Instale a extensão da nuvem Spring do Azure:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Crie um grupo de recursos para conter seu serviço de nuvem Spring do Azure:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Provisionar uma instância do Azure Spring Cloud:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Bifurcar o repositório de [métricas transportado](https://github.com/Azure-Samples/piggymetrics) para sua própria conta do github. No computador local, clone seu repositório em um diretório chamado `source-code`:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Configure o servidor de configuração. Certifique-se de substituir &lt;sua ID do GitHub&gt; com o valor correto.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Compile o projeto:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Crie os três microserviços: **Gateway**, **serviço de autenticação**e **serviço de conta**:

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Implantar os aplicativos: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Atribuir ponto de extremidade público ao gateway:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Consulte o aplicativo de gateway para obter a URL para que você possa verificar se o aplicativo está em execução.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Navegue até a URL fornecida pelo comando anterior para executar o aplicativo PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você prepara o servidor Jenkins para executar uma compilação, o que é bom para teste. No entanto, devido à implicação de segurança, você deve usar um [agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) ou [agente de contêiner do Azure](https://plugins.jenkins.io/azure-container-agents) para criar um agente no Azure para executar suas compilações. Para obter mais informações, veja o artigo do Jenkins sobre as [implicações em termos de segurança da compilação no servidor Mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Instalar plug-ins

1. Entre no servidor do Jenkins. Escolha **gerenciar Jenkins > gerenciar plug-ins**.
2. Na guia **disponível** , selecione os seguintes plug-ins:
    * [Integração do GitHub](https://plugins.jenkins.io/github-pullrequest)
    * [Credencial do Azure](https://plugins.jenkins.io/azure-credentials)

    Se esses plug-ins não aparecerem na lista, marque a guia **instalado** para ver se eles já estão instalados.

3. Para instalar os plug-ins, escolha **baixar agora e instalar após a reinicialização**.

4. Reinicie o servidor Jenkins para concluir a instalação.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Adicionar suas credenciais de entidade de serviço do Azure no repositório de credenciais do Jenkins

1. Você precisa de uma entidade de serviço do Azure para implantar no Azure. Para obter mais informações, consulte a seção [Create Service principal](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) no tutorial Deploy to Azure app Service. A saída de `az ad sp create-for-rbac` é semelhante a esta:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. No dashboard do Jenkins, selecione **Credentials** > **System** (Credenciais > Sistema). Em seguida, selecione **Global credentials(unrestricted)** (Credenciais globais (sem restrições).

3. Selecione **Adicionar credenciais**. 

4. Selecione **Microsoft Azure entidade de serviço** como tipo.

5. Forneça valores para: * ID da assinatura: Use sua ID de assinatura do Azure * ID do cliente: Use `appId` * segredo do cliente: Use `password` * ID do locatário: usar `tenant` ambiente do Azure: selecione um valor predefinido. Por exemplo, use o **Azure** para o Azure global * ID: definir como **azure_service_principal**. Usamos essa ID em uma etapa posterior neste artigo * Descrição: é um campo opcional. É recomendável fornecer um valor significativo aqui.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Instalar a extensão Maven e AZ CLI Spring-Cloud

O pipeline de exemplo usa o Maven para criar e AZ CLI para implantar na instância do serviço. Quando o Jenkins é instalado, ele cria uma conta de administrador denominada *Jenkins*. Verifique se o usuário *Jenkins* tem permissão para executar a extensão Spring-Cloud.

1. Conecte-se ao mestre do Jenkins via SSH. 

2. Instalar o Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Instale a CLI do Azure. Para obter mais informações, consulte [instalando o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). O CLI do Azure é instalado por padrão se você usar o [mestre do Jenkins no Azure](https://aka.ms/jenkins-on-azure).

4. Alterne para o usuário do `jenkins`:

    ```bash
        sudo su jenkins
    ```

5. Adicione a extensão **Spring-Cloud** :

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Criar um Jenkinsfile
1. Em seu próprio repositório (https://github.com/&lt ; sua ID do GitHub&gt; /piggymetrics), crie um **Jenkinsfile** na raiz.

2. Atualize o arquivo da seguinte maneira. Certifique-se de substituir os valores de **\<nome do grupo de recursos >** e **\<nome do serviço >** . Substitua **azure_service_principal** pela ID correta se você usar um valor diferente quando adicionou a credencial em Jenkins. 

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

3. Salve e confirme a alteração.

## <a name="create-the-job"></a>Criar o trabalho

1. No painel do Jenkins, clique em **novo item**.

2. Forneça um nome, *Deploy-PiggyMetrics* para o trabalho e selecione **pipeline**. Clique em OK.

3. Em seguida, clique no separador **Pipeline**.

4. Em **Definition** (Definição), selecione **Pipeline script from SCM** (Script de pipeline do SCM).

5. Em **SCM**, selecione **Git**.

6. Insira a URL do GitHub para seu repositório bifurcado: **https://github.com/&lt ; sua ID do github&gt; /piggymetrics.git**

7. Verifique se o **especificador de ramificação (preto para ' qualquer ')** é * **/Azure**

8. Manter **caminho de script** como **Jenkinsfile**

7. Clicar em **Guardar**

## <a name="validate-and-run-the-job"></a>Validar e executar o trabalho

Antes de executar o trabalho, vamos atualizar o texto na caixa de entrada de logon para **Inserir a ID de logon**.

1. Em seu próprio repositório, abra `index.html` no **/Gateway/src/main/resources/static/**

2. Pesquise "Insira seu logon" e atualize para "inserir ID de logon"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Confirmar as alterações

4. Execute o trabalho no Jenkins manualmente. No painel do Jenkins, clique no trabalho *Deploy-PiggyMetrics* e, em seguida, **compile agora**.

Depois que o trabalho for concluído, navegue até o IP público do aplicativo de **Gateway** e verifique se seu aplicativo foi atualizado. 

![Métricas de transportado atualizadas](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar o CLI do Azure no Jenkins para automatizar a integração e a entrega contínuas (CI/CD) para o Azure Spring Cloud.

Para saber mais sobre o provedor de Jenkins do Azure, consulte o Jenkins no site do Azure.

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins/)
