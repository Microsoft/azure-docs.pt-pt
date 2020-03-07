---
title: Otimize Azure CDN para o tipo de entrega de conteúdo
description: Otimize Azure CDN para o tipo de entrega de conteúdo
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: da8f17da9225da1d2b92bd8515d645bce9a1bbaa
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394376"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Otimize Azure CDN para o tipo de entrega de conteúdo

Quando entrega conteúdo a um grande público global, é fundamental garantir a entrega otimizada do seu conteúdo. A Rede de Entrega de [Conteúdos Azure (CDN)](cdn-overview.md) pode otimizar a experiência de entrega com base no tipo de conteúdo que tem. O conteúdo pode ser um site, um live stream, um vídeo ou um grande arquivo para download. Quando cria um ponto final do CDN, especifica um cenário no **Otimizado para** opção. A sua escolha determina qual a otimização aplicada ao conteúdo entregue a partir do ponto final da CDN.

As escolhas de otimização são projetadas para usar comportamentos de boas práticas para melhorar o desempenho da entrega de conteúdo e melhor carga de origem. As suas escolhas de cenário afetam o desempenho modificando as configurações para o cache parcial, a chunking do objeto e a política de retry de falha de origem. 

Este artigo fornece uma visão geral de várias funcionalidades de otimização e quando deve usá-las. Para obter mais informações sobre funcionalidades e limitações, consulte os respetivos artigos em cada tipo de otimização individual.

> [!NOTE]
> Quando cria um ponto final de CDN, o **Otimizado para** opções pode variar com base no tipo de perfil em que o ponto final é criado. Os fornecedores de CDN azure aplicam melhorias de diferentes formas, dependendo do cenário. 

## <a name="provider-options"></a>Opções de fornecedor

**O Azure CDN Standard dos** perfis da Microsoft suporta as seguintes otimizações:

