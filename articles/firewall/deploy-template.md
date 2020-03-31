---
title: Implementar o Azure Firewall através de um modelo
description: Implementar firewall azure usando um modelo. A rede criada tem um VNet com três subnets. Duas máquinas virtuais do Windows Server de dois núcleos estão implantadas.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169206"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Implementar o Azure Firewall através de um modelo

O modelo de configuração de caixa de [areia Create AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) cria um ambiente de rede de teste com uma firewall. A rede tem uma rede virtual (VNet) com três subredes: *AzureFirewallSubnet,* *ServersSubnet*e *JumpboxSubnet*. A sub-rede *ServersSubnet* e *JumpboxSubnet* têm uma única máquina virtual do Windows Server de dois núcleos.

A firewall está na subnet *AzureFirewallSubnet,* e tem uma coleção de `www.microsoft.com`regras de aplicação com uma única regra que permite o acesso a .

Uma rede de pontos de rota definida pelo utilizador a partir da subnet *ServersSubnet* através da firewall, onde são aplicadas as regras de firewall.

Para mais informações sobre o Firewall Azure, consulte [O Conjunto e configure a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Use o modelo para implementar firewall Azure

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

**Para instalar e implantar o Firewall Azure utilizando o modelo:**

1. Aceda ao [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)modelo em .
   
1. Leia a introdução e, quando estiver pronto para ser implantado, selecione **Deploy para Azure**.
   
1. Se for necessário, inicie sessão no portal do Azure. 

1. No portal, na **Create a sandbox configuração da página AzureFirewall,** escreva ou selecione os seguintes valores:
   
   - **Grupo de recursos**: Selecione **Criar novo,** digite um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da rede virtual**: Digite um nome para o novo VNet. 
   - **Nome de utilizador do Administrador**: Digite um nome de utilizador para a conta de utilizador do administrador.
   - **Palavra-passe do administrador**: Digite uma palavra-passe do administrador. 
   
1. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições acima indicados**.
   
1. Selecione **Comprar**.
   
   Levará alguns minutos para criar os recursos. 
   
1. Explore os recursos que foram criados com a firewall. 

Para saber mais sobre a sintaxe jSON e propriedades para uma firewall num modelo, consulte [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, pode remover o grupo de recursos, firewall e todos os recursos relacionados executando o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Para remover um grupo de recursos chamado *MyResourceGroup,* executar: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Não remova o grupo de recursos e a firewall ainda, se pretender continuar no tutorial de monitorização de firewall. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode monitorizar os registos da Firewall Azure:

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
