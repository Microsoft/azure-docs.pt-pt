---
title: 'Quickstart: Criar uma Firewall Azure com Zonas de Disponibilidade - Modelo de Gestor de Recursos'
description: Implementar firewall azure usando um modelo. A rede virtual tem um VNet com três subnets. Duas máquinas virtuais do Windows Server estão implantadas; uma caixa de salto e um servidor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 9b9b7926caa717c1a02988ac7a927bd9bd39d52a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683701"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Quickstart: Implementar firewall azure com zonas de disponibilidade - modelo de gestor de recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para implementar uma Firewall Azure em três Zonas de Disponibilidade. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

O modelo cria um ambiente de rede de teste com uma firewall. A rede tem uma rede virtual (VNet) com três subredes: *AzureFirewallSubnet,* *ServersSubnet*e *JumpboxSubnet*. A sub-rede *ServersSubnet* e *JumpboxSubnet* têm uma única máquina virtual do Windows Server de dois núcleos.

A firewall está na subnet *AzureFirewallSubnet,* e tem uma coleção de regras de aplicação com uma única regra que permite o acesso a `www.microsoft.com` .

Uma rede de pontos de rota definida pelo utilizador a partir da subnet *ServersSubnet* através da firewall, onde são aplicadas as regras de firewall.

Para mais informações sobre o Firewall Azure, consulte [O Conjunto e configure a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall-with-availability-zones"></a>Criar uma Firewall Azure com Zonas de Disponibilidade

Este modelo cria uma Firewall Azure com Zonas de Disponibilidade, juntamente com os recursos necessários para suportar o Firewall Azure.

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

Vários recursos Azure são definidos no modelo:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>Implementar o modelo

Desloque o modelo de Gestor de Recursos para o Azure:

1. Selecione **Implementar para Azure** para iniciar sessão no Azure e abrir o modelo. O modelo cria uma Firewall Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. No portal, na **Create a sandbox configuração de Azure Firewall com página Zones,** escreva ou selecione os seguintes valores:
   - **Grupo de recursos**: Selecione **Criar novo,** digite um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da rede virtual**: Digite um nome para o novo VNet. 
   - **Nome de utilizador do Administrador**: Digite um nome de utilizador para a conta de utilizador do administrador.
   - **Palavra-passe do administrador**: Digite uma palavra-passe do administrador. 

3. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A implantação pode demorar 10 minutos ou mais para ser concluída.

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Explore os recursos que foram criados com a firewall.

Para saber mais sobre a sintaxe jSON e propriedades para uma firewall num modelo, consulte [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, pode remover o grupo de recursos, firewall e todos os recursos relacionados executando o `Remove-AzResourceGroup` comando PowerShell. Para remover um grupo de recursos chamado *MyResourceGroup,* executar: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Não remova o grupo de recursos e a firewall se pretender continuar no tutorial de monitorização de firewall. 

## <a name="next-steps"></a>Próximos passos

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](tutorial-diagnostics.md)