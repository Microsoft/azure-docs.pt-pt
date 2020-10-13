---
title: Tutorial - Identidade gerida para invocar funções Azure a partir de uma aplicação Azure Spring Cloud
description: Utilizar uma identidade gerida para invocar Funções do Azure a partir de uma aplicação do Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 44268bf1b7805ece8de4a3499a7d53fc851af142
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91664992"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Tutorial: Use uma identidade gerida para invocar funções Azure a partir de uma aplicação Azure Spring Cloud

Este artigo mostra-lhe como criar uma identidade gerida para uma aplicação Azure Spring Cloud e usá-la para invocar Funções desencadeadas http.

Tanto as Funções Azure como os Serviços de Aplicações construíram suporte para a autenticação do Azure Ative Directory (Azure AD). Aproveitando esta capacidade de autenticação incorporada juntamente com identidades geridas para a Azure Spring Cloud, podemos invocar serviços RESTful usando a semântica moderna da OAuth. Este método não requer o armazenamento de segredos em código e fornece mais controlos granulares para controlar o acesso a recursos externos. 


## <a name="prerequisites"></a>Pré-requisitos

* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Instalar Maven 3.0 ou superior](https://maven.apache.org/download.cgi)
* [Instale a versão 3.0.2009 ou superior das Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos para conter tanto a app Function como a Cloud de primavera utilizando o grupo de comando [az criar:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Criar uma Aplicação de Funções
Para criar uma aplicação Function tem primeiro de criar uma conta de armazenamento de suporte, utilize a conta de armazenamento command [az create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> Cada aplicação de função e conta de armazenamento deve ter um nome único. Substitua <o nome do seu> de aplicação por funções pelo nome da sua aplicação Function e <o nome da sua contagem de armazenamento> pelo nome da sua Conta de Armazenamento nos seguintes exemplos.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Uma vez criada a Conta de Armazenamento, pode criar a aplicação Função.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Tome nota dos **hostes devolvidosNames,** que estarão no formato "https://<o seu nome de aplicação>.azurewebsites.net". Será usado num passo seguinte.


## <a name="enable-azure-active-directory-authentication"></a>Ativar a autenticação ativa do Diretório Azure

Aceda à aplicação Função recém-criada a partir do [portal Azure](https://portal.azure.com) e selecione "Autenticação/Autorização" a partir do menu de definições. Ativar a Autenticação do Serviço de Aplicações e definir a "Ação a tomar quando o pedido não for autenticado" para "Iniciar sessão com o Diretório Ativo Azure". Esta definição garantirá que todos os pedidos não autenticados sejam negados (401 resposta).

![Definições de autenticação que mostram o Azure Ative Directory como fornecedor predefinido](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Em Fornecedores de Autenticação, selecione Azure Ative Directory para configurar o registo de pedidos. Selecionar o Modo de Gestão Expresso criará automaticamente um registo de inscrição no seu inquilino AZure AD com a configuração correta.

![Azure Ative Directory provider definido para o Modo de Gestão Expresso](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Assim que guardar as definições, a aplicação de função reiniciará e todos os pedidos subsequentes serão solicitados para iniciar sessão via Azure AD. Pode testar que os pedidos não autenticados estão agora a ser rejeitados navegando para o URL raiz de aplicações de função (devolvido na saída **hostNames** no degrau acima). Você deve ser redirecionado para o ecrã de login Azure AD das suas organizações.


## <a name="create-an-http-triggered-function"></a>Criar uma função de desencadeamento em http

Num diretório local vazio, crie uma nova aplicação de função e adicione uma função de http desencadeada.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Por predefinição, as funções utilizam a autenticação baseada em chaves para proteger os pontos finais de Http. Uma vez que vamos permitir a autenticação AZure AD para garantir o acesso às Funções, queremos [definir o nível de auth de função para anónimo](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

A aplicação pode agora ser publicada na instância da [aplicação Function](#create-a-function-app) criada no passo anterior.

```console
func azure functionapp publish <your-functionapp-name>
```

A saída do comando de publicação deve listar o URL à sua função recém-criada.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Criar serviço e app Azure Spring Cloud
Depois de instalar a extensão de nuvem de mola, crie uma instância Azure Spring Cloud com o comando Azure CLI `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

O exemplo a seguir cria uma aplicação com o nome `msiapp` de uma identidade gerida atribuída ao sistema, conforme solicitado pelo `--assign-identity` parâmetro.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --is-public true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Construa a aplicação De Arranque de primavera de amostra para invocar a Função

Esta amostra invocará a função desencadeada http, solicitando primeiro um token de acesso a partir do ponto final do [MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) e utilizando esse token para autenticar o pedido http da Função.

1. Clone o projeto da amostra. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Especifique a sua função URI e o nome do gatilho nas propriedades da sua aplicação. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Para utilizar a identidade gerida para aplicações Azure Spring Cloud, adicione propriedades com os seguintes conteúdos para *src/main/resources/application.properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Embale o seu aplicativo de amostra. 

    ```console
    mvn clean package
    ```

4. Agora implemente a aplicação para Azure com o comando Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Aceda ao ponto final público ou ao ponto final de teste para testar a sua aplicação. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Verá a seguinte mensagem devolvida no corpo de resposta.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Pode tentar passar valores diferentes para a função alterando o parâmetro do caminho.

## <a name="next-steps"></a>Passos seguintes

* [Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [Saiba mais sobre identidades geridas para recursos Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Configure um pedido de cliente daemon para chamadas de serviço-a-serviço](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad#configure-a-daemon-client-application-for-service-to-service-calls)
