---
title: Configurar uma rede Virtual para um Azure Premium Cache de Redis | Documentos da Microsoft
description: Saiba como criar e gerir o suporte de rede Virtual para o escalão Premium Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: d4b8fd6ccb3fc7cb2627d4bd3e103239181e4d9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994394"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar o suporte de rede Virtual para uma Cache do Azure Premium para Redis
A Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de tamanho de cache e funcionalidades, incluindo as funcionalidades do escalão Premium, tais como clustering, persistência e suporte de rede virtual. Uma VNet é uma rede privada na cloud. Quando uma Cache do Azure para a instância de Redis está configurada com uma VNet, não é publicamente endereçável e só pode ser acedida a partir de máquinas virtuais e aplicações dentro da VNet. Este artigo descreve como configurar o suporte de rede virtual para uma Cache do Azure de premium para a instância de Redis.

> [!NOTE]
> A Cache de Redis do Azure suporta clássicas e VNets do Resource Manager.
> 
> 

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que motivo VNet?
[Rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) implementação fornece maior segurança e isolamento para a sua Cache do Azure para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringem o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
Suporte da virtual Network (VNet) é configurado no **nova Cache do Azure para Redis** painel durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Assim que tiver selecionado um escalão de preço premium, pode configurar a integração de Redis VNet ao selecionar uma VNet que está na mesma subscrição e localização da sua cache. Para utilizar uma nova VNet, criá-lo primeiro ao seguir os passos em [criar uma rede virtual com o portal do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [criar uma rede virtual (clássico) com o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e, em seguida, voltar para o **Nova Cache do Azure para Redis** painel para criar e configurar a cache de premium.

Para configurar a VNet para a nova cache, clique em **rede Virtual** sobre o **novo Azure Cache de Redis** painel e selecione a VNet pretendida na lista pendente.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede pretendida do **sub-rede** pendente lista e especifique o pretendido **endereço IP estático**. Se estiver a utilizar uma VNet clássica a **endereço IP estático** campo é opcional e, se não for especificado, um é escolhido da sub-rede selecionada.

> [!IMPORTANT]
> Ao implementar uma Cache do Azure para Redis a uma VNet do Resource Manager, a cache tem de ser numa sub-rede dedicada que não contém outros recursos, exceto para a Cache do Azure para instâncias de Redis. Se for feita uma tentativa para implementar uma Cache do Azure para Redis a uma VNet do Resource Manager para uma sub-rede que contém outros recursos, a implementação falhar.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> O Azure reserva alguns endereços IP em cada sub-rede e não não possível utilizar estes endereços. Os endereços IP primeiros e últimos das sub-redes são reservados para conformidade com o protocolo, juntamente com três outros endereços utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Além dos endereços IP usados pela infra-estrutura de VNET do Azure, cada Redis instâncias nos endereços IP do sub-rede utiliza dois por partição horizontal e um endereço IP adicional para o Balanceador de carga. Uma cache não agrupado é considerada como ter uma partição.
> 
> 

Depois da cache é criada, pode ver a configuração para a VNet ao clicar **rede Virtual** partir a **menu recursos**.

![Rede virtual][redis-cache-vnet-info]

Para ligar à Cache para a instância de Redis do Azure ao utilizar uma VNet, especifique o nome de anfitrião da cache na cadeia de ligação, conforme mostrado no exemplo a seguir:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Cache do Azure para a VNet de Redis FAQ
A lista seguinte contém respostas para perguntas freqüentes sobre a Cache do Azure para dimensionar de Redis.

* Quais são alguns problemas comuns de configuração incorreta com a Cache do Azure para o Redis e VNets?
* [Como eu posso verificar minha cache está a funcionar numa VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Quando tentar ligar a minha Cache do Azure para o Redis numa VNET, por que estou recebendo um erro a indicar que o certificado remoto é inválido?
* [Pode utilizar VNets com uma cache standard ou basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Por que a criação de uma Cache do Azure para Redis falha em alguns sub-redes, mas não para outros?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todas as funcionalidades de cache funcionam ao hospedar um cache numa VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta com a Cache do Azure para o Redis e VNets?
Quando a Cache de Redis do Azure está alojada numa VNet, as portas nas tabelas seguintes são utilizadas. 

>[!IMPORTANT]
>Se as portas nas tabelas seguintes estão bloqueadas, a cache poderá não funcionar corretamente. Ter uma ou mais destas portas bloqueadas é o problema mais comum de uma configuração incorreta ao utilizar a Cache do Azure para o Redis numa VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Existem sete requisitos de porta de saída.

- Todas as ligações de saída à internet podem ser feitas por meio de um cliente do locais auditoria de dispositivo.
- Três das portas encaminham o tráfego para pontos finais do Azure, manutenção de armazenamento do Azure e o DNS do Azure.
- Os intervalos de portas restantes e para as comunicações internas de sub-rede de Redis. Não existem regras NSG de sub-rede são necessárias para as comunicações internas de sub-rede de Redis.

| Porta (s) | Direção | Protocolo de transporte | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Redis dependências no Azure armazenamento/PKI (Internet) | (Redis sub-rede) |* |
| 53 |Saída |TCP/UDP |Redis dependências no DNS (Internet/VNet) | (Redis sub-rede) |* |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) | (Redis sub-rede) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) | (Redis sub-rede) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |


#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Existem oito requisitos de intervalo de porta de entrada. Pedidos de entrada destes intervalos são a entrada de outros serviços hospedados na mesma VNET ou interno para as comunicações de sub-rede de Redis.

| Porta (s) | Direção | Protocolo de transporte | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com Redis, balanceamento de carga do Azure | (Redis sub-rede) | (Redis sub-rede), rede Virtual, o Balanceador de carga do Azure |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 8500 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Redis sub-rede) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede), o Balanceador de carga do Azure |
| 13000-13999 |Entrada |TCP |Comunicação de cliente aos Clusters do Redis, balanceamento de carga do Azure | (Redis sub-rede) |Rede virtual, o Balanceador de carga do Azure |
| 15000-15999 |Entrada |TCP |Comunicação do cliente para Clusters de Redis, o balanceamento de carga do Azure | (Redis sub-rede) |Rede virtual, o Balanceador de carga do Azure |
| 16001 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Redis sub-rede) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos de conectividade de rede VNET adicionais

