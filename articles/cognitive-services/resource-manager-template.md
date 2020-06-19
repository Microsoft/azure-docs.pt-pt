---
title: 'Quickstart: Criar um recurso Azure Cognitive Services utilizando modelos ARM / Microsoft Docs'
description: Começa com o uso de um modelo de Gestor de Recursos Azure para implementar um recurso de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/18/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 9b2091c7acd42d4c967ec5baef82d970344f9231
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081581"
---
# <a name="create-a-cognitive-services-resource-using-an-azure-resource-manager-template"></a>Crie um recurso de Serviços Cognitivos usando um modelo de Gestor de Recursos Azure

Utilize este artigo para criar e implantar um recurso de Serviços Cognitivos, utilizando um modelo Azure Resource Manager (ARM). Este recurso multi-serviço permite-lhe:
* Aceda a vários Serviços Cognitivos Azure com uma única chave e ponto final.
* Consolidar faturação dos serviços que utiliza.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]


## <a name="prerequisites"></a>Pré-requisitos 

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é a partir dos [modelos Azure quickstart ARM](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

No modelo, está definido um recurso do Azure:
* [Microsoft.CognitiveServices/contas:](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts)cria um recurso de Serviços Cognitivos.

### <a name="deploy-the-template"></a>Implementar o modelo

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Clique no **botão Implementar para Azure.**

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Introduza os seguintes valores.
    
    |Valor  |Description  |
    |---------|---------|
    | **Subscrição** | Selecione uma subscrição do Azure. |
    | **Grupo de recursos** | **Selecione Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**. |
    | **Região** | selecione uma região.  Por exemplo, **Leste dos EUA** |
    | **Nome do serviço cognitivo** | Substitua-o por um nome único para o seu recurso. Vai precisar do nome na secção seguinte quando validar a implementação. |
    | **Localização** | Substitua-a pela região acima utilizada. |
    | **Sku** | O [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) para o seu recurso. |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Ecrã de criação de recursos.":::

3. Selecione **Rever + Criar** e, em seguida, clique em **Criar**. Depois de o recurso ter terminado a implementação com sucesso, o botão **Go para o recurso** será realçado.

    

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

> [!NOTE]
> `az deployment group`criar requer Azure CLI versão 2.6 ou mais tarde. Para visualizar o tipo de versão `az --version` . Para mais informações, consulte a [documentação.](https://docs.microsoft.com/cli/azure/deployment/group)

Execute o seguinte script utilizando a Interface da Linha de Comando Azure (CLI) [na sua máquina local,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ou num browser com o botão **Tente.a.** Introduza um nome e localização (por `centralus` exemplo) para um novo grupo de recursos, e o modelo ARM será usado para implantar um recurso de Serviços Cognitivos dentro dele. Lembre-se do nome que usa. Irá usá-lo mais tarde para validar a implementação.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---


## <a name="validate-the-deployment"></a>Validar a implementação

# <a name="portal"></a>[Portal](#tab/portal)

Quando a sua implementação terminar, poderá clicar no botão **'Ir ao botão's para** ver o seu novo recurso. Também pode encontrar o grupo de recursos através de:

1. Selecionando **grupos** de recursos a partir do menu de navegação à esquerda.
2. Selecionando o nome do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Utilizando o CLI Azure, execute o seguinte script e insira o nome do grupo de recursos que criou anteriormente. 

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos contidos no grupo.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localizar o grupo de recursos que contém o recurso a eliminar
3. Clique com o botão direito na listagem do grupo de recursos. Selecione **Eliminar grupo de recursos** e confirme.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Utilizando o CLI Azure, execute o seguinte script e insira o nome do grupo de recursos que criou anteriormente. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Passos seguintes

* [Autenticar pedidos aos Serviços Cognitivos da Azure](authentication.md)
* [O que é a Azure Cognitive Services?](Welcome.md)
* [Suporte linguístico natural](language-support.md)
* [Suporte de contentor estivador](cognitive-services-container-support.md)
