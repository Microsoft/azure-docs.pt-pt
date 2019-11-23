---
title: Usando o plug-in Azure Dev Spaces para Jenkins com o serviço kubernetes do Azure
description: Saiba como usar o plug-in Azure Dev Spaces em um pipeline de integração contínua.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 9dba0307db8ebbf07422fd770ea336b2abc031bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209678"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Tutorial: usando o plug-in Azure Dev Spaces para Jenkins com o serviço kubernetes do Azure 

Azure Dev Spaces permite testar e desenvolver iterativamente seu aplicativo de microatendimento em execução no AKS (serviço de kubernetes do Azure) sem a necessidade de replicar ou simular dependências. O plug-in Azure Dev Spaces para Jenkins ajuda a usar espaços de desenvolvimento em seu pipeline de integração e entrega contínua (CI/CD).

Este tutorial também usa o ACR (registro de contêiner do Azure). O ACR armazena imagens, e uma tarefa ACR cria artefatos Docker e Helm. Usar a tarefa ACR e ACR para geração de artefatos elimina a necessidade de instalar software adicional, como o Docker, no seu servidor Jenkins. 

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Criar um cluster AKS habilitado para Azure Dev Spaces
> * Implantar um aplicativo de vários serviços no AKS
> * Preparar o servidor Jenkins
> * Use o plug-in Azure Dev Spaces em um pipeline do Jenkins para visualizar as alterações de código antes de mesclá-las no projeto

