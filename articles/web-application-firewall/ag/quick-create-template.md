---
title: 'Quickstart: Criar um Azure WAF v2 no Gateway de Aplicação - Modelo de Gestor de Recursos'
titleSuffix: Azure Application Gateway
description: Aprenda a usar um modelo de Gestor de Recursos para criar um Firewall v2 de aplicação web no Gateway de Aplicações Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 2a13d4ef440a75045c72f97db02d4be6f2e2b134
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656384"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Quickstart: Criar um Azure WAF v2 no Gateway de Aplicação - Modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar um Firewall v2 de aplicação Web Azure no Gateway de Aplicação.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-web-application-firewall"></a>Criar uma firewall de aplicação web

Este modelo cria uma firewall v2 de aplicação web simples no Gateway de Aplicação Azure. Isto inclui um endereço IP frontend IP público, configurações HTTP, uma regra com um ouvinte básico na porta 80, e uma piscina de backend. Uma política waf com uma regra personalizada é criada para bloquear o tráfego para a piscina de backend com base num tipo de correspondência de endereço IP.

### <a name="review-the-template"></a>Reveja o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Vários recursos Azure são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses) : um para o gateway da aplicação e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : para configurar o IIS e as páginas web

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar modelo de Gestor de Recursos para Azure:

1. Selecione **Implementar para Azure** para iniciar sessão no Azure e abrir o modelo. O modelo cria um portal de aplicação, a infraestrutura de rede e duas máquinas virtuais na piscina de backend que funciona o IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json"><img src="../media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou crie o seu grupo de recursos.
3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A implantação pode demorar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Embora o IIS não seja necessário para criar o gateway da aplicação, está instalado nos servidores de backend para verificar se o Azure criou com sucesso um WAF v2 no gateway da aplicação. 

Utilize o IIS para testar o gateway de aplicação:

1. Encontre o endereço IP público para o gateway da aplicação na sua página **'Visão Geral'.** ![Registre o endereço](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP público da aplicação Ou, pode selecionar **Todos os recursos,** introduzir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Overview'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereços do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta **proibida 403** verifica que o WAF foi criado com sucesso e está bloqueando ligações à piscina de backend.
4. Altere a regra personalizada para permitir o **tráfego**.
  Execute o seguinte script Azure PowerShell, substituindo o nome do seu grupo de recursos:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Refresque o seu navegador várias vezes e deverá ver ligações tanto para myVM1 como myVM2.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com o portal de aplicação, elimine o grupo de recursos. Isto remove o portal de aplicação e todos os recursos conexos.

Para eliminar o grupo `Remove-AzResourceGroup` de recursos, ligue para o cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar um portal de aplicação com uma Firewall de aplicação Web utilizando o portal Azure](application-gateway-web-application-firewall-portal.md)