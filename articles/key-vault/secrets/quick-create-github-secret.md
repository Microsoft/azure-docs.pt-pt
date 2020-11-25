---
title: Quickstart - Use segredos do Cofre chave Azure em fluxos de trabalho de ações gitHub
description: Use segredos key vault em GitHub Actions para automatizar os fluxos de trabalho de desenvolvimento de software
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920287"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Use segredos do Cofre chave em fluxos de trabalho de ações gitHub

Use segredos do Cofre chave nas suas [ações do GitHub](https://help.github.com/en/articles/about-github-actions) e guarde de forma segura senhas e outros segredos num cofre de chaves Azure. Saiba mais sobre [o Key Vault.](../general/overview.md) 

Com Key Vault e GitHub Actions, você tem os benefícios de uma ferramenta centralizada de gestão de segredos e todas as vantagens das ações do GitHub. GitHub Actions é um conjunto de funcionalidades no GitHub para automatizar os fluxos de trabalho de desenvolvimento de software. Pode implementar fluxos de trabalho no mesmo local onde armazena código e colaborar em pedidos e problemas de puxar. 


## <a name="prerequisites"></a>Pré-requisitos 
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)  
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma app Azure ligada a um repositório GitHub. Este exemplo utiliza [recipientes de implantação para o Serviço de Aplicações Azure.](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01) 
- Um cofre de chaves Azure.  Pode criar um Cofre de Chaves Azure utilizando o portal Azure CLI ou Azure PowerShell.

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem para autenticar com ações GitHub duas secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. <br /> 3. Adicione uma tarefa de função. |
|**Cofre de Chaves** | 1. Adicione a ação do cofre da chave. <br /> 2. Segredo do cofre de chaves de referência. |

## <a name="authentication"></a>Autenticação

Pode criar um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição e nome de grupo de recursos. Substitua o espaço reservado `myApp` pelo nome da sua aplicação. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para mais tarde. Só vai precisar das secções com os `clientId` `clientSecret` `subscriptionId` valores e `tenantId` valores. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

Crie segredos para as suas credenciais Azure, grupo de recursos e subscrições. 

1. No [GitHub,](https://github.com/)navegue no seu repositório.

1. Selecione **Definições > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê o nome ao `AZURE_CREDENTIALS` segredo.

1. Copie o valor de `clientId` usar mais tarde. 

### <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função 
 
Você precisa dar acesso ao principal do serviço Azure para que você possa aceder ao seu cofre `get` chave para e `list` operações. Se não fizer isto, não poderá usar o diretor de serviço. 

`keyVaultName`Substitua-o pelo nome do seu cofre-chave e `clientIdGUID` pelo valor do seu `clientId` . 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Use a ação do cofre da chave Azure

Com a ação do [cofre da chave Azure,](https://github.com/marketplace/actions/azure-key-vault-get-secrets)você pode obter um ou mais segredos de uma instância de cofre chave Azure e consumi-lo em seus fluxos de trabalho GitHub Action.

Os segredos recolhidos são definidos como saídas e também como variáveis ambientais. As variáveis são automaticamente mascaradas quando são impressas na consola ou em registos.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Para usar um cofre chave no seu fluxo de trabalho, você precisa tanto da ação do cofre chave como para fazer referência a essa ação. 

Neste exemplo, o cofre-chave é `containervault` nomeado. Dois segredos chave do cofre são adicionados ao ambiente com a ação do cofre chave - `containerPassword` e `containerUsername` . 

Os valores-chave do cofre são posteriormente referenciados na tarefa de login do estivador com o prefixo `steps.myGetSecretAction.outputs` . 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando a sua aplicação Azure, o repositório GitHub e o cofre-chave já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos para a app, o repositório GitHub e o cofre de chaves.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Key Vault](../general/overview.md)
