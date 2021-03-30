---
title: 'Quickstart: Secure virtual hub usando Azure Firewall Manager - Modelo de Gestor de Recursos'
description: Neste arranque rápido, aprende-se a proteger o seu hub virtual utilizando o Azure Firewall Manager.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 050dfa737ad04127f8c72158fbecb2e8e70e05c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075346"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Quickstart: Fixe o seu hub virtual usando o Azure Firewall Manager - modelo ARM

Neste arranque rápido, utilize um modelo de Gestor de Recursos Azure (modelo ARM) para proteger o seu hub virtual utilizando o Azure Firewall Manager. A firewall implantada tem uma regra de aplicação que permite ligações a `www.microsoft.com` . Duas máquinas virtuais do Windows Server 2019 são implementadas para testar a firewall. Um servidor de salto é utilizado para ligar ao servidor de carga de trabalho. A partir do servidor de carga de trabalho, só é possível ligar-se a `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para mais informações sobre o Azure Firewall Manager, veja [o que é O Gestor de Firewall da Azure?](overview.md)

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria um hub virtual seguro usando o Azure Firewall Manager, juntamente com os recursos necessários para suportar o cenário.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAcontas**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria um Azure Firewall, um wan virtual e um hub virtual, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. No portal, na página de **centros virtuais Seguros,** escreva ou selecione os seguintes valores:
   - Subscrição: Selecione das subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou **selecione Criar novos**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome de utilizador Admin: Nome de utilizador do tipo para a conta de utilizador do administrador 
   - Palavra-passe de administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Rever + criar** e, em seguida, selecione **Criar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Agora, teste as regras da firewall para confirmar que funciona como esperado.

1. A partir do portal Azure, reveja as definições de rede para a máquina virtual **Workload-Srv** e note o endereço IP privado.
2. Ligue um ambiente de trabalho remoto à máquina virtual **Jump-Srv** e inscreva-se. A partir daí, abra uma ligação de ambiente de trabalho remoto ao endereço IP privado **Workload-Srv.**

3. Abra o Internet Explorer e navegue até `www.microsoft.com`.
4. Selecione **OK**  >  **Feche** os alertas de segurança do Internet Explorer.

   Devia ver a página inicial da Microsoft.

5. Navegue para `www.google.com`.

   Deve estar bloqueado pela firewall.

Então agora verificaste que as regras da firewall estão a funcionar:

- Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os fornecedores de parceiros de segurança](trusted-security-partners.md)
