---
title: Utilizar o plug-in de espaços de desenvolvimento do Azure para Jenkins com o serviço Kubenetes do Azure
description: Saiba como utilizar o plug-in de espaços de desenvolvimento do Azure num pipeline de integração contínua.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: f5f74ebeb803a5c493f1dbedb6501adf3a88c215
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785661"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Tutorial: Utilizar o plug-in de espaços de desenvolvimento do Azure para Jenkins com o serviço Kubenetes do Azure 

Espaços de desenvolvimento do Azure permite-lhe testar e desenvolver iterativamente a sua aplicação de microsserviços em execução no Azure Kubernetes Service (AKS) sem a necessidade de replicar ou dependências de teste. O plug-in de espaços de desenvolvimento do Azure para Jenkins ajuda-o a utilizar espaços de desenvolvimento na sua integração contínua e um pipeline de entrega (CI/CD).

Este tutorial também utiliza o Azure Container Registry (ACR). ACR armazena imagens e baseia-se uma tarefa de ACR artefactos de Docker e Helm. Com o ACR e tarefas do ACR para geração de artefacto remove a necessidade de instalar software adicional, como o Docker, em seu servidor do Jenkins. 

Neste tutorial, irá concluir estas tarefas:

> [!div class="checklist"]
> * Criar um cluster do AKS ativado espaços de desenvolvimento do Azure
> * Implementar uma aplicação múltiplos serviço no AKS
> * Preparar o servidor Jenkins
> * Utilizar o plug-in de espaços de desenvolvimento do Azure num pipeline do Jenkins para pré-visualizar as alterações de código antes da intercalação-los para o projeto