Existem requisitos de conectividade de rede para a Cache do Azure para Redis que não podem ser cumpridos inicialmente numa rede virtual. A Cache de Redis do Azure requer que todos os itens seguintes para funcionar corretamente quando utilizado numa rede virtual.

* Conectividade de rede de saída para os pontos finais de armazenamento do Azure em todo o mundo. Isto inclui pontos de extremidade localizados na mesma região que a Cache do Azure para a instância de Redis, bem como pontos finais de armazenamento localizados na **outros** regiões do Azure. Pontos finais de armazenamento do Azure resolver sob os seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*e *file.core.windows.net*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com*, e *crl.microsoft.com*. Essa conectividade é necessária para suportar a funcionalidade SSL.
* A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos de extremidade e domínios mencionados no pontos anteriores. Estes requisitos de DNS podem ser cumpridos ao garantir uma infraestrutura de DNS válida é configurada e mantida para a rede virtual.
* Conectividade de rede de saída para as monitorização do Azure pontos finais seguintes, que resolver sob os seguintes domínios DNS: shoebox2-black.shoebox2.metrics.nsatc.net, Norte – prod2.prod2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, Leste-prod2.prod2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como eu posso verificar minha cache está a funcionar numa VNET?

>[!IMPORTANT]
>Ao ligar a uma Cache do Azure para a instância de Redis está alojada numa VNET, os clientes de cache tem de ser na mesma VNET ou numa VNET com o VNET peering ativado. Isto inclui quaisquer aplicações de teste ou o ping de ferramentas de diagnóstico. Independentemente de onde está alojado o aplicativo cliente, os grupos de segurança de rede tem de ser configurados, de modo a que o tráfego de rede do cliente tem permissão para aceder a instância de Redis.
>
>

Uma vez os requisitos de porta configurados conforme descrito na secção anterior, pode verificar se a cache está a funcionar, efetuando os passos seguintes.

