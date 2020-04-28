---
title: Problemas de Bad Gateway erros - Gateway de aplicação Azure
description: 'Saiba como resolver problemas erro do servidor do Gateway: 502 - O servidor web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74130483"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de problemas de erros de gateway incorreto no Gateway de Aplicação

Saiba como resolver problemas com erros de gateway (502) recebidos ao utilizar o Portal de Aplicações Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicação, um dos erros que pode ver é "Error Server: 502 - O servidor web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy". Este erro pode acontecer pelas seguintes razões principais:

* NSG, UDR ou Custom DNS estão bloqueando o acesso aos membros do pool backend.
* VMs de back-end ou instâncias de conjunto de escala de máquina virtual não estão a responder à sonda de saúde padrão.
* Configuração inválida ou incorreta das pesquisas do estado de funcionamento personalizadas.
* A piscina traseira do Azure Application Gateway [não está configurada ou vazia.](#empty-backendaddresspool)
* Nenhum dos VMs ou instâncias em conjunto de escala de [máquina virtual são saudáveis](#unhealthy-instances-in-backendaddresspool).
* [Solicite problemas](#request-time-out) de tempo ou conectividade com pedidos de utilizador.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Grupo de Segurança de Rede, Rota Definida pelo Utilizador ou problema dNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao backend for bloqueado por causa de um NSG, UDR ou DNS personalizado, as instâncias de gateway da aplicação não podem chegar à piscina de backend. Isto causa falhas na sonda, resultando em 502 erros.

O NSG/UDR pode estar presente na sub-rede de gateway de aplicação ou na subnet onde os VMs da aplicação são implantados.

Da mesma forma, a presença de um DNS personalizado na VNet também pode causar problemas. Um FQDN utilizado para os membros do pool de backend pode não resolver corretamente pelo servidor DNS configurado pelo utilizador para o VNet.

### <a name="solution"></a>Solução

Valide a configuração NSG, UDR e DNS, passando pelos seguintes passos:

* Verifique os NSGs associados à sub-rede de gateway de aplicação. Certifique-se de que a comunicação para apoiar não está bloqueada.
* Verifique a UDR associada à sub-rede de gateway de aplicação. Certifique-se de que o UDR não está a direcionar o tráfego para longe da sub-rede de backend. Por exemplo, verifique se o encaminhamento para aparelhos virtuais de rede ou rotas padrão estão a ser publicitadas na subnet de gateway da aplicação via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique o NSG eficaz e a rota com o VM traseiro

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique a presença de DNS personalizados no VNet. O DNS pode ser verificado olhando para os detalhes das propriedades VNet na saída.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se presente, certifique-se de que o servidor DNS pode resolver corretamente o FQDN do membro do pool de backend.

## <a name="problems-with-default-health-probe"></a>Problemas com sonda de saúde padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes de que a sonda de saúde padrão não consegue atingir VMs de back-end.

Quando uma instância de gateway de aplicação é aprovisionada, ele automaticamente configura uma sonda de saúde padrão para cada BackendAddressPool usando propriedades do BackendHttpSetting. Não é necessária nenhuma entrada do utilizador para definir esta sonda. Especificamente, quando uma regra de equilíbrio de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Uma sonda predefinida é configurada para cada uma destas associações e o gateway da aplicação inicia uma ligação periódica de verificação de saúde a cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela que se segue enumera os valores associados à sonda de saúde padrão:

| Propriedade da sonda | Valor | Descrição |
| --- | --- | --- |
| URL da sonda |`http://127.0.0.1/` |Caminho de URL |
| Intervalo |30 |Intervalo da sonda em segundos |
| Limite de Tempo Excedido |30 |Tempo de sonsão em segundos |
| Limiar com funcionamento incorreto |3 |Contagem de tentativas de nova contagem. O servidor de back-end é marcado para baixo após a contagem de falhas de sonda consecutiva atingir o limiar insalubre. |

### <a name="solution"></a>Solução

* Certifique-se de que um site predefinido está configurado e está a ouvir a 127.0.0.1.
* Se o BackendHttpSetting especificar uma porta que não seja 80, o site predefinido deve ser configurado para ouvir nessa porta.
* A chamada `http://127.0.0.1:port` deve devolver um código de resultados HTTP de 200. Isto deve ser devolvido dentro do período de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não existem regras de firewall ou Grupos de Segurança da Rede Azure, que bloqueiam o tráfego de entrada ou saída na porta configurada.
* Se os VMs clássicos do Azure ou o Cloud Service forem utilizados com um FQDN ou um IP público, certifique-se de que o [ponto final](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente é aberto.
* Se o VM estiver configurado através do Gestor de Recursos Azure e estiver fora do VNet onde o gateway da aplicação é implementado, um Grupo de Segurança de [Rede](../virtual-network/security-overview.md) deve ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com sonda de saúde personalizada

### <a name="cause"></a>Causa

As sondas de saúde personalizadas permitem flexibilidade adicional ao comportamento de sondagem padrão. Quando utilizar sondas personalizadas, pode configurar o intervalo da sonda, o URL, o caminho a testar e quantas respostas falhadas aceitar antes de marcar a instância de piscina de back-end como insalubre.

São adicionadas as seguintes propriedades adicionais:

| Propriedade da sonda | Descrição |
| --- | --- |
| Nome |Nome da sonda. Este nome é utilizado para se referir à sonda nas definições http back-end. |
| Protocolo |O protocolo costumava enviar a sonda. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome anfitrião para enviar a sonda. Aplicável apenas quando vários sites estiverem configurados no gateway da aplicação. Isto é diferente do nome de anfitrião vm. |
| Caminho |Caminho relativo da sonda. O caminho válido começa a partir de '/'. A sonda é \<\>enviada\<\>para\<\>\<o protocolo :// anfitrião: caminho portuário\> |
| Intervalo |Intervalo da sonda em segundos. Este é o intervalo de tempo entre duas sondas consecutivas. |
| Limite de Tempo Excedido |Intervalo da sonda em segundos. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda está marcada como falhada. |
| Limiar com funcionamento incorreto |Contagem de tentativas de nova contagem. O servidor de back-end é marcado para baixo após a contagem de falhas de sonda consecutiva atingir o limiar insalubre. |

### <a name="solution"></a>Solução

Valide que a Sonda de Saúde Personalizada está configurada corretamente como a tabela anterior. Além das etapas anteriores de resolução de problemas, certifique-se também das seguintes:

* Certifique-se de que a sonda está corretamente especificada de acordo com o [guia](application-gateway-create-probe-ps.md).
* Se o gateway da aplicação estiver configurado para um único `127.0.0.1`site, por padrão, o nome anfitrião deve ser especificado como , salvo configuração em contrário em sonda personalizada.
* Certifique-se de\<que\>\<uma\>\<chamada para http:// anfitrião: a via\> portuária devolve um código de resultado seletiva HTTP de 200.
* Certifique-se de que intervalo, timeout e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se utilizar uma sonda HTTPS, certifique-se de que o servidor de backend não necessita de SNI configurando um certificado de recuo no próprio servidor de backend.

## <a name="request-time-out"></a>Pedir tempo de descanso

### <a name="cause"></a>Causa

Quando um pedido de utilizador é recebido, o gateway da aplicação aplica as regras configuradas ao pedido e encaminha-o para uma instância de piscina de back-end. Aguarda um intervalo de tempo configurável para uma resposta da instância de fundo. Por defeito, este intervalo é de **20** segundos. Se o gateway da aplicação não receber uma resposta da aplicação back-end neste intervalo, o pedido do utilizador obtém um erro de 502.

### <a name="solution"></a>Solução

O Gateway da aplicação permite-lhe configurar esta definição através do BackendHttpSetting, que pode ser aplicado em diferentes piscinas. Diferentes piscinas de back-end podem ter backendHttpSetting diferente, e um tempo de tempo de pedido diferente configurado.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway da aplicação não tiver VMs ou conjunto de escala de máquina virtual configurado no conjunto de endereços de back-end, não pode encaminhar qualquer pedido do cliente e enviar um erro de gateway errado.

### <a name="solution"></a>Solução

Certifique-se de que a piscina de endereços traseiros não está vazia. Isto pode ser feito através de PowerShell, CLI ou portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter uma piscina de endereços não vazios. O exemplo seguinte mostra duas piscinas devolvidas que são configuradas com um FQDN ou um endereço IP para os VMs de backend. O estado de provisionamento do BackendAddressPool deve ser "bem sucedido".

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Casos pouco saudáveis em BackendAddressPool

### <a name="cause"></a>Causa

Se todos os casos de BackendAddressPool não forem saudáveis, então o gateway da aplicação não tem qualquer pedido de back-end para o utilizador. Isto também pode ser o caso quando as instâncias de back-end são saudáveis, mas não têm a aplicação necessária implementada.

### <a name="solution"></a>Solução

Certifique-se de que os casos são saudáveis e a aplicação está devidamente configurada. Verifique se as instâncias traseiras podem responder a um ping de outro VM no mesmo VNet. Se configurado com um ponto final público, certifique-se de que um pedido de navegador para a aplicação web é útil.

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/)

