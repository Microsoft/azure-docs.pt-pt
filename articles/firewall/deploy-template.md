---
title: 'Quickstart: Criar uma Firewall Azure com Zonas de Disponibilidade - Modelo de Gestor de Recursos'
description: Neste arranque rápido, você implanta Azure Firewall usando um modelo. A rede virtual tem um VNet com três sub-redes. Duas máquinas virtuais do Windows Server estão implantadas; uma caixa de salto e um servidor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 478f3454a728871040cdbbf9f817394cffe6b82f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660258"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>Quickstart: Implementar firewall Azure com Zonas de Disponibilidade - modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para implementar uma Firewall Azure em três Zonas de Disponibilidade.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

O modelo cria um ambiente de rede de teste com uma firewall. A rede tem uma rede virtual (VNet) com três sub-redes: *AzureFirewallSubnet,* *ServersSubnet* e *JumpboxSubnet*. A *sub-rede ServersSubnet* e *JumpboxSubnet* tem uma única máquina virtual do Windows Server de dois núcleos.

A firewall está na sub-rede *AzureFirewallSubnet,* e tem uma coleção de regras de aplicação com uma única regra que permite o acesso a `www.microsoft.com` .

Uma rota definida pelo utilizador aponta o tráfego de rede a partir da sub-rede *ServersSubnet* através da firewall, onde as regras de firewall são aplicadas.

Para obter mais informações sobre o Azure Firewall, consulte [implementar e configurar a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria uma Firewall Azure com Zonas de Disponibilidade, juntamente com os recursos necessários para suportar a Firewall Azure.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Storage/storageAcontas**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria uma Firewall Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. No portal, na configuração de **uma caixa de areia de Azure Firewall com página de Zonas,** escreva ou selecione os seguintes valores:
   - **Grupo de recursos**: Selecione **Criar novo,** digitar um nome para o grupo de recursos e selecionar **OK**. 
   - **Nome da rede virtual**: Digite um nome para o novo VNet.
   - **Nome de utilizador Admin**: Digite um nome de utilizador para a conta de utilizador do administrador.
   - **Admin Password**: Digite uma senha de administrador.

3. Leia os termos e condições e, em seguida, **selecione Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Explore os recursos que foram criados com a firewall.

Para saber mais sobre a sintaxe JSON e propriedades para uma firewall num modelo, consulte [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar, pode remover o grupo de recursos, firewall e todos os recursos relacionados executando o `Remove-AzResourceGroup` comando PowerShell. Para remover um grupo de recursos chamado *MyResourceGroup,* corra:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Não remova o grupo de recursos e firewall se pretender continuar para o tutorial de monitorização da firewall. 

## <a name="next-steps"></a>Próximos passos

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)