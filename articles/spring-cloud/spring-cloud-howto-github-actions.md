---
title: Azure Spring Cloud CI/CD com ações do GitHub
description: Como construir fluxo de trabalho CI/CD para Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: e17fa3e99de9f429fa279ba2e5413b60e9084de8
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125707"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD com ações do GitHub

GitHub Actions suporta um fluxo de trabalho automatizado de ciclo de vida de desenvolvimento de software. Com o GitHub Actions for Azure Spring Cloud pode criar fluxos de trabalho no seu repositório para construir, testar, embalar, lançar e implementar para a Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Este exemplo requer o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Configurar o repositório GitHub e autenticar
Precisa de uma credencial principal do serviço Azure para autorizar a ação de login do Azure. Para obter uma credencial Azure, execute os seguintes comandos na sua máquina local:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Para aceder a um grupo de recursos específicos, pode reduzir o âmbito:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
O comando deve descoduar um objeto JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Este exemplo utiliza a amostra [piggy Metrics](https://github.com/Azure-Samples/piggymetrics) no GitHub.  Forque a amostra, abra a página de repositório do GitHub e clique no **separador Definições.** Menu **De Segredos Abertos** e clique em **Adicionar um novo segredo**:

 ![Adicionar novo segredo](./media/github-actions/actions1.png)

Desista o nome secreto `AZURE_CREDENTIALS` e o seu valor para a cadeia JSON que encontrou no título *Configurar o seu repositório GitHub e autenticar*.

 ![Definir dados secretos](./media/github-actions/actions2.png)

Você também pode obter a credencial de login Azure a partir de Key Vault em ações GitHub, como explicado em [Authenticate Azure Spring com Key Vault em GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Instância de serviço de prestação
Para providenciar a sua instância de serviço Azure Spring Cloud, execute os seguintes comandos utilizando o CLI Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Construir o fluxo de trabalho
O fluxo de trabalho é definido utilizando as seguintes opções.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparar para implantação com o Azure CLI
O comando `az spring-cloud app create` não é atualmente idempotente.  Recomendamos este fluxo de trabalho sobre aplicações e instâncias existentes da Azure Spring Cloud.

Utilize os seguintes comandos Azure CLI para preparação:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Implementar diretamente com o Azure CLI
Criar o `.github/workflow/main.yml` ficheiro no repositório:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Implementar com ação Azure CLI
O comando az `run` usará a versão mais recente do Azure CLI. Se houver alterações de rutura, também pode utilizar uma versão específica do Azure CLI com azure/CLI `action` . 

> [!Note] 
> Este comando funcionará num novo recipiente, pelo `env` que não funcionará, e o acesso a ficheiros de ação cruzada pode ter restrições extra.

Crie o ficheiro .github/workflow/main.yml no repositório:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implementar com o Maven Plugin
Outra opção é utilizar o [Plugin Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) para implementar o Jar e atualizar as definições da App. O comando `mvn azure-spring-cloud:deploy` é idempotente e criará automaticamente Apps se necessário. Não precisa de criar aplicações correspondentes com antecedência.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Executar o fluxo de trabalho
As **ações** do GitHub devem ser ativadas automaticamente depois de empurrar `.github/workflow/main.yml` para o GitHub. A ação será desencadeada quando empurrares um novo compromisso. Se criar este ficheiro no browser, a sua ação já deveria ter sido executada.

Para verificar se a ação foi ativada, clique no **separador Ações** na página do repositório GitHub:

 ![Verificar a ação ativada](./media/github-actions/actions3.png)

Se a sua ação correr por engano, por exemplo, se não tiver definido a credencial Azure, pode repetir as verificações após a correção do erro. On the GitHub repository page, click **Actions**, select the specific workflow task, and then click the **Rerun checks** button to rerun checks:

 ![Verificações de repetição](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Passos seguintes
* [Cofre chave para ações do GitHub da nuvem de primavera](./spring-cloud-github-actions-key-vault.md)
* [Diretores de serviço azure ative](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub Actions para o Azure](https://github.com/Azure/actions/)
