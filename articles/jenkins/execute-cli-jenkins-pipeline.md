---
title: Executar a CLI do Azure com o Jenkins
description: Saiba como utilizar a CLI do Azure para implementar uma aplicação Web Java no Azure no Pipeline do Jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, serviço de aplicações, cli
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 5728a9ab70c5b7db10a123d6964b498e70f96588
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546148"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Implementar no Serviço de Aplicações do Azure com o Jenkins e a CLI do Azure
Para implementar uma aplicação Web Java no Azure, pode utilizar a CLI do Azure no [Pipeline do Jenkins](https://jenkins.io/doc/book/pipeline/). Neste tutorial, vai criar um pipeline de CI/CD numa VM do Azure, incluindo como:

> [!div class="checklist"]
> * Criar uma VM do Jenkins
> * Configurar o Jenkins
> * Criar uma aplicação Web no Azure
> * Preparar um repositório do GitHub
> * Criar o pipeline do Jenkins
> * Executar o pipeline e verificar a aplicação Web

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Criar e Configurar a instância do Jenkins
Se ainda não tiver um mestre do Jenkins, comece com o [Modelo de Solução](install-jenkins-solution-template.md), que inclui o plug-in [Azure Credentials](https://plugins.jenkins.io/azure-credentials) necessário, por predefinição. 

O plug-in Azure Credential permite-lhe armazenar credenciais de principal de serviço do Microsoft Azure no Jenkins. Na versão 1.2, adicionámos o suporte para que o Pipeline do Jenkins possa obter as credenciais do Azure. 

Certifique-se de que tem a versão 1.2 ou posterior:
* No dashboard do Jenkins, clique em **Manage Jenkins (Gerir Jenkins) -> Plugin Manager (Gestor de Plug-ins)->** e procure **Azure Credential**. 
* Atualize o plug-in se a versão for anterior à versão 1.2.

O Java JDK e o Maven também são necessários no mestre do Jenkins. Para instalar, inicie sessão no mestre do Jenkins com SSH e execute os seguintes comandos:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adicionar o principal de serviço do Azure à credencial do Jenkins

Uma credencial do Azure é necessária para executar a CLI do Azure.

* No dashboard do Jenkins, clique em **Credentials (Credenciais) -> System (Sistema) ->**. Clique em **Global credentials(unrestricted)** (Credenciais globais (sem restrições)).
* Clique em **Add Credentials** (Adicionar Credenciais) para adicionar um [principal de serviço do Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json), preenchendo os campos Subscription ID (ID de Subscrição), Client ID (ID de Cliente), Client Secret (Segredo do Cliente) e OAuth 2.0 Token Endpoint (Ponto Final de Tokens de OAuth 2.0). Indique um ID para utilizar no passo seguinte.

![Adicionar Credenciais](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Criar um Serviço de Aplicações do Azure para implementar a aplicação Web Java

Crie um plano do Serviço de Aplicações do Azure com o escalão de preço **FREE**, através do comando da CLI [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). O plano do serviço de aplicações define os recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano do serviço de Aplicações partilham estes recursos, o que lhe permite economizar quando alojar várias aplicações. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Quando o plano estiver pronto, a CLI do Azure mostra um resultado semelhante ao seguinte exemplo:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar uma aplicação Web do Azure

 Utilize o comando da CLI [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) para criar uma definição de aplicação Web no plano do Serviço de Aplicações `myAppServicePlan`. A definição da aplicação Web fornece um URL com o qual pode aceder à sua aplicação e configura várias opções para implementar o código no Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Substitua o marcador de posição `<app_name>` pelo nome exclusivo da sua aplicação. Este nome exclusivo faz parte do nome de domínio predefinido da aplicação Web, pelo que o nome tem de ser exclusivo relativamente a todas as aplicações no Azure. Pode mapear qualquer entrada de nome de domínio personalizada para a aplicação Web antes de a expor aos utilizadores.

Quando a definição de aplicação Web estiver pronta, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java 

Configure a configuração do runtime de Java de que a sua aplicação precisa, com o comando [az appservice web config update](/cli/azure/webapp/config).

O comando seguinte configura a aplicação Web para ser executada num JDK recente do Java 8 e [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparar um Repositório do GitHub
Abra o repositório [Aplicação Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Para duplicar (fork) o repositório para a sua conta do GitHub, clique no botão **Fork**, no canto superior direito.

* Na IU da Web do GitHub, abra o ficheiro **Jenkinsfile**. Clique no ícone de lápis para editar este ficheiro para atualizar o grupo de recursos e o nome da sua aplicação Web na linha 20 e 21, respetivamente.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Altere a linha 23 para atualizar o ID de credencial na sua instância do Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Criar o pipeline do Jenkins
Abra o Jenkins num browser e clique em **New Item** (Novo Item). 

* Indique um nome para a tarefa e selecione **Pipeline**. Clique em **OK**.
* Em seguida, clique no separador **Pipeline**. 
* Em **Definition** (Definição), selecione **Pipeline script from SCM** (Script de pipeline do SCM).
* Em **SCM**, selecione **Git**.
* Introduza o URL do GitHub para o repositório duplicado: https:\<o repositório duplicado\>.git
* Clique em **Guardar**

## <a name="test-your-pipeline"></a>Testar o pipeline
* Vá para o pipeline que criou e clique em **Build Now** (Compilar Agora)
* Uma compilação deve ser criada com êxito em poucos segundos e pode ir para a compilação e clicar em **Console Output** (Saída da Consola) para ver os detalhes

## <a name="verify-your-web-app"></a>Verificar a aplicação Web
Para verificar se o ficheiro WAR foi implementado com êxito na sua aplicação Web, abra um browser:

* Aceda a http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Verá:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Aceda a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x> e &lt;y> por quaisquer números) para obter a soma de x e y

![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Implementar na Aplicação Web do Azure no Linux
Agora que já sabe como utilizar a CLI do Azure no seu pipeline do Jenkins, pode modificar o script para implementar numa Aplicação Web do Azure no Linux.

A Aplicação Web no Linux suporta uma forma diferente de executar a implementação, que consiste em utilizar o Docker. Para implementar, tem de fornecer um Dockerfile que empacote a aplicação Web com o runtime de serviço numa imagem do Docker. Em seguida, o plug-in compila a imagem, envia-a para um registo do Docker e implementa-a na aplicação Web.

* Siga os passos [aqui](../app-service/containers/quickstart-nodejs.md) indicados para criar uma aplicação Web do Azure em execução no Linux.
* Instale o Docker na sua instância do Jenkins, seguindo as instruções deste [artigo](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Crie um Container Registry no portal do Azure através dos passos [aqui](/azure/container-registry/container-registry-get-started-azure-cli) indicados.
* No mesmo repositório [Aplicação Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample) que duplicou, edite o ficheiro **Jenkinsfile2**:
    * Linha 18-21, atualize com os nomes do seu grupo de recursos, aplicação Web e ACR, respetivamente. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Linha 24, atualize \<azsrvprincipal\> com o seu ID de credencial
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Crie um novo pipeline do Jenkins tal como fez quando implementou na aplicação Web do Azure no Windows, só que desta vez, utilize **Jenkinsfile2** em alternativa.
* Execute a nova tarefa.
* Para verificar, na CLI do Azure, execute:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Obtém o resultado seguinte:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Aceda a http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Verá a mensagem: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Aceda a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x> e &lt;y> por quaisquer números) para obter a soma de x e y
    
## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, configurou um pipeline do Jenkins que dá saída do código fonte no repositório do GitHub. Executa o Maven para compilar um ficheiro war e, em seguida, utiliza a CLI do Azure para implementar no Serviço de Aplicações do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Jenkins
> * Configurar o Jenkins
> * Criar uma aplicação Web no Azure
> * Preparar um repositório do GitHub
> * Criar o pipeline do Jenkins
> * Executar o pipeline e verificar a aplicação Web