Este tutorial pressupõe um conhecimento intermediário de núcleo de serviços do Azure, o AKS, ACR, espaços de desenvolvimento do Azure, Jenkins [pipelines](https://jenkins.io/doc/book/pipeline/) e plug-ins e o GitHub. Familiaridade básica com suporte a ferramentas como o kubectl e o Helm é útil.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta do GitHub. Se não tiver uma conta do GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

* [Visual Studio Code](https://code.visualstudio.com/download) com o [do Azure Dev espaços](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) extensão instalada.

* [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.43 ou superior.

* Um servidor mestre do Jenkins. Se ainda não tiver um Jenkins master, implemente [Jenkins](https://aka.ms/jenkins-on-azure) no Azure ao seguir os passos neste [início rápido](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* O servidor Jenkins tem de ter o Helm e o kubectl instalado e disponibilizado para a conta do Jenkins, conforme explicado posteriormente neste tutorial.

* O VS Code, o Terminal de código do VS ou WSL e Bash. 


## <a name="create-azure-resources"></a>Criar recursos do Azure

Nesta secção, vai criar recursos do Azure:

* Um grupo de recursos para conter todos os recursos do Azure para este tutorial.
* Uma [do Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) cluster (AKS).
* Uma [do Azure container registry](https://docs.microsoft.com/azure/container-registry/) (ACR) para criar a (utilizar as tarefas de ACR) e armazenar imagens do Docker.

1. Crie um grupo de recursos.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Crie um cluster do AKS. Criar o cluster do AKS numa [região que suporta espaços de desenvolvimento](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configure o AKS para utilizar os espaços de desenvolvimento.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Este passo instala o `azds` extensão da CLI.

4. Crie um registo de contentor.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Implementar aplicações de exemplo para o cluster do AKS

Nesta secção, configurar um espaço de desenvolvimento e implementar uma aplicação de exemplo para o cluster do AKS que criou na última secção. O aplicativo consiste em duas partes, *webfrontend* e *mywebapi*. Ambos os componentes são implantados num espaço de desenvolvimento. Mais tarde neste tutorial, irá enviar um pedido pull contra mywebapi para acionar o pipeline de CI no Jenkins.

Para obter mais informações sobre como utilizar espaços de desenvolvimento do Azure e o desenvolvimento de múltiplos serviço com os espaços de desenvolvimento do Azure, consulte [começar a trabalhar com espaços de desenvolvimento do Azure com Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java), e [desenvolvimento múltiplos serviço com os espaços de desenvolvimento do Azure](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Esses tutoriais fornecem mais informações gerais não incluídas aqui.

1. Transferir o https://github.com/Azure/dev-spaces repositório do GitHub.

2. Abra o `samples/java/getting-started/webfrontend` pasta no VS Code. (Pode ignorar os pedidos predefinidos para adicionar recursos de erro ou restaurar o projeto.)

3. Atualização `/src/main/java/com/ms/sample/webfrontend/Application.java` para ter um aspeto semelhante ao seguinte:

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

4. Clique em **View** , em seguida, **Terminal** para abrir o Terminal integrado no VS Code.

5. Execute o `azds prep` comando para preparar a sua aplicação para ser executado num espaço de desenvolvimento. Este comando deve ser executado a partir `dev-spaces/samples/java/getting-started/webfrontend` para preparar seu aplicativo corretamente:

    ```bash
    azds prep --public
    ```

    A CLI de espaços de desenvolvimento `azds prep` comando gera ativos de Docker e Kubernetes com configurações padrão. Estes ficheiros persistam durante o tempo de vida do projeto, e eles podem ser personalizados:

    * `./Dockerfile` e `./Dockerfile.develop` descrevem a aplicação imagem de contentor e como o código-fonte é criado e é executado dentro do contentor.
    * Um [gráfico Helm](https://helm.sh/docs/developing_charts/) em `./charts/webfrontend` descreve como implementar o contentor no Kubernetes.
    * `./azds.yaml` é o arquivo de configuração de espaços de desenvolvimento do Azure.

    Para obter mais informações, consulte [como os espaços de desenvolvimento do Azure funciona e é configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Compilar e executar a aplicação no AKS com o `azds up` comando:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Analisar o resultado da consola para obter informações sobre o URL que foi criada com o `up` comando. Estará na forma:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Abra este URL numa janela do browser e deverá ver a aplicação web. À medida que o contentor é executado, os resultados `stdout` e `stderr` são transmitidos para a janela do terminal.

8. Em seguida, configure e implemente *mywebapi*:

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

Nesta secção, prepare o servidor do Jenkins para executar o pipeline de CI de exemplo.

* Instalar o plug-ins
* Instale o Helm e CLI do Kubernetes
* Adicionar credenciais

### <a name="install-plugins"></a>Instalar o plug-ins

1. Inicie sessão no seu servidor do Jenkins. Escolher **gerir Jenkins > Gerir plug-ins**.
2. Sobre o **disponível** separador, selecione os plug-ins seguintes:
    * [Espaços de desenvolvimento do Azure](https://plugins.jenkins.io/azure-dev-spaces)
    * [Tarefas de registo de contentor do Azure](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Injetor de ambiente](https://plugins.jenkins.io/envinject)
    * [Integração do GitHub](https://plugins.jenkins.io/github-pullrequest)

    Se esses plug-ins não aparecem na lista, verifique os **instalada** guia para ver se já estiver instalados.

3. Para instalar os plug-ins, escolha **agora a transferir e instalar após o reinício**.

4. Reinicie o servidor do Jenkins para concluir a instalação.

### <a name="install-helm-and-kubectl"></a>Instale o Helm e kubectl

O pipeline de exemplo utiliza o Helm e o kubectl para implementar no espaço de desenvolvimento. Quando o Jenkins é instalado, ele cria uma conta de administrador com o nome *jenkins*. O Helm e o kubectl precisam de estar acessíveis ao utilizador jenkins.

1. Efetue uma ligação SSH a mestre do Jenkins. 

2. Mude para o `jenkins` utilizador:
    ```bash
    sudo su jenkins
    ```

3. Instale o Helm CLI. Para obter mais informações, consulte [Helm instalar](https://helm.sh/docs/using_helm/#installing-helm).

4. Instale o kubectl. Para obter mais informações, consulte [ **az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Adicionar credenciais ao Jenkins

1. Jenkins precisa de um principal de serviço do Azure para autenticar e aceder aos recursos do Azure. Para criar o principal de serviço, consulte a [criar principal de serviço](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) secção a implementar no tutorial do App Service do Azure. Certifique-se de que guarde uma cópia da saída do `create-for-rbac` porque essas informações para concluir o passo seguinte é necessário. A saída será algo parecido com isto:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adicionar uma *Principal de serviço do Microsoft Azure* tipo no Jenkins, com as informações do principal de serviço do passo anterior de credencial. Os nomes na captura de ecrã abaixo correspondem à saída de `create-for-rbac`.

    O **ID** campo é o nome da credencial Jenkins para o seu principal de serviço. O exemplo utiliza o valor de `displayName` (neste caso, `xxxxxxxjenkinssp`), mas pode usar qualquer texto que pretende. Este nome da credencial é o valor para a variável de ambiente de AZURE_CRED_ID na próxima seção.

    ![Adicionar credenciais de principal de serviço para o Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    O **Descrição** é opcional. Para obter instruções mais detalhadas, consulte [adicionar principal de serviço para o Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) secção a implementar no tutorial do App Service do Azure. 



3. Para mostrar as suas credenciais do ACR, execute este comando:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Fazer uma cópia da saída do JSON, que deve ter um aspeto semelhante ao seguinte:

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

4. Adicionar uma *nome de utilizador com palavra-passe* tipo no Jenkins de credencial. O **nome de utilizador** é o nome de utilizador do último passo, neste exemplo `acr01`. O **palavra-passe** é o valor para palavra-passe primeiro neste exemplo `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. O **ID** desta credencial é o valor de ACR_CRED_ID.

5. Defina uma credencial AKS. Adicionar uma *configuração do Kubernetes (kubeconfig)* tipo no Jenkins (utilize a opção "Enter diretamente") de credencial. Para obter as credenciais de acesso para o seu cluster do AKS, execute o seguinte comando:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   O **ID** esta credencial é o valor de KUBE_CONFIG_ID na próxima seção.

## <a name="create-a-pipeline"></a>Criar um pipeline

O cenário selecionado para o pipeline de exemplo baseia-se num padrão de mundo real: Um pedido pull um CI de acionadores de pipelines que baseia-se e, em seguida, implementa as alterações propostas para um espaço de desenvolvimento do Azure para teste e de revisão. Consoante o resultado da revisão, as alterações são intercaladas e implementadas no AKS ou eliminadas. Por fim, o espaço de desenvolvimento é removido.

A configuração de pipeline do Jenkins e o Jenkinsfile definem as fases no pipeline de CI. Este fluxograma que mostra os estágios de pipeline e os pontos de decisão definidos do Jenkinsfile:

![Fluxo de pipeline do Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Baixe uma versão modificada dos *mywebapi* do projeto de https://github.com/azure-devops/mywebapi. Este projeto contém vários arquivos necessários para criar um pipeline, incluindo o *Jenkinsfile*, *Dockerfiles*e o gráfico do Helm.

2. Inicie sessão no Jenkins. No menu à esquerda, selecione **Add Item**.

3. Selecione **Pipeline**e, em seguida, introduza um nome na **introduza um nome de item** caixa. Selecione **OK**, e, em seguida, a tela de configuração do pipeline será aberta automaticamente.

4. Sobre o **gerais** separador, verificação **preparar um ambiente para a execução**. 

5. Verifique **manter as variáveis de ambiente Jenkins** e **manter as variáveis de compilação Jenkins**.

6. Na **propriedades conteúdo** , introduza as seguintes variáveis de ambiente:

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

    Utilizar os valores de exemplo fornecidos nas secções anteriores, a lista de variáveis de ambiente deve ter um aspeto semelhante ao seguinte:

    ![Variáveis de ambiente de pipeline do Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Escolher **Pipeline de script do SCM** na **Pipeline > definição**.
8. Na **SCM**, escolha **Git** e, em seguida, introduza o URL do repositório.
9. Na **especificador de ramo**, introduza `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Preencha o SCM repositório URL e o script de caminho "Jenkinsfile".
11. **Check-out leve** deve ser verificada.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Criar um pedido pull para acionar o pipeline

Para o passo concluído 3 nesta secção, precisará comentar a parte do Jenkinsfile, caso contrário, receberá um erro 404 quando tentar exibir as versões nova e antigas lado a lado. Por predefinição, quando opta por intercalar o PR, a versão anterior de partilhado do mywebapi será removida e substituída pela nova versão. Faça a alteração seguinte para o Jenkinsfile antes de concluir o passo 1:

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

1. Fazer uma alteração para `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`e, em seguida, crie um pedido pull. Por exemplo:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Inicie sessão no Jenkins e selecione o nome do pipeline e, em seguida, escolha **compilar agora**. 

    Também pode configurar uma *webhook* para acionar o pipeline de Jenkins automaticamente. Quando um pedido pull for inserido, o GitHub emite uma POSTAGEM para o Jenkins, acionar o pipeline. Para obter mais informações sobre como configurar um webhook, veja [Jenkins ligar-se ao GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Compare as alterações para a versão partilhada atual:

    1. Abra o browser e navegue para a versão partilhada `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contém o URL.

    2. Abra outra guia e, em seguida, introduza o URL de espaço de desenvolvimento de PR. É semelhante ao `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Encontrará o link no **histórico criar >< compilação # >> saída da consola** para a tarefa do Jenkins. A página de pesquisa `aksapp`, ou para ver apenas o prefixo, procure `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Construir o URL para o espaço de desenvolvimento de subordinados

Quando submeta um pedido pull, o Jenkins cria um espaço de desenvolvimento de subordinados com base no espaço de desenvolvimento compartilhado da Equipe e executará o código do seu pedido pull filho espaço de desenvolvimento. O URL para o espaço de desenvolvimento do filho assume a forma `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** é definido durante a execução de pipeline, o plug-in de espaços de desenvolvimento do Azure por **devSpacesCreate**:

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

O `test_endpoint` é o URL para a aplicação de webfrontend que implementou anteriormente usando `azds up`na [implementar aplicações de exemplo para o cluster do AKS, 7 passo](#test_endpoint). O valor de `$env.TEST_ENDPOINT` está definido na configuração do pipeline. 

O fragmento de código seguinte mostra como o URL de espaço de desenvolvimento de subordinado é utilizado no `smoketest` estágio. O código verifica se o espaço de desenvolvimento do filho TEST_ENDPOINT está disponível e, nesse caso, transfere o texto de saudação para stdout:

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

Quando terminar a utilizar a aplicação de exemplo, limpar os recursos do Azure ao eliminar o grupo de recursos:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a utilizar o plug-in de espaços de desenvolvimento do Azure para Jenkins e o plug-in do Azure Container Registry para criar código e implementar um espaço de desenvolvimento.

A seguinte lista de recursos fornece mais informações sobre espaços de desenvolvimento do Azure, as tarefas de ACR e CI/CD com Jenkins.

Espaços de desenvolvimento do Azure:
* [Como o Azure Dev Spaces funciona e é configurado](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Tarefas ACR:
* [Automatizar a aplicação de patches ao SO e à arquitetura com as Tarefas do ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Compilação automática em consolidação de código](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD com Jenkins no Azure:
* [Implementação contínua do Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
