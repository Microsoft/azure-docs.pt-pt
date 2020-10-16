---
title: 'Quickstart: Criar um Azure WAF v2 no Application Gateway - Modelo de Gestor de Recursos Azure'
titleSuffix: Azure Application Gateway
description: Aprenda a usar um modelo de arranque rápido (modelo ARM) do Gestor de Recursos Azure para criar uma Firewall de Aplicação Web v2 no Gateway de aplicações Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091326"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Quickstart: Criar um Azure WAF v2 no Gateway de aplicação usando um modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um Azure Web Application Firewall v2 no Gateway de aplicações.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Este modelo cria um simples Firewall de Aplicação Web v2 no Gateway de aplicação Azure. Isto inclui um endereço IP ip público, definições HTTP, uma regra com um ouvinte básico na porta 80, e um pool de backend. Uma política DE WAF com uma regra personalizada é criada para bloquear o tráfego para o pool de backend com base num tipo de correspondência de endereço IP.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) um para o gateway de aplicações e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : para configurar o IIS e as páginas web

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria um gateway de aplicação, a infraestrutura de rede e duas máquinas virtuais na piscina de backend que executa o IIS.

   [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos.
3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A colocação pode demorar 10 minutos ou mais a ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Embora o IIS não seja necessário para criar o gateway de aplicações, está instalado nos servidores backend para verificar se o Azure criou com sucesso um HA v2 no gateway da aplicação.

Utilize o IIS para testar o gateway de aplicações:

1. Encontre o endereço IP público para a porta de aplicação na sua página **de visão geral.** ![ Registar endereço IP público de gateway de aplicações ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) Ou, pode selecionar **Todos os recursos,** inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Vista Geral'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereço do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta **proibida 403** verifica que a WAF foi criada com sucesso e está a bloquear ligações à piscina de backend.
4. Altere a regra personalizada para **permitir o tráfego**.
  Executar o seguinte script Azure PowerShell, substituindo o nome do grupo de recursos:
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

   Refresque o seu navegador várias vezes e deverá ver ligações tanto para o myVM1 como para o myVM2.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicações, elimine o grupo de recursos. Isto remove o gateway de aplicação e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar um gateway de aplicações com uma Firewall de Aplicação Web utilizando o portal Azure](application-gateway-web-application-firewall-portal.md)