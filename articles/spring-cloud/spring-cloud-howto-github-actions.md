---
title: Azure Spring Cloud CI/CD com ações gitHub
description: Como acumular fluxo de trabalho CI/CD para Azure Spring Cloud com ações GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 303f24ef6d934c0382bd8917833e3ec545f2a540
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776485"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD com ações gitHub

GitHub Actions suportam um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Com as ações gitHub para azure Spring Cloud você pode criar fluxos de trabalho no seu repositório para construir, testar, embalar, lançar e implementar para Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Este exemplo requer o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Configurar o repositório GitHub e autenticar
Precisa de uma credencial de princípio de serviço Azure para autorizar a ação de login azure. Para obter uma credencial Azure, execute os seguintes comandos na sua máquina local:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Para aceder a um grupo de recursos específicos, pode reduzir o âmbito:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
O comando deve ser de saída de um objeto JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Este exemplo utiliza a amostra [de Métricas piggy](https://github.com/Azure-Samples/piggymetrics) no GitHub.  Bifurque a amostra, abra a página de repositório GitHub e clique no separador **Definições.** Menu **Segredos** Abertos e clique em **Adicionar um novo segredo:**

 ![Adicione novo segredo](./media/github-actions/actions1.png)

Detete o nome secreto para `AZURE_CREDENTIALS` e o seu valor para a cadeia JSON que encontrou sob o título Instale o *seu repositório GitHub e autenticar*.

 ![Definir dados secretos](./media/github-actions/actions2.png)

Você também pode obter a credencial de login Azure a partir de Key Vault em ações GitHub, como explicado em [Authenticate Azure Spring com Key Vault em GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Instância de serviço de prestação
Para fornecer a sua instância de serviço Azure Spring Cloud, execute os seguintes comandos utilizando o Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Construir o fluxo de trabalho
O fluxo de trabalho é definido utilizando as seguintes opções.

### <a name="prepare-for-deployment-with-azure-cli"></a>Prepare-se para a implantação com o Azure CLI
O comando `az spring-cloud app create` não é atualmente idempotente.  Recomendamos este fluxo de trabalho em aplicações e instâncias existentes da Azure Spring Cloud.

Utilize os seguintes comandos Azure CLI para preparação:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Implementar diretamente com o Azure CLI
Crie o ficheiro `.github/workflow/main.yml` no repositório:

```
name: AzureSpringCloud

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
        mvn clean package -D skipTests
    
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
### <a name="deploy-with-azure-cli-action"></a>Implementar com a ação Azure CLI
O comando az `run` usará a versão mais recente do Azure CLI. Se houver alterações de rutura, também pode utilizar uma versão específica do Azure CLI com `action`azure/CLI . 

> [!Note] 
> Este comando será executado num novo contentor, para que `env` não funcione, e o acesso a ficheiros de ação cruzada pode ter restrições adicionais.

Crie o ficheiro .github/workflow/main.yml no repositório:
```
name: AzureSpringCloud

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
        mvn clean package -D skipTests
        
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

## <a name="deploy-with-maven-plugin"></a>Implementar com Maven Plugin
Outra opção é utilizar o [Maven Plugin](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) para implementar o Jar e atualizar as definições da App. O comando `mvn azure-spring-cloud:deploy` é idempotente e criará automaticamente apps se necessário. Não precisa de criar aplicações correspondentes com antecedência.

```
name: AzureSpringCloud

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
        mvn clean package -D skipTests
        
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
**As ações** GitHub devem ser ativadas automaticamente depois de empurrar `.github/workflow/main.yml` para o GitHub. A ação será desencadeada quando forçares um novo compromisso. Se criar este ficheiro no navegador, a sua ação já deveria ter sido executada.

Para verificar se a ação foi ativada, clique no separador **Ações** na página de repositório GitHub:

 ![Verificar a ação ativada](./media/github-actions/actions3.png)

Se a sua ação correr por engano, por exemplo, se não tiver definido a credencial Azure, pode reproduzir as verificações após a correção do erro. Na página de repescagem GitHub, clique **em Ações,** selecione a tarefa específica de fluxo de trabalho e, em seguida, clique no botão **de verificação rerun** para reproduzir verificações:

 ![Reexecutar cheques](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Passos seguintes
* [Cofre chave para ações gitHub de nuvem de primavera](./spring-cloud-github-actions-key-vault.md)
* [Diretores de Diretório Sonérório Sonérbio Azure](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Ações gitHub para Azure](https://github.com/Azure/actions/)
