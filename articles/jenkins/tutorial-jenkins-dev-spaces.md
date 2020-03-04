---
title: Utilização do Plug-in Azure Dev Spaces para Jenkins com serviço Azure Kubernetes
description: Aprenda a utilizar o plug-in Azure Dev Spaces num pipeline de integração contínua.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 7b23893eb331f55ff41992a2ca660e79f44a609a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250892"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Tutorial: Utilização do Plug-in azure Dev Spaces para Jenkins com serviço Azure Kubernetes 

A Azure Dev Spaces permite-lhe testar e desenvolver iterativamente a sua aplicação de microserviço sem necessidade de replicar ou ridicularizar dependências. O plug-in Azure Dev Spaces para Jenkins ajuda-o a utilizar espaços Dev no seu pipeline de integração e entrega contínua (CI/CD).

Este tutorial também utiliza o Registo de Contentores Azure (ACR). A ACR armazena imagens, e uma Tarefa ACR constrói artefactos Docker e Helm. A utilização de Tarefas ACR e ACR para a geração de artefactos remove a necessidade de instalar software adicional, como o Docker, no seu servidor Jenkins. 

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Criar um cluster Azure Dev Spaces habilitado para AKS
> * Implementar uma aplicação multi-serviço para aks
> * Prepare o seu servidor Jenkins
> * Utilize o plug-in Azure Dev Spaces num oleoduto Jenkins para pré-visualizar alterações de código antes de as fundir no projeto

