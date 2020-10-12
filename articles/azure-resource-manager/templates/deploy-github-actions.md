---
title: Implementar modelos de gestor de recursos utilizando ações do GitHub
description: Descreve como implementar modelos de Gestor de Recursos Azure utilizando ações do GitHub.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85854745"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Implemente modelos de gestor de recursos Azure usando ações do GitHub

[As Ações GitHub](https://help.github.com/en/actions) permitem-lhe criar fluxos de trabalho personalizados para desenvolvimento de software no seu repositório GitHub onde os seus modelos de Gestor de Recursos Azure (ARM) estão armazenados. Um [fluxo de trabalho](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) é definido por um ficheiro YAML. Os fluxos de trabalho têm um ou mais empregos em cada trabalho contendo um conjunto de passos que executam tarefas individuais. Os passos podem executar comandos ou usar uma ação. Pode criar as suas próprias ações ou usar ações partilhadas pela [comunidade GitHub](https://github.com/marketplace?type=actions) e personalizá-las conforme necessário. Este artigo mostra como usar a [Azure CLI Action](https://github.com/marketplace/actions/azure-cli-action) para implementar modelos de Gestor de Recursos.

A Azure CLI Action tem duas ações dependentes:

- **[Check-out](https://github.com/marketplace/actions/checkout)**: Verifique o seu repositório para que o fluxo de trabalho possa aceder a qualquer modelo especificado de Gestor de Recursos.
- **[Azure Login](https://github.com/marketplace/actions/azure-login)**: Faça login com as suas credenciais Azure

Um fluxo de trabalho básico para a implementação de um modelo de Gestor de Recursos pode ter três etapas:

1. Verifique um ficheiro de modelo.
2. Inicie sessão no Azure.
3. Implemente um modelo de gestor de recursos

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um repositório GitHub para armazenar os seus modelos de Gestor de Recursos e os seus ficheiros de fluxo de trabalho. Para criar um, consulte [criar um novo repositório.](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)

## <a name="configure-deployment-credentials"></a>Configurar as credenciais de implementação

A ação de login da Azure utiliza um principal de serviço para autenticar contra o Azure. O principal de um fluxo de trabalho CI/CD normalmente precisa do direito do contribuinte incorporado para implantar recursos Azure.

O seguinte script Azure CLI mostra como gerar um Diretor de Serviço Azure com permissões de contribuinte num grupo de recursos Azure. Este grupo de recursos é onde o fluxo de trabalho implementa os recursos definidos no seu modelo de Gestor de Recursos.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalize o valor de **$projectName** e **$location** no script. O nome do grupo de recursos é o nome do projeto com **rg** anexado. Tem de especificar o nome do grupo de recursos no seu fluxo de trabalho.

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

Copie a saída JSON e guarde-a como um segredo do GitHub dentro do seu repositório GitHub. Consulte [o Pré-requisito](#prerequisites) se ainda não tem um repositório.

1. A partir do seu repositório GitHub, selecione o **separador Definições.**
1. Selecione **Segredos** do menu esquerdo.
1. Introduza os seguintes valores:

    - **Nome**: AZURE_CREDENTIALS
    - **Valor**: (Colar a saída JSON)
1. **Selecione Adicionar segredo**.

Precisa especificar o nome secreto no fluxo de trabalho.

## <a name="add-resource-manager-template"></a>Adicionar modelo de gestor de recursos

Adicione um modelo de Gestor de Recursos ao repositório GitHub. Se não tiver um, pode usar o seguinte modelo. O modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pode colocar o ficheiro em qualquer lugar do repositório. A amostra de fluxo de trabalho na secção seguinte pressupõe que o ficheiro do modelo é nomeado **azuredeploy.jsem**, e é armazenado numa pasta chamada **modelos** na raiz do seu repositório.

## <a name="create-workflow"></a>Criar fluxo de trabalho

O ficheiro de fluxo de trabalho deve ser armazenado na pasta **.github/workflows** na raiz do seu repositório. A extensão do ficheiro workflow pode ser **.yml** ou **.yaml**.

Pode criar um ficheiro de fluxo de trabalho e, em seguida, empurrar/carregar o ficheiro para o repositório, ou utilizar o seguinte procedimento:

1. A partir do seu repositório GitHub, selecione **Actions** from the top menu.
1. Selecione **Novo fluxo de trabalho**.
1. Selecione **configurar um fluxo de trabalho por si mesmo**.
1. Mude o nome do ficheiro workflow se preferir um nome diferente do **main.yml**. Por exemplo: **implementarStorageAccount.yml**.
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
    - **em**: O nome dos eventos do GitHub que desencadeia o fluxo de trabalho. O fluxo de trabalho é desencadeado quando há um evento de impulso no ramo principal, que modifica pelo menos um dos dois ficheiros especificados. Os dois ficheiros são o ficheiro de fluxo de trabalho e o ficheiro de modelo.

        > [!IMPORTANT]
        > Verifique se os dois ficheiros e os seus caminhos coincidem com os seus.
    - **empregos**: Uma corrida ao fluxo de trabalho é constituída por um ou mais postos de trabalho. Há apenas um trabalho chamado **modelo de conta de armazenamento de implementação.**  Este trabalho tem três passos:

        - **Código fonte de saída**.
        - **Faça login no Azure**.

            > [!IMPORTANT]
            > Verifique se o nome secreto corresponde ao que guardou no seu repositório. Consulte [as credenciais de implantação Configure](#configure-deployment-credentials).
        - **Implementar o modelo ARM**. Substitua o valor do **recursoGroupName**.  Se usou o script Azure CLI em [credenciais de implantação Configure,](#configure-deployment-credentials)o nome do grupo de recursos gerados é o nome do projeto com **rg** anexado. Verifique o valor do **modeloLocation**.

1. Selecione **Iniciar o compromisso**.
1. **Selecione Comprometa-se diretamente com o ramo principal**.
1. **Selecione Cometer novo ficheiro** (ou cometer **alterações).**

Uma vez que o fluxo de trabalho está configurado para ser acionado pelo ficheiro de fluxo de trabalho ou pelo ficheiro do modelo a ser atualizado, o fluxo de trabalho começa logo após cometer as alterações.

## <a name="check-workflow-status"></a>Verificar o estado do fluxo de trabalho

1. Selecione o **separador Ações.** Você verá um **Create deployStorageAccount.yml** workflow listado. Leva 1-2 minutos para executar o fluxo de trabalho.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione **o modelo de conta de armazenamento de implementação** (nome de trabalho) a partir do menu esquerdo. O nome do trabalho é definido no fluxo de trabalho.
1. Selecione **implementar o modelo ARM** (nome de passo) para expandi-lo. Pode ver a resposta da API REST.

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implemente o seu primeiro modelo ARM](template-tutorial-create-first-template.md).
