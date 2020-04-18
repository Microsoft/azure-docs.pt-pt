---
title: 'Quickstart: Criar um Azure Firewall e grupos IP - modelo de Gestor de Recursos'
description: Aprenda a usar um modelo de Gestor de Recursos para criar um Azure Firewall e ip Groups.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605235"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Quickstart: Criar um Azure Firewall e grupos IP - modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para implementar um Firewall Azure com grupos IP de amostra utilizados numa regra de rede e regra de aplicação.

Um Grupo IP é um recurso de alto nível que permite definir e agrupar endereços IP, gamas e subnets num único objeto. Isto é útil para a gestão de endereços IP nas regras da Firewall Azure. Pode introduzir manualmente endereços IP ou importá-los a partir de um ficheiro.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall-and-ip-groups"></a>Criar um Azure Firewall e grupos IP

Este modelo cria um Azure Firewall e grupos IP, juntamente com os recursos necessários para suportar o Firewall Azure.

### <a name="review-the-template"></a>Reveja o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Vários recursos Azure são definidos no modelo:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Implementar o modelo

Implementar modelo de Gestor de Recursos para Azure:

1. Selecione **Implementar para Azure** para iniciar sessão no Azure e abrir o modelo. O modelo cria uma Firewall Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. No portal, na Create a Azure Firewall com página **IpGroups,** escreva ou selecione os seguintes valores:
   - Subscrição: Selecione a partir de subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou selecione **Criar novo**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome da rede virtual: Digite um nome para a nova rede virtual (VNet) 
   - Nome do grupo IP 1: Nome de tipo para grupo IP 1 
   - Nome do grupo IP 2: Nome de tipo para grupo IP 2 
   - Nome de utilizador do Administrador: Digite o nome de utilizador para a conta de utilizador do administrador 
   - Autenticação: Selecione sshPublicKey ou password 
   - Palavra-passe do administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A implantação pode demorar 10 minutos ou mais para ser concluída.

## <a name="review-deployed-resources"></a>Rever os recursos implantados

No portal Azure, reveja os recursos implantados, especialmente as regras de firewall que utilizam grupos IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Grupos IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Regras da rede.":::

Para saber mais sobre a sintaxe jSON e propriedades para uma firewall num modelo, consulte a referência do [modelo Microsoft.Network azureFirewalls](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo `Remove-AzResourceGroup` de recursos, ligue para o cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar e configurar firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)