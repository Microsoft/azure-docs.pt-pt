---
title: Resolver erros de Gateway inválido do Azure Application Gateway (502)
description: Saiba como resolver erros de 502 Gateway de aplicação
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697169"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de problemas de erros de gateway inválido no Gateway de aplicação

Saiba como resolver erros de gateway inválido (502) recebidos ao utilizar o Gateway de aplicação do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicação, um dos erros que poderá ver é "erro de servidor: 502 - o Servidor Web recebeu uma resposta inválida enquanto funcionava como um servidor de gateway ou proxy". Este erro pode acontecer pelos seguintes motivos principais:

* NSG, o UDR ou o DNS personalizado está a bloquear o acesso para membros do conjunto de back-end.
* VMs ou instâncias do conjunto de dimensionamento de máquina virtual de back-end não estão a responder para a sonda de estado de funcionamento padrão.
* Configuração inválida ou incorreta de sondas de estado de funcionamento personalizados.
* O Azure do Gateway de aplicação [conjunto de back-end não está configurado ou está vazio](#empty-backendaddresspool).
* Nenhuma das VMs ou instâncias num [conjunto de dimensionamento de máquina virtual estão em bom estado](#unhealthy-instances-in-backendaddresspool).
* [Problemas de conectividade ou limite de tempo do pedido](#request-time-out) com pedidos de utilizador.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema de DNS personalizado, rota definida pelo utilizador ou grupo de segurança de rede

### <a name="cause"></a>Causa

Se o acesso ao back-end estiver bloqueado devido a um NSG, o UDR ou o DNS personalizado, instâncias de gateway de aplicação não é possível alcançar o conjunto de back-end. Isso faz com que as falhas de pesquisa, resultando em 502 erros.

O NSG/UDR podem estar presente na sub-rede de gateway de aplicação ou a sub-rede onde as VMs de aplicação estão implementadas.

Da mesma forma, a presença de um DNS personalizado na VNet também pode causar problemas. Um FQDN utilizado para membros do agrupamento de back-end poderá não resolver corretamente pelo servidor DNS configurado do utilizador para a VNet.

### <a name="solution"></a>Solução

Valide a configuração de NSG, UDR e DNS ao efetuar os seguintes passos:

* Verifique os NSGs associados com a sub-rede do gateway de aplicação. Certifique-se de que a comunicação com o back-end não está bloqueada.
* Verifique a que UDR associado à sub-rede de gateway de aplicação. Certifique-se de que o UDR não é direcionar o tráfego para fora da sub-rede de back-end. Por exemplo, verificar para o encaminhamento para aplicações virtuais ou rotas predefinidas que está a ser anunciadas para a sub-rede do gateway de aplicação através do ExpressRoute/VPN de rede.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique NSG em vigor e a rota com a VM de back-end

```azurepowershell
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
Se estiver presente, certifique-se de que o servidor DNS pode resolver FQDN o membro de conjunto de back-end corretamente.

## <a name="problems-with-default-health-probe"></a>Problemas com a sonda de estado de funcionamento predefinida

### <a name="cause"></a>Causa

502 erros também podem ser indicadores freqüentes que a sonda de estado de funcionamento predefinido não é possível contactar a VMs de back-end.

Quando uma instância de gateway de aplicação é aprovisionada, este configura automaticamente uma sonda de estado de funcionamento padrão para cada BackendAddressPool usando propriedades do BackendHttpSetting. Sem intervenção do utilizador tem de definir esta pesquisa. Especificamente, quando uma regra de balanceamento de carga estiver configurada, é efetuada uma associação entre um BackendHttpSetting e um BackendAddressPool. Uma pesquisa predefinida está configurada para cada um dessas associações e o gateway de aplicação é iniciada uma ligação de verificação periódica do Estado de funcionamento para cada instância no BackendAddressPool nas portas especificada no elemento BackendHttpSetting. 

A tabela seguinte lista os valores associados a sonda de estado de funcionamento predefinida:

| Propriedade de pesquisa | Value | Descrição |
| --- | --- | --- |
| URL de Pesquisa |`http://127.0.0.1/` |Caminho do URL |
| Interval |30 |Intervalo de sonda em segundos |
| Tempo limite |30 |Tempo limite de sonda em segundos |
| Limiar com funcionamento incorreto |3 |Contagem de repetições de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

* Certifique-se de que um site predefinido está configurado e está à escuta em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente da 80, o site predefinido deve ser configurado para escutar nessa porta.
* A chamada para `http://127.0.0.1:port` deverá devolver um código de resultado HTTP de 200. Este deve ser devolvido dentro do período de tempo limite de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não há regras de firewall ou grupos de segurança do Azure rede, que bloqueia o tráfego de entrada ou de saída na porta configurada.
* Se as VMs clássicas do Azure ou serviço em nuvem é utilizado com um FQDN ou um IP público, certifique-se de que o correspondente [ponto final](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) é aberto.
* Se a VM é configurada através do Azure Resource Manager e estiver fora da VNet em que o gateway de aplicação é implementado, uma [grupo de segurança de rede](../virtual-network/security-overview.md) tem de ser configurado para permitir o acesso a porta pretendida.

## <a name="problems-with-custom-health-probe"></a>Problemas com a sonda de estado de funcionamento personalizados

### <a name="cause"></a>Causa

Sondas de estado de funcionamento personalizadas permitem maior flexibilidade para a predefinição de comportamento de pesquisa. Ao utilizar sondas personalizadas, pode configurar o intervalo de pesquisa, o URL, o caminho para testar e quantos respostas com falhas para aceitar antes de os marcar a instância de conjunto de back-end como mau estado de funcionamento.

As seguintes propriedades adicionais são adicionadas:

| Propriedade de pesquisa | Descrição |
| --- | --- |
| Name |Nome da sonda. Este nome é utilizado para fazer referência a sonda nas definições de HTTP de back-end. |
| Protocolo |Protocolo utilizado para enviar a sonda. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome de anfitrião para enviar a sonda. Aplicável apenas quando vários sites está configurada no gateway de aplicação. Isso é diferente do nome de anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa com "/". A sonda é enviada ao \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\> |
| Interval |Intervalo de sonda em segundos. Este é o intervalo de tempo entre dois sondas consecutivos. |
| Tempo limite |Sonda de tempo limite em segundos. Se não for recebida uma resposta de válida dentro deste período de tempo limite, a sonda está marcada como falhado. |
| Limiar com funcionamento incorreto |Contagem de repetições de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

Verifique se a sonda de estado de funcionamento personalizado está configurada corretamente, como a tabela anterior. Além dos passos de resolução de problemas anteriores, certifique-se também o seguinte:

* Certifique-se de que a sonda está especificada corretamente, de acordo a [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicação está configurado para um único site, por predefinição, o anfitrião o nome deve ser especificado como `127.0.0.1`, a menos que caso contrário, configuradas na sonda personalizada.
* Certifique-se de que uma chamada para http://\<host\>:\<porta\>\<caminho\> devolve um código de resultado HTTP de 200.
* Certifique-se de que intervalo de tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se utilizar uma sonda HTTPS, certifique-se de que o servidor de back-end não requer SNI ao configurar um certificado de contingência no próprio servidor de back-end.

## <a name="request-time-out"></a>Limite de tempo do pedido

### <a name="cause"></a>Causa

Quando é recebido um pedido de utilizador, o gateway de aplicação aplica as regras configuradas para o pedido e a encaminha para uma instância de conjunto de back-end. Ele aguarda um intervalo configurável de tempo para uma resposta a partir da instância de back-end. Por predefinição, é este intervalo **20** segundos. Se o gateway de aplicação não recebeu uma resposta da aplicação de back-end neste intervalo, o pedido de utilizador obtém um erro de 502.

### <a name="solution"></a>Solução

Gateway de aplicação permite-lhe configurar esta definição através de BackendHttpSetting, que pode ser aplicado, em seguida, para conjuntos diferentes. Diferentes conjuntos de back-end podem ter diferente BackendHttpSetting e um limite de tempo do pedido diferentes configurados.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicação não tem VMs ou conjunto de dimensionamento de máquina virtual configurado no conjunto de endereços de back-end, ele não é possível encaminhar qualquer solicitação do cliente e envia um erro de gateway inválido.

### <a name="solution"></a>Solução

Certifique-se de que o conjunto de endereços de back-end não está vazio. Isso pode ser feito através do PowerShell, CLI ou do portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o conjunto de endereços de back-end não vazia. O exemplo seguinte mostra dois conjuntos de devolvido que estão configurado com um FQDN ou um endereços IP para o VMs de back-end. O estado de aprovisionamento do BackendAddressPool deve ser "com êxito".

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

Se todas as instâncias de BackendAddressPool estiverem em mau estado de funcionamento, o gateway de aplicação não tem qualquer back-end para encaminhar o pedido de utilizador para. Isso também pode ser o caso quando as instâncias de back-end estão em bom estadas, mas não tem a aplicação necessária implementada.

### <a name="solution"></a>Solução

Certifique-se de que as instâncias estão em bom estadas e a aplicação está configurada corretamente. Verifique se as instâncias de back-end podem responder a um ping a partir de outra VM na mesma VNet. Se configurado com um ponto final público, certifique-se de que um pedido de navegador para o aplicativo web é serviceable na.

## <a name="next-steps"></a>Passos Seguintes

Se os passos anteriores não resolverem o problema, abra um [pedido de suporte](https://azure.microsoft.com/support/options/).

