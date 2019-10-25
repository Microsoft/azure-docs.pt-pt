---
title: Implantar em Azure Functions usando o plug-in de Azure Functions Jenkins
description: Saiba como implantar no Azure Functions usando o plug-in de Azure Functions Jenkins
ms.service: jenkins
keywords: Jenkins, Azure, DevOps, Java, Azure Functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 58267c607b0c4f2eaaf242c8e0752451f8c04c9a
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882045"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Implantar em Azure Functions usando o plug-in de Azure Functions Jenkins

[Azure Functions](/azure/azure-functions/) é um serviço de computação sem servidor. Usando Azure Functions, você pode executar o código sob demanda sem provisionar ou gerenciar a infraestrutura. Este tutorial mostra como implantar uma função Java para Azure Functions usando o plug-in Azure Functions.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Servidor Jenkins**: se você não tiver um servidor Jenkins instalado, consulte o artigo [criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > O código-fonte usado para este tutorial está localizado no [repositório GitHub do Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Criar uma função Java

Para criar uma função Java com a pilha de tempo de execução Java, use o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](/cli/azure/?view=azure-cli-latest).

As etapas a seguir mostram como criar uma função Java usando o CLI do Azure:

1. Crie um grupo de recursos, substituindo o espaço reservado **&lt;resource_group >** pelo nome do grupo de recursos.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Crie uma conta de armazenamento do Azure, substituindo os espaços reservados pelos valores apropriados.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Crie o aplicativo de função de teste, substituindo os espaços reservados pelos valores apropriados.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

As etapas a seguir explicam como preparar o servidor Jenkins:

1. Implantar um [servidor Jenkins](https://aka.ms/jenkins-on-azure) no Azure. Se você ainda não tiver uma instância do servidor Jenkins instalado, o artigo [criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md) o orientará durante o processo.

1. Entre na instância do Jenkins com SSH.

1. Na instância do Jenkins, instale o Maven usando o seguinte comando:

    ```terminal
    sudo apt install -y maven
    ```

1. Na instância do Jenkins, instale o [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) emitindo os seguintes comandos em um prompt do terminal:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. No painel do Jenkins, instale os seguintes plug-ins:

    - Plug-in Azure Functions
    - Plug-in EnvInject

1. O Jenkins precisa de uma entidade de serviço do Azure para autenticar e acessar os recursos do Azure. Consulte o [serviço implantar no Azure app](./tutorial-jenkins-deploy-web-app-azure-app-service.md) para obter instruções passo a passo.

1. Usando a entidade de serviço do Azure, adicione um tipo de credencial "entidade de serviço Microsoft Azure" em Jenkins. Consulte o tutorial [implantar no serviço Azure app](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) .

## <a name="fork-the-sample-github-repo"></a>Bifurcar o repositório GitHub de exemplo

1. [Entre no repositório do GitHub para o aplicativo de exemplo ímpar ou até mesmo](https://github.com/VSChina/odd-or-even-function.git).

1. No canto superior direito do GitHub, escolha **bifurcação**.

1. Siga os prompts para selecionar sua conta do GitHub e concluir a bifurcação.

## <a name="create-a-jenkins-pipeline"></a>Criar um pipeline do Jenkins

Nesta seção, você criará o [pipeline Jenkins](https://jenkins.io/doc/book/pipeline/).

1. No painel do Jenkins, crie um pipeline.

1. Habilite **preparar um ambiente para a execução**.

1. Adicione as seguintes variáveis de ambiente no **conteúdo de propriedades**, substituindo os espaços reservados pelos valores apropriados para o seu ambiente:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Na seção **pipeline-> definição** , selecione **script de pipeline do SCM**.

1. Insira a URL e o caminho do script do GitHub Fork ("Doc/Resources/Jenkins/JenkinsFile") para usar no [exemplo JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

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

## <a name="build-and-deploy"></a>Compilar e implantar

Agora é hora de executar o trabalho Jenkins.

1. Primeiro, obtenha a chave de autorização por meio das instruções no artigo [Azure Functions gatilhos e associações http](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) .

1. No navegador, insira a URL do aplicativo. Substitua os espaços reservados pelos valores apropriados e especifique um valor numérico para **&lt;input_number >** como entrada para a função Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Você verá resultados semelhantes à saída de exemplo a seguir (em que um número ímpar-365-foi usado como um teste):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua os recursos criados com a seguinte etapa:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Azure Functions, consulte o seguinte recurso:
> [!div class="nextstepaction"]
> [Documentação do Azure Functions](/azure/azure-functions/)
