---
title: Solucionar erros de gateway inválidos-Aplicativo Azure gateway
description: 'Saiba como solucionar problemas de erro do servidor do gateway de aplicativo: 502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130483"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solucionando problemas de erros de gateway inválidos no gateway de aplicativo

Saiba como solucionar problemas de erros de gateway inválidos (502) recebidos ao usar o gateway de Aplicativo Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicativo, um dos erros que você pode ver é "erro de servidor: 502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy". Esse erro pode ocorrer pelos seguintes motivos principais:

* NSG, UDR ou DNS personalizado está bloqueando o acesso aos membros do pool de back-end.
* As VMs de back-end ou as instâncias do conjunto de dimensionamento de máquinas virtuais não estão respondendo à investigação de integridade padrão.
* Configuração inválida ou incorreta de investigações de integridade personalizadas.
* O pool de [back-end do Gateway aplicativo Azure não está configurado ou vazio](#empty-backendaddresspool).
* Nenhuma das VMs ou instâncias no [conjunto de dimensionamento de máquinas virtuais está íntegra](#unhealthy-instances-in-backendaddresspool).
* [Tempo limite de solicitação ou problemas de conectividade](#request-time-out) com solicitações do usuário.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Grupo de segurança de rede, rota definida pelo usuário ou problema de DNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao back-end for bloqueado por causa de um DNS NSG, UDR ou personalizado, as instâncias do gateway de aplicativo não poderão alcançar o pool de back-end. Isso causa falhas de investigação, resultando em erros 502.

O NSG/UDR pode estar presente na sub-rede do gateway de aplicativo ou na sub-rede em que as VMs de aplicativo são implantadas.

Da mesma forma, a presença de um DNS personalizado na VNet também poderia causar problemas. Um FQDN usado para membros do pool de back-end pode não ser resolvido corretamente pelo servidor DNS configurado pelo usuário para a VNet.

### <a name="solution"></a>Solução

Valide a configuração de NSG, UDR e DNS percorrendo as seguintes etapas:

* Verifique NSGs associado à sub-rede do gateway de aplicativo. Verifique se a comunicação com o back-end não está bloqueada.
* Verifique UDR associado à sub-rede do gateway de aplicativo. Verifique se o UDR não está direcionando o tráfego para fora da sub-rede de back-end. Por exemplo, verifique o roteamento para dispositivos virtuais de rede ou rotas padrão sendo anunciadas para a sub-rede do gateway de aplicativo via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verificar NSG e rotear em vigor com a VM de back-end

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique a presença do DNS personalizado na VNet. O DNS pode ser verificado examinando os detalhes das propriedades de VNet na saída.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, verifique se o servidor DNS pode resolver corretamente o FQDN do membro do pool de back-end.

## <a name="problems-with-default-health-probe"></a>Problemas com a investigação de integridade padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes que a investigação de integridade padrão não pode alcançar VMs de back-end.

Quando uma instância do gateway de aplicativo é provisionada, ela configura automaticamente uma investigação de integridade padrão para cada BackendAddressPool usando as propriedades de BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Uma investigação padrão é configurada para cada uma dessas associações e o gateway de aplicativo inicia uma conexão de verificação de integridade periódica para cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela a seguir lista os valores associados à investigação de integridade padrão:

| Propriedade de investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |`http://127.0.0.1/` |Caminho da URL |
| Intervalo |30 |Intervalo de investigação em segundos |
| Tempo limite |30 |Tempo limite de investigação em segundos |
| Limite não íntegro |3 |Contagem de repetição de investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite não íntegro. |

### <a name="solution"></a>Solução

* Verifique se um site padrão está configurado e escutando em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
* A chamada para `http://127.0.0.1:port` deve retornar um código de resultado HTTP de 200. Isso deve ser retornado dentro do período de tempo limite de 30 segundos.
* Verifique se a porta configurada está aberta e se não há regras de firewall ou grupos de segurança de rede do Azure, que bloqueiam o tráfego de entrada ou saída na porta configurada.
* Se as VMs clássicas do Azure ou o serviço de nuvem forem usados com um FQDN ou um IP público, verifique se o [ponto de extremidade](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente está aberto.
* Se a VM estiver configurada via Azure Resource Manager e estiver fora da VNet em que o gateway de aplicativo está implantado, um [grupo de segurança de rede](../virtual-network/security-overview.md) deverá ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com a investigação de integridade personalizada

### <a name="cause"></a>Causa

As investigações de integridade personalizadas permitem flexibilidade adicional para o comportamento de investigação padrão. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL, o caminho a ser testado e quantas respostas com falha aceitar antes de marcar a instância do pool de back-end como não íntegra.

As seguintes propriedades adicionais são adicionadas:

| Propriedade de investigação | Descrição |
| --- | --- |
| Nome |Nome da investigação. Esse nome é usado para fazer referência à investigação nas configurações de HTTP de back-end. |
| Protocolo |Protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP de back-end |
| Anfitrião |Nome do host para enviar a investigação. Aplicável somente quando vários sites estão configurados no gateway de aplicativo. Isso é diferente do nome de host da VM. |
| Caminho |Caminho relativo da investigação. O caminho válido começa com '/'. A investigação é enviada para \<protocolo\>://\<host\>:\<porta\>caminho \<\> |
| Intervalo |Intervalo de investigação em segundos. Esse é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite de investigação em segundos. Se uma resposta válida não for recebida dentro desse período de tempo limite, a investigação será marcada como com falha. |
| Limite não íntegro |Contagem de repetição de investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite não íntegro. |

### <a name="solution"></a>Solução

Valide se a investigação de integridade personalizada está configurada corretamente como a tabela anterior. Além das etapas de solução de problemas anteriores, verifique também o seguinte:

* Verifique se a investigação foi especificada corretamente de acordo com o [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicativo estiver configurado para um único site, por padrão, o nome do host deverá ser especificado como `127.0.0.1`, a menos que configurado de outra forma na investigação personalizada.
* Verifique se uma chamada para http://\<host\>:\<porta\>caminho \<\> retorna um código de resultado HTTP 200.
* Certifique-se de que intervalo, tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se estiver usando uma investigação HTTPS, certifique-se de que o servidor back-end não exija SNI Configurando um certificado de fallback no próprio servidor back-end.

## <a name="request-time-out"></a>Tempo limite da solicitação

### <a name="cause"></a>Causa

Quando uma solicitação de usuário é recebida, o gateway de aplicativo aplica as regras configuradas à solicitação e a roteia para uma instância de pool de back-end. Ele aguarda um intervalo configurável de tempo para uma resposta da instância de back-end. Por padrão, esse intervalo é de **20** segundos. Se o gateway de aplicativo não receber uma resposta do aplicativo de back-end nesse intervalo, a solicitação do usuário receberá um erro 502.

### <a name="solution"></a>Solução

O gateway de aplicativo permite que você defina essa configuração por meio do BackendHttpSetting, que pode ser aplicado a diferentes pools. Diferentes pools de back-end podem ter BackendHttpSetting diferentes e um tempo limite de solicitação diferente configurado.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicativo não tiver VMs ou um conjunto de dimensionamento de máquinas virtuais configurado no pool de endereços de back-end, ele não poderá rotear nenhuma solicitação de cliente e enviará um erro de gateway inadequado.

### <a name="solution"></a>Solução

Verifique se o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do Portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter um pool de endereços de back-end não vazio. O exemplo a seguir mostra dois pools retornados que são configurados com um FQDN ou endereços IP para as VMs de back-end. O estado de provisionamento de BackendAddressPool deve ser ' Succeeded '.

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias não íntegras em BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias de BackendAddressPool não estiverem íntegras, o gateway de aplicativo não terá nenhum back-end para rotear a solicitação do usuário para. Esse também pode ser o caso quando as instâncias de back-end estão íntegras, mas não têm o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM na mesma VNet. Se configurado com um ponto de extremidade público, verifique se uma solicitação de navegador para o aplicativo Web é de serviço.

## <a name="next-steps"></a>Passos seguintes

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).

