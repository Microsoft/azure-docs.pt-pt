---
title: Amostra de script Azure PowerShell - Crie regras personalizadas WAF
description: Amostra de script Azure PowerShell - Crie regras personalizadas de firewall de aplicação web
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "66743424"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Crie regras personalizadas WAF com a Azure PowerShell

Este script cria uma Firewall de aplicação web de gateway de aplicação que utiliza regras personalizadas. A regra personalizada bloqueia o tráfego se o cabeçalho de pedido contiver *evilbot*user-agent .

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se optar por instalar e utilizar o Azure PowerShell localmente, este script requer a versão 2.1.0 ou mais tarde do módulo PowerShell.

1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).
2. Para criar uma ligação `Connect-AzAccount`com o Azure, corra.

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
| [Rede Nova AzVirtual](/powershell/module/az.network/new-azvirtualnetwork) | Cria a rede virtual com as configurações de sub-rede. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria o endereço IP público do gateway de aplicação. |
| [Configuração IPIP new-AzApplicationGatewayIP](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Cria a configuração que associa uma sub-rede ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Cria a configuração que atribui um endereço IP público ao gateway de aplicação. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Atribui uma porta que vai ser utilizada para aceder ao gateway de aplicação. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Cria um conjunto de back-end para um gateway de aplicação. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Configura as definições para um conjunto de back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Cria um serviço de escuta. |
| [Nova AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Cria uma regra de encaminhamento. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Especifica a camada e a capacidade para um gateway de aplicação. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Cria um gateway de aplicação. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |
|[Configuração de escala new-AzApplicationGatewayAuto](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Cria uma configuração de escala automática para o Gateway de Aplicação.|
|[Nova AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Cria uma variável de correspondência para a condição de firewall.|
|[Nova AzApplicationGatewayFirewallFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Cria uma condição de correspondência para a regra personalizada.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Cria uma nova regra personalizada para a política de firewall de gateway de aplicação.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Cria uma política de firewall de porta de entrada de aplicação.|
|[Nova AzApplicationGatewayApplicationApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Cria uma configuração WAF para um gateway de aplicação.|

## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre as regras personalizadas da WAF, consulte [regras personalizadas para firewall](../custom-waf-rules-overview.md) de aplicação web
- Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).
- Pode encontrar exemplos adicionais de script do PowerShell de gateway de aplicação na [documentação do Gateway de Aplicação do Azure](../powershell-samples.md).
