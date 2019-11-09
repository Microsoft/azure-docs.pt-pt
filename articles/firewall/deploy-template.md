---
title: Implantar o Firewall do Azure usando um modelo
description: Implantar o Firewall do Azure usando um modelo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: c0a6cda54a58e3cc03ba31e221fb57fc725dd779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839395"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Implantar o Firewall do Azure usando um modelo

O [modelo de instalação criar AzureFirewall sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) cria um ambiente de rede de teste com um firewall. A rede tem uma rede virtual (VNet) com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet*e *JumpboxSubnet*. A sub-rede *ServersSubnet* e *JumpboxSubnet* tem uma única máquina virtual de dois núcleos do Windows Server.

O firewall está na sub-rede *AzureFirewallSubnet* e tem uma coleção de regras de aplicativo com uma única regra que permite o acesso a `www.microsoft.com`.

Uma rota definida pelo usuário aponta o tráfego de rede da sub-rede *ServersSubnet* por meio do firewall, onde as regras de firewall são aplicadas.

Para obter mais informações sobre o Firewall do Azure, consulte [implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Usar o modelo para implantar o Firewall do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

**Para instalar e implantar o Firewall do Azure usando o modelo:**

1. Acesse o modelo em [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Leia a introdução e, quando estiver pronto para implantar, selecione **implantar no Azure**.
   
1. Entre no portal do Azure, se necessário. 

1. No portal, na página **criar uma configuração de área restrita do AzureFirewall** , digite ou selecione os seguintes valores:
   
   - **Grupo de recursos**: selecione **criar novo**, digite um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da rede virtual**: digite um nome para a nova VNet. 
   - **Nome**de usuário do administrador: digite um nome de usuário para a conta do administrador.
   - **Senha**do administrador: digite uma senha de administrador. 
   
1. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições declarados acima**.
   
1. Selecione **Comprar**.
   
   Levará alguns minutos para criar os recursos. 
   
1. Explore os recursos que foram criados com o firewall. 

Para saber mais sobre a sintaxe JSON e as propriedades de um firewall em um modelo, consulte [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deles, poderá remover o grupo de recursos, o firewall e todos os recursos relacionados executando o comando do PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Para remover um grupo de recursos chamado *MyResource*Group, execute: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Não remova o grupo de recursos e o firewall ainda, se você planeja continuar no tutorial de monitoramento do firewall. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode monitorar os logs de firewall do Azure:

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
