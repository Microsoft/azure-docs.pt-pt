---
title: Crie um recurso Azure Cognitive Services utilizando modelos ARM | Microsoft Docs
description: Crie um recurso de Serviço Cognitivo Azure com modelo ARM.
keywords: serviços cognitivos, soluções cognitivas, inteligência cognitiva, inteligência artificial cognitiva
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 161c5779926acad8814ec057f24e36f371738483
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864368"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Quickstart: Criar um recurso de Serviços Cognitivos usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar Serviços Cognitivos.

Os Azure Cognitive Services são serviços de base na nuvem com APIs rest, e SDKs da biblioteca de clientes disponíveis para ajudar os desenvolvedores a construir inteligência cognitiva em aplicações sem ter inteligência artificial direta (IA) ou habilidades ou conhecimentos de ciência de dados. O Azure Cognitive Services permite que os desenvolvedores adicionem facilmente funcionalidades cognitivas nas suas aplicações com soluções cognitivas que podem ver, ouvir, falar, entender e até começar a raciocinar.

Crie um recurso utilizando um modelo de Gestor de Recursos Azure (modelo ARM). Este recurso multi-serviço permite-lhe:

* Aceda a vários Serviços Cognitivos Azure com uma única chave e ponto final.
* Consolidar faturação dos serviços que utiliza.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Desloque o seu serviço cognitivo para Azure](../media/template-deployments/deploy-to-azure.svg "Desloque o seu serviço cognitivo para Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

No modelo, está definido um recurso do Azure:
* [Microsoft.CognitiveServices/contas:](/azure/templates/microsoft.cognitiveservices/accounts)cria um recurso de Serviços Cognitivos.

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Clique no **botão Implementar para Azure.**

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Introduza os seguintes valores.

    |Valor  |Descrição  |
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
> `az deployment group` criar requer Azure CLI versão 2.6 ou mais tarde. Para visualizar o tipo de versão `az --version` . Para obter mais informações, consulte a [documentação](/cli/azure/deployment/group).

Execute o seguinte script utilizando a Interface da Linha de Comando Azure (CLI) [na sua máquina local,](/cli/azure/install-azure-cli)ou num browser com o botão **Tente.a.** Introduza um nome e localização (por `centralus` exemplo) para um novo grupo de recursos, e o modelo ARM será usado para implantar um recurso de Serviços Cognitivos dentro dele. Lembre-se do nome que usa. Irá usá-lo mais tarde para validar a implementação.


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

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

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


## <a name="clean-up-resources"></a>Limpar os recursos

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

## <a name="see-also"></a>Ver também

* Consulte **[pedidos autenticados aos Serviços Cognitivos da Azure](authentication.md)** sobre como trabalhar de forma segura com os Serviços Cognitivos.
* Veja **[o que são os Serviços Cognitivos Azure para](./what-are-cognitive-services.md)** obter uma lista de diferentes categorias dentro dos Serviços Cognitivos.
* Consulte o **[suporte linguístico natural](language-support.md)** para ver a lista de línguas naturais que os Serviços Cognitivos suportam.
* Consulte **[o Uso de Serviços Cognitivos como recipientes](cognitive-services-container-support.md)** para entender como utilizar os Serviços Cognitivos no pré-pré-m.
* Consulte **[o Plano e gere os custos dos Serviços Cognitivos](plan-manage-costs.md)** para estimar o custo da utilização dos Serviços Cognitivos.
