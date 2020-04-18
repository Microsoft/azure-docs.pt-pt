---
title: 'Quickstart: Criar uma Firewall Azure com vários endereços IP públicos - modelo de Gestor de Recursos'
description: Aprenda a usar um modelo de Gestor de Recursos para criar uma Firewall Azure com vários endereços IP públicos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605212"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Quickstart: Criar uma Firewall Azure com vários endereços IP públicos - modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para implementar uma Firewall Azure com vários endereços IP públicos.

A firewall implementada tem regras de recolha de regras NAT que permitem ligações RDP a duas máquinas virtuais Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter mais informações sobre o Azure Firewall com vários endereços IP públicos, consulte [Implementar uma Firewall Azure com vários endereços IP públicos utilizando o Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall"></a>Criar uma Firewall Azure

Este modelo cria uma Firewall Azure com dois endereços IP públicos, juntamente com os recursos necessários para suportar o Firewall Azure.

### <a name="review-the-template"></a>Reveja o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Vários recursos Azure são definidos no modelo:

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

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. No portal, no Create an Azure Firewall com várias páginas de **endereços públicos IP,** escreva ou selecione os seguintes valores:
   - Subscrição: Selecione a partir de subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou selecione **Criar novo**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome de utilizador do Administrador: Digite o nome de utilizador para a conta de utilizador do administrador 
   - Palavra-passe do administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A implantação pode demorar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

No portal Azure, reveja os recursos implantados. Note os endereços IP públicos da firewall.  

Utilize a Ligação de Ambiente de Trabalho Remoto para ligar aos endereços IP públicos da firewall. As ligações bem sucedidas demonstram regras nat de firewall que permitem a ligação aos servidores de backend.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo `Remove-AzResourceGroup` de recursos, ligue para o cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar e configurar firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)