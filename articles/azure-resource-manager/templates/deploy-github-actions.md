---
title: Implementar modelos de gestor de recursos utilizando ações do GitHub
description: Descreve como implementar modelos de Gestor de Recursos Azure (modelos ARM) utilizando ações do GitHub.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781978"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Implemente modelos ARM utilizando ações do GitHub

[GitHub Actions](https://docs.github.com/en/actions) é um conjunto de funcionalidades no GitHub para automatizar os fluxos de trabalho de desenvolvimento de software no mesmo local onde armazena código e colabora em pedidos e problemas de puxar.

Utilize o [Modelo de Gestor de Recursos Azure](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) implementar a implementação de um modelo de Gestor de Recursos Azure (modelo ARM) para Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)
    - Um repositório GitHub para armazenar os seus modelos de Gestor de Recursos e os seus ficheiros de fluxo de trabalho. Para criar um, consulte [criar um novo repositório.](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)


## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem duas secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implementar** | 1. Implementar o modelo de Gestor de Recursos. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação


Pode criar um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

Crie um grupo de recursos se ainda não tiver um.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Substitua o espaço reservado `myApp` pelo nome da sua aplicação.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição e nome de grupo de recursos. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para mais tarde. Só vai precisar das secções com os `clientId` `clientSecret` `subscriptionId` valores e `tenantId` valores.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo acesso. O âmbito do exemplo anterior limita-se ao grupo de recursos.



## <a name="configure-the-github-secrets"></a>Configure os segredos do GitHub

Você precisa criar segredos para as suas credenciais Azure, grupo de recursos e subscrições.

1. No [GitHub,](https://github.com/)navegue no seu repositório.

1. Selecione **Definições > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê o nome ao `AZURE_CREDENTIALS` segredo.

1. Criar outro segredo chamado `AZURE_RG` . Adicione o nome do seu grupo de recursos ao campo de valor do segredo (exemplo: `myResourceGroup` ).

1. Crie um segredo adicional chamado `AZURE_SUBSCRIPTION` . Adicione o seu ID de subscrição ao campo de valor do segredo (exemplo: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Adicionar modelo de gestor de recursos

Adicione um modelo de Gestor de Recursos ao seu repositório GitHub. Este modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pode colocar o ficheiro em qualquer lugar do repositório. A amostra de fluxo de trabalho na secção seguinte pressupõe que o ficheiro do modelo é nomeado **azuredeploy.js,** e é armazenado na raiz do seu repositório.

## <a name="create-workflow"></a>Criar um fluxo de trabalho

O ficheiro de fluxo de trabalho deve ser armazenado na pasta **.github/workflows** na raiz do seu repositório. A extensão do ficheiro workflow pode ser **.yml** ou **.yaml**.

1. A partir do seu repositório GitHub, selecione **Actions** from the top menu.
1. Selecione **Novo fluxo de trabalho**.
1. Selecione **configurar um fluxo de trabalho por si mesmo**.
1. Mude o nome do ficheiro workflow se preferir um nome diferente do **main.yml**. Por exemplo: **implementarStorageAccount.yml**.
1. Substitua os conteúdos do ficheiro yml pelo seguinte:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Pode especificar um ficheiro de parâmetros de formato JSON na ação ARM Deploy (exemplo: `.azuredeploy.parameters.json` ).

    A primeira secção do ficheiro de fluxo de trabalho inclui:

    - **nome**: O nome do fluxo de trabalho.
    - **em**: O nome dos eventos do GitHub que desencadeia o fluxo de trabalho. O fluxo de trabalho é desencadeado quando há um evento de impulso no ramo principal, que modifica pelo menos um dos dois ficheiros especificados. Os dois ficheiros são o ficheiro de fluxo de trabalho e o ficheiro de modelo.

1. Selecione **Start commit** (Iniciar consolidação).
1. **Selecione Comprometa-se diretamente com o ramo principal**.
1. **Selecione Cometer novo ficheiro** (ou cometer **alterações).**

Uma vez que o fluxo de trabalho está configurado para ser acionado pelo ficheiro de fluxo de trabalho ou pelo ficheiro do modelo a ser atualizado, o fluxo de trabalho começa logo após cometer as alterações.

## <a name="check-workflow-status"></a>Verificar o estado do fluxo de trabalho

1. Selecione o **separador Ações.** Você verá um **Create deployStorageAccount.yml** workflow listado. Leva 1-2 minutos para executar o fluxo de trabalho.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione **executar a implementação** DO ARM a partir do menu para verificar a implementação.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando o seu grupo de recursos e repositório já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie o seu primeiro modelo ARM](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Aprenda módulo: Automatize a implementação de modelos ARM utilizando ações do GitHub](/learn/modules/deploy-templates-command-line-github-actions/)
