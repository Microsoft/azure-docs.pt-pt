---
title: Exemplo de script Azure PowerShell-criar regras personalizadas do WAF
description: Exemplo de script Azure PowerShell-criar Firewall do aplicativo Web nas regras personalizadas do gateway de aplicativo
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501576"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Criar regras personalizadas do WAF com Azure PowerShell

Esse script cria um firewall do aplicativo Web do gateway de aplicativo que usa regras personalizadas. A regra personalizada bloqueará o tráfego se o cabeçalho de solicitação contiver *evilbot*de agente de usuário.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se você optar por instalar e usar Azure PowerShell localmente, esse script exigirá o Azure PowerShell do módulo versão 2.1.0 ou posterior.

1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).
2. Para criar uma conexão com o Azure, execute `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria a configuração de sub-rede. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria a rede virtual com as configurações de sub-rede. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria o endereço IP público do gateway de aplicação. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Cria a configuração que associa uma sub-rede ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Cria a configuração que atribui um endereço IP público ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Atribui uma porta que vai ser utilizada para aceder ao gateway de aplicação. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Cria um conjunto de back-end para um gateway de aplicação. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Configura as definições para um conjunto de back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Cria um serviço de escuta. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Cria uma regra de encaminhamento. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Especifica a camada e a capacidade para um gateway de aplicação. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Cria um gateway de aplicação. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Cria uma configuração de dimensionamento automático para o gateway de aplicativo.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Cria uma variável de correspondência para a condição de firewall.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Cria uma condição de correspondência para a regra personalizada.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Cria uma nova regra personalizada para a política de firewall do gateway de aplicativo.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Cria uma política de firewall do gateway de aplicativo.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Cria uma configuração de WAF para um gateway de aplicativo.|

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre regras personalizadas do WAF, consulte [regras personalizadas para o Firewall do aplicativo Web](../ag/custom-waf-rules-overview.md)
- Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).
