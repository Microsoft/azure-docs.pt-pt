---
title: Otimizar a Azure CDN para o tipo de entrega de conteúdos
description: Otimizar a Azure CDN para o tipo de entrega de conteúdos
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 0255d7f47c8f9c63a2943830fa323a2e11994a9e
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887558"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Otimizar a Azure CDN para o tipo de entrega de conteúdos

Quando entrega conteúdo a um grande público global, é fundamental garantir a entrega otimizada do seu conteúdo. [A Azure Content Delivery Network (CDN)](cdn-overview.md) pode otimizar a experiência de entrega com base no tipo de conteúdo que tem. O conteúdo pode ser um site, um live stream, um vídeo ou um grande arquivo para download. Quando cria um ponto final cdn, especifica um cenário na opção **Otimizada.** A sua escolha determina qual a otimização aplicada ao conteúdo entregue a partir do ponto final da CDN.

As opções de otimização são projetadas para usar comportamentos de boas práticas para melhorar o desempenho da entrega de conteúdos e uma melhor descarga de origem. As suas escolhas de cenário afetam o desempenho modificando configurações para caching parcial, a chunking de objetos e a política de relagem de falha de origem. 

Este artigo fornece uma visão geral de várias funcionalidades de otimização e quando deve usá-las. Para obter mais informações sobre funcionalidades e limitações, consulte os respetivos artigos em cada tipo de otimização individual.

> [!NOTE]
> Quando cria um ponto final cdn, o **Otimizado para** opções pode variar com base no tipo de perfil em que o ponto final é criado. Os fornecedores de CDN Azure aplicam o melhoramento de diferentes formas, dependendo do cenário. 

## <a name="provider-options"></a>Opções de fornecedor

**O Azure CDN Standard dos** perfis da Microsoft suporta as seguintes otimizações:

