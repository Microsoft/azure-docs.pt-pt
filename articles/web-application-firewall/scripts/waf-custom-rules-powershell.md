---
title: Amostra de script Azure PowerShell que usa regras personalizadas waf
description: Amostra de script Azure PowerShell - Crie firewall de aplicação web nas regras personalizadas do Gateway de aplicações
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8d800e77ed0e609091d31d9b3177af447ac06f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079511"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Crie regras personalizadas waf com Azure PowerShell

Este script cria uma Firewall de Aplicação Gateway Web que utiliza regras personalizadas. A regra personalizada bloqueia o tráfego se o cabeçalho do pedido contiver User-Agent *evilbot*.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se optar por instalar e utilizar o Azure PowerShell localmente, este script requer a versão 2.1.0 ou posterior do módulo Azure PowerShell.

1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).
2. Para criar uma ligação com a Azure, corra `Connect-AzAccount` .

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
| [Nova-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Cria a configuração que associa uma sub-rede ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Cria a configuração que atribui um endereço IP público ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Atribui uma porta que vai ser utilizada para aceder ao gateway de aplicação. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Cria um conjunto de back-end para um gateway de aplicação. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Configura as definições para um conjunto de back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Cria um serviço de escuta. |
| [New-AzApplicationGatewayRequestroutingrule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Cria uma regra de encaminhamento. |
| [New-AzApplicationGatewaysku](/powershell/module/az.network/new-azapplicationgatewaysku) | Especifica a camada e a capacidade para um gateway de aplicação. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Cria um gateway de aplicação. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |
|[Nova-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Cria uma configuração de escala automática para o Gateway de Aplicações.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Cria uma variável de correspondência para a condição de firewall.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Cria uma condição de correspondência para regras personalizadas.|
|[New-AzApplicationGatewayFirewallCustomrule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Cria uma nova regra personalizada para a política de firewall de gateway de aplicação.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Cria uma política de firewall de gateway de aplicação.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Cria uma configuração WAF para um gateway de aplicação.|

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as regras personalizadas da WAF, consulte [as regras personalizadas para firewall de aplicações web](../ag/custom-waf-rules-overview.md)
- Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).