Este tutorial pressupõe o conhecimento intermediário dos principais serviços do Azure, AKS, ACR, Azure Dev Spaces, [pipelines](https://jenkins.io/doc/book/pipeline/) e plug-ins do Jenkins e github. A familiaridade básica com ferramentas de suporte como kubectl e Helm é útil.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta do GitHub. Se você não tiver uma conta do GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* [Visual Studio Code](https://code.visualstudio.com/download) com a extensão [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada.

* [CLI do Azure instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.43 ou superior.

* Um servidor mestre do Jenkins. Se você ainda não tiver um mestre do Jenkins, implante o [Jenkins](https://aka.ms/jenkins-on-azure) no Azure seguindo as etapas neste guia de [início rápido](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* O servidor Jenkins deve ter Helm e kubectl instalados e disponíveis para a conta Jenkins, conforme explicado posteriormente neste tutorial.

* VS Code, o VS Code terminal ou WSL e bash. 


## <a name="create-azure-resources"></a>Criar recursos do Azure

Nesta seção, você criará recursos do Azure:

* Um grupo de recursos para conter todos os recursos do Azure para este tutorial.
* Um cluster do AKS ( [serviço de kubernetes do Azure](https://docs.microsoft.com/azure/aks/) ).
* Um ACR ( [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ) para compilar (usando tarefas ACR) e armazenar imagens do Docker.

1. Crie um grupo de recursos.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Crie um cluster AKS. Crie o cluster AKS em uma [região que dê suporte a espaços de desenvolvimento](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configure o AKS para usar espaços de desenvolvimento.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Esta etapa instala a extensão da CLI do `azds`.

4. Crie um registro de contêiner.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Implantar aplicativos de exemplo no cluster AKS

Nesta seção, você configura um espaço de desenvolvimento e implanta um aplicativo de exemplo no cluster AKS criado na última seção. O aplicativo consiste em duas partes, *WebFrontEnd* e *mywebapi*. Ambos os componentes são implantados em um espaço de desenvolvimento. Posteriormente neste tutorial, você enviará uma solicitação de pull contra o mywebapi para disparar o pipeline de CI em Jenkins.

Para obter mais informações sobre como usar Azure Dev Spaces e desenvolvimento de vários serviços com o Azure Dev Spaces, consulte Introdução [ao Azure dev Spaces com Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)e [desenvolvimento de vários serviços com o Azure dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Esses tutoriais fornecem informações adicionais em segundo plano não incluídas aqui.

1. Baixe o repositório de https://github.com/Azure/dev-spaces do GitHub.

2. Abra a pasta `samples/java/getting-started/webfrontend` em VS Code. (Pode ignorar os pedidos predefinidos para adicionar recursos de erro ou restaurar o projeto.)

3. Atualize `/src/main/java/com/ms/sample/webfrontend/Application.java` para ser semelhante ao seguinte:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Clique em **Exibir** e em **terminal** para abrir o terminal integrado no vs Code.

5. Execute o comando `azds prep` para preparar seu aplicativo para ser executado em um espaço de desenvolvimento. Este comando deve ser executado de `dev-spaces/samples/java/getting-started/webfrontend` para preparar o aplicativo corretamente:

    ```bash
    azds prep --public
    ```

    O comando `azds prep` da CLI de espaços de desenvolvimento gera ativos de Docker e kubernetes com as configurações padrão. Esses arquivos persistem durante o tempo de vida do projeto e podem ser personalizados:

    * `./Dockerfile` e `./Dockerfile.develop` descrevem a imagem de contêiner do aplicativo e como o código-fonte é compilado e executado dentro do contêiner.
    * Um [gráfico Helm](https://helm.sh/docs/topics/charts/) em `./charts/webfrontend` descreve como implementar o contentor no Kubernetes.
    * `./azds.yaml` é o arquivo de configuração de Azure Dev Spaces.

    Para obter mais informações, consulte [como Azure dev Spaces funciona e está configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Compile e execute o aplicativo no AKS usando o comando `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Examine a saída do console para obter informações sobre a URL que foi criada pelo comando `up`. Estará na forma:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Abra essa URL em uma janela do navegador e você deverá ver o aplicativo Web. À medida que o contentor é executado, os resultados `stdout` e `stderr` são transmitidos para a janela do terminal.

8. Em seguida, configure e implante *mywebapi*:

    1. Altere o diretório para `dev-spaces/samples/java/getting-started/mywebapi`

    2. Executar

        ```bash
        azds prep
        ```

    3. Executar

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você prepara o servidor Jenkins para executar o pipeline de CI de exemplo.

* Instalar plug-ins
* Instalar a CLI do Helm e do kubernetes
* Adicionar credenciais

### <a name="install-plug-ins"></a>Instalar plug-ins

1. Entre no servidor do Jenkins. Escolha **gerenciar Jenkins > gerenciar plug-ins**.
2. Na guia **disponível** , selecione os seguintes plug-ins:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Tarefas do registro de contêiner do Azure](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Injetador de ambiente](https://plugins.jenkins.io/envinject)
    * [Integração do GitHub](https://plugins.jenkins.io/github-pullrequest)

    Se esses plug-ins não aparecerem na lista, marque a guia **instalado** para ver se eles já estão instalados.

3. Para instalar os plug-ins, escolha **baixar agora e instalar após a reinicialização**.

4. Reinicie o servidor Jenkins para concluir a instalação.

### <a name="install-helm-and-kubectl"></a>Instalar o Helm e o kubectl

O pipeline de exemplo usa Helm e kubectl para implantar no espaço de desenvolvimento. Quando o Jenkins é instalado, ele cria uma conta de administrador denominada *Jenkins*. Helm e kubectl precisam estar acessíveis para o usuário do Jenkins.

1. Faça uma conexão SSH com o mestre Jenkins. 

2. Alterne para o usuário do `jenkins`:
    ```bash
    sudo su jenkins
    ```

3. Instale a CLI do Helm. Para obter mais informações, consulte [instalando o Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Instale o kubectl. Para obter mais informações, consulte [**AZ ACS kubernetes install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Adicionar credenciais a Jenkins

1. O Jenkins precisa de uma entidade de serviço do Azure para autenticar e acessar os recursos do Azure. Para criar a entidade de serviço, consulte a seção [Create Service principal](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) no tutorial Deploy to Azure app Service. Lembre-se de salvar uma cópia da saída de `create-for-rbac` porque você precisa dessas informações para concluir a próxima etapa. A saída terá uma aparência semelhante a esta:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adicione um tipo de credencial de *entidade de serviço Microsoft Azure* no Jenkins, usando as informações da entidade de serviço da etapa anterior. Os nomes na captura de tela abaixo correspondem à saída de `create-for-rbac`.

    O campo **ID** é o nome da credencial Jenkins para sua entidade de serviço. O exemplo usa o valor de `displayName` (nesta instância, `xxxxxxxjenkinssp`), mas você pode usar qualquer texto desejado. Esse nome de credencial é o valor para a variável de ambiente AZURE_CRED_ID na próxima seção.

    ![Adicionar credenciais da entidade de serviço a Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **Descrição** é opcional. Para obter instruções mais detalhadas, consulte a seção [Add Service principal to Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) no tutorial Deploy to Azure app Service. 



3. Para mostrar suas credenciais de ACR, execute este comando:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Faça uma cópia da saída JSON, que deve ser semelhante a esta:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Adicione um *nome de usuário com* o tipo de credencial de senha em Jenkins. O **nome de usuário** é o nome de usuário da última etapa, neste exemplo `acr01`. A **senha** é o valor da primeira senha, neste exemplo `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. A **ID** dessa credencial é o valor de ACR_CRED_ID.

5. Configure uma credencial AKS. Adicione um tipo de credencial de *configuração de kubernetes (kubeconfig)* em Jenkins (use a opção "inserir diretamente"). Para obter as credenciais de acesso para o cluster AKS, execute o seguinte comando:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A **ID** essa credencial é o valor de KUBE_CONFIG_ID na próxima seção.

## <a name="create-a-pipeline"></a>Criar um pipeline

O cenário selecionado para o pipeline de exemplo é baseado em um padrão do mundo real: uma solicitação de pull dispara um pipeline de CI que cria e implanta as alterações propostas em um espaço de desenvolvimento do Azure para teste e análise. Dependendo do resultado da revisão, as alterações são mescladas e implantadas em AKS ou descartadas. Por fim, o espaço de desenvolvimento é removido.

A configuração de pipeline Jenkins e Jenkinsfile definem os estágios no pipeline de CI. Este fluxograma mostra os estágios de pipeline e os pontos de decisão definidos pelo Jenkinsfile:

![Fluxo de pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Baixe uma versão modificada do projeto *mywebapi* do https://github.com/azure-devops/mywebapi. Este projeto contém vários arquivos necessários para criar um pipeline, incluindo o gráfico *Jenkinsfile*, *Dockerfiles*e Helm.

2. Faça logon no Jenkins. No menu à esquerda, selecione **Adicionar item**.

3. Selecione **pipeline**e insira um nome na caixa **Inserir um nome de item** . Selecione **OK**e a tela de configuração do pipeline será aberta automaticamente.

4. Na guia **geral** , marque **preparar um ambiente para a execução**. 

5. Marque **manter variáveis de ambiente Jenkins** e **manter as variáveis de Build Jenkins**.

6. Na caixa **conteúdo das propriedades** , insira as seguintes variáveis de ambiente:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Usando os valores de exemplo fornecidos nas seções anteriores, a lista de variáveis de ambiente deve ser semelhante a esta:

    ![Variáveis de ambiente do pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Escolha o **script de pipeline do SCM** no **pipeline > definição**.
8. No **SCM**, escolha **git** e, em seguida, insira a URL do repositório.
9. Em **especificador de ramificação**, insira `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Preencha a URL do repositório SCM e o caminho do script "Jenkinsfile".
11. O **check-out leve** deve ser verificado.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Criar uma solicitação de pull para disparar o pipeline

Para concluir a etapa 3 nesta seção, você precisará comentar parte do Jenkinsfile, caso contrário, você receberá um erro 404 ao tentar exibir as versões novas e antigas lado a lado. Por padrão, quando você opta por mesclar a PR, a versão compartilhada anterior do mywebapi será removida e substituída pela nova versão. Faça a seguinte alteração no Jenkinsfile antes de concluir a etapa 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Faça uma alteração no `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`e, em seguida, crie uma solicitação de pull. Por exemplo:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Entre no Jenkins e selecione o nome do pipeline e, em seguida, escolha **Compilar agora**. 

    Você também pode configurar um *webhook* para disparar automaticamente o pipeline Jenkins. Quando uma solicitação de pull é inserida, o GitHub emite um POST para Jenkins, disparando o pipeline. Para obter mais informações sobre como configurar um webhook, consulte [conectar o Jenkins ao GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Comparar alterações com a versão compartilhada atual:

    1. Abra o navegador e navegue até a versão compartilhada `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contém a URL.

    2. Abra outra guia e, em seguida, insira a URL de espaço de desenvolvimento de PR. Ele será semelhante a `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Você encontrará o link no **histórico de compilação > < Build # > > saída do console** para o trabalho Jenkins. Pesquise a página `aksapp`, ou para ver apenas o prefixo, procure por `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Construindo a URL para o espaço de desenvolvimento filho

Quando você arquiva uma solicitação de pull, o Jenkins cria um espaço de desenvolvimento filho com base no espaço de desenvolvimento compartilhado da equipe e executa o código da sua solicitação de pull nesse espaço de desenvolvimento filho. A URL para o espaço de desenvolvimento filho usa a forma `http://$env.azdsprefix.<test_endpoint>`. 

**$env. azdsprefix** é definido durante a execução do pipeline pelo plug-in de Azure dev Spaces pelo **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

O `test_endpoint` é a URL para o aplicativo WebFrontEnd que você implantou anteriormente usando `azds up`em [implantar aplicativos de exemplo no cluster AKs, etapa 7](#test_endpoint). O valor de `$env.TEST_ENDPOINT` é definido na configuração do pipeline. 

O trecho de código a seguir mostra como a URL de espaço de desenvolvimento filho é usada no estágio de `smoketest`. O código verifica se o espaço de desenvolvimento filho TEST_ENDPOINT está disponível e, nesse caso, baixa o texto de saudação para stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar o aplicativo de exemplo, limpe os recursos do Azure excluindo o grupo de recursos:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar o plug-in de Azure Dev Spaces para Jenkins e o plug-in de registro de contêiner do Azure para compilar o código e implantá-lo em um espaço de desenvolvimento.

A lista de recursos a seguir fornece mais informações sobre Azure Dev Spaces, tarefas ACR e CI/CD com Jenkins.

Azure Dev Spaces:
* [Como o Azure Dev Spaces funciona e é configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Tarefas de ACR:
* [Automatizar a aplicação de patches ao SO e à arquitetura com as Tarefas do ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Compilação automática na confirmação de código](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD com Jenkins no Azure:
* [Implantação contínua do Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
