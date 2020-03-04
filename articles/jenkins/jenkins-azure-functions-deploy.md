---
title: Implemente para funções azure utilizando o plug-in das funções Jenkins Azure
description: Saiba como se implementar nas Funções Azure utilizando o plug-in das Funções Jenkins Azure
keywords: jenkins, azure, devops, java, funções azure
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250913"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Implemente para funções azure utilizando o plug-in das funções Jenkins Azure

[O Azure Functions](/azure/azure-functions/) é um serviço de computação sem servidores. Utilizando funções Azure, pode executar código a pedido sem fornecer ou gerir infraestruturas. Este tutorial mostra como implementar uma função Java para funções Azure utilizando o plug-in funções Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Servidor Jenkins**: Se não tiver um servidor Jenkins instalado, consulte o artigo, [Crie um servidor Jenkins no Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > O código fonte utilizado para este tutorial está localizado no [visual studio china gitHub repo](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Criar uma função Java

Para criar uma função Java com a pilha de tempo de funcionamento Java, utilize o [portal Azure](https://portal.azure.com) ou o [Azure CLI](/cli/azure/?view=azure-cli-latest).

Os seguintes passos mostram como criar uma função Java utilizando o ClI Azure:

1. Crie um grupo de recursos, substituindo o **&lt;resource_group>** espaço reservado pelo seu nome de grupo de recursos.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Crie uma conta de armazenamento Azure, substituindo os espaços reservados pelos valores apropriados.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Crie a aplicação de função de teste, substituindo os espaços reservados com os valores apropriados.

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Preparar servidor Jenkins

Os seguintes passos explicam como preparar o servidor Jenkins:

1. Implante um [servidor Jenkins](https://aka.ms/jenkins-on-azure) no Azure. Se ainda não tiver uma instância do servidor Jenkins instalada, o artigo, [Create a Jenkins server on Azure](./install-jenkins-solution-template.md) guia-o através do processo.

1. Inscreva-se na instância Jenkins com sSH.

1. Na instância Jenkins, instale maven usando o seguinte comando:

    ```terminal
    sudo apt install -y maven
    ```

1. Na instância Jenkins, [instale as Ferramentas Core funções Do Azure,](/azure/azure-functions/functions-run-local) emitindo os seguintes comandos num ponto de terminais:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. No painel jenkins, instale os seguintes plugins:

    - Plug-in funções azure
    - EnvInject Plug-in

1. Jenkins precisa de um serviço Azure principal para autenticar e aceder aos recursos Azure. Consulte o [Serviço de Aplicações Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md) para obter instruções passo a passo.

1. Utilizando o diretor de serviço azure, adicione um tipo de credencial "Microsoft Azure Service Principal" em Jenkins. Consulte o tutorial de Implementação para o Serviço de [Aplicações Azure.](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)

## <a name="fork-the-sample-github-repo"></a>Garfo a amostra GitHub repo

1. [Inscreva-se no repo gitHub para a aplicação de amostras ímpar ou mesmo.](https://github.com/VSChina/odd-or-even-function.git)

1. No canto superior direito em GitHub, escolha **Fork**.

1. Siga as instruções para selecionar a sua conta GitHub e terminar a marcação.

## <a name="create-a-jenkins-pipeline"></a>Criar um Gasoduto Jenkins

Nesta secção, cria-se o [Gasoduto Jenkins.](https://jenkins.io/doc/book/pipeline/)

1. No painel jenkins, crie um Pipeline.

1. Ativar **Prepare um ambiente para a execução**.

1. Adicione as seguintes variáveis ambientais no **Conteúdo das Propriedades,** substituindo os espaços reservados pelos valores apropriados para o seu ambiente:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Na secção **Pipeline>Definição,** selecione **script pipeline a partir de SCM**.

1. Introduza o URL e o script do seu garfo GitHub ("doc/resources/jenkins/JenkinsFile") para utilizar no [exemplo jenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Construir e implantar

Está na hora de gerir o emprego do Jenkins.

1. Em primeiro lugar, obtenha a chave de autorização através das instruções do artigo [funções Azure HTTP e encaderna do](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys) artigo.

1. No seu navegador, introduza o URL da aplicação. Substitua os espaços reservados pelos valores apropriados e especifique um valor numérico para **&lt;input_number>** como entrada para a função Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Verá resultados semelhantes à seguinte saída de exemplo (onde um número ímpar - 365 - foi usado como um teste):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine os recursos que criou com o seguinte passo:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Funções Azure, consulte o seguinte recurso:
> [!div class="nextstepaction"]
> [Documentação de Funções Azure](/azure/azure-functions/)
