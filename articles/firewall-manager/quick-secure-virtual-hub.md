---
title: 'Quickstart: Secure virtual hub usando a pré-visualização do Gestor de Firewall Azure - modelo de Gestor de Recursos'
description: Aprenda a proteger o seu hub virtual utilizando a Pré-visualização do Gestor de Firewall Azure.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: victorh
ms.openlocfilehash: b9839e51fcea1e8fe4adc4760e16ae2d73b163ee
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694236"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>Quickstart: Proteja o seu hub virtual usando o Azure Firewall Manager - Modelo de Gestor de Recursos

Neste arranque rápido, utiliza um modelo de Gestor de Recursos para proteger o seu hub virtual utilizando a Pré-visualização do Gestor de Firewall Azure.

A firewall implantada tem uma regra de aplicação que permite ligações a `www.microsoft.com` . Duas máquinas virtuais do Windows Server 2019 são implementadas para testar a firewall. Um servidor de salto é usado para ligar ao servidor de carga de trabalho. A partir do servidor de carga de trabalho, só é possível ligar a `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para mais informações sobre a Pré-visualização do Gestor de Firewall do Azure, consulte [o que é a Pré-visualização do Gestor de Firewall do Azure?](overview.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-secured-virtual-hub"></a>Criar um centro virtual seguro

Este modelo cria um hub virtual seguro usando a Pré-visualização do Gestor de Firewall Azure, juntamente com os recursos necessários para suportar o cenário.

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/fwm-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

Vários recursos Azure são definidos no modelo:

- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar modelo de Gestor de Recursos para Azure:

1. Selecione **Implementar para Azure** para iniciar sessão no Azure e abrir o modelo. O modelo cria um Azure Firewall, um WAN virtual e um hub virtual, a infraestrutura de rede e duas máquinas virtuais.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. No portal, na página de **hubs virtuais protegidos,** escreva ou selecione os seguintes valores:
   - Subscrição: Selecione a partir de subscrições existentes 
   - Grupo de recursos: Selecione dos grupos de recursos existentes ou selecione **Criar novo**, e selecione **OK**.
   - Localização: Selecione uma localização
   - Nome de utilizador do Administrador: Digite o nome de utilizador para a conta de utilizador do administrador 
   - Palavra-passe do administrador: Digite uma palavra-passe ou chave do administrador

3. Selecione **Rever + criar** e, em seguida, selecione **Criar**. A implantação pode demorar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Agora, teste as regras da firewall para confirmar que funciona como esperado.

1. A partir do portal Azure, reveja as definições de rede para a máquina virtual **Workload-Srv** e note o endereço IP privado.
2. Ligue um ambiente de trabalho remoto à máquina virtual **Jump-Srv** e inscreva-se. A partir daí, abra uma ligação remota de ambiente de trabalho ao endereço IP privado **Workload-Srv.**

3. Abra o Internet Explorer e navegue até `www.microsoft.com`.
4. Selecione **OK**  >  **Close** nos alertas de segurança do Internet Explorer.

   Devia ver a página inicial da Microsoft.

5. Navegue para `www.google.com`.

   Deve estar bloqueado pela firewall.

Então agora verificou que as regras da firewall estão funcionando:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com a firewall, elimine o grupo de recursos. Isto remove a firewall e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Conheça parceiros de segurança fidedignos](trusted-security-partners.md)
