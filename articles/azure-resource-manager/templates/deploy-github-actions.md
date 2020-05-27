---
title: Implementar modelos de Gestor de Recursos usando ações gitHub
description: Descreve como implementar modelos de Gestor de Recursos Azure utilizando as ações do GitHub.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: f2e0d73c838d16c161605972b87d6f07ef8869b9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869188"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Implementar modelos de Gestor de Recursos Azure utilizando ações gitHub

[As Ações GitHub](https://help.github.com/en/actions) permitem-lhe criar fluxos de trabalho de ciclo de vida de desenvolvimento de software personalizados diretamente no seu repositório GitHub onde os seus modelos de Gestor de Recursos Azure (ARM) estão armazenados. Um [fluxo de trabalho](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) é definido por um ficheiro YAML. Os fluxos de trabalho têm um ou mais postos de trabalho com cada trabalho contendo um conjunto de passos que executam tarefas individuais. Os passos podem executar comandos ou usar uma ação. Você pode criar suas próprias ações ou usar ações partilhadas pela [comunidade GitHub](https://github.com/marketplace?type=actions) e personalizá-las conforme necessário. Este artigo mostra como usar a [Ação ClI Azure](https://github.com/marketplace/actions/azure-cli-action) para implementar modelos de Gestor de Recursos.

A Azure CLI Action tem duas ações dependentes:

- **[Checkout](https://github.com/marketplace/actions/checkout)**: Verifique o seu repositório para que o fluxo de trabalho possa aceder a qualquer modelo especificado do Gestor de Recursos.
- **[Azure Login](https://github.com/marketplace/actions/azure-login)**: Faça login com as suas credenciais Azure

Um fluxo de trabalho básico para a implementação de um modelo de Gestor de Recursos pode ter três passos:

1. Confira um ficheiro de modelo.
2. Inicie sessão no Azure.
3. Implementar um modelo de Gestor de Recursos

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um repositório GitHub para armazenar os seus modelos de Gestor de Recursos e os seus ficheiros de fluxo de trabalho. Para criar um, consulte [Criar um novo repositório.](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)

## <a name="configure-deployment-credentials"></a>Configurar as credenciais de implementação

A ação de login Azure utiliza um diretor de serviço para autenticar contra o Azure. O diretor de um fluxo de trabalho CI/CD normalmente precisa do direito de contribuinte incorporado para implantar recursos Azure.

O seguinte guião Azure CLI mostra como gerar um Diretor de Serviço Azure com permissões contributivas num grupo de recursos Azure. Este grupo de recursos é onde o fluxo de trabalho implanta os recursos definidos no seu modelo de Gestor de Recursos.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalize o valor de **$projectName** e **$location** no guião. O nome do grupo de recursos é o nome do projeto com **rg** anexado. Precisa especificar o nome do grupo de recursos no seu fluxo de trabalho.

O script produz um objeto JSON semelhante a este:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Copie a saída JSON e guarde-a como um segredo GitHub dentro do seu repositório GitHub. Consulte o [Pré-Requisito](#prerequisites) se ainda não tem um repositório.

1. A partir do seu repositório GitHub, selecione o separador **Definições.**
1. Selecione **Segredos** do menu esquerdo.
1. Introduza os seguintes valores:

    - **Nome**: AZURE_CREDENTIALS
    - **Valor**: (Colar a saída JSON)
1. Selecione **Adicionar segredo**.

Tens de especificar o nome secreto no fluxo de trabalho.

## <a name="add-resource-manager-template"></a>Adicionar modelo de gestor de recursos

Adicione um modelo de Gestor de Recursos ao repositório GitHub. Se não tiver um, pode usar o seguinte modelo. O modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pode colocar o ficheiro em qualquer lugar do repositório. A amostra de fluxo de trabalho na secção seguinte pressupõe que o ficheiro do modelo é denominado **azuredeploy.json**, e é armazenado numa pasta chamada **modelos** na raiz do seu repositório.

## <a name="create-workflow"></a>Criar fluxo de trabalho

O ficheiro workflow deve ser armazenado na pasta **.github/workflow** na raiz do seu repositório. A extensão do ficheiro de fluxo de trabalho pode ser **.yml** ou **.yaml**.

Pode criar um ficheiro de fluxo de trabalho e, em seguida, empurrar/carregar o ficheiro para o repositório, ou utilizar o seguinte procedimento:

1. A partir do seu repositório GitHub, selecione **Ações** do menu superior.
1. Selecione **Novo fluxo de trabalho**.
1. **Selecione configurar um fluxo**de trabalho por si mesmo .
1. Mude o nome do ficheiro workflow se preferir um nome diferente do **principal.yml**. Por exemplo: **implementarStorageAccount.yml**.
1. Substitua o conteúdo do ficheiro yml pelo seguinte:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    O ficheiro workflow tem três secções:

    - **nome**: O nome do fluxo de trabalho.
    - **sobre**: O nome dos eventos GitHub que desencadeia o fluxo de trabalho. O fluxo de trabalho é acionado quando há um evento de pressão no ramo principal, que modifica pelo menos um dos dois ficheiros especificados. Os dois ficheiros são o ficheiro workflow e o ficheiro do modelo.

        > [!IMPORTANT]
        > Verifique se os dois ficheiros e os seus caminhos coincidem com os seus.
    - **empregos**: Uma corrida ao fluxo de trabalho é constituída por um ou mais postos de trabalho. Há apenas um trabalho chamado **modelo de conta de armazenamento de armazenamento.**  Este trabalho tem três passos:

        - **Código fonte de check-out**.
        - **Login para Azure**.

            > [!IMPORTANT]
            > Verifique se o nome secreto corresponde ao que guardou para o seu repositório. Consulte as credenciais de [implementação do Configure](#configure-deployment-credentials).
        - **Implementar o modelo ARM**. Substitua o valor dos **recursosGroupName**.  Se usou o script Azure CLI nas credenciais de [implementação da Configuração,](#configure-deployment-credentials)o nome do grupo de recursos gerados é o nome do projeto com **rg** anexado. Verifique o valor do **modeloLocalização**.

1. Selecione **Começar a comprometer.**
1. Selecione **Comprometa-se diretamente com o ramo principal**.
1. Selecione **Cometer novo ficheiro** (ou cometer **alterações**).

Uma vez que o fluxo de trabalho está configurado para ser acionado pelo ficheiro de fluxo de trabalho ou pelo ficheiro do modelo que está a ser atualizado, o fluxo de trabalho começa logo após o seu erro.

## <a name="check-workflow-status"></a>Verifique o estado do fluxo de trabalho

1. Selecione o separador **Ações.** Verá um fluxo de trabalho **Create deployStorageAccount.yml** listado. Leva 1-2 minutos para executar o fluxo de trabalho.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione modelo **de conta de armazenamento de implementação** (nome de trabalho) do menu esquerdo. O nome do trabalho é definido no fluxo de trabalho.
1. Selecione **'Implementar modelo ARM'** (nome de passo) para o expandir. Pode ver a resposta da API rest.

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implante o seu primeiro modelo ARM](template-tutorial-create-first-template.md).
