---
title: Configurar uma rede virtual para um cache Premium do Azure para Redis | Microsoft Docs
description: Saiba como criar e gerenciar o suporte de rede virtual para o cache do Azure da camada Premium para instâncias de Redis
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
ms.openlocfilehash: 6fc17f08db5951a3d693c7a5e3d5556d848d2efb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075051"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar o suporte de rede virtual para um cache Premium do Azure para Redis
O cache do Azure para Redis tem diferentes ofertas de cache, que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo recursos da camada Premium, como clustering, persistência e suporte à rede virtual. Uma VNet é uma rede privada na nuvem. Quando um cache do Azure para instância Redis é configurado com uma VNet, ele não é publicamente endereçável e só pode ser acessado de máquinas virtuais e aplicativos na VNet. Este artigo descreve como configurar o suporte de rede virtual para um cache do Azure Premium para instância Redis.

> [!NOTE]
> O cache do Azure para Redis dá suporte a VNets clássicas e do Resource Manager.
> 
> 

Para obter informações sobre outros recursos de cache Premium, consulte [introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que VNet?
A implantação da [VNet (rede virtual) do Azure](https://azure.microsoft.com/services/virtual-network/) fornece segurança e isolamento aprimorados para o cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte à VNet (rede virtual) é configurado no **novo cache do Azure para a folha Redis** durante a criação do cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de selecionar um tipo de preço premium, você pode configurar a integração VNet Redis selecionando uma VNet que esteja na mesma assinatura e local que o cache. Para usar uma nova VNet, crie-a primeiro seguindo as etapas em [criar uma rede virtual usando o portal do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [criar uma rede virtual (clássica) usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e, em seguida, retorne para a folha **novo cache do Azure para Redis** para criar e configurar seu cache Premium.

Para configurar a VNet para o novo cache, clique em **rede virtual** na folha **novo cache do Azure para Redis** e selecione a VNet desejada na lista suspensa.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada na lista suspensa **sub-rede** e especifique o **endereço IP estático**desejado. Se você estiver usando uma VNet clássica, o campo **endereço IP estático** será opcional e, se nenhum for especificado, um será escolhido na sub-rede selecionada.

> [!IMPORTANT]
> Ao implantar um cache do Azure para Redis em uma VNet do Resource Manager, o cache deve estar em uma sub-rede dedicada que não contém outros recursos, exceto para o cache do Azure para instâncias do Redis. Se for feita uma tentativa de implantar um cache do Azure para Redis em uma VNet do Resource Manager para uma sub-rede que contenha outros recursos, a implantação falhará.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> O Azure reserva alguns endereços IP dentro de cada sub-rede e esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo, juntamente com mais três endereços usados para os serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como usar endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Além dos endereços IP usados pela infraestrutura de VNET do Azure, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Um cache não clusterizado é considerado como tendo um fragmento.
> 
> 

Depois que o cache é criado, você pode exibir a configuração da VNet clicando em **rede virtual** no **menu de recursos**.

![Rede virtual][redis-cache-vnet-info]

Para se conectar ao cache do Azure para a instância do Redis ao usar uma VNet, especifique o nome do host do seu cache na cadeia de conexão, conforme mostrado no exemplo a seguir:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>FAQ do cache do Azure para VNet Redis
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para o dimensionamento de Redis.

* Quais são alguns problemas comuns de configuração incorreta com o cache do Azure para Redis e VNets?
* [Como posso verificar se meu cache está funcionando em uma VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ao tentar se conectar ao meu cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?
* [Posso usar o VNets com um cache Standard ou básico?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Por que a criação de um cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todos os recursos de cache funcionam ao hospedar um cache em uma VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta com o cache do Azure para Redis e VNets?
Quando o cache do Azure para Redis é hospedado em uma VNet, as portas nas tabelas a seguir são usadas. 

>[!IMPORTANT]
>Se as portas nas tabelas a seguir forem bloqueadas, o cache poderá não funcionar corretamente. Ter uma ou mais dessas portas bloqueadas é o problema de configuração incorreta mais comum ao usar o cache do Azure para Redis em uma VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há nove requisitos de porta de saída.

- Todas as conexões de saída para a Internet podem ser feitas por meio de um dispositivo de auditoria local do cliente.
- Três das portas roteiam o tráfego para os pontos de extremidade do Azure que atendem ao armazenamento do Azure e ao DNS do Azure.
- Os intervalos de porta restantes e para comunicações de sub-rede Redis internas. Nenhuma regra de NSG de sub-rede é necessária para comunicações internas de sub-rede Redis.

| Porta (s) | Direção | Protocolo de transporte | Objetivo | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências do Redis no armazenamento do Azure/PKI (Internet) | (Sub-rede Redis) |* |
| 443 | Saída | TCP | Dependência Redis em Azure Key Vault | (Sub-rede Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências do Redis no DNS (Internet/VNet) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e replicação geográfica | (Sub-rede Redis) |(Sub-rede Redis) (Sub-rede par de réplica geográfica) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar a marca de serviço ' AzureKeyVault ' com grupos de segurança de rede do Resource Manager.

<sup>2</sup> esses endereços IP pertencentes à Microsoft são usados para resolver a VM do host que serve o DNS do Azure.

<sup>3</sup> não é necessário para sub-redes sem servidor DNS personalizado ou caches Redis mais recentes que ignoram o DNS personalizado.

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos de porta de pares de replicação geográfica

Se você estiver usando a replicação geocompleta entre caches em redes virtuais do Azure, observe que a configuração recomendada é desbloquear as portas 15000-15999 para toda a sub-rede nas direções de entrada e saída para ambos os caches, para que todos os componentes de réplica na sub-rede pode se comunicar diretamente entre si mesmo no caso de um failover geográfico futuro.

#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Há oito requisitos de intervalo de porta de entrada. As solicitações de entrada nesses intervalos são de entrada de outros serviços hospedados na mesma VNET ou internas para as comunicações de sub-rede Redis.

| Porta (s) | Direção | Protocolo de transporte | Objetivo | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com o Redis, balanceamento de carga do Azure | (Sub-rede Redis) | (Sub-rede Redis), rede virtual, Azure Load Balancer <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis), Azure Load Balancer |
| 13000-13999 |Entrada |TCP |Comunicação de cliente com clusters Redis, balanceamento de carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 15000-15999 |Entrada |TCP |Comunicação de cliente com clusters Redis, balanceamento de carga do Azure e replicação geográfica | (Sub-rede Redis) |Rede virtual, Azure Load Balancer (sub-rede par de réplica geográfica) |
| 16001 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar a marca de serviço ' AzureLoadBalancer ' (Gerenciador de recursos) (ou ' AZURE_LOADBALANCER ' para clássico) para criar as regras de NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos adicionais de conectividade de rede de VNET

Há requisitos de conectividade de rede para o cache do Azure para Redis que podem não ser atendidos inicialmente em uma rede virtual. O cache do Azure para Redis exige que todos os itens a seguir funcionem corretamente quando usados em uma rede virtual.

* Conectividade de rede de saída para pontos de extremidade de armazenamento do Azure em todo o mundo. Isso inclui pontos de extremidade localizados na mesma região que o cache do Azure para instância Redis, bem como pontos de extremidade de armazenamento localizados em **outras** regiões do Azure. Os pontos de extremidade do armazenamento do Azure são resolvidos nos seguintes domínios DNS: *Table.Core.Windows.net*, *BLOB.Core.Windows.net*, *Queue.Core.Windows.net*e *File.Core.Windows.net*. 
* Conectividade de rede de saída para *OCSP.msocsp.com*, *mscrl.Microsoft.com*e *CRL.Microsoft.com*. Essa conectividade é necessária para dar suporte à funcionalidade SSL.
* A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos garantindo que uma infraestrutura DNS válida seja configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes pontos de extremidade de monitoramento do Azure, que são resolvidos nos seguintes domínios DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como posso verificar se meu cache está funcionando em uma VNET?

>[!IMPORTANT]
>Ao se conectar a um cache do Azure para a instância Redis hospedada em uma VNET, os clientes de cache devem estar na mesma VNET ou em uma VNET com emparelhamento VNET habilitado. Isso inclui quaisquer aplicativos de teste ou ferramentas de ping de diagnóstico. Independentemente de onde o aplicativo cliente está hospedado, os grupos de segurança de rede devem ser configurados de modo que o tráfego de rede do cliente tenha permissão para alcançar a instância Redis.
>
>

Depois que os requisitos de porta são configurados conforme descrito na seção anterior, você pode verificar se o cache está funcionando executando as etapas a seguir.

- [Reinicialize](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não puderem ser atingidas (conforme documentado em [requisitos de porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e requisitos de porta de [saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), o cache não poderá ser reiniciado com êxito.
- Depois que os nós de cache forem reiniciados (conforme relatado pelo status do cache no portal do Azure), você poderá executar os seguintes testes:
  - execute ping no ponto de extremidade do cache (usando a porta 6380) de um computador que esteja na mesma VNET que o cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a ferramenta de `tcping` relatar que a porta está aberta, o cache estará disponível para conexão de clientes na VNET.

  - Outra maneira de testar é criar um cliente de cache de teste (que pode ser um aplicativo de console simples usando StackExchange. Redis) que se conecta ao cache e adiciona e recupera alguns itens do cache. Instale o aplicativo cliente de exemplo em uma VM que está na mesma VNET que o cache e execute-o para verificar a conectividade com o cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ao tentar se conectar ao meu cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?

Ao tentar se conectar a um cache do Azure para Redis em uma VNET, você verá um erro de validação de certificado, como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que você está se conectando ao host pelo endereço IP. É recomendável usar o nome do host. Em outras palavras, use o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite usar o endereço IP semelhante à seguinte cadeia de conexão:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não for possível resolver o nome DNS, algumas bibliotecas de cliente incluem opções de configuração como `sslHost` que é fornecida pelo cliente StackExchange. Redis. Isso permite que você substitua o nome do host usado para validação de certificado. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar o VNets com um cache Standard ou básico?
VNets só pode ser usado com caches Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Por que a criação de um cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
Se você estiver implantando um cache do Azure para Redis em uma VNet do Resource Manager, o cache deverá estar em uma sub-rede dedicada que não contenha nenhum outro tipo de recurso. Se for feita uma tentativa de implantar um cache do Azure para Redis em uma sub-rede VNet do Resource Manager que contenha outros recursos, a implantação falhará. Você deve excluir os recursos existentes dentro da sub-rede antes de poder criar um novo cache do Azure para Redis.

Você pode implantar vários tipos de recursos em uma VNet clássica, contanto que tenha endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP dentro de cada sub-rede e esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo, juntamente com mais três endereços usados para os serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como usar endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de VNET do Azure, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Um cache não clusterizado é considerado como tendo um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todos os recursos de cache funcionam ao hospedar um cache em uma VNET?
Quando o cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como resultado, os seguintes recursos de gerenciamento de cache não funcionam no momento.

* Console do Redis – como o console do Redis é executado no navegador local, que está fora da VNET, ele não pode se conectar ao seu cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usar o ExpressRoute com o cache do Azure para Redis

Os clientes podem conectar um circuito [do Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual, estendendo, assim, a rede local para o Azure. 

Por padrão, um circuito de ExpressRoute recém-criado não executa o túnel forçado (anúncio de uma rota padrão, 0.0.0.0/0) em uma VNET. Como resultado, a conectividade de Internet de saída é permitida diretamente da VNET e os aplicativos cliente são capazes de se conectar a outros pontos de extremidade do Azure, incluindo o cache do Azure para Redis.

No entanto, uma configuração de cliente comum é usar o túnel forçado (anunciar uma rota padrão), que força o tráfego de Internet de saída para o fluxo local. Esse fluxo de tráfego interrompe a conectividade com o cache do Azure para Redis se o tráfego de saída é bloqueado localmente, de modo que o cache do Azure para a instância do Redis não seja capaz de se comunicar com suas dependências.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contém o cache do Azure para Redis. Um UDR define rotas específicas de sub-rede que serão respeitadas em vez da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0 e, por padrão, túneis de todos os tráfegos de saída no local.
* O UDR aplicado à sub-rede que contém o cache do Azure para Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego TCP/IP para a Internet pública; por exemplo, definindo o tipo do próximo salto como "Internet".

O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo acesso de Internet de saída do cache do Azure para Redis.

Conectar-se a um cache do Azure para instância Redis de um aplicativo local usando o ExpressRoute não é um cenário de uso típico devido a motivos de desempenho (para melhor desempenho, o cache do Azure para clientes Redis deve estar na mesma região que o cache do Azure para Redis) .

>[!IMPORTANT] 
>As rotas definidas em um UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo a seguir usa o intervalo de endereços 0.0.0.0/0 amplo e, possivelmente, pode ser acidentalmente substituído por anúncios de rota usando intervalos de endereços mais específicos.

>[!WARNING]  
>Não há suporte para o cache do Azure para Redis com configurações de ExpressRoute que **incorretamente anunciam rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações do ExpressRoute que têm emparelhamento público configurado recebem anúncios de rota da Microsoft para um grande conjunto de Microsoft Azure intervalos de endereços IP. Se esses intervalos de endereços forem incorretamente anunciados no caminho de emparelhamento privado, o resultado será que todos os pacotes de saída de rede do cache do Azure para a sub-rede da instância do Redis serão incorretamente encapsulados em um túnel forçado para a rede local do cliente infra-estruturas. Esse fluxo de rede interrompe o cache do Azure para Redis. A solução para esse problema é parar as rotas de publicidade cruzada do caminho de emparelhamento público para o caminho de emparelhamento privado.


Informações básicas sobre rotas definidas pelo usuário estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [visão geral técnica do expressroute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
