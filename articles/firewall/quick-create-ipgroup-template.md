---
title: 'Quickstart: Criar um Azure Firewall e grupos IP - Modelo de gestor de recursos'
description: Neste arranque rápido, você aprende a usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar um Azure Firewall e grupos IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079137"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Quickstart: Criar um Azure Firewall e grupos IP - modelo ARM

Neste arranque rápido, utiliza-se um modelo de Gestor de Recursos Azure (modelo ARM) para implantar uma Firewall Azure com grupos IP de amostras utilizados numa regra de rede e regra de aplicação. Um Grupo IP é um recurso de alto nível que lhe permite definir e agrupar endereços IP, gamas e sub-redes num único objeto. Isto é útil para gerir endereços IP nas regras do Azure Firewall. Pode introduzir manualmente endereços IP ou importá-los a partir de um ficheiro.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria um Azure Firewall e grupos IP, juntamente com os recursos necessários para suportar o Azure Firewall.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. No portal, na **Firewall Criar um Azure Firewall com página ipGroups,** digite ou selecione os seguintes valores:
   - Subscrição: Selecione das subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou **selecione Criar novos**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome da rede virtual: Digite um nome para a nova rede virtual (VNet) 
   - Nome do grupo IP 1: Nome do tipo ip grupo 1 
   - Nome do grupo IP 2: Nome do tipo ip grupo 2 
   - Nome de utilizador Admin: Nome de utilizador do tipo para a conta de utilizador do administrador 
   - Autenticação: Selecione sshPublicKey ou senha 
   - Palavra-passe de administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

No portal Azure, reveja os recursos implantados, especialmente as regras de firewall que utilizam grupos IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Grupos IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Regras de rede.":::

Para saber mais sobre a sintaxe JSON e propriedades para uma firewall em um modelo, consulte [a referência do modelo Microsoft.Network azureFirewalls](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar e configurar a Firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)