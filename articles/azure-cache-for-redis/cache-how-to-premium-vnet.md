---
title: Configure uma Rede Virtual - Premium Azure Cache para Redis
description: Saiba como criar e gerir suporte à Rede Virtual para o seu Premium Tier Azure Cache para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 6c7c041565f6376e7f8b8b84f5076b30c1eec7bf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278119"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar suporte de rede virtual para um Cache Premium Azure para Redis
O Azure Cache for Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e funcionalidades do cache, incluindo funcionalidades de nível Premium, como clustering, persistência e suporte de rede virtual. Um VNet é uma rede privada na nuvem. Quando um cache azure para redis é configurado com um VNet, não é publicamente endereçado e só pode ser acedido a partir de máquinas e aplicações virtuais dentro do VNet. Este artigo descreve como configurar o suporte de rede virtual para um azure cache premium para a instância Redis.

> [!NOTE]
> Azure Cache para Redis suporta vNets clássicos e gestores de recursos.
> 
> 

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que VNet?
A implantação da [Rede Virtual Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) proporciona uma maior segurança e isolamento para o seu Azure Cache para Redis, bem como subredes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte da Rede Virtual (VNet) está configurado no **Novo Cache Azure para** lâmina Redis durante a criação de cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de ter selecionado um nível de preços premium, pode configurar a integração Redis VNet selecionando um VNet que se encontra na mesma subscrição e localização que a sua cache. Para utilizar um novo VNet, crie-o primeiro seguindo os passos em [Criar uma rede virtual utilizando o portal Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou Criar uma rede virtual [(clássica) utilizando o portal Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e depois voltar ao **Novo Cache Azure para** a lâmina Redis para criar e configurar a sua cache premium.

Para configurar o VNet para a sua nova cache, clique em **Rede Virtual** na Nova Cache Azure para lâmina **Redis** e selecione o VNet desejado a partir da lista de lançamentos.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada da lista de lançamentos da **Subnet.**  Se desejar, especifique um **endereço IP estático**. O campo de **endereços IP estático** é opcional e, se nenhum for especificado, é escolhido a partir da sub-rede selecionada.

> [!IMPORTANT]
> Ao implantar um Cache Azure para Redis para um Gestor de Recursos VNet, a cache deve estar numa subnet dedicada que não contenha outros recursos, exceto para o Azure Cache para os casos Redis. Se for feita uma tentativa de implantar um Cache Azure para Redis para uma VNet gestora de recursos para uma subnet que contenha outros recursos, a implementação falha.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> O Azure reserva alguns endereços IP dentro de cada subnet, e estes endereços não podem ser usados. Os primeiros e últimos endereços IP das subnets estão reservados para conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância Redis na sub-rede utiliza dois endereços IP por fragmento e um endereço IP adicional para o equilibrista de carga. Uma cache não agrupada é considerada como tendo um fragmento.
> 
> 

Após a criação da cache, pode visualizar a configuração do VNet clicando na **Rede Virtual** a partir do **menu Recurso**.

![Rede virtual][redis-cache-vnet-info]

Para ligar ao seu Cache Azure para a instância Redis quando utilizar um VNet, especifique o nome do anfitrião da sua cache na cadeia de ligação, tal como mostrado no seguinte exemplo:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Cache Azure para Redis VNet FAQ
A lista que se segue contém respostas a perguntas comumente feitas sobre o Cache Azure para escala redis.

* Quais são alguns problemas comuns de configuração com O Cache Azure para Redis e VNets?
* [Como posso verificar se a minha cache está a funcionar numa VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ao tentar ligar-me ao meu Azure Cache para Redis num VNET, porque é que estou a ter um erro a dizer que o certificado remoto é inválido?
* [Posso usar VNets com uma cache padrão ou básica?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Porque é que criar um Cache Azure para redis falha em algumas subredes, mas não noutras?
* [Quais são os requisitos de espaço da sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todas as funcionalidades de cache funcionam ao hospedar uma cache num VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração com O Cache Azure para Redis e VNets?
Quando o Azure Cache for Redis é hospedado num VNet, as portas das seguintes mesas são utilizadas. 

>[!IMPORTANT]
>Se as portas das tabelas seguintes estiverem bloqueadas, a cache pode não funcionar corretamente. Ter uma ou mais destas portas bloqueadas é o problema de configuração mais comum ao utilizar o Azure Cache para Redis num VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos portuário de saída

Há nove requisitos de saída. Os pedidos de saída nestas gamas são ou de saída para outros serviços necessários para que o cache funcione ou interno para a subnet Redis para comunicação interno. Para a geo-replicação, existem requisitos adicionais de saída para a comunicação entre as subredes da cache primária e secundária.

| Porto(s) | Direção | Protocolo de Transportes | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências redis em Armazenamento Azure/PKI (Internet) | (Sub-rede Redis) |* |
| 443 | Saída | TCP | Dependência de Redis no Cofre chave Azure | (Sub-rede Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências de Redis em DNS (Internet/VNet) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e Geo-Replication | (Sub-rede Redis) |(Sub-rede Redis) (Subnet de pares geo-réplica) |
| 6379-6380 |Saída |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar a etiqueta de serviço 'AzureKeyVault' com grupos de segurança de rede do Gestor de Recursos.

<sup>2</sup> Estes endereços IP pertencentes à Microsoft são utilizados para endereçar o VM do anfitrião que serve o DNS Azure.

<sup>3</sup> Não é necessário para subredes sem servidor DNS personalizado, ou caches redis mais recentes que ignoram DNS personalizados.

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos de porto de geo-replicação

Se estiver a utilizar a georeplicação entre caches em Redes Virtuais Azure, por favor note que a configuração recomendada é desbloquear portas 15000-15999 para toda a subrede em ambas as direções de entrada e saída para ambos os caches, de modo que todos os componentes de réplica na sub-rede pode comunicar diretamente uns com os outros mesmo em caso de uma futura geo-falha.

#### <a name="inbound-port-requirements"></a>Requisitos de portas de entrada

Existem oito requisitos de alcance de entrada. Os pedidos de entrada nestas gamas são de entrada de outros serviços hospedados no mesmo VNET ou internos para as comunicações de sub-rede Redis.

| Porto(s) | Direção | Protocolo de Transportes | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente à Redis, equilíbrio de carga azure | (Sub-rede Redis) | (Redis subnet), Rede Virtual, Equilíbrio de Carga Azure <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Equilíbrio de carga azul | (Sub-rede Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis), Equilíbrio de Carga Azure |
| 13000-13999 |Entrada |TCP |Comunicação do cliente aos Clusters Redis, equilíbrio de carga Azure | (Sub-rede Redis) |Rede Virtual, Equilíbrio de Carga Azure |
| 15000-15999 |Entrada |TCP |Comunicação do cliente aos Clusters Redis, Equilíbrio de Carga Azure e Geo-Replicação | (Sub-rede Redis) |Rede Virtual, Azure Load Balancer(Subnet de georéplica) |
| 16001 |Entrada |TCP/UDP |Equilíbrio de carga azul | (Sub-rede Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar a etiqueta de serviço 'AzureLoadBalancer' (Gestor de Recursos) (ou 'AZURE_LOADBALANCER' para clássico) para a autoria das regras da NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos adicionais de conectividade da rede VNET

Existem requisitos de conectividade de rede para O Cache Azure para Redis que podem não ser inicialmente cumpridos numa rede virtual. O Azure Cache for Redis requer que todos os seguintes itens funcionem corretamente quando utilizados dentro de uma rede virtual.

* Conectividade de rede de saída para pontos finais de Armazenamento Azure em todo o mundo. Isto inclui pontos finais localizados na mesma região que o Azure Cache para o caso Redis, bem como pontos finais de armazenamento localizados em **outras** regiões do Azure. Os pontos finais do Armazenamento Azure resolvem-se nos seguintes domínios DNS: *table.core.windows.net,* *blob.core.windows.net,* *queue.core.windows.net*e *file.core.windows.net.* 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com*e *crl.microsoft.com*. Esta conectividade é necessária para suportar a funcionalidade SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos finais e domínios mencionados nos pontos anteriores. Estes requisitos dNS podem ser cumpridos garantindo que uma infraestrutura DNS válida é configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes pontos finais de monitorização Azure, que se resolvem sob os seguintes domínios DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como posso verificar se a minha cache está a funcionar num VNET?

>[!IMPORTANT]
>Ao ligar-se a um Azure Cache para a instância Redis que esteja hospedado num VNET, os seus clientes de cache devem estar no mesmo VNET ou num VNET com o lúbis VNET ativado na mesma região Azure. O Global VNET Peering não é atualmente apoiado. Isto inclui quaisquer aplicações de teste ou ferramentas de diagnóstico de pinging. Independentemente do local onde a aplicação do cliente esteja hospedada, os grupos de segurança da rede devem ser configurados de modo a que o tráfego de rede do cliente seja autorizado a chegar à instância Redis.
>
>

Uma vez configurados os requisitos da porta como descrito na secção anterior, pode verificar se a sua cache está a funcionar executando os seguintes passos.

- [Reinicie](cache-administration.md#reboot) todos os nós de cache. Se não for possível alcançar todas as dependências de cache necessárias (conforme documentado nos [requisitos](cache-how-to-premium-vnet.md#inbound-port-requirements) da porta de entrada e nos requisitos da porta de [saída),](cache-how-to-premium-vnet.md#outbound-port-requirements)a cache não poderá reiniciar com sucesso.
- Uma vez reiniciados os nódosos de cache (conforme relatado pelo estado da cache no portal Azure), pode efetuar os seguintes testes:
  - ping o ponto final da cache (utilizando a porta 6380) de uma máquina que se encontra dentro do mesmo VNET que a cache, utilizando [o tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a ferramenta `tcping` reportar que a porta está aberta, a cache está disponível para ligação de clientes no VNET.

  - Outra forma de testar é criar um cliente de cache de teste (que poderia ser uma simples aplicação de consola usando StackExchange.Redis) que se conecta à cache e adiciona e recupera alguns itens da cache. Instale a aplicação do cliente da amostra num VM que esteja no mesmo VNET que a cache e execute-a para verificar a conectividade com a cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ao tentar ligar-me ao meu Azure Cache para Redis num VNET, porque é que estou a ter um erro a dizer que o certificado remoto é inválido?

Ao tentar ligar-se a um Azure Cache for Redis num VNET, vê-se um erro de validação de certificados como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que esteja a ligar-se ao hospedeiro pelo endereço IP. Recomendamos a utilização do nome de anfitrião. Por outras palavras, utilize o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite utilizar o endereço IP semelhante à seguinte cadeia de ligação:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não conseguir resolver o nome DNS, algumas bibliotecas de clientes incluem opções de configuração como `sslHost` que é fornecida pelo cliente StackExchange.Redis. Isto permite-lhe anular o nome de anfitrião utilizado para validação de certificado. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar VNets com uma cache padrão ou básica?
Os VNets só podem ser utilizados com caches premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Porque é que criar um Cache Azure para redis falha em algumas subredes, mas não noutras?
Se estiver a implantar um Cache Azure para Redis para um VNet do Gestor de Recursos, a cache deve estar numa subnet dedicada que não contenha outro tipo de recurso. Se for feita uma tentativa de implantar um Cache Azure para Redis para uma subnet VNet do Gestor de Recursos que contenha outros recursos, a implementação falha. Tem de eliminar os recursos existentes dentro da subnet antes de poder criar um novo Azure Cache para redis.

Pode implementar vários tipos de recursos para um VNet clássico, desde que tenha endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço da sub-rede?
O Azure reserva alguns endereços IP dentro de cada subnet, e estes endereços não podem ser usados. Os primeiros e últimos endereços IP das subnets estão reservados para conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância Redis na sub-rede utiliza dois endereços IP por fragmento e um endereço IP adicional para o equilibrista de carga. Uma cache não agrupada é considerada como tendo um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todas as funcionalidades de cache funcionam ao hospedar uma cache num VNET?
Quando a sua cache faz parte de um VNET, apenas os clientes do VNET podem aceder à cache. Como resultado, as seguintes funcionalidades de gestão de cache não funcionam neste momento.

* Consola Redis - Como a Consola Redis funciona no seu navegador local, que está fora do VNET, não pode ligar-se à sua cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Use ExpressRoute com Azure Cache para Redis

Os clientes podem ligar um circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual, estendendo assim a sua rede no local ao Azure. 

Por padrão, um circuito ExpressRoute recém-criado não realiza túneis forçados (anúncio de uma rota padrão, 0.0.0/0) num VNET. Como resultado, a conectividade de saída da Internet é permitida diretamente a partir do VNET e as aplicações de clientes são capazes de se conectar a outros pontos finais Azure, incluindo Azure Cache para Redis.

No entanto, uma configuração comum do cliente é usar túneis forçados (anunciar uma rota padrão) que força o tráfego de internet de saída a fluir no local. Este fluxo de tráfego rompe a conectividade com o Azure Cache for Redis se o tráfego de saída for bloqueado no local de tal forma que o azure cache for Redis por exemplo não é capaz de comunicar com as suas dependências.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém o Cache Azure para Redis. Um UDR define rotas específicas da sub-rede que serão honradas em vez da rota padrão.

Se possível, recomenda-se utilizar a seguinte configuração:

* A configuração ExpressRoute anuncia 0.0.0.0/0 e por predefinição de túneis de força todo o tráfego de saída no local.
* A UDR aplicada à subnet que contém o Azure Cache for Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego de TCP/IP para a internet pública; por exemplo, definindo o próximo tipo de lúpulo para "Internet".

O efeito combinado destas etapas é que o UDR de nível de subnet tem precedência sobre a via de túneis forçados da ExpressRoute, garantindo assim o acesso à Internet a partir do Azure Cache for Redis.

Ligar-se a um Azure Cache for Redis por exemplo a partir de uma aplicação no local utilizando o ExpressRoute não é um cenário de utilização típico devido a razões de desempenho (para melhor desempenho Os clientes Azure Cache para os clientes Redis devem estar na mesma região que o Azure Cache for Redis) .

>[!IMPORTANT] 
>As rotas definidas num UDR **devem** ser específicas o suficiente para prevalecer sobre quaisquer rotas anunciadas pela configuração ExpressRoute. O exemplo seguinte utiliza a gama de endereços de 0.0.0.0/0 e, como tal, pode potencialmente ser acidentalmente ultrapassado por anúncios de rotas utilizando faixas de endereço mais específicas.

>[!WARNING]  
>O Azure Cache for Redis não é suportado com configurações expressRoute que **incorretamente cruzam rotas do caminho**de espreitar o público para o caminho de espreitar privado . As configurações expressRoute que têm o público configurado, recebem anúncios de rotas da Microsoft para um grande conjunto de gamas de endereços IP microsoft Azure. Se estas gamas de endereços forem incorretamente publicitadas no caminho privado de observação, o resultado é que todos os pacotes de rede de saída da subnet azure Cache for Redis por exemplo são incorretamente submetidos a um túnel de força para a rede de instalações de um cliente no local infraestrutura. Este fluxo de rede quebra o Cache Azure para redis. A solução para este problema é parar as rotas de publicidade cruzada do caminho de espreitar o público para o caminho do público.


As informações de fundo sobre as rotas definidas pelo utilizador estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para mais informações sobre o ExpressRoute, consulte a visão geral técnica do [ExpressRoute.](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais funcionalidades de cache premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