* [Entrega geral da web.](#general-web-delivery) Esta otimização também é usada para streaming de mídia e grande download de ficheiros.

> [!NOTE]
> A aceleração dinâmica do site da Microsoft é oferecida através do [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**A azure CDN Standard da Verizon** e **Azure CDN Premium dos** perfis Verizon suportam as seguintes otimizações:

* [Entrega geral da web.](#general-web-delivery) Esta otimização também é usada para streaming de mídia e grande download de ficheiros.

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 


**A Azure CDN Standard dos** perfis da Akamai suporta as seguintes otimizações:

* [Entrega geral da web](#general-web-delivery) 

* [Streaming de meios de comunicação geral](#general-media-streaming)

* [Streaming de meios de vídeo a pedido](#video-on-demand-media-streaming)

* [Descarregamento de ficheiros grandes](#large-file-download)

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 

A Microsoft recomenda que teste as variações de desempenho entre diferentes fornecedores para selecionar o fornecedor ideal para a sua entrega.

## <a name="select-and-configure-optimization-types"></a>Selecione e configuure tipos de otimização

Quando criar um ponto final cdn, selecione um tipo de otimização que melhor corresponda ao cenário e tipo de conteúdo que deseja que o ponto final entregue. **A entrega geral da web** é a seleção predefinitiva. Para o **Azure CDN Standard** apenas a partir de pontos finais da Akamai, pode atualizar a opção de otimização a qualquer momento. Esta alteração não interrompe a entrega da Azure CDN. 

1. Num **Azure CDN Standard do** perfil Akamai, selecione um ponto final.

    ![Seleção de pontos finais](./media/cdn-optimization-overview/01_Akamai.png)

2. Em DEFINIÇÕES, **selecione Otimização**. Em seguida, selecione um tipo da lista **otimizada para** o drop-down.

    ![Otimização e seleção de tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Pode otimizar o ponto final do CDN para um destes cenários. 

### <a name="general-web-delivery"></a>Entrega geral da web

A entrega geral da web é a opção de otimização mais comum. É projetado para otimização geral de conteúdos web, como páginas web e aplicações web. Esta otimização também pode ser usada para downloads de ficheiros e vídeos.

Um site típico contém conteúdo estático e dinâmico. O conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilo que podem ser em cache e entregues a diferentes utilizadores. O conteúdo dinâmico é personalizado para um utilizador individual, como itens noticiosos adaptados a um perfil de utilizador. O conteúdo dinâmico, como o conteúdo do carrinho de compras, não está em cache porque é exclusivo de cada utilizador. A entrega geral da Web pode otimizar todo o seu website. 

> [!NOTE]
> Se estiver a utilizar um **Padrão Azure CDN do** perfil Da Akamai, selecione este tipo de otimização se o tamanho médio do ficheiro for inferior a 10 MB. Caso contrário, se o seu tamanho médio de ficheiro for superior a 10 MB, selecione Download de **ficheiros Large** da lista **Otimizada para** drop-down.

### <a name="general-media-streaming"></a>Streaming de meios de comunicação geral

Se precisar de utilizar o ponto final para streaming ao vivo e streaming vídeo-on-demand, selecione o tipo geral de otimização de streaming de meios de comunicação.

O streaming de mídia é sensível ao tempo, porque os pacotes que chegam atrasados ao cliente, como o buffering frequente de conteúdos de vídeo, podem causar uma experiência de visualização degradada. A otimização do streaming de mídia reduz a latência da entrega de conteúdos de mídia e proporciona uma experiência de streaming suave para os utilizadores. 

Este cenário é comum para os clientes do serviço de comunicação da Azure. Quando utiliza os serviços de comunicação do Azure, obtém-se um único ponto final de streaming que pode ser utilizado tanto para o streaming ao vivo como para o streaming a pedido. Com este cenário, os clientes não precisam de mudar para outro ponto final quando mudam de live para on demand streaming. A otimização geral do streaming de meios de comunicação suporta este tipo de cenário.

Para **o Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, e **Azure CDN Premium da Verizon**, utilize o tipo geral de otimização de entrega web para fornecer conteúdo sonoro de streaming geral.

Para obter mais informações sobre a otimização do streaming de meios de [comunicação, consulte a otimização do streaming de mídia](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming de meios de vídeo a pedido

A otimização de streaming de meios de vídeo-on-demand melhora o conteúdo de streaming de vídeo-on-demand. Se utilizar um ponto final para o streaming vídeo-on-demand, utilize esta opção.

Para **o Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, e **Azure CDN Premium a partir de** perfis Verizon, utilize o tipo geral de otimização de entrega web para fornecer conteúdo sonoro de streaming de vídeo a pedido.

Para obter mais informações sobre a otimização do streaming de meios de [comunicação, consulte a otimização do streaming de mídia](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto final do CDN servir principalmente conteúdo vídeo-on-demand, utilize este tipo de otimização. A grande diferença entre este tipo de otimização e o tipo geral de otimização de streaming de mídia é o tempo de retenção de ligação. O tempo de 2019 é muito mais curto para trabalhar com cenários de streaming ao vivo.
>

### <a name="large-file-download"></a>Descarregamento de ficheiros grandes

Para **o Azure CDN Standard a partir de** perfis da Akamai, os downloads de ficheiros grandes são otimizados para conteúdos superiores a 10 MB. Se o seu tamanho médio de ficheiro for inferior a 10 MB, utilize a entrega geral da web. Se os tamanhos médios dos ficheiros forem consistentemente maiores do que 10 MB, pode ser mais eficiente criar um ponto final separado para ficheiros grandes. Por exemplo, as atualizações de firmware ou software normalmente são ficheiros grandes. Para entregar ficheiros superiores a 1,8 GB, é necessária a otimização do download de ficheiros grandes.

Para **o Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, e **Azure CDN Premium a partir de** perfis Verizon, utilize o tipo geral de otimização de entrega web para fornecer conteúdo de descarregamento de ficheiros grandes. Não existe qualquer limitação no tamanho do download de ficheiros.

Para obter mais informações sobre a otimização de ficheiros grandes, consulte [a otimização de ficheiros Large](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmico

 A aceleração dinâmica do site (DSA) está disponível para **Azure CDN Standard da Akamai**, **Azure CDN Standard de Verizon**, e **Azure CDN Premium a partir de** perfis Verizon. Esta otimização envolve uma taxa adicional a utilizar; para obter mais informações, consulte [os preços da Rede de Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/)

> [!NOTE]
> A aceleração dinâmica do site da Microsoft é oferecida através do [Azure Front Door Service,](https://docs.microsoft.com/azure/frontdoor/front-door-overview) que é um serviço global [de qualquercast](https://en.wikipedia.org/wiki/Anycast) que aproveita a rede global privada da Microsoft para fornecer cargas de trabalho da sua aplicação.

A DSA inclui várias técnicas que beneficiam a latência e o desempenho de conteúdos dinâmicos. As técnicas incluem otimização de rotas e rede, otimização de TCP, e muito mais. 

Pode usar esta otimização para acelerar uma aplicação web que inclui inúmeras respostas que não são cacheable. Exemplos são resultados de pesquisa, transações de check-out ou dados em tempo real. Pode continuar a utilizar as capacidades de cache do Core Azure CDN para dados estáticos. 

Para obter mais informações sobre a aceleração dinâmica do site, consulte [a aceleração do site Dinâmico](cdn-dynamic-site-acceleration.md).



