---
title: 'Quickstart: Criar uma Firewall Azure e uma política de firewall - Modelo de Gestor de Recursos'
description: Neste arranque rápido, implementa-se uma firewall Azure e uma política de firewall.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529868"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Quickstart: Crie uma Firewall Azure e uma política de firewall - modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma firewall Azure e uma política de firewall. A política de firewall tem uma regra de aplicação que permite ligações `www.microsoft.com` e uma regra que permite ligações ao Windows Update usando a etiqueta FQDN do **WindowsUpdate.** Uma regra de rede permite ligações UDP a um servidor de tempo em 13.86.101.172.

Além disso, os grupos IP são utilizados nas regras para definir os endereços IP **source.**

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter informações sobre o Azure Firewall Manager, veja [o que é O Gestor de Firewall da Azure?](overview.md)

Para obter informações sobre a Azure Firewall, veja [o que é Azure Firewall?](../firewall/overview.md)

Para obter informações sobre grupos IP, consulte [grupos IP em Azure Firewall](../firewall/ip-groups.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria uma rede virtual hub, juntamente com os recursos necessários para suportar o cenário.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria um Azure Firewall, um wan virtual e um hub virtual, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. No portal, na página **Criar uma Firewall e FirewallPolicy com regras e páginas ipgroups,** digite ou selecione os seguintes valores:
   - Subscrição: Selecione das subscrições existentes.
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou **selecione Criar novos**, e selecione **OK**.
   - Região: Selecione uma região.
   - Firewall Name: digite um nome para a firewall.

3. Selecione **Rever + criar** e, em seguida, selecione **Criar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Após a implementação concluída, verá os seguintes recursos semelhantes.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Recursos mobilizados":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da política do Azure Firewall Manager](policy-overview.md)
