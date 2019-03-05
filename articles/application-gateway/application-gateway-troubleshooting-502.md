---
title: Resolver erros de Gateway inválido do Azure Application Gateway (502) | Documentos da Microsoft
description: Saiba como resolver erros de 502 Gateway de aplicação
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: d50f25fbe10fc5ac4e834141fe7ac45fbed918ab
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309031"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de problemas de erros de gateway inválido no Gateway de aplicação

Saiba como resolver erros de gateway inválido (502) recebidos ao utilizar o gateway de aplicação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicação, um dos erros que os utilizadores poderão encontrar é "erro de servidor: 502 - o Servidor Web recebeu uma resposta inválida enquanto funcionava como um servidor de gateway ou proxy". Este erro pode ocorrer devido ao seguinte principal:

* NSG, o UDR ou o DNS personalizado está a bloquear o acesso para membros do conjunto de back-end.
* VMs ou instâncias do conjunto de dimensionamento de máquina virtual de back-end não estão a responder para a sonda de estado de funcionamento padrão.
* Configuração inválida ou incorreta de sondas de estado de funcionamento personalizados.
* O Azure do Gateway de aplicação [conjunto de back-end não está configurado ou vazio](#empty-backendaddresspool).
* Nenhuma das VMs ou instâncias num [conjunto de dimensionamento de máquina virtual estão em bom estado](#unhealthy-instances-in-backendaddresspool).
* [Problemas de conectividade ou limite de tempo do pedido](#request-time-out) com pedidos de utilizador.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema de DNS personalizado, rota definida pelo utilizador ou grupo de segurança de rede

### <a name="cause"></a>Causa

Se o acesso ao back-end estiver bloqueado devido à presença de NSG, o UDR ou o DNS personalizado, as instâncias de Gateway de aplicação não será capazes de alcançar o conjunto de back-end e iria resultar em falhas de sonda que está causando 502 erros. Tenha em atenção que o NSG/UDR podem estar presente na sub-rede de Gateway de aplicação ou a sub-rede onde as VMs de aplicação estão implementadas. Da mesma forma presença de DNS personalizado na VNET também pode causar problemas se o FQDN é utilizado para membros do agrupamento de back-end e não for resolvido corretamente pelo servidor DNS configurado do utilizador para a VNET.

### <a name="solution"></a>Solução

Valide a configuração de NSG, UDR e DNS ao efetuar os seguintes passos:
* Verifique os NSGs associados à sub-rede de Gateway de aplicação. Certifique-se de que a comunicação com o back-end não está bloqueada.
* Verifique o UDR associado à sub-rede de Gateway de aplicação. Certifique-se de que UDR não está a encaminhar tráfego para fora da sub-rede de back-end - por exemplo, verificar para o encaminhamento para aplicações virtuais ou rotas predefinidas que está a ser anunciadas à sub-rede de Gateway de aplicação através do ExpressRoute/VPN de rede.

```powershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique NSG em vigor e a rota com a VM de back-end

```powershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique a presença de DNS personalizado na VNet. DNS pode ser verificado ao observar a detalhes das propriedades na saída da VNet.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, certifique-se de que o servidor DNS é capaz de resolver corretamente o FQDN do membro do conjunto de back-end.

## <a name="problems-with-default-health-probe"></a>Problemas com a sonda de estado de funcionamento predefinida

### <a name="cause"></a>Causa

502 erros também podem ser indicadores freqüentes que a sonda de estado de funcionamento predefinido não é capaz de alcançar a VMs de back-end. Quando uma instância de Gateway de aplicação é aprovisionada, este configura automaticamente uma sonda de estado de funcionamento padrão para cada BackendAddressPool usando propriedades do BackendHttpSetting. Sem intervenção do utilizador tem de definir esta pesquisa. Especificamente, quando uma regra de balanceamento de carga estiver configurada, é efetuada uma associação entre um BackendHttpSetting e BackendAddressPool. Uma pesquisa predefinida está configurada para cada um dessas associações e Gateway de aplicação inicia uma ligação de verificação periódica do Estado de funcionamento para cada instância em BackendAddressPool nas portas especificada no elemento BackendHttpSetting. A tabela seguinte lista os valores associados a sonda de estado de funcionamento padrão.

| Propriedade de pesquisa | Value | Descrição |
| --- | --- | --- |
| URL de Pesquisa |http://127.0.0.1/ |Caminho do URL |
| Intervalo |30 |Intervalo de sonda em segundos |
| Tempo limite |30 |Tempo limite de sonda em segundos |
| Limiar com funcionamento incorreto |3 |Contagem de repetições de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

* Certifique-se de que um site predefinido está configurado e está à escuta em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente da 80, o site predefinido deve ser configurado para escutar nessa porta.
* A chamada para http://127.0.0.1:port deverá devolver um código de resultado HTTP de 200. Isso deve ser retornado ao período de tempo limite de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não há regras de firewall ou grupos de segurança do Azure rede, que bloqueia o tráfego de entrada ou de saída na porta configurada.
* Se as VMs clássicas do Azure ou serviço em nuvem é utilizado com o FQDN ou IP público, certifique-se de que o correspondente [ponto final](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) é aberto.
* Se a VM é configurada através do Azure Resource Manager e estiver fora da VNet em que o Gateway de aplicação é implementado, [grupo de segurança de rede](../virtual-network/security-overview.md) tem de ser configurado para permitir o acesso a porta pretendida.

## <a name="problems-with-custom-health-probe"></a>Problemas com a sonda de estado de funcionamento personalizados

### <a name="cause"></a>Causa

Sondas de estado de funcionamento personalizadas permitem maior flexibilidade para a predefinição de comportamento de pesquisa. Ao utilizar sondas personalizadas, os utilizadores podem configurar o intervalo de pesquisa, o URL e caminho para testar e quantos respostas com falhas para aceitar antes de os marcar a instância de conjunto de back-end como mau estado de funcionamento. As seguintes propriedades adicionais são adicionadas.

| Propriedade de pesquisa | Descrição |
| --- | --- |
| Name |Nome da sonda. Este nome é utilizado para fazer referência a sonda nas definições de HTTP de back-end. |
| Protocolo |Protocolo utilizado para enviar a sonda. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome de anfitrião para enviar a sonda. Aplicável apenas quando vários sites está configurada no Gateway de aplicação. Isso é diferente do nome de anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa com "/". A sonda é enviada ao \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\> |
| Intervalo |Intervalo de sonda em segundos. Este é o intervalo de tempo entre dois sondas consecutivos. |
| Tempo limite |Sonda de tempo limite em segundos. Se uma resposta válida não está a ser recebida durante este período de tempo limite, a sonda está marcada como falhado. |
| Limiar com funcionamento incorreto |Contagem de repetições de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

Verifique se a sonda de estado de funcionamento personalizado está configurada corretamente, como a tabela anterior. Além dos passos de resolução de problemas anteriores, certifique-se também o seguinte:

* Certifique-se de que a sonda está especificada corretamente, de acordo a [guia](application-gateway-create-probe-ps.md).
* Se o Gateway de aplicação está configurado para um único site, por predefinição, o anfitrião nome deve ser especificado como '127.0.0.1', a menos que caso contrário, é configurado na sonda personalizada.
* Certifique-se de que uma chamada para http://\<host\>:\<porta\>\<caminho\> devolve um código de resultado HTTP de 200.
* Certifique-se de que um intervalo, limite de tempo e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se utilizar uma sonda HTTPS, certifique-se de que o servidor de back-end não requer SNI ao configurar um certificado de contingência no próprio servidor de back-end.

## <a name="request-time-out"></a>Limite de tempo do pedido

### <a name="cause"></a>Causa

Quando é recebido um pedido de utilizador, o Gateway de aplicação aplica as regras configuradas para a solicitação e encaminha o mesmo para uma instância de conjunto de back-end. Ele aguarda um intervalo configurável de tempo para uma resposta a partir da instância de back-end. Por predefinição, é este intervalo **30 segundos**. Se o Gateway de aplicação não recebeu uma resposta da aplicação de back-end neste intervalo, o pedido de utilizador veria um erro de 502.

### <a name="solution"></a>Solução

Gateway de aplicação permite aos utilizadores configurar esta definição através de BackendHttpSetting, que pode ser aplicado, em seguida, para conjuntos diferentes. Diferentes conjuntos de back-end podem ter diferente BackendHttpSetting e limite de tempo do pedido, por conseguinte, diferentes configurados.

```powershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o Gateway de aplicação não tem VMs ou conjunto de dimensionamento de máquina virtual configurado no conjunto de endereços de back-end, ele não é possível encaminhar qualquer solicitação do cliente e emite um erro de gateway inválido.

### <a name="solution"></a>Solução

Certifique-se de que o conjunto de endereços de back-end não está vazio. Isso pode ser feito através do PowerShell, CLI ou do portal.

```powershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o conjunto de endereços de back-end não vazia. Segue-se um exemplo em que dois conjuntos, são devolvidos que estão configurados com endereços IP ou FQDN para VMs de back-end. O estado de aprovisionamento do BackendAddressPool deve ser "com êxito".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias de mau estado de funcionamento no BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias de BackendAddressPool estiverem em mau estado de funcionamento, em seguida, o Gateway de aplicação não terá qualquer back-end para encaminhar o pedido de utilizador para. Também pode ser o caso quando as instâncias de back-end estão em bom estadas, mas não tem a aplicação necessária implementada.

### <a name="solution"></a>Solução

Certifique-se de que as instâncias estão em bom estadas e a aplicação está configurada corretamente. Verifique se as instâncias de back-end são capazes de responder a um ping a partir de outra VM na mesma VNet. Se configurado com um ponto final público, certifique-se de que um pedido de navegador para o aplicativo web é serviceable na.

## <a name="next-steps"></a>Passos Seguintes

Se os passos anteriores não resolverem o problema, abra um [pedido de suporte](https://azure.microsoft.com/support/options/).

