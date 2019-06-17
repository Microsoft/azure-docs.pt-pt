---
title: Implantar o Firewall do Azure através de um modelo
description: Implantar o Firewall do Azure através de um modelo
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: e0fbec8b22993345114d8d6642e42095191d0b37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115693"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Implantar o Firewall do Azure através de um modelo

O [modelo de configuração de sandbox criar AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) cria um ambiente de rede de teste com uma firewall. A rede tem uma rede virtual (VNet) com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet*, e *JumpboxSubnet*. O *ServersSubnet* e *JumpboxSubnet* cada sub-rede tiver uma máquina virtual do Windows Server única e dois núcleos.

A firewall está no *AzureFirewallSubnet* sub-rede, e tem uma coleção de regras de aplicação com uma única regra que permite o acesso à *www.microsoft.com*.

Tráfego de rede a partir de pontos de uma rota definida pelo utilizador a *ServersSubnet* sub-rede através da firewall, em que são aplicadas as regras de firewall.

Para obter mais informações sobre a Firewall do Azure, consulte [implementar e configurar a Firewall do Azure no portal do Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Utilize o modelo para implantar o Firewall do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

**Para instalar e implantar o Firewall do Azure com o modelo:**

1. Aceder ao modelo em [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Leia a introdução e, quando estiver pronto para implementar, selecione **implementar no Azure**.
   
1. Se necessário, inicie sessão no portal do Azure. 

1. No portal, sobre o **criar uma configuração de proteção de segurança de AzureFirewall** página, escreva ou selecione os seguintes valores:
   
   - **Grupo de recursos**: Selecione **criar novo**, escreva um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da rede virtual**: Escreva um nome para a nova VNet. 
   - **Nome de utilizador administrador**: Escreva um nome de utilizador da conta de utilizador de administrador.
   - **Palavra-passe de administrador**: Escreva uma palavra-passe de administrador. 
   
1. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições indicados acima**.
   
1. Selecione **Comprar**.
   
   Irá demorar alguns minutos para criar os recursos. 
   
1. Explore os recursos que foram criados com o firewall. 

Para saber mais sobre a sintaxe JSON e propriedades de uma firewall num modelo, veja [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar delas, pode remover o grupo de recursos, o firewall e a todos os recursos relacionados ao executar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando do PowerShell. Para remover um grupo de recursos chamado *MyResourceGroup*, execute: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Não remova o grupo de recursos e o firewall ainda, se pretender avançar para o firewall do tutorial de monitorização. 

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode monitorizar os registos de Firewall do Azure:

> [!div class="nextstepaction"]
> [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
