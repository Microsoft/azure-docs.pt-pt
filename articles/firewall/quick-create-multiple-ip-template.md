---
title: 'Quickstart: Criar uma Firewall Azure com vários endereços IP públicos - Modelo de Gestor de Recursos'
description: Neste arranque rápido, você aprende a usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma Firewall Azure com vários endereços IP públicos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 33d110f327a381241fd9dbf55996a6e3f9fd3a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057982"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>Quickstart: Criar uma Firewall Azure com vários endereços IP públicos - modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para implementar uma Firewall Azure com vários endereços IP públicos a partir de um prefixo de endereço IP público. A firewall implementada tem regras de recolha de regras NAT que permitem ligações RDP a duas máquinas virtuais Do Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter mais informações sobre o Azure Firewall com vários endereços IP públicos, consulte [Implementar uma Firewall Azure com vários endereços IP públicos utilizando Azure PowerShell](deploy-multi-public-ip-powershell.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria um Azure Firewall com dois endereços IP públicos, juntamente com os recursos necessários para apoiar o Azure Firewall.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPPrefix**](/azure/templates/microsoft.network/publicipprefixes)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAcontas**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria uma Firewall Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. No portal, no **Create a Azure Firewall com várias páginas de endereços públicos IP,** escreva ou selecione os seguintes valores:
   - Subscrição: Selecione das subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou **selecione Criar novos**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome de utilizador Admin: Nome de utilizador do tipo para a conta de utilizador do administrador 
   - Palavra-passe de administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

No portal Azure, reveja os recursos mobilizados. Observe os endereços IP públicos da firewall.  

Utilize ligação de ambiente de trabalho remoto para ligar aos endereços IP públicos da firewall. As ligações bem sucedidas demonstram as regras DE NA de firewall que permitem a ligação aos servidores backend.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Implementar e configurar a Firewall Azure numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md)