* [Entrega geral da web.](#general-web-delivery) Esta otimização também é usada para streaming de mídia e grande download de ficheiros.

> [!NOTE]
> A aceleração dinâmica do site da Microsoft é oferecida através do [Serviço de Porta Frontal Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**A Norma Azure CDN da Verizon** e **azure CDN Premium dos** perfis verizon suportam as seguintes otimizações:

* [Entrega geral da web.](#general-web-delivery) Esta otimização também é usada para streaming de mídia e grande download de ficheiros.

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 


**O Azure CDN Standard dos** perfis da Akamai suporta as seguintes otimizações:

* [Entrega geral da web](#general-web-delivery) 

* [Transmissão geral dos meios de comunicação](#general-media-streaming)

* [Streaming de mídia vídeo-on-demand](#video-on-demand-media-streaming)

* [Grande download de ficheiros](#large-file-download)

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 

A Microsoft recomenda que teste variações de desempenho entre diferentes fornecedores para selecionar o fornecedor ideal para a sua entrega.

## <a name="select-and-configure-optimization-types"></a>Selecione e configure tipos de otimização

Quando criar um ponto final do CDN, selecione um tipo de otimização que melhor corresponda ao cenário e ao tipo de conteúdo que pretende que o ponto final entregue. **A entrega geral da web** é a seleção padrão. Para o **Padrão CDN Azure existente apartir apenas de** pontos finais da Akamai, pode atualizar a opção de otimização a qualquer momento. Esta alteração não interrompe a entrega do Azure CDN. 

1. Num **Padrão CDN Azure a partir do** perfil akamai, selecione um ponto final.

    ![Seleção endpoint](./media/cdn-optimization-overview/01_Akamai.png)

2. Em DEFINIÇÕES, **selecione Otimização**. Em seguida, selecione um tipo da lista **Otimizada para** a lista de drop-down.

    ![Otimização e seleção de tipos](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Pode otimizar o ponto final do CDN para um destes cenários. 

### <a name="general-web-delivery"></a>Entrega geral da web

A entrega geral da web é a opção de otimização mais comum. É projetado para otimização geral de conteúdos web, como páginas web e aplicações web. Esta otimização também pode ser usada para downloads de ficheiros e vídeos.

Um site típico contém conteúdo estático e dinâmico. O conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilo que podem ser emcacheed e entregues a diferentes utilizadores. O conteúdo dinâmico é personalizado para um utilizador individual, como itens de notícias que são adaptados a um perfil de utilizador. O conteúdo dinâmico, como o conteúdo do carrinho de compras, não é cacheporque é único para cada utilizador. A entrega geral da web pode otimizar todo o seu website. 

> [!NOTE]
> Se estiver a utilizar um **Azure CDN Standard a partir do** perfil Akamai, selecione este tipo de otimização se o tamanho médio do ficheiro for inferior a 10 MB. Caso contrário, se o tamanho médio do ficheiro for superior a 10 MB, selecione O download de **ficheiros Big** da lista **Otimizada para** a lista de drop-down.

### <a name="general-media-streaming"></a>Transmissão geral dos meios de comunicação

Se precisar de utilizar o ponto final para streaming ao vivo e streaming de vídeo a pedido, selecione o tipo geral de otimização de streaming de mídia.

O streaming de meios de comunicação é sensível ao tempo, porque os pacotes que chegam atrasados ao cliente, como o amortecedor frequente de conteúdos de vídeo, podem causar uma experiência de visualização degradada. A otimização do streaming de meios reduz a latência da entrega de conteúdos dos media e proporciona uma experiência de streaming suave para os utilizadores. 

Este cenário é comum para os clientes do serviço de mídia Azure. Quando utiliza os serviços de mídia Azure, obtém-se um único ponto final de streaming que pode ser utilizado tanto para o streaming ao vivo como para o streaming a pedido. Com este cenário, os clientes não precisam de mudar para outro ponto final quando mudam de streaming ao vivo para o streaming a pedido. A otimização geral do streaming de meios de comunicação suporta este tipo de cenário.

Para **o Azure CDN Standard da Microsoft,** **o Azure CDN Standard da Verizon**e o **Azure CDN Premium da Verizon,** utilizam o tipo geral de otimização da entrega web para fornecer conteúdo geral de streaming de meios de streaming.

Para mais informações sobre a otimização do streaming de mídia, consulte a otimização do [streaming de meios.](cdn-media-streaming-optimization.md)

### <a name="video-on-demand-media-streaming"></a>Streaming de mídia vídeo-on-demand

A otimização do streaming de mídia sonoro melhora o conteúdo de streaming de vídeo a pedido. Se utilizar um ponto final para o streaming de vídeo a pedido, utilize esta opção.

Para **o Azure CDN Standard da Microsoft,** **O Padrão CDN Azure da Verizon**e o **Azure CDN Premium dos** perfis da Verizon, utilizam o tipo geral de otimização de entrega web para fornecer conteúdo sonoro de streaming de vídeo a pedido.

Para mais informações sobre a otimização do streaming de mídia, consulte a otimização do [streaming de meios.](cdn-media-streaming-optimization.md)

> [!NOTE]
> Se o ponto final do CDN servir principalmente conteúdo sonoro de vídeo a pedido, utilize este tipo de otimização. A grande diferença entre este tipo de otimização e o tipo geral de otimização de streaming de mídia é o tempo de retenção de ligação. O intervalo é muito mais curto para trabalhar com cenários de streaming ao vivo.
>

### <a name="large-file-download"></a>Grande download de ficheiros

Para **o Azure CDN Standard a partir de** perfis Akamai, os grandes downloads de ficheiros são otimizados para conteúdos superiores a 10 MB. Se o tamanho médio do ficheiro for inferior a 10 MB, utilize a entrega geral da Web. Se os tamanhos médios dos ficheiros forem consistentemente superiores a 10 MB, pode ser mais eficiente criar um ponto final separado para ficheiros grandes. Por exemplo, as atualizações de firmware ou software normalmente são ficheiros grandes. Para entregar ficheiros superiores a 1,8 GB, é necessária a otimização do descarregamento de ficheiros.

Para **o Azure CDN Standard da Microsoft,** **O Padrão CDN Azure da Verizon**e o **Azure CDN Premium dos** perfis da Verizon, utilizam o tipo geral de otimização de entrega web para fornecer grandes conteúdos de descarregamento de ficheiros. Não há nenhuma limitação no tamanho do download de ficheiros.

Para mais informações sobre a otimização de ficheiros grandes, consulte a otimização de [ficheiros grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmico

 A aceleração dinâmica do site (DSA) está disponível para **o Azure CDN Standard da Akamai,** **Azure CDN Standard da Verizon**e **Azure CDN Premium dos** perfis verizon. Esta otimização envolve uma taxa adicional para usar; para mais informações, consulte os preços da Rede de [Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/)

> [!NOTE]
> A aceleração dinâmica do site da Microsoft é oferecida através do [Azure Front Door Service,](https://docs.microsoft.com/azure/frontdoor/front-door-overview) que é um serviço global de [qualquer elenco](https://en.wikipedia.org/wiki/Anycast) que aproveita a rede global privada da Microsoft para fornecer as suas cargas de trabalho da sua aplicação.

A DSA inclui várias técnicas que beneficiam a latência e o desempenho de conteúdos dinâmicos. As técnicas incluem a otimização de rotas e rede, otimização de TCP, e muito mais. 

Pode usar esta otimização para acelerar uma aplicação web que inclui inúmeras respostas que não são cacheable. Exemplos são resultados de pesquisa, transações de check-out ou dados em tempo real. Pode continuar a utilizar capacidades de cache core Azure CDN para dados estáticos. 

Para obter mais informações sobre a aceleração dinâmica do site, consulte [a aceleração do site Dinâmico.](cdn-dynamic-site-acceleration.md)



