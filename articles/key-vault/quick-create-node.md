---
title: Início rápido - conjunto e obter um segredo no Azure Key Vault ao utilizar uma aplicação web Node | Documentos da Microsoft
description: Neste início rápido, definir e obter um segredo no Azure Key Vault, com uma aplicação web Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 1e234b599325da0626c83a57d86ff977b88b5577
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991279"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Início rápido: Definir e obter um segredo no Azure Key Vault ao utilizar uma aplicação web Node 

Este guia de introdução mostra como armazenar um segredo no Cofre de chaves do Azure e como recuperá-la utilizando uma aplicação web. Com o Key Vault ajuda a manter as informações seguras. Para ver o valor secreto, terá de executar este guia de introdução no Azure. O início rápido utiliza o Node.js e identidades geridas para os recursos do Azure. Saiba como:

* Criar um cofre de chaves.
* Armazene um segredo no cofre de chaves.
* Obter um segredo do cofre de chaves.
* Criar uma aplicação Web do Azure.
* Ative uma [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para a aplicação Web.
* Conceda as permissões exigidas para a aplicação ler dados do cofre de chaves.

Antes de continuar, certifique-se de que esteja familiarizado com o [conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> O Key Vault é um repositório central para armazenar segredos de forma programática. Mas para tal, primeiro as aplicações e os utilizadores têm de autenticar para o Key Vault, ou seja, apresentar um segredo. Para manter as melhores práticas de segurança, este segredo primeiro tem de ser girado periodicamente. 
>
> Com o [identidades de serviço para recursos do Azure geridas](../active-directory/managed-identities-azure-resources/overview.md), uma identidade que o Azure gere automaticamente de obtenção das aplicações que são executadas no Azure. Isto ajuda a resolver o *problema de introdução do segredo*, para que os utilizadores e as aplicações possam seguir as melhores práticas e não precisem de se preocupar sobre trocar o primeiro segredo.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 ou posterior. Este início rápido requer que execute a CLI do Azure localmente. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Selecione um nome do grupo de recursos e preencha o marcador de posição.
O exemplo seguinte cria um grupo de recursos na localização E.U.A. Leste.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que acabou de criar é utilizado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, crie um cofre de chaves ao utilizar o grupo de recursos que criou no passo anterior. Embora este artigo utiliza "ContosoKeyVault" como o nome, precisa usar um nome exclusivo. Forneça as seguintes informações:

* Nome do Cofre de chaves.
* Nome do grupo de recursos. O nome tem de ser uma cadeia de caracteres de 3 a 24 carateres e tem de conter apenas 0-9, a-z, A-Z e um hífen (-).
* Localização: **E.U.A. Leste**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Pode armazenar uma cadeia de ligação do SQL ou outras informações que precise de manter em segurança, mas mantenha disponível para a sua aplicação. Neste tutorial, a palavra-passe será chamada de **AppSecret** e irá armazenar o valor de **MySecret** no mesmo.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado **AppSecret**. Este segredo irá armazenar o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Para ver o valor contido no segredo como texto simples:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando apresenta informações do segredo, incluindo o URI. Depois de concluir estes passos, deve ter um URI para um segredo num cofre de chaves. Tome nota destas informações. Irá precisar deles noutro passo.

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório para fazer uma cópia local onde pode editar a origem. Execute o seguinte comando:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalar dependências

Execute os seguintes comandos para instalar dependências:

```
cd key-vault-node-quickstart
npm install
```

Esse projeto usa dois módulos de nó: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) e [azure keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publicar a aplicação Web no Azure

Criar uma [App Service do Azure](https://azure.microsoft.com/services/app-service/) plano. Pode armazenar várias aplicações Web nesse plano.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Em seguida, crie uma aplicação web. No exemplo seguinte, substitua `<app_name>` com um nome de aplicação globalmente exclusivo (carateres válidos são a-z, 0-9 e -). O runtime está definido como NODE|6.9. Para ver todos os runtimes suportados, execute `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Navegue para a sua aplicação web recentemente criada e deverá ver que ele está a funcionar. Substitua `<app_name>` com um nome de aplicação único.

    ```
    http://<app name>.azurewebsites.net
    ```
O comando anterior também cria uma aplicação ativada para Git que permite-lhe implementar no Azure a partir do seu repositório de Git local. O repositório de Git local está configurado com esta URL: https://<username>@< app_name >.scm.azurewebsites.net/ < app_name > projeto.

Depois de concluir o comando anterior, pode adicionar um Azure remoto para o seu repositório de Git local. Substitua `<url>` com o URL do repositório Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Ativar uma identidade gerida para a aplicação Web

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. A [descrição geral das identidades geridas para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) simplifica a resolução deste problema ao conferir aos serviços do Azure uma identidade gerida automaticamente no Microsoft Azure Active Directory (Microsoft Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

Execute o comando assign-identity para criar a identidade para esta aplicação:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Este comando é o equivalente a aceder ao portal e mudar a definição **Identidade/Sistema atribuído** para **Ativado** nas propriedades da aplicação Web.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões à sua aplicação para ler segredos do Cofre de Chaves

Tome nota da saída do comando anterior. Deverá estar no formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o seguinte comando com o nome do seu Cofre de chaves e o valor de **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Implementar a aplicação de nó para o Azure e obter o valor secreto

Execute o seguinte comando para implementar a aplicação no Azure:

```
git push azure master
```

Depois disso, ao navegar para https://<app_name>.azurewebsites.net, pode ver o valor secreto. Certifique-se de que substituiu o nome <YourKeyVaultName> com o nome do cofre.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Azure SDK para o nó](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
