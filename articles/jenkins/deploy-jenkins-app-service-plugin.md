---
title: Implementar no Serviço de Aplicações do Azure com o plug-in Jenkins
description: Saiba como utilizar o plug-in Jenkins do Serviço de Aplicações do Azure para implementar uma aplicação Web Java no Azure através do Jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, serviço de aplicações
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 58835b66824d55b64b77e34df64d34c8da1c269a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864818"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Implementar no Serviço de Aplicações do Azure com o plug-in Jenkins 

Para implementar uma aplicação Web Java no Azure, pode utilizar a CLI do Azure no [Pipeline do Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) ou o [plug-in Jenkins do Serviço de Aplicações do Azure](https://plugins.jenkins.io/azure-app-service). A versão 1.0 do plug-in Jenkins suporta a implementação contínua mediante a utilização da funcionalidade Aplicações Web do Serviço de Aplicações do Azure através de:
* Carregamento de ficheiros.
* Docker para Aplicações Web em Linux.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar o Jenkins para implementar Aplicações Web através do carregamento de ficheiros.
> * Configurar o Jenkins para implementar Aplicações Web para Contentores.

## <a name="create-and-configure-a-jenkins-instance"></a>Criar e configurar uma instância do Jenkins

Se ainda não tiver uma instância Mestre do Jenkins, comece com o [modelo de solução](install-jenkins-solution-template.md), que inclui a versão 8 do Java Development Kit (JDK) e os seguintes plug-ins do Jenkins necessários:

* [Plug-in de cliente do Git do Jenkins](https://plugins.jenkins.io/git-client) versão 2.4.6 
* [Plug-in de Docker Commons](https://plugins.jenkins.io/docker-commons) versão 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) versão 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) versão 0.1

Pode utilizar o plug-in Jenkins para implementar uma aplicação Web em qualquer linguagem que as Aplicações Web suportem, como C#, PHP, Java e Node.js. Neste tutorial, utilizamos uma [aplicação Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Para criar um fork do repositório para a sua própria conta do GitHub, selecione o botão **Fork**, no canto superior direito da interface do GitHub.  

> [!NOTE]
> Para compilar o projeto Java, precisa do Java JDK e do Maven. Instale estes componentes na instância Mestre do Jenkins ou no agente de VM, se utilizar o agente para a integração contínua. Se estiver a implementar uma aplicação Java SE, o ZIP também é necessário no servidor de compilação.
>

Para instalar os componentes, inicie sessão na instância do Jenkins com SSH e execute os seguintes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para implementar na Aplicação Web para Contentores, instale o Docker na instância Mestre do Jenkins ou no agente de VM utilizado para a compilação. Para instruções, veja [Install Docker on Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/) (Instalar o Docker em Ubuntu).

## <a name="service-principal"></a> Adicionar um principal de serviço do Azure às credenciais do Jenkins

Para implementar no Azure, precisa de um principal de serviço do Azure. 


1. Para criar um Azure principal de serviço, utilize o [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou a [portal do Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. No dashboard do Jenkins, selecione **Credentials** > **System** (Credenciais > Sistema). Em seguida, selecione **Global credentials(unrestricted)** (Credenciais globais (sem restrições).
3. Para adicionar um principal de serviço do Microsoft Azure, selecione **Add Credentials** (Adicionar Credenciais). Indique valores para os campos **Subscription ID** (ID da Subscrição), **Client ID** (ID do Cliente), **Client Secret** (Segredo do Cliente) e **OAuth 2.0 Token Endpoint** (Ponto Final de Tokens de OAuth 2.0). Defina o campo **ID** como **mySp**. Vamos utilizar este ID em passos subsequentes deste artigo.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Configurar o Jenkins para implementar Aplicações Web mediante o carregamento de ficheiros

Para implementar o seu projeto nas Aplicações Web, pode carregar os artefactos de compilação através do carregamento de ficheiros. O Serviço de Aplicações do Azure suporta várias opções de implementação. O plug-in Jenkins do Serviço de Aplicações do Azure facilita e deriva a opção de implementação com base no tipo de ficheiro. 

* Para aplicações Java EE, é utilizada a [implementação WAR](/azure/app-service/deploy-zip#deploy-war-file).
* Para aplicações Java SE, é utilizada a [implementação ZIP](/azure/app-service/deploy-zip#deploy-zip-file).
* Para outras linguagens, é utilizada a [implementação Git](/azure/app-service/deploy-local-git).

Antes de configurar o trabalho no Jenkins, precisa de um plano do Serviço de Aplicações e de uma aplicação Web para executar a aplicação Web.


1. Utilize o [comando da CLI do Azure](/cli/azure/appservice/plan#az-appservice-plan-create) `az appservice plan create` para criar um plano do Serviço de Aplicações com o escalão de preço **GRATUITO**- O plano do Serviço de Aplicações define os recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano do Serviço de Aplicações partilham esses recursos. Os recursos partilhados ajudam-no a reduzir custos se alojar várias aplicações.
2. Crie uma aplicação Web. Pode utilizar o [portal do Azure](/azure/app-service-web/web-sites-configure) ou o comando `az` da CLI do Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Configure a configuração do runtime de Java de que a sua aplicação precisa. O seguinte comando da CLI do Azure configura a aplicação Web para ser executada num JDK 8 recente e no [Apache Tomcat](https://tomcat.apache.org/) versão 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Configurar o trabalho do Jenkins

1. Crie um projeto **freestyle** novo no dashboard do Jenkins.
2. Configure o campo **Source Code Management** (Gestão do Código de Origem) para utilizar o seu fork local da [aplicação Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Indique o valor de **Repository URL** (URL do Repositório). Por exemplo: http:\//github.com/&lt;your_ID > / javawebappsample.
3. Adicione um passo para compilar o projeto com o Maven ao adicionar o comando **Execute shell**. Neste exemplo, precisamos de outro comando para mudar o nome do ficheiro \*.war na pasta raiz para **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Selecione **Publish an Azure Web App** (Publicar uma Aplicação Web do Azure) para adicionar uma ação de pós-compilação.
5. Forneça **mySp** como o principal de serviço do Azure. Esse principal foi armazenado como [Azure Credentials](#service-principal) (Credenciais do Azure) num passo anterior.
6. Na secção **App Configuration** (Configuração da aplicação), escolha o grupo de recursos e a aplicação Web na sua subscrição. O plug-in Jenkins deteta automaticamente se a aplicação Web é baseada em Windows ou Linux. Para aplicações Web do Windows, é apresentada a opção **Publish Files** (Publicar Ficheiros).
7. Preencha os ficheiros que pretende implementar. Por exemplo, se estiver a utilizar Java, especifique o pacote WAR. Utilize os parâmetros **Source Directory** (Diretório de Origem) e **Target Directory** (Diretório de Destino) para especificar as pastas de origem e destino que vão ser utilizadas para o carregamento do ficheiro. A aplicação Web Java no Azure é executada num servidor Tomcat. Portanto, para Java, carregue seu pacote WAR para a pasta webapps. Neste exemplo, defina o valor de **Source Directory** (Diretório de Origem) como **target** (destino) e de **Target Directory** (Diretório de Destino) como **webapps**.
8. Se quiser implementar noutro bloco que não de produção, também pode definir o nome **Slot**.
9. Guarde o projeto e compile-o. Após a conclusão da compilação, a aplicação Web é implementada no Azure.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Implementar Aplicações Web mediante o carregamento de ficheiros com o Pipeline do Jenkins

O plug-in Jenkins do Serviço de Aplicações do Azure está pronto para pipelines. Pode ver o exemplo seguinte no repositório do GitHub.

1. Na interface do GitHub, abra o ficheiro **Jenkinsfile_ftp_plugin**. Para editar o ficheiro, selecione o ícone de lápis. Atualize as definições **resourceGroup** e **webAppName** da aplicação Web nas linhas 11 e 12, respetivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Defina **withCredentials**, na linha 14, como o ID da credencial na sua instância do Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Criar um pipeline do Jenkins

1. Abra o Jenkins num browser. Selecione **Novo Item**.
2. Indique um nome para a tarefa e selecione **Pipeline**. Selecione **OK**.
3. Selecione o separador **Pipeline**.
4. No valor de **Definition** (Definição), selecione **Pipeline script from SCM** (Script de pipeline do SCM).
5. No valor de **SCM**, selecione **Git**. Introduza o URL do GitHub do repositório fork. Por exemplo, https://&lt;o_seu_repositório_fork>.git.
6. Atualize o valor de **Script Path** (Caminho do Script) para **Jenkinsfile_ftp_plugin**.
7. Selecione **Save** (Guardar) e execute o trabalho.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configurar o Jenkins para implementar Aplicações Web para Contentores

As Aplicações Web no Linux suportam a implementação com a utilização do Docker. Para implementar a aplicação web com o Docker, terá de fornecer um Dockerfile que empacota a sua aplicação web com um tempo de execução do serviço numa imagem do Docker. Depois, o plug-in Jenkins compila a imagem, envia-a para um registo do Docker e implementa-a na aplicação Web.

As Aplicações Web no Linux também suportam métodos de implementação tradicionais, como Git e carregamento de ficheiros, mas apenas para linguagens incorporadas (.NET Core, Node.js, PHP e Ruby). Relativamente a outras linguagens, tem de empacotar o código da aplicação e o runtime de serviço em conjunto numa imagem do Docker e utilizar este último para a implementação.

Antes de configurar o trabalho no Jenkins, precisa de uma aplicação Web no Linux. Também precisa de um registo de contentor para armazenar e gerir as suas imagens privadas do contentor do Docker. Pode utilizar o DockerHub para criar o registo de contentor. Neste exemplo, utilizamos o Azure Container Registry.

* [Crie a sua aplicação Web no Linux](../app-service/containers/quickstart-nodejs.md).
* O Azure Container Registry é um serviço de [Docker Registry](https://docs.docker.com/registry/) gerido e baseado na versão 2.0 do Docker Registry de código aberto. [Crie um registo do Azure Container Registry](/azure/container-registry/container-registry-get-started-azure-cli). Também pode utilizar o DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Configurar o trabalho do Jenkins para Docker

1. Crie um projeto **freestyle** novo no dashboard do Jenkins.
2. Configure o campo **Source Code Management** (Gestão do Código de Origem) para utilizar o seu fork local da [aplicação Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Indique o valor de **Repository URL** (URL do Repositório). Por exemplo: http:\//github.com/&lt;your_ID > / javawebappsample.
3. Adicione um passo para compilar o projeto com o Maven ao adicionar o comando **Execute shell**. Inclua a linha seguinte no comando:
    ```bash
    mvn clean package
    ```

4. Selecione **Publish an Azure Web App** (Publicar uma Aplicação Web do Azure) para adicionar uma ação de pós-compilação.
5. Forneça **mySp** como o principal de serviço do Azure. Esse principal foi armazenado como [Azure Credentials](#service-principal) (Credenciais do Azure) num passo anterior.
6. Na secção **App Configuration** (Configuração da aplicação), escolha o grupo de recursos e uma aplicação Web no Linux na sua subscrição.
7. Escolha **Publish via Docker** (Publicar através do Docker).
8. Preencha o valor do caminho **Dockerfile**. Pode manter o valor predefinido, /Dockerfile.
No valor de **Docker registry URL** (URL do Docker Registry), indique o URL com o formato https://&lt;yourRegistry>.azurecr.io, caso utilize o Azure Container Registry. Se utilizar o DockerHub, deixe o valor em branco.
9. No valor de **Registry credentials** (Credenciais do registo), adicione a credencial do registo de contentor. Pode executar os seguintes comandos na CLI do Azure para obter o userid e a palavra-passe. O primeiro comando ativa a conta de administrador:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. O nome da imagem do Docker e o valor da etiqueta no separador **Advanced** (Avançadas) são opcionais. Por predefinição, o valor do nome da imagem é obtido a partir do nome da imagem que configurou no portal do Azure, na definição **Contentor do Docker**. A etiqueta é gerada a partir de $BUILD_NUMBER.
    > [!NOTE]
    > Confirme que especifica o nome da imagem no portal do Azure ou que fornece o valor de uma **Imagem do Docker** no separador **Advanced** (Avançadas). Neste exemplo, defina o valor de **Docker image** (Imagem do Docker) como &lt;your_Registry>.azurecr.io/calculator e deixe o valor de **Docker Image Tag** (Etiqueta da Imagem do Docker) em branco.

11. Se utilizar uma definição de imagem do Docker incorporada, a implementação falha. Altere a configuração do Docker para utilizar uma imagem personalizada na definição **Contentor do Docker** no portal do Azure. Para imagens incorporadas, utilize a abordagem de carregamento de ficheiro para implementar.
12. Tal como na abordagem do carregamento de ficheiro, pode escolher um nome de **Slot** (Bloco) diferente de **production** (produção).
13. Guarde e compile o projeto. A imagem de contentor é enviada para o seu registo e a aplicação Web é implementada.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Implementar a Aplicação Web para Contentores com o Pipeline do Jenkins

1. Na interface do GitHub, abra o ficheiro **Jenkinsfile_container_plugin**. Para editar o ficheiro, selecione o ícone de lápis. Atualize as definições **resourceGroup** e **webAppName** da aplicação Web nas linhas 11 e 12, respetivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Altere a linha 13 para o servidor do seu registo de contentor:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Altere a linha 16 para utilizar o ID de credencial na sua instância do Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Criar um pipeline do Jenkins    

1. Abra o Jenkins num browser. Selecione **Novo Item**.
2. Indique um nome para a tarefa e selecione **Pipeline**. Selecione **OK**.
3. Selecione o separador **Pipeline**.
4. No valor de **Definition** (Definição), selecione **Pipeline script from SCM** (Script de pipeline do SCM).
5. No valor de **SCM**, selecione **Git**. Introduza o URL do GitHub do repositório fork. Por exemplo, https://&lt;o_seu_repositório_fork>.git.
7. Atualize o valor de **Script Path** (Caminho do Script) para **Jenkinsfile_container_plugin**.
8. Selecione **Save** (Guardar) e execute o trabalho.

## <a name="verify-your-web-app"></a>Verificar a aplicação Web

1. Para confirmar que o ficheiro WAR foi implementado com êxito na sua aplicação Web, abra um browser.
2. Aceda a http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name> pelo nome da aplicação Web. Verá a mensagem:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Aceda a http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Substitua &lt;x> e &lt;y> por números para obter a soma de x + y. A Calculadora mostra a soma: ![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>No Serviço de Aplicações do Azure no Linux

1. Para verificar a aplicação Web, execute o seguinte comando na CLI do Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    É apresentada a seguinte mensagem:
    ```CLI
    ["calculator"]
    ```
    
2. Aceda a http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name> pelo nome da aplicação Web. Verá a mensagem: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Aceda a http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Substitua &lt;x> e &lt;y> por números para obter a soma de x + y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou o plug-in Jenkins do Serviço de Aplicações do Azure para implementar no Azure.

Aprendeu a:

> [!div class="checklist"]
> * Configurar o Jenkins para implementar no Serviço de Aplicações do Azure através do carregamento de ficheiros 
> * Configurar o Jenkins para implementar na Aplicação Web para Contentores 
