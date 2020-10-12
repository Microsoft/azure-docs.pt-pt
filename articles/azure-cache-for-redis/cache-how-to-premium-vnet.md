---
title: Configurar uma Rede Virtual - Cache Premium Azure para Redis
description: Saiba como criar e gerir o suporte da Rede Virtual para o seu Azure Cache de nível Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 82003ef84571c8e07982826124b33763c0e53194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88205562"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar suporte de rede virtual para uma cache premium Azure para Redis
O Azure Cache para Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e funcionalidades da cache, incluindo características de nível Premium, tais como clustering, persistência e suporte de rede virtual. Um VNet é uma rede privada na nuvem. Quando uma cache Azure para a instância Redis é configurada com um VNet, não é publicamente endereçada e só pode ser acedida a partir de máquinas e aplicações virtuais dentro do VNet. Este artigo descreve como configurar o suporte de rede virtual para um Azure Cache premium para a instância Redis.

> [!NOTE]
> A azure Cache para Redis suporta vNets clássicos e gestores de recursos.
> 
> 

## <a name="why-vnet"></a>Por que vNet?
A implementação [da Rede Virtual Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) proporciona uma maior segurança e isolamento para o seu Cache Azure para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte de Rede Virtual (VNet) está configurado na **Cache Novo Azure para** lâmina Redis durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Uma vez selecionado um nível de preços premium, pode configurar a integração do Redis VNet selecionando um VNet que está na mesma subscrição e localização que o seu cache. Para utilizar um novo VNet, crie-o primeiro seguindo os passos na [Criação de uma rede virtual utilizando o portal Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [Crie uma rede virtual (clássica) utilizando o portal Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e, em seguida, volte à Cache Novo **Azure para** a lâmina Redis para criar e configurar o seu cache premium.

Para configurar o VNet para o seu novo cache, clique em **Rede Virtual** na Cache **Novo Azure para** lâmina Redis e selecione o VNet desejado da lista de drop-down.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada da lista de drop-down **sub-rede.**  Se desejar, especifique um **endereço IP estático**. O campo **de endereços IP estático** é opcional e, se nenhum for especificado, um é escolhido a partir da sub-rede selecionada.

> [!IMPORTANT]
> Ao implementar uma Cache Azure para Redis para um Resource Manager VNet, a cache deve estar numa sub-rede dedicada que não contenha outros recursos, exceto a Azure Cache para instâncias Redis. Se for feita uma tentativa de implantar uma Cache Azure para Redis para um VNet gestor de recursos para uma sub-rede que contenha outros recursos, a implementação falha.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> O Azure reserva alguns endereços IP dentro de cada sub-rede, e estes endereços não podem ser utilizados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância Redis na sub-rede utiliza dois endereços IP por fragmento e um endereço IP adicional para o balançador de carga. Uma cache não agrupada é considerada como tendo um fragmento.
> 
> 

Após a criação da cache, pode visualizar a configuração do VNet clicando em **Rede Virtual** a partir do **menu Recursos**.

![Rede virtual][redis-cache-vnet-info]

Para ligar ao seu Cache Azure para a instância Redis ao utilizar um VNet, especifique o nome de anfitrião da sua cache na cadeia de ligação, como mostra o seguinte exemplo:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Cache Azure para Redis VNet FAQ
A lista que se segue contém respostas para perguntas comumente feitas sobre a Cache Azure para a escala de Redis.

* Quais são alguns problemas comuns de configuração errada com Azure Cache para Redis e VNets?
* [Como posso verificar se a minha cache está a funcionar num VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ao tentar ligar-me ao meu Azure Cache para redis num VNET, porque estou a receber um erro ao afirmar que o certificado remoto é inválido?
* [Posso usar VNets com uma cache padrão ou básica?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Porque é que a criação de um Cache Azure para redis falha em algumas sub-redes, mas não noutras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todas as funcionalidades de cache funcionam ao hospedar uma cache num VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração errada com Azure Cache para Redis e VNets?
Quando a Cache Azure para Redis é hospedada num VNet, as portas nas seguintes tabelas são utilizadas. 

>[!IMPORTANT]
>Se as portas das seguintes tabelas estiverem bloqueadas, a cache pode não funcionar corretamente. Ter uma ou mais destas portas bloqueadas é o problema de configuração errada mais comum quando se utiliza a cache de Azure para redis num VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há nove requisitos portuários de saída. Os pedidos de saída nestas gamas são de saída para outros serviços necessários para que a cache funcione ou internamente para a sub-rede Redis para comunicação internade. Para a geo-replicação, existem requisitos adicionais de saída para a comunicação entre sub-redes da cache primária e réplica.

| Porta(s) | Direção | Protocolo de Transporte | Objetivo | Local IP | Endereço IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências do Redis no Azure Storage/PKI (Internet) | (Sub-rede Redis) |* |
| 443 | Saída | TCP | Dependência de Redis no Cofre da Chave Azure | (Sub-rede Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências de Redis em DNS (Internet/VNet) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e Geo-Replication | (Sub-rede Redis) |(Sub-rede Redis) (Sub-rede peer de réplica de réplica) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar a etiqueta de serviço 'AzureKeyVault' com grupos de segurança da rede de gestores de recursos.

<sup>2</sup> Estes endereços IP pertencentes à Microsoft são utilizados para endereçar o VM anfitrião que serve DNS Azure.

<sup>3</sup> Não é necessário para sub-redes sem servidor DNS personalizado, ou caches redis mais recentes que ignoram DNS personalizados.

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos da porta do elemento de rede de georreplicação

Se estiver a utilizar georeplicação entre caches em Redes Virtuais Azure, tenha em atenção que a configuração recomendada é desbloquear as portas 15000-15999 para toda a sub-rede em ambas as direções de entrada e saída para ambas as caches, de modo a que todos os componentes de réplica na sub-rede possam comunicar diretamente uns com os outros mesmo em caso de uma futura geo-falha.

#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Existem oito requisitos de gama portuária de entrada. Os pedidos de entrada nestas gamas são de entrada de outros serviços alojados no mesmo VNET ou internos para as comunicações de sub-rede Redis.

| Porta(s) | Direção | Protocolo de Transporte | Objetivo | Local IP | Endereço IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com Redis, Azure load balance | (Sub-rede Redis) | (Sub-rede Redis), Rede Virtual, Balançador de Carga Azure <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis), Equilibrador de Carga Azure |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Redis Clusters, Azure load balance | (Sub-rede Redis) |Rede Virtual, Equilibrador de Carga Azure |
| 15000-15999 |Entrada |TCP |Comunicação do cliente com Redis Clusters, Azure load Balanceing, e Geo-Replication | (Sub-rede Redis) |Rede Virtual, Balançador de Carga Azure(Sub-rede de pares geo-réplica) |
| 16001 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar a Etiqueta de Serviço 'AzureLoadBalancer' (ou 'AZURE_LOADBALANCER' para clássico) para a autoria das regras NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos adicionais de conectividade da rede VNET

Existem requisitos de conectividade de rede para Azure Cache para Redis que podem não ser inicialmente cumpridos numa rede virtual. O Azure Cache para Redis requer que todos os seguintes itens funcionem corretamente quando utilizados numa rede virtual.

* Conectividade de rede de saída para pontos finais do Azure Storage em todo o mundo. Isto inclui pontos finais localizados na mesma região que a Cache Azure para a instância Redis, bem como pontos finais de armazenamento localizados em **outras** regiões do Azure. Os pontos finais de armazenamento Azure resolvem-se nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*e *file.core.windows.net*. 
* Conectividade de rede de saída para *ocsp.msocsp.com,* *mscrl.microsoft.com*e *crl.microsoft.com*. Esta conectividade é necessária para suportar a funcionalidade TLS/SSL.
* A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos finais e domínios mencionados nos pontos anteriores. Estes requisitos dns podem ser cumpridos garantindo que uma infraestrutura de DNS válida é configurada e mantida para a rede virtual.
* Conectividade de rede de saída aos seguintes pontos finais de Monitorização Azure, que resolvem sob os seguintes domínios DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como posso verificar se a minha cache está a funcionar num VNET?

>[!IMPORTANT]
>Ao ligar-se a uma Cache Azure para o caso Redis que esteja hospedada num VNET, os seus clientes de cache devem estar no mesmo VNET ou num VNET com o seu porte VNET ativado dentro da mesma região Azure. O VNET Peering global não é suportado atualmente. Isto inclui quaisquer aplicações de teste ou ferramentas de diagnóstico de pinging. Independentemente do local onde a aplicação do cliente está hospedada, os grupos de segurança da Rede devem ser configurados de modo a que o tráfego de rede do cliente seja autorizado a chegar à instância Redis.
>
>

Uma vez configurados os requisitos da porta, conforme descrito na secção anterior, pode verificar se o seu cache está a funcionar executando os seguintes passos.

- [Reinicie](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não puderem ser alcançadas (conforme documentado nos [requisitos da porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [requisitos de porta de saída),](cache-how-to-premium-vnet.md#outbound-port-requirements)a cache não será capaz de reiniciar com sucesso.
- Uma vez reiniciados os nó de cache (conforme relatado pelo estado da cache no portal Azure), pode efetuar os seguintes testes:
  - ping o ponto final da cache (utilizando a porta 6380) de uma máquina que se encontra dentro do mesmo VNET que o cache, utilizando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a `tcping` ferramenta reportar que a porta está aberta, a cache está disponível para ligação de clientes no VNET.

  - Outra forma de testar é criar um cliente de cache de teste (que poderia ser uma aplicação de consola simples usando StackExchange.Redis) que se conecta à cache e adiciona e recupera alguns itens da cache. Instale a aplicação do cliente da amostra num VM que esteja no mesmo VNET que o cache e execute-o para verificar a conectividade com a cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ao tentar ligar-me ao meu Azure Cache para redis num VNET, porque estou a receber um erro ao afirmar que o certificado remoto é inválido?

Ao tentar ligar-se a uma Cache Azure para Redis num VNET, vê um erro de validação de certificados como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que esteja a ligar-se ao anfitrião pelo endereço IP. Recomendamos a utilização do nome de anfitrião. Por outras palavras, utilize o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite utilizar o endereço IP semelhante ao seguinte fio de ligação:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não conseguir resolver o nome DNS, algumas bibliotecas de clientes incluem opções de configuração como `sslHost` as que são fornecidas pelo cliente StackExchange.Redis. Isto permite-lhe anular o nome de anfitrião utilizado para validação de certificados. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar VNets com uma cache padrão ou básica?
Os VNets só podem ser utilizados com caches premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Porque é que a criação de um Cache Azure para redis falha em algumas sub-redes, mas não noutras?
Se estiver a implementar uma Cache Azure para Redis para um VNet do Gestor de Recursos, a cache deve estar numa sub-rede dedicada que não contenha outro tipo de recurso. Se for feita uma tentativa de implantar uma Cache Azure para Redis numa sub-rede VNet do Gestor de Recursos que contenha outros recursos, a implementação falha. Tem de eliminar os recursos existentes dentro da sub-rede antes de poder criar uma nova Cache Azure para Redis.

Pode implementar vários tipos de recursos para um VNet clássico, desde que tenha endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP dentro de cada sub-rede, e estes endereços não podem ser utilizados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Para além dos endereços IP utilizados pela infraestrutura Azure VNET, cada instância Redis na sub-rede utiliza dois endereços IP por fragmento e um endereço IP adicional para o balançador de carga. Uma cache não agrupada é considerada como tendo um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todas as funcionalidades de cache funcionam ao hospedar uma cache num VNET?
Quando a sua cache faz parte de um VNET, apenas os clientes do VNET podem aceder à cache. Como resultado, as seguintes funcionalidades de gestão de cache não funcionam neste momento.

* Consola Redis - Como a Consola Redis funciona no seu navegador local, que está fora do VNET, não consegue ligar-se à sua cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Use ExpressRoute com Cache Azure para Redis

Os clientes podem ligar um circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual, estendendo assim a sua rede no local ao Azure. 

Por predefinição, um circuito ExpressRoute recém-criado não realiza túneis forçados (anúncio de uma rota padrão, 0.0.0.0/0) num VNET. Como resultado, a conectividade de saída da Internet é permitida diretamente a partir do VNET e as aplicações do cliente são capazes de se conectar a outros pontos finais Azure, incluindo Azure Cache para Redis.

No entanto, uma configuração comum do cliente é usar túneis forçados (anunciar uma rota padrão) que força o tráfego de saída da Internet a fluir no local. Este fluxo de tráfego quebra a conectividade com a Cache Azure para a Redis se o tráfego de saída for bloqueado no local de modo a que a cache Azure para a instância Redis não seja capaz de comunicar com as suas dependências.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém a Cache Azure para Redis. Um UDR define rotas específicas da sub-rede que serão honradas em vez da rota padrão.

Se possível, recomenda-se a utilização da seguinte configuração:

* A configuração ExpressRoute anuncia 0.0.0.0/0 e por defeito os túneis de força de saída em todos os locais.
* A UDR aplicada à sub-rede que contém a Cache Azure para Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego TCP/IP para a internet pública; por exemplo, definindo o próximo tipo de lúpulo para 'Internet'.

O efeito combinado destes passos é que o UDR de nível de sub-rede tem precedência sobre o túnel forçado ExpressRoute, garantindo assim o acesso à Internet de saída a partir da Cache Azure para Redis.

Ligar-se a uma cache Azure para redis a partir de uma aplicação no local usando ExpressRoute não é um cenário de utilização típico devido a razões de desempenho (para melhor desempenho, Azure Cache para clientes Redis deve estar na mesma região que a Cache Azure para Redis).

>[!IMPORTANT] 
>As rotas definidas numa UDR **devem** ser específicas o suficiente para ter precedência sobre quaisquer rotas anunciadas pela configuração ExpressRoute. O exemplo a seguir utiliza a ampla gama de endereços 0.0.0.0/0 e, como tal, pode potencialmente ser ultrapassado acidentalmente por anúncios de rotas utilizando intervalos de endereços mais específicos.

>[!WARNING]  
>A Azure Cache for Redis não é suportado com configurações ExpressRoute que **incorretamente cruzam rotas do caminho de observação pública para o caminho de observação privada**. As configurações ExpressRoute que têm o olhar público configurado, recebem anúncios de rotas da Microsoft para um grande conjunto de gamas de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem incorretamente cruzados na via de observação privada, o resultado é que todos os pacotes de rede de saída da cache Azure para a sub-rede da Redis instance são incorretamente escavados à força para a infraestrutura de rede de um cliente no local. Este fluxo de rede quebra a cache Azure para Redis. A solução para este problema é parar as rotas de publicidade cruzada do caminho de observação pública para o caminho de observação privado.


Informações de fundo sobre rotas definidas pelo utilizador estão disponíveis nesta [visão geral.](../virtual-network/virtual-networks-udr-overview.md)

Para obter mais informações sobre o ExpressRoute, consulte [a visão técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