- [Reiniciar](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não podem ser atingidas (conforme documentado no [requisitos de portas de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [requisitos de porta de saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), a cache não vai conseguir reiniciar com êxito.
- Assim que os nós de cache ser reiniciado (como reportado pelo Estado da cache no portal do Azure), pode executar os seguintes testes:
  - enviar pings para o ponto final da cache (através da porta 6380) de uma máquina que está na mesma VNET como a cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se o `tcping` ferramenta comunica que a porta está aberta, o cache está disponível para a ligação de clientes na VNET.

  - Outra forma de testar é criar um cliente de cache de teste (que pode ser uma simples aplicação de consola com stackexchange. redis) que liga à cache e adiciona e obtém alguns itens da cache. Instale o aplicativo de cliente de exemplo numa VM que está na mesma VNET como a cache e executá-lo para verificar a conectividade à cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Quando tentar ligar a minha Cache do Azure para o Redis numa VNET, por que estou recebendo um erro a indicar que o certificado remoto é inválido?

Ao tentar ligar a uma Cache do Azure para o Redis numa VNET, verá um erro de validação de certificado, como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que está a ligar o anfitrião, o endereço IP. Recomendamos que utilize o nome do anfitrião. Em outras palavras, utilize o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite a utilizar o endereço IP semelhante para a seguinte cadeia de ligação:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não conseguir resolver o nome DNS, algumas bibliotecas de cliente incluem opções de configuração como `sslHost` que é fornecido pelo cliente stackexchange. redis. Isto permite-lhe substituir o nome de anfitrião utilizada para validação do certificado. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Pode utilizar VNets com uma cache standard ou basic?
VNets só pode ser utilizadas com premium caches.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Por que a criação de uma Cache do Azure para Redis falha em alguns sub-redes, mas não para outros?
Se estiver a implementar uma Cache do Azure para Redis a uma VNet do Resource Manager, a cache tem de ser numa sub-rede dedicada que não contém a nenhum outro tipo de recurso. Se for feita uma tentativa para implementar uma Cache do Azure para Redis a uma sub-rede de VNet do Resource Manager que contém outros recursos, a implementação falhar. Tem de eliminar os recursos existentes dentro da sub-rede antes de poder criar uma nova Cache do Azure para Redis.

Pode implementar vários tipos de recursos a uma VNet clássica, desde que tem endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP em cada sub-rede e não não possível utilizar estes endereços. Os endereços IP primeiros e últimos das sub-redes são reservados para conformidade com o protocolo, juntamente com três outros endereços utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infra-estrutura de VNET do Azure, cada Redis instâncias nos endereços IP do sub-rede utiliza dois por partição horizontal e um endereço IP adicional para o Balanceador de carga. Uma cache não agrupado é considerada como ter uma partição.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todas as funcionalidades de cache funcionam ao hospedar um cache numa VNET?
Quando a sua cache faz parte de uma VNET, apenas os clientes na VNET podem aceder à cache. Como resultado, as seguintes funcionalidades de gestão de cache não funcionam neste momento.

* A consola do redis - porque a consola de Redis é executado no seu navegador local, o que está fora da VNET, não é possível ligar à cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>ExpressRoute de utilização com a Cache do Azure para Redis

Os clientes podem ligar-se um [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito para a sua infraestrutura de rede virtual, assim, expandir a sua rede no local para o Azure. 

Por predefinição, um circuito ExpressRoute criado recentemente não efetua o túnel forçado (anúncio de uma rota predefinida, 0.0.0.0/0) numa VNET. Como resultado, conectividade de Internet de saída é permitida diretamente a partir da VNET e aplicativos de cliente são capazes de ligar a outros pontos de extremidade do Azure, incluindo o Azure Cache de Redis.

No entanto uma configuração de cliente comum é usar o túnel forçado (anunciar uma rota predefinida) que força o tráfego de Internet de saída para o fluxo em vez disso, no local. Este fluxo de tráfego quebras de conectividade com a Cache do Azure para Redis, se o tráfego de saída é, em seguida, bloqueado no local, de modo a que a Cache do Azure para a instância de Redis não é capaz de comunicar com as respetivas dependências.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém a Cache do Azure para Redis. Um UDR define as rotas de sub-rede específica que serão cumpridas em vez da rota predefinida.

Se possível, é recomendado que utilize a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0 e por padrão, force túneis de todo o tráfego de saída no local.
* O UDR aplicado à sub-rede que contém a Cache do Azure para Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego de TCP/IP para a internet pública; Por exemplo, definindo o tipo de próximo salto "Internet".

O efeito combinado uma dessas etapas é que o nível de sub-rede UDR tem precedência sobre o ExpressRoute imposição de túnel, que garante o acesso de Internet de saída da Cache do Azure para Redis.

Ligar a uma Cache do Azure para a instância de Redis a partir de uma local do aplicativo através do ExpressRoute não é um cenário de uso típico devido a motivos de desempenho (para um melhor desempenho do Azure Cache de Redis que os clientes devem estar na mesma região que a Cache do Azure para Redis) .

>[!IMPORTANT] 
>As rotas definidas num UDR **tem** ser específico o suficiente para têm precedência sobre quaisquer rotas anunciadas pela configuração do ExpressRoute. O exemplo seguinte utiliza o intervalo de endereços abrangente 0.0.0.0/0 e assim pode potencialmente ser substituído por acidente anúncios de rota de utilização de intervalos de endereço mais específicos.

>[!WARNING]  
>A Cache de Redis do Azure não é suportada com configurações do ExpressRoute que **incorretamente anunciem transversalmente rotas do caminho de peering público para o caminho de peering privado**. Configurações do ExpressRoute com peering público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem incorretamente anunciados transversalmente no caminho de peering privado, o resultado é que todos os pacotes de rede de saída da Cache para a sub-rede de instância de Redis do Azure estão incorretamente imposição de túnel para rede no local de um cliente infraestrutura. Este fluxo de rede quebra Azure Cache de Redis. A solução para esse problema é parar anunciar transversalmente rotas do caminho de peering público para o caminho de peering privado.


Informações gerais sobre as rotas definidas pelo utilizador estão disponíveis neste [descrição geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, veja [visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passos Seguintes
Saiba como utilizar as funcionalidades de cache premium mais.

* [Introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

