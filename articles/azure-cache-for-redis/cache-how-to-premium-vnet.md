---
title: Configure uma rede virtual - Cache Azure de nível premium para a instância Redis
description: Saiba como criar e gerir o suporte de rede virtual para o seu Azure Cache de nível Premium para instâncias Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 908254fec0d9e92b0e30c2e4968c3c505bbbdbf8
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833835"
---
# <a name="configure-virtual-network-support-for-a-premium-tier-azure-cache-for-redis-instance"></a>Configure suporte de rede virtual para uma Cache Azure de nível Premium para a instância Redis

A Azure Cache para Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e características da cache. As funcionalidades de nível premium incluem clustering, persistência e suporte à rede virtual. Uma rede virtual é uma rede privada na nuvem. Quando um Azure Cache para o caso Redis é configurado com uma rede virtual, não é publicamente endereçado e só pode ser acedido a partir de máquinas virtuais e aplicações dentro da rede virtual. Este artigo descreve como configurar o suporte de rede virtual para um Azure Cache de nível Premium para a instância Redis.

> [!NOTE]
> A Azure Cache para Redis suporta tanto o modelo de implementação clássico como as redes virtuais Azure Resource Manager.
> 

## <a name="why-virtual-network"></a>Porquê a Rede Virtual?

A implementação [da Rede Virtual Azure](https://azure.microsoft.com/services/virtual-network/) proporciona uma maior segurança e isolamento para o seu Azure Cache para a instância Redis, juntamente com sub-redes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual

O suporte de rede virtual está configurado na **Cache New Azure para o** painel Redis durante a criação da cache.

1. Para criar uma cache de nível Premium, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso**. Além de criar caches no portal Azure, também pode criá-los utilizando modelos de Gestor de Recursos, PowerShell ou o Azure CLI. Para obter mais informações sobre como criar uma Cache Azure para a instância Redis, consulte [Criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Screenshot que mostra Criar um recurso.":::
   
1. Na página **Nova,** selecione **Bases de Dados.** Em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Screenshot que mostra a seleção de Azure Cache para Redis.":::

1. Na página **New Redis Cache,** configufique as definições para a sua nova cache de nível Premium.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenham apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será *\<DNS name> .redis.cache.windows.net*. |
   | **Subscrição** | Selecione a sua subscrição a partir da lista de drop-down. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. |
   | **Grupo de recursos** | Selecione um grupo de recursos da lista de drop-down ou selecione **Criar novo** e introduza um novo nome de grupo de recursos. | O nome do grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. |
   | **Localização** | Selecione uma localização da lista de drop-down. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Tipo cache** |Selecione uma cache de nível Premium da lista de drop-down para configurar funcionalidades de nível Premium. Para mais informações, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [a Cache Azure para ver visão geral do Redis](cache-overview.md). |

1. Selecione o **separador 'Rede'** ou selecione o botão **de Rede** na parte inferior da página.

1. No **separador 'Rede',** selecione **Redes Virtuais** como método de conectividade. Para utilizar uma nova rede virtual, crie-a primeiro seguindo os passos na [Criação de uma rede virtual utilizando o portal Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [Crie uma rede virtual (clássica) utilizando o portal Azure.](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) Em seguida, volte à **Cache New Azure para o** painel Redis para criar e configurar a sua cache de nível Premium.

   > [!IMPORTANT]
   > Quando implementar a Azure Cache para Redis numa rede virtual do Resource Manager, a cache deve estar numa sub-rede dedicada que não contenha outros recursos, exceto a Azure Cache para instâncias Redis. Se tentar implantar uma cache Azure para a instância Redis para uma sub-rede de rede virtual do Gestor de Recursos que contenha outros recursos, a implementação falha.
   > 
   > 

   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Rede virtual** | Selecione a sua rede virtual a partir da lista de drop-down. | Selecione uma rede virtual que esteja na mesma subscrição e localização que o seu cache. |
   | **Sub-rede** | Selecione a sua sub-rede na lista de drop-down. | O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Deve ser contido no espaço de endereço da rede virtual. |
   | **Endereço IP estático** | (Opcional) Insira um endereço IP estático. | Se não especificar um endereço IP estático, um endereço IP é escolhido automaticamente. |

   > [!IMPORTANT]
   > O Azure reserva alguns endereços IP dentro de cada sub-rede, e estes endereços não podem ser utilizados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Para além dos endereços IP utilizados pela infraestrutura de rede virtual Azure, cada Cache Azure para redis na sub-rede utiliza dois endereços IP por fragmento e um endereço IP adicional para o equilibrador de carga. Uma cache não aglomerada é considerada como tendo um fragmento.
   > 

1. Selecione o **seguinte: Separador avançado** ou selecione o botão **Seguinte: Avançado** na parte inferior da página.

1. No separador **Avançado** para uma instância de cache de nível Premium, configufique as definições para a porta não-TLS, clustering e persistência de dados.

1. Selecione o **separador Seguinte: Etiquetas** ou selecione o botão **Seguinte: Tags** na parte inferior da página.

1. Opcionalmente, no separador **Tags, insira** o nome e o valor se quiser categorizar o recurso.

1. Selecione **Rever + criar**. É levado para o **separador 'Rever +' onde** o Azure valida a sua configuração.

1. Depois de aparecer a mensagem **de validação** verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada. Após a criação da cache, pode visualizar a configuração da rede virtual selecionando **a Rede Virtual** a partir do menu **Recursos.**

![Rede virtual][redis-cache-vnet-info]

Para ligar ao seu Cache Azure para a instância Redis quando utilizar uma rede virtual, especifique o nome de anfitrião da sua cache na cadeia de ligação, como mostra o seguinte exemplo:

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

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Cache Azure para Redis rede virtual FAQ

A lista que se segue contém respostas para perguntas comumente feitas sobre Azure Cache para o escalonamento de Redis.

* Quais são alguns problemas comuns de configuração errada com Azure Cache para Redis e redes virtuais?
* [Como posso verificar se a minha cache está a funcionar numa rede virtual?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Quando tento ligar-me ao meu Azure Cache para o caso Redis numa rede virtual, porque é que recebo um erro ao afirmar que o certificado remoto é inválido?
* [Posso utilizar redes virtuais com uma cache padrão ou básica?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Porque é que a criação de uma Cache Azure para o caso Redis falha em algumas sub-redes, mas não noutras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todas as funcionalidades de cache funcionam quando uma cache é hospedada numa rede virtual?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Quais são alguns problemas comuns de configuração errada com Azure Cache para Redis e redes virtuais?

Quando o Azure Cache para Redis é hospedado numa rede virtual, as portas das seguintes tabelas são utilizadas.

>[!IMPORTANT]
>Se as portas das seguintes tabelas estiverem bloqueadas, a cache pode não funcionar corretamente. Ter uma ou mais destas portas bloqueadas é o problema de configuração errada mais comum quando se utiliza a Cache Azure para Redis numa rede virtual.
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há nove requisitos portuários de saída. Os pedidos de saída nestas gamas são de saída para outros serviços necessários para que a cache funcione ou internamente para a sub-rede Redis para comunicação internade. Para a geo-replicação, existem requisitos adicionais de saída para a comunicação entre sub-redes da cache primária e réplica.

| Portas | Direção | Protocolo de transporte | Objetivo | Local IP | Endereço IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências do Redis no Azure Storage/PKI (internet) | (Sub-rede Redis) |* <sup>4</sup> |
| 443 | Saída | TCP | Dependência de Redis no Cofre da Chave Azure e monitor Azure | (Sub-rede Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências do Redis em DNS (internet/rede virtual) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e geo-replicação | (Sub-rede Redis) |(Sub-rede Redis) (Sub-rede peer de réplica de réplica) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar as tags de serviço AzureKeyVault e AzureMonitor com grupos de segurança de rede Resource Manager (NSGs).

<sup>2</sup> Estes endereços IP pertencentes à Microsoft são utilizados para dirigir-se ao VM anfitrião que serve DNS Azure.

<sup>3</sup> Esta informação não é necessária para sub-redes sem servidor DNS personalizado ou caches redis mais recentes que ignoram DNS personalizados.

<sup>4</sup> Para obter mais informações, consulte [requisitos adicionais de conectividade da rede virtual](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos da porta do elemento de rede de georreplicação

Se estiver a utilizar a geo-replicação entre caches em redes virtuais Azure, desbloqueie as portas 15000-15999 para toda a sub-rede em ambas as direções de entrada *e* saída para ambos os caches. Com esta configuração, todos os componentes de réplica na sub-rede podem comunicar-se diretamente uns com os outros, mesmo que haja uma futura falha de geo-failover.

#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Existem oito requisitos de gama portuária de entrada. Os pedidos de entrada nestas gamas são de entrada de outros serviços alojados na mesma rede virtual ou internos para as comunicações de sub-rede Redis.

| Portas | Direção | Protocolo de transporte | Objetivo | Local IP | Endereço IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com Redis, Azure load balance | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede de clientes), AzureLoadBalancer <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 10221-10231 |Entrada |TCP |Comunicação do cliente com Redis Clusters, comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis), AzureLoadBalancer, (sub-rede do cliente) |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Redis Clusters, Azure load balance | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede de clientes), AzureLoadBalancer |
| 15000-15999 |Entrada |TCP |Comunicação do cliente com Redis Clusters, equilíbrio de carga Azure e geo-replicação | (Sub-rede Redis) | (sub-rede Redis), (sub-rede de clientes), AzureLoadBalancer, (sub-rede de pares de réplicas de geo-réplica) |
| 16001 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> Pode utilizar a etiqueta de serviço AzureLoadBalancer para Gestor de Recursos ou AZURE_LOADBALANCER para o modelo de implementação clássico para a autoria das regras NSG.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Requisitos adicionais de conectividade da rede virtual

Existem requisitos de conectividade de rede para Azure Cache para Redis que podem não ser inicialmente cumpridos numa rede virtual. O Azure Cache for Redis requer que todos os seguintes itens funcionem corretamente quando utilizados numa rede virtual:

* Conectividade de rede de saída para pontos finais do Azure Storage em todo o mundo. Estão incluídos pontos finais localizados na mesma região que a Cache Azure para a instância Redis e pontos finais de armazenamento localizados *noutras* regiões de Azure. Os pontos finais de armazenamento Azure resolvem-se nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.
* A conectividade da rede de saída para *ocsp.digicert.com,* *crl4.digicert.com,* *ocsp.msocsp.com,* *mscrl.microsoft.com, crl3.digicert.com,* *cacerts.digicert.com,* *oneocsp.microsoft.com* e *crl.microsoft.com.*  Esta conectividade é necessária para suportar a funcionalidade TLS/SSL.
* A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos finais e domínios mencionados nos pontos anteriores. Estes requisitos dns podem ser cumpridos garantindo que uma infraestrutura de DNS válida é configurada e mantida para a rede virtual.
* A conectividade da rede de saída aos seguintes pontos finais do Azure Monitor, que resolvem nos seguintes domínios DNS: *shoebox2-black.shoebox2.metrics.nsatc.net*, *north-prod2.prod2.metrics.nsatc.net*, *azglobal-black.azglobal.metrics.nsatc.net*, *shoebox2-red.shoebox2.metrics.nsatc.net*, *east-prod2.prod2.metrics.nsatc.net,* *azglobal-red.azglobal.metrics.nsatc.net,* *shoebox3.prod.microsoftmetrics.com,* *shoebox3-red.prod.microsoftmetrics.com* e *shoebox3-black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Como posso verificar se a minha cache está a funcionar numa rede virtual?

>[!IMPORTANT]
>Quando se conecta a um Azure Cache para o caso Redis que está hospedado numa rede virtual, os seus clientes de cache devem estar na mesma rede virtual ou numa rede virtual com olhando para a rede virtual ativada na mesma região do Azure. O espreitamento global da rede virtual não é suportado atualmente. Este requisito aplica-se a quaisquer aplicações de teste ou ferramentas de diagnóstico de pinging. Independentemente do local onde a aplicação do cliente está hospedada, os NSGs ou outras camadas de rede devem ser configurados de modo a que o tráfego de rede do cliente seja autorizado a chegar à Cache Azure para a instância Redis.
>

Depois de configurados os requisitos da porta, conforme descrito na secção anterior, pode verificar se a sua cache está a funcionar seguindo estes passos:

- [Reinicie](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não puderem ser alcançadas, como documentado nos [requisitos portuários de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [requisitos de porta de saída,](cache-how-to-premium-vnet.md#outbound-port-requirements)a cache não será capaz de reiniciar com sucesso.
- Depois de os nódoas de cache terem sido reiniciados, conforme relatado pelo estado da cache no portal Azure, pode fazer os seguintes testes:
  - Ping o ponto final da cache utilizando a porta 6380 de uma máquina que está dentro da mesma rede virtual que a cache, utilizando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a `tcping` ferramenta reportar que a porta está aberta, a cache está disponível para ligação de clientes na rede virtual.

  - Outra forma de testar é criar um cliente de cache de teste (que poderia ser uma aplicação de consola simples usando StackExchange.Redis) que se conecta à cache e adiciona e recupera alguns itens da cache. Instale a aplicação do cliente da amostra num VM que esteja na mesma rede virtual que a cache. Em seguida, executá-lo para verificar a conectividade com a cache.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Quando tento ligar-me ao meu Azure Cache para o caso Redis numa rede virtual, porque é que recebo um erro ao afirmar que o certificado remoto é inválido?

Quando tenta ligar-se a uma cache Azure para o caso Redis numa rede virtual, vê um erro de validação de certificados como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que esteja a ligar-se ao anfitrião pelo endereço IP. Recomendamos que use o nome de anfitrião. Por outras palavras, utilize o seguinte fio:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite utilizar o endereço IP semelhante ao seguinte fio de ligação:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não conseguir resolver o nome DNS, algumas bibliotecas de clientes incluem opções de configuração como `sslHost` , que é fornecida pelo cliente StackExchange.Redis. Esta opção permite-lhe anular o nome de anfitrião utilizado para validação de certificados. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Posso utilizar redes virtuais com uma cache padrão ou básica?

As redes virtuais só podem ser utilizadas com caches de nível Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Porque é que a criação de uma Cache Azure para o caso Redis falha em algumas sub-redes, mas não noutras?

Se estiver a implantar uma cache Azure para redis para uma rede virtual, a cache deve estar numa sub-rede dedicada que não contenha outro tipo de recurso. Se for feita uma tentativa de implantar uma cache Azure para a instância Redis para uma sub-rede de rede virtual do Gestor de Recursos que contenha outros recursos, tais como instâncias de Gateway de Aplicação Azure e NAT de saída, a implementação geralmente falhará. Deve eliminar os recursos existentes de outros tipos antes de poder criar um novo Azure Cache para a instância Redis.

Também deve ter endereços IP suficientes disponíveis na sub-rede.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?

O Azure reserva alguns endereços IP dentro de cada sub-rede, e estes endereços não podem ser utilizados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade com o protocolo, juntamente com mais três endereços utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP utilizados pela infraestrutura de rede virtual Azure, cada Cache Azure para redis na sub-rede utiliza dois endereços IP por fragmento de cluster, além de endereços IP adicionais para réplicas adicionais, se houver. Um endereço IP adicional é usado para o equilibrador de carga. Uma cache não aglomerada é considerada como tendo um fragmento.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Todas as funcionalidades de cache funcionam quando uma cache é hospedada numa rede virtual?

Quando a sua cache faz parte de uma rede virtual, apenas os clientes da rede virtual podem aceder à cache. Como resultado, as seguintes funcionalidades de gestão de cache não funcionam neste momento:

* **Consola Redis**: Como a Consola Redis funciona no seu navegador local, que normalmente está numa máquina de desenvolvimento que não está ligada à rede virtual, não pode ligar-se ao seu cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Use ExpressRoute com Cache Azure para Redis

Os clientes podem ligar um circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual. Desta forma, estendem a sua rede no local ao Azure.

Por padrão, um circuito ExpressRoute recém-criado não realiza túneis forçados (anúncio de uma rota padrão, 0.0.0.0/0) numa rede virtual. Como resultado, a conectividade de saída da Internet é permitida diretamente a partir da rede virtual. As aplicações do cliente podem ligar-se a outros pontos finais do Azure, que inclui uma Cache Azure para a instância Redis.

Uma configuração comum do cliente é usar túneis forçados (anunciar uma rota padrão), o que força o tráfego de internet de saída a fluir no local. Este fluxo de tráfego quebra a conectividade com a Cache Azure para Redis se o tráfego de saída for bloqueado no local de modo que a cache de Azure para redis não é capaz de comunicar com as suas dependências.

A solução é definir uma ou mais rotas definidas pelo utilizador (UDRs) na sub-rede que contém a Cache Azure para a instância Redis. Um UDR define rotas específicas da sub-rede que serão honradas em vez da rota padrão.

Se possível, utilize a seguinte configuração:

* A configuração ExpressRoute anuncia 0.0.0.0/0 e, por defeito, força os túneis de trânsito no local.
* A UDR aplicada à sub-rede que contém a cache Azure para a instância Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego TCP/IP para a internet pública. Por exemplo, define o próximo tipo de lúpulo para a *internet*.

O efeito combinado destes passos é que a UDR de nível sub-rede tem precedência sobre o túnel forçado ExpressRoute, que garante o acesso à Internet de saída a partir da Cache Azure para o exemplo de Redis.

Ligar-se a um Azure Cache para redis instância a partir de uma aplicação no local usando ExpressRoute não é um cenário de utilização típico devido a razões de desempenho. Para melhor desempenho, a Azure Cache para clientes Redis deve estar na mesma região que a Cache Azure para a instância Redis.

>[!IMPORTANT]
>As rotas definidas numa UDR *devem* ser específicas o suficiente para ter precedência sobre quaisquer rotas anunciadas pela configuração ExpressRoute. O exemplo a seguir utiliza a ampla gama de endereços 0.0.0.0/0 e, como tal, pode potencialmente ser ultrapassado acidentalmente por anúncios de rotas que utilizam intervalos de endereços mais específicos.

>[!WARNING]
>A azure Cache para Redis não é suportado com configurações ExpressRoute que *incorretamente cruzam rotas do caminho de observação pública para o caminho de observação privada*. As configurações ExpressRoute que têm o olhar público configurado recebem anúncios de rotas da Microsoft para um grande conjunto de gamas de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem incorretamente cruzados na via de observação privada, o resultado é que todos os pacotes de rede de saída da cache Azure para a sub-rede da Redis instance são incorretamente escavados à força para a infraestrutura de rede de um cliente no local. Este fluxo de rede quebra a cache Azure para Redis. A solução para este problema é parar as rotas de publicidade cruzada do caminho de observação pública para o caminho de observação privado.

Informações de fundo sobre UDRs estão disponíveis no [encaminhamento de tráfego de rede virtual.](../virtual-network/virtual-networks-udr-overview.md)

Para obter mais informações sobre o ExpressRoute, consulte [a visão técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
