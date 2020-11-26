---
title: Erros de Bad Gateway de resolução de problemas - Gateway de aplicação Azure
description: 'Saiba como resolver problemas o Erro do Servidor gateway da aplicação: 502 - O servidor Web recebeu uma resposta inválida enquanto atuava como porta de entrada ou servidor de procuração.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182942"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de problemas de erros de gateway incorreto no Gateway de Aplicação

Saiba como resolver erros de porta de entrada (502) recebidos quando utilizar o Gateway de Aplicação Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicações, um dos erros que pode ver é "Erro do Servidor: 502 - Servidor Web recebeu uma resposta inválida enquanto agia como um gateway ou servidor de procuração". Este erro pode acontecer pelas seguintes razões principais:

* NSG, UDR ou DNS personalizados estão bloqueando o acesso aos membros do pool backend.
* VMs de back-end ou casos de escala de máquina virtual definido não estão respondendo à sonda de saúde padrão.
* Configuração inválida ou incorreta das pesquisas do estado de funcionamento personalizadas.
* A piscina traseira do Azure Application Gateway [não está configurada ou vazia.](#empty-backendaddresspool)
* Nenhum dos VMs ou instâncias em [conjunto de escala de máquina virtual são saudáveis.](#unhealthy-instances-in-backendaddresspool)
* [Solicite problemas de tempo ou conectividade](#request-time-out) com pedidos do utilizador.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Grupo de Segurança de Rede, Rota Definida pelo Utilizador ou Emissão de DNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao backend for bloqueado por causa de um NSG, UDR ou DNS personalizados, as instâncias de gateway de aplicações não podem chegar ao pool de backend. Isto causa falhas na sonda, resultando em 502 erros.

O NSG/UDR pode estar presente quer na sub-rede do gateway de aplicação, quer na sub-rede onde os VMs de aplicação são implantados.

Da mesma forma, a presença de um DNS personalizado no VNet também pode causar problemas. Um FQDN utilizado para membros do pool backend pode não ser resolvido corretamente pelo servidor DNS configurado pelo utilizador para o VNet.

### <a name="solution"></a>Solução

Validar a configuração NSG, UDR e DNS através dos seguintes passos:

* Verifique os NSGs associados à sub-rede de gateway de aplicação. Certifique-se de que a comunicação para o backend não está bloqueada.
* Verifique a UDR associada à sub-rede do gateway de aplicação. Certifique-se de que o UDR não está a direcionar o tráfego para longe da sub-rede de backend. Por exemplo, verifique se o encaminhamento para aparelhos virtuais de rede ou rotas predefinidos estão a ser anunciados para a sub-rede de gateway de aplicações via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique o NSG eficaz e a rota com o VM backend

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
Se estiver presente, certifique-se de que o servidor DNS pode resolver corretamente o FQDN do membro do pool de backend.

## <a name="problems-with-default-health-probe"></a>Problemas com sonda de saúde padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes de que a sonda de saúde padrão não consegue alcançar VMs de back-end.

Quando uma instância de gateway de aplicação é a provisionada, configura automaticamente uma sonda de saúde predefinida a cada BackendAddressPool utilizando propriedades do BackendHttpSetting. Não é necessária nenhuma entrada do utilizador para definir esta sonda. Especificamente, quando uma regra de equilíbrio de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Uma sonda predefinida é configurada para cada uma destas associações e o gateway de aplicação inicia uma ligação periódica de verificação de saúde a cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela a seguir lista os valores associados à sonda de saúde padrão:

| Propriedade sonda | Valor | Descrição |
| --- | --- | --- |
| URL da sonda |`http://127.0.0.1/` |Caminho url |
| Intervalo |30 |Intervalo da sonda em segundos |
| Limite de Tempo Excedido |30 |Tempo de tempo da sonda em segundos |
| Limiar com funcionamento incorreto |3 |Contagem de sondagens. O servidor back-end é marcado para baixo depois que a contagem de falha da sonda consecutiva atinge o limiar insalubre. |

### <a name="solution"></a>Solução

* Certifique-se de que um local predefinido está configurado e está a ouvir a 127.0.0.1.
* Se o BackendHttpSetting especificar uma porta que não seja 80, o local predefinido deve ser configurado para ouvir nessa porta.
* A chamada `http://127.0.0.1:port` deve devolver um código de resultados HTTP de 200. Isto deve ser devolvido dentro do período de tempo de 30 segundos.
* Certifique-se de que a porta está aberta e que não existem regras de firewall ou grupos de segurança da rede Azure, que bloqueiam o tráfego de entrada ou saída na porta configurado.
* Se os VMs clássicos do Azure ou o Cloud Service forem utilizados com um FQDN ou um IP público, certifique-se de que o [ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente é aberto.
* Se o VM estiver configurado através do Azure Resource Manager e estiver fora do VNet onde o gateway de aplicação é implantado, um [Grupo de Segurança de Rede](../virtual-network/network-security-groups-overview.md) deve ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com sonda de saúde personalizada

### <a name="cause"></a>Causa

As sondas de saúde personalizadas permitem flexibilidade adicional ao comportamento de sondagem padrão. Quando utilizar sondas personalizadas, pode configurar o intervalo da sonda, o URL, o caminho a testar e quantas respostas falhadas a aceitar antes de marcar a instância da piscina de fundo como insalubre.

São adicionadas as seguintes propriedades adicionais:

| Propriedade sonda | Descrição |
| --- | --- |
| Nome |O nome da sonda. Este nome é utilizado para se referir à sonda nas definições HTTP de fundo. |
| Protocolo |Protocolo usado para enviar a sonda. A sonda utiliza o protocolo definido nas definições HTTP de back-end |
| Anfitrião |Nome do anfitrião para enviar a sonda. Aplicável apenas quando vários locais estiverem configurados no gateway de aplicações. Isto é diferente do nome do anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa a partir de '/'. A sonda é enviada para \<protocol\> \<host\> :\<port\>\<path\> |
| Intervalo |Intervalo da sonda em segundos. Este é o intervalo de tempo entre duas sondas consecutivas. |
| Limite de Tempo Excedido |Tempo de tempo de sonda em segundos. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda é marcada como falhada. |
| Limiar com funcionamento incorreto |Contagem de sondagens. O servidor back-end é marcado para baixo depois que a contagem de falha da sonda consecutiva atinge o limiar insalubre. |

### <a name="solution"></a>Solução

Valide que a Sonda de Saúde Personalizada está configurada corretamente como a tabela anterior. Para além das etapas anteriores de resolução de problemas, as seguintes medidas asseguram:

* Certifique-se de que a sonda está corretamente especificada de acordo com o [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicações estiver configurado para um único site, por predefinição o nome anfitrião deve ser especificado como `127.0.0.1` , salvo configuração em contrário na sonda personalizada.
* Certifique-se de que uma chamada para \<host\> http://: \<port\> \<path\> devolva um código de resultados HTTP de 200.
* Certifique-se de que o intervalo, o tempo e o unhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se utilizar uma sonda HTTPS, certifique-se de que o servidor backend não necessita de SNI configurando um certificado de recuo no próprio servidor de backend.

## <a name="request-time-out"></a>Solicitar tempo de 100 horas

### <a name="cause"></a>Causa

Quando um pedido de utilizador é recebido, o gateway de aplicação aplica as regras configuradas ao pedido e encaminha-o para uma instância de piscina de back-end. Aguarda um intervalo de tempo configurável para uma resposta da parte de trás. Por predefinição, este intervalo é **de 20** segundos. Se o gateway de aplicações não receber uma resposta da aplicação back-end neste intervalo, o pedido do utilizador recebe um erro de 502.

### <a name="solution"></a>Solução

O Gateway de Aplicação permite-lhe configurar esta definição através do BackendHttpSetting, que pode ser aplicado em diferentes piscinas. Diferentes piscinas traseiras podem ter diferentes BackendHttpSetting, e um tempo de tempo de pedido diferente configurado.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicações não tiver VMs ou escala de máquina virtual configurado na piscina de endereços back-end, não pode encaminhar qualquer pedido do cliente e enviar um erro de gateway errado.

### <a name="solution"></a>Solução

Certifique-se de que a piscina de endereços traseiros não está vazia. Isto pode ser feito através de PowerShell, CLI ou portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter um conjunto de endereços traseiros não vazios. O exemplo a seguir mostra duas piscinas devolvidas que são configuradas com um FQDN ou um endereço IP para os VMs de backend. O estado de provisionamento do BackendAddressPool deve ser "bem sucedido".

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

Se todas as instâncias do BackendAddressPool não forem saudáveis, então o gateway de aplicações não tem nenhum back-end para encaminhar o pedido do utilizador. Isto também pode ser o caso quando as instâncias de back-end são saudáveis, mas não têm a aplicação necessária implementada.

### <a name="solution"></a>Solução

Certifique-se de que as ocorrências são saudáveis e que a aplicação está devidamente configurada. Verifique se as instâncias de back-end podem responder a um ping de outro VM no mesmo VNet. Se configurado com um ponto final público, certifique-se de que um pedido de navegador para a aplicação web é útil.

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).