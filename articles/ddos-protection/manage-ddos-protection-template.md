---
title: Crie e ative um plano de proteção Azure DDoS utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
description: Aprenda a criar e a capacitar um plano de proteção Azure DDoS utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99092505"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Quickstart: Criar uma Norma de Proteção Azure DDoS usando o modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar um plano de proteção de negação de serviço distribuído (DDoS) e rede virtual (VNet), e depois permite o plano de proteção para o VNet. Um plano Azure DDoS Protection Standard define um conjunto de redes virtuais que têm proteção DDoS ativada através de subscrições. Pode configurar um plano de proteção DDoS para a sua organização e ligar redes virtuais de várias subscrições ao mesmo plano.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

O modelo define dois recursos:

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Implementar o modelo

Neste exemplo, o modelo cria um novo grupo de recursos, um plano de proteção DDoS e um VNet.

1. Para iniciar súb pouco e abrir o modelo, selecione o botão **Implementar para Azul.**

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Insira os valores para criar um novo grupo de recursos, plano de proteção DDoS e nome VNet.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Modelo de arranque rápido DDoS.":::

    - **Assinatura**: Nome da subscrição Azure onde os recursos serão utilizados.
    - **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo grupo de recursos.
    - **Região**: Região onde o grupo de recursos é implantado, como os EUA Orientais.
    - **Nome do Plano de Proteção DDOS**: O nome do novo plano de proteção do DDoS.
    - **Nome da rede virtual**: Cria um nome para o novo VNet.
    - **Localização**: Função que utiliza a mesma região que o grupo de recursos para a implantação de recursos.
    - **Prefixo do endereço Vnet**: Utilize o valor predefinido ou introduza o seu endereço VNet.
    - **Prefixo de sub-rede**: Utilize o valor predefinido ou introduza a sua sub-rede VNet.
    - **Plano de Proteção Ddos Ativado**: O padrão é `true` para ativar o plano de proteção DDoS.

1. Selecione **Rever + criar**.
1. Verifique se a validação do modelo passou e selecione **Criar** para iniciar a implementação.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Para copiar o comando Azure CLI ou Azure PowerShell, selecione o botão **Copiar.** O botão **Try it** abre a Azure Cloud Shell para executar o comando.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

A produção mostra os novos recursos.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, pode apagar os recursos. O comando elimina o grupo de recursos e todos os recursos que contém.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Passos seguintes

Para aprender a visualizar e configurar a telemetria para o seu plano de proteção DDoS, continue para os tutoriais.

> [!div class="nextstepaction"]
> [Ver e configurar telemetria de proteção contra DDoS](telemetry.md)
