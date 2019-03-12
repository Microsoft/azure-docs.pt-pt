---
title: Implementar as funções do Azure com o plug-in das funções do Azure do Jenkins
description: Saiba como implementar as funções do Azure com o plug-in das funções do Azure do Jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, java, as funções do azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 93504de6384be530ba037f662f7b043729aa3f99
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536926"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Implementar as funções do Azure com o plug-in das funções do Azure do Jenkins

[As funções do Azure](/azure/azure-functions/) é um serviço de computação sem servidor. Utilizar as funções do Azure, pode executar código a pedido sem aprovisionar ou gerir infraestrutura. Este tutorial mostra como implementar uma função de Java para as funções do Azure com o plug-in de funções do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Servidor Jenkins**: Se não tiver instalado um servidor do Jenkins, veja o artigo [criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md).

 > [!TIP]
 > O código-fonte usado para este tutorial está localizado na [repositório do GitHub de China do Visual Studio](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Criar uma função de Java

Para criar uma função de Java com a pilha de tempo de execução Java, utilize o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](/cli/azure/?view=azure-cli-latest).

Os passos seguintes mostram como criar uma função de Java com a CLI do Azure:

1. Criar um grupo de recursos, substituindo os  **&lt;resource_group >** espaço reservado com o nome do grupo de recursos.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Crie uma conta de armazenamento do Azure, substituindo os marcadores de posição pelos valores adequados.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Crie a aplicação de funções de teste, substituindo os marcadores de posição pelos valores adequados.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Atualizar para versão 2.x do runtime, substituindo os marcadores de posição pelos valores adequados.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

Os passos seguintes explicam como preparar o servidor Jenkins:

1. Implementar um [servidor Jenkins](https://aka.ms/jenkins-on-azure) no Azure. Se ainda não tiver uma instância do servidor Jenkins instalado, o artigo [criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md) orienta-o ao longo do processo.

1. Inicie sessão para a instância do Jenkins com SSH.

1. Na instância do Jenkins, instale o maven com o seguinte comando:

    ```terminal
    sudo apt install -y maven
    ```

1. Na instância do Jenkins, instalar o [ferramentas de núcleo de funções do Azure](/azure/azure-functions/functions-run-local) emitindo os seguintes comandos numa linha de comandos de terminal:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. No dashboard do Jenkins, instale os plug-ins seguintes:

    - Plug-in de funções do Azure
    - Plug-in do EnvInject

1. Jenkins tem um principal de serviço do Azure para autenticar e aceder aos recursos do Azure. Consulte a [implementar no serviço de aplicações do Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md) para obter instruções passo a passo.

1. Com o principal de serviço do Azure, adicione um tipo de credencial "Microsoft Azure Service Principal" no Jenkins. Consulte a [implementar no serviço de aplicações do Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) tutorial.

## <a name="fork-the-sample-github-repo"></a>Bifurcar o repositório do GitHub de exemplo

1. [Inicie sessão para o repositório do GitHub para a aplicação ímpar ou até mesmo exemplo](https://github.com/VSChina/odd-or-even-function.git).

1. No canto superior-direito no GitHub, escolha **Fork**.

1. Siga as instruções para selecionar a sua conta do GitHub e concluir a bifurcação.

## <a name="create-a-jenkins-pipeline"></a>Criar um Pipeline do Jenkins

Nesta secção, vai criar a [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/).

1. No dashboard do Jenkins, crie um Pipeline.

1. Ativar **preparar um ambiente para a execução**.

1. Adicione as seguintes variáveis de ambiente no **propriedades conteúdo**, substituindo os marcadores de posição pelos valores adequados para o seu ambiente:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Na **Pipeline -> definição** secção, selecione **Pipeline de script do SCM**.

1. Introduzir caminho URL e o script do seu fork do GitHub ("doc/recursos/jenkins/JenkinsFile") para utilizar o [JenkinsFile exemplo](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

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

## <a name="build-and-deploy"></a>Criar e implementar

Agora está na altura de executar a tarefa do Jenkins.

1. Em primeiro lugar, obter a chave de autorização por meio das instruções a [HTTP de funções do Azure acionadores e enlaces](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) artigo.

1. No seu browser, introduza o URL da aplicação. Substitua os marcadores de posição pelos valores adequados e especifique um valor numérico para  **&lt;input_number >** como entrada para a função de Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Verá os resultados semelhantes à saída de exemplo seguinte (em que foi utilizado um número ímpar - 365 – como um teste):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine os recursos que criou com o seguinte passo:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as funções do Azure, veja o seguinte recurso:
> [!div class="nextstepaction"]
> [Documentação de funções do Azure](/azure/azure-functions/)