Este tutorial assume conhecimento intermédio dos serviços core Azure, AKS, ACR, Azure Dev Spaces, [oleodutos](https://jenkins.io/doc/book/pipeline/) Jenkins e plug-ins, e GitHub. A familiaridade básica com ferramentas de apoio como kubectl e Helm é útil.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta do GitHub. Se não tiver uma conta GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* [Código de estúdio visual](https://code.visualstudio.com/download) com a extensão [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada.

* [Azure CLI instalado](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.43 ou superior.

* Um servidor mestre do Jenkins. Se ainda não tem um mestre Jenkins, desloque [jenkins](https://aka.ms/jenkins-on-azure) em Azure seguindo os passos neste [arranque rápido.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) 

* O servidor Jenkins deve ter helm e kubectl instalados e disponíveis na conta Jenkins, como explicado mais tarde neste tutorial.

* Código VS, o Terminal de Código VS ou WSL, e Bash. 


## <a name="create-azure-resources"></a>Criar recursos do Azure

Nesta secção, cria-se recursos Azure:

* Um grupo de recursos para conter todos os recursos do Azure para este tutorial.
* Um cluster [azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS).
* Um registo de [contentores Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) para construir (utilizando tarefas ACR) e armazenar imagens do Docker.

1. Crie um grupo de recursos.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Criar um aglomerado AKS. Crie o aglomerado AKS numa região que apoie os [Espaços Dev.](../dev-spaces/about.md#supported-regions-and-configurations)

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configure aks para usar Espaços Dev.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Este passo instala a extensão `azds` CLI.

4. Crie um registo de contentores.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Implementar aplicativos de amostra para o cluster AKS

Nesta secção, você configura um espaço de v e implementa uma aplicação de amostra para o cluster AKS que criou na última secção. A aplicação é constituída por duas partes, *webfrontend* e *mywebapi.* Ambos os componentes são implantados num espaço de v. Mais tarde neste tutorial, vai supor um pedido de atração contra mywebapi para desencadear o oleoduto CI em Jenkins.

Para obter mais informações sobre a utilização de Espaços Azure Dev e desenvolvimento multi-serviço com espaços Azure Dev, consulte [Start start on Azure Dev Spaces with Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java), e desenvolvimento [multi-serviço com espaços Azure Dev.](https://docs.microsoft.com/azure/dev-spaces/multi-service-java) Esses tutoriais fornecem informações adicionais de fundo não incluídas aqui.

1. Baixe o https://github.com/Azure/dev-spaces repo do GitHub.

2. Abra a pasta `samples/java/getting-started/webfrontend` no Código VS. (Pode ignorar os pedidos predefinidos para adicionar recursos de erro ou restaurar o projeto.)

3. Atualizar `/src/main/java/com/ms/sample/webfrontend/Application.java` para parecer o seguinte:

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

4. Clique em **Ver** e depois **Terminal** para abrir o Terminal Integrado em Código VS.

5. Execute o comando `azds prep` para preparar a sua aplicação para funcionar num espaço de v. Este comando deve ser executado a partir de `dev-spaces/samples/java/getting-started/webfrontend` para preparar corretamente a sua aplicação:

    ```bash
    azds prep --public
    ```

    O comando de `azds prep` do Dev Spaces CLI gera ativos do Docker e da Kubernetes com definições padrão. Estes ficheiros persistem durante toda a vida do projeto, e podem ser personalizados:

    * `./Dockerfile` e `./Dockerfile.develop` descrevem a imagem do contentor da aplicação e como o código fonte é construído e funciona dentro do contentor.
    * Um [gráfico Helm](https://helm.sh/docs/topics/charts/) em `./charts/webfrontend` descreve como implementar o contentor no Kubernetes.
    * `./azds.yaml` é o ficheiro de configuração do Azure Dev Spaces.

    Para mais informações, consulte [como funciona o Azure Dev Spaces e está configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Construir e executar a aplicação em AKS usando o comando `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Scaneie a saída da consola para obter informações sobre o URL que foi criado pelo comando `up`. Estará na forma:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Abra este URL numa janela do navegador e deverá ver a aplicação web. À medida que o contentor é executado, os resultados `stdout` e `stderr` são transmitidos para a janela do terminal.

8. Em seguida, configurar e implementar *mywebapi:*

    1. Mude o diretório para `dev-spaces/samples/java/getting-started/mywebapi`

    2. Executar

        ```bash
        azds prep
        ```

    3. Executar

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Preparar servidor Jenkins

Nesta secção, prepara-se o servidor Jenkins para executar o pipeline CI da amostra.

* Instalar plug-ins
* Instale Helm e Kubernetes CLI
* Adicionar credenciais

### <a name="install-plug-ins"></a>Instalar plug-ins

1. Inscreva-se no seu servidor Jenkins. Escolha **Gerir Jenkins > Gerir plugins**.
2. No separador **Disponível,** selecione os seguintes plug-ins:
    * [Espaços Azure Dev](https://plugins.jenkins.io/azure-dev-spaces)
    * [Tarefas de Registo de Contentores Azure](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Injetor de Ambiente](https://plugins.jenkins.io/envinject)
    * [Integração GitHub](https://plugins.jenkins.io/github-pullrequest)

    Se estes plug-ins não aparecerem na lista, verifique o separador **Instalado** para ver se já estão instalados.

3. Para instalar os plug-ins, escolha **O download agora e instale-o após o reinício**.

4. Reinicie o seu servidor Jenkins para completar a instalação.

### <a name="install-helm-and-kubectl"></a>Instale o Leme e o Kubectl

O gasoduto de amostra utiliza Helm e kubectl para implantar para o espaço de v. Quando o Jenkins é instalado, cria uma conta de administração chamada *Jenkins.* Tanto helm como kubectl precisam de ser acessíveis ao utilizador jenkins.

1. Faça uma ligação SSH com o mestre Jenkins. 

2. Mude para o utilizador `jenkins`:
    ```bash
    sudo su jenkins
    ```

3. Instale o HELM CLI. Para mais informações, consulte [Instalar o Leme](https://helm.sh/docs/using_helm/#installing-helm).

4. Instale kubectl. Para mais informações, consulte [**az acs kubernetes instalar-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Adicione credenciais a Jenkins

1. Jenkins precisa de um diretor de serviço Azure para autenticar e aceder aos recursos azure. Para criar o diretor de serviço, consulte a secção de principal de [serviço Create](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) no tutorial de Implementação para o Serviço de Aplicações Azure. Certifique-se de guardar uma cópia da saída de `create-for-rbac` porque precisa dessa informação para completar o próximo passo. A saída será mais ou menos assim:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adicione um tipo de credencial principal do *serviço Microsoft Azure* em Jenkins, utilizando as informações principais do serviço a partir do passo anterior. Os nomes na imagem abaixo correspondem à saída de `create-for-rbac`.

    O campo **de identificação** é o nome de credencial jenkins para o seu diretor de serviço. O exemplo utiliza o valor de `displayName` (neste caso, `xxxxxxxjenkinssp`), mas pode utilizar qualquer texto que pretenda. Este nome credencial é o valor para a variável ambiente AZURE_CRED_ID na secção seguinte.

    ![Adicione credenciais principais de serviço a Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **descrição** é opcional. Para obter instruções mais detalhadas, consulte [Adicionar o principal do serviço à secção de Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) no tutorial do Serviço de Aplicações Azure. 



3. Para mostrar as suas credenciais ACR, dirija este comando:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Faça uma cópia da saída JSON, que deve ser algo assim:

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

4. Adicione um *nome de utilizador com* tipo credencial de senha em Jenkins. O nome de **utilizador** é o nome de utilizador do último passo, neste exemplo `acr01`. A **palavra-passe** é o valor da primeira palavra-passe, neste exemplo `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. A **identificação** desta credencial é o valor de ACR_CRED_ID.

5. Criar uma credencial AKS. Adicione uma *configuração kubernetes (kubeconfig)* tipo de credencial em Jenkins (use a opção "Enter directly"). Para obter as credenciais de acesso para o seu cluster AKS, execute o seguinte comando:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A **identificação** desta credencial é o valor de KUBE_CONFIG_ID na secção seguinte.

## <a name="create-a-pipeline"></a>Criar um pipeline

O cenário selecionado para o oleoduto exemplo baseia-se num padrão real: Um pedido de puxão desencadeia um oleoduto CI que constrói e, em seguida, implementa as alterações propostas para um espaço Azure dev para testes e revisão. Dependendo do resultado da revisão, as alterações são fundidas e implantadas para AKS ou descartadas. Finalmente, o espaço de dev é removido.

A configuração do gasoduto Jenkins e jenkinsfile definem as etapas do oleoduto CI. Este fluxograma mostra as fases do gasoduto e os pontos de decisão definidos pelo Jenkinsfile:

![Fluxo de gasoduto Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Faça o download de uma versão modificada do projeto *mywebapi* a partir de https://github.com/azure-devops/mywebapi. Este projeto contém vários ficheiros necessários para criar um pipeline, incluindo o *gráfico Jenkinsfile,* *Dockerfiles*e Helm.

2. Entra no Jenkins. A partir do menu à esquerda, selecione **Adicionar Item**.

3. Selecione **Pipeline**e, em seguida, introduza um nome na caixa de **nomes Insira.** Selecione **OK**, e, em seguida, o ecrã de configuração do pipeline será automaticamente aberto.

4. No separador **Geral,** verifique **Prepare um ambiente para a execução**. 

5. Verifique **manter as variáveis ambientais jenkins** e manter jenkins construir **variáveis**.

6. Na caixa de conteúdo de **propriedades,** introduza as seguintes variáveis ambientais:

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

    Utilizando os valores da amostra dados nas secções anteriores, a lista de variáveis ambientais deve ser semelhante a esta:

    ![Variáveis ambientais do gasoduto Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Escolha **o script pipeline de SCM** em Pipeline **> Definição**.
8. Em **SCM,** escolha **Git** e, em seguida, introduza o seu URL repo.
9. No **Especificador de Ramificação,** introduza `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Preencha o URL de repo sCM e o script "Jenkinsfile".
11. **O check-out leve** deve ser verificado.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Crie um pedido de puxar para desencadear o oleoduto

Para completar o passo 3 nesta secção, terá de comentar parte do Ficheiro Jenkins, caso contrário terá um erro de 404 quando tentar ver as versões novas e antigas lado a lado. Por padrão, quando optar por fundir o PR, a versão partilhada anterior do mywebapi será removida e substituída pela nova versão. Faça a seguinte alteração no Ficheiro Jenkins antes de completar o passo 1:

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

1. Faça uma alteração para `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`e, em seguida, crie um pedido de puxão. Por exemplo:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Assine o Jenkins e selecione o nome do pipeline e, em seguida, escolha **Build Now**. 

    Também pode configurar um *webhook* para acionar automaticamente o oleoduto Jenkins. Quando um pedido de puxão é introduzido, o GitHub emite um POST para Jenkins, desencadeando o oleoduto. Para obter mais informações sobre a criação de um webhook, consulte [Connect Jenkins para GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Compare alterações à versão partilhada atual:

    1. Abra o seu navegador e navegue para a versão partilhada `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contém o URL.

    2. Abra outro separador e, em seguida, introduza o URL do espaço PR dev. Será semelhante a `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Encontrará o link em **Build History > <build#> > Consola Output** for the Jenkins job. Procure na página por `aksapp`, ou para ver apenas o prefixo, procure `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Construindo o URL para o espaço de dev infantil

Quando apresentaum pedido de puxão, Jenkins cria um espaço de dev infantil baseado no espaço de dev partilhado da equipa e executa o código a partir do seu pedido de puxão naquele espaço de dev infantil. O URL para o espaço de dev infantil assume a forma `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** é definido durante a execução do gasoduto pelo plug-in Azure Dev Spaces pela **devSpacesCreate**:

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

O `test_endpoint` é o URL da aplicação webfrontend que implementou anteriormente usando `azds up`em [implementar aplicações de amostra para o cluster AKS, Passo 7](#test_endpoint). O valor do `$env.TEST_ENDPOINT` é definido na configuração do gasoduto. 

O seguinte código de corte mostra como o URL de espaço de v infantil é usado no estágio `smoketest`. O código verifica se o espaço de dev infantil TEST_ENDPOINT está disponível e, em caso afirmativo, descarrega o texto de saudação para stdout:

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

Quando terminar a aplicação da amostra, limpe os recursos do Azure apagando o grupo de recursos:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [CI/CD com Jenkins em Azure](jenkins-continuous-deployment.md)