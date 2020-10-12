---
title: Otimização de streaming de mídia com Azure CDN
description: Saiba mais sobre opções para otimizar os meios de streaming na Rede de Entrega de Conteúdos Azure, como a partilha parcial de cache e o tempo de espera de preenchimento de cache.
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
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192602"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Otimização de streaming de mídia com Azure CDN 
 
A utilização de vídeos de alta definição está a aumentar na internet, o que cria dificuldades para uma entrega eficiente de ficheiros grandes. Os clientes esperam uma reprodução suave de vídeos a pedido ou ativos de vídeo ao vivo em várias redes e clientes em todo o mundo. Um mecanismo de entrega rápido e eficiente para ficheiros de streaming de meios de comunicação é fundamental para garantir uma experiência suave e agradável do consumidor.  

Os meios de streaming ao vivo são especialmente difíceis de fornecer devido aos grandes tamanhos e número de espectadores simultâneos. Atrasos prolongados fazem com que os utilizadores saiam. Como as transmissões ao vivo não podem ser colocadas em cache antes do tempo e as grandes latências não são aceitáveis para os espectadores, os fragmentos de vídeo devem ser entregues em tempo útil. 

Os padrões de pedido de streaming também proporcionam alguns novos desafios. Quando um popular live stream ou uma nova série é lançado para vídeo a pedido, milhares a milhões de espectadores podem solicitar o stream ao mesmo tempo. Neste caso, a consolidação de pedidos inteligentes é vital para não sobrecarregar os servidores de origem quando os ativos ainda não estão em cache.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Otimizações de streaming de mídia para Azure CDN da Microsoft

**O Azure CDN Standard dos** pontos finais da Microsoft fornece ativos de streaming de mídia diretamente utilizando o tipo geral de otimização de entrega web. 

A otimização de streaming de mídia para **O Azure CDN Standard da Microsoft** é eficaz para meios de streaming ao vivo ou vídeo-on-demand que usam fragmentos de mídia individuais para entrega. Este processo é diferente de um único grande ativo transferido através de download progressivo ou usando pedidos byte-range. Para obter informações sobre este estilo de entrega de mídia, consulte a otimização do download de [ficheiros Large com a Azure CDN](cdn-large-file-optimization.md).

Os tipos gerais de entrega de mídia ou de fornecimento de meios de vídeo-on-demand utilizam a Rede de Entrega de Conteúdos Azure (CDN) com otimizações de back-end para fornecer ativos de mídia mais rapidamente. Também usam configurações para meios de comunicação com base nas melhores práticas aprendidas ao longo do tempo.

### <a name="partial-cache-sharing"></a>Partilha parcial de cache
A partilha parcial de cache permite que o CDN sirva conteúdo parcialmente em cache a novos pedidos. Por exemplo, se o primeiro pedido à CDN resultar numa falha de cache, o pedido é enviado para a origem. Embora este conteúdo incompleto seja carregado na cache cdn, outros pedidos para o CDN podem começar a obter estes dados. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Otimizações de streaming de mídia para Azure CDN de Verizon

**Azure CDN Standard da Verizon** e **Azure CDN Premium dos** pontos finais da Verizon fornecem ativos de streaming de meios diretamente utilizando o tipo geral de otimização de entrega web. Algumas funcionalidades no CDN ajudam diretamente na entrega de ativos de mídia por padrão.

### <a name="partial-cache-sharing"></a>Partilha parcial de cache

A partilha parcial de cache permite que o CDN sirva conteúdo parcialmente em cache a novos pedidos. Por exemplo, se o primeiro pedido à CDN resultar numa falha de cache, o pedido é enviado para a origem. Embora este conteúdo incompleto seja carregado na cache cdn, outros pedidos para o CDN podem começar a obter estes dados. 

### <a name="cache-fill-wait-time"></a>Cache preencher tempo de espera

 A funcionalidade de tempo de espera de preenchimento de cache força o servidor edge a reter quaisquer pedidos subsequentes para o mesmo recurso até que os cabeçalhos de resposta HTTP cheguem do servidor de origem. Se os cabeçalhos de resposta HTTP da origem chegarem antes do temporizador expirar, todos os pedidos que foram colocados em espera são servidos fora da cache de cultivo. Ao mesmo tempo, a cache é preenchida por dados da origem. Por predefinição, o tempo de espera de preenchimento da cache é definido para 3.000 milissegundos. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Otimizações de streaming de mídia para Azure CDN da Akamai
 
**O Azure CDN Standard da Akamai** oferece uma funcionalidade que fornece ativos de streaming de meios de comunicação eficientemente aos utilizadores em todo o mundo em escala. A funcionalidade reduz as latências porque reduz a carga nos servidores de origem. Esta funcionalidade está disponível com o nível de preços padrão da Akamai. 

A otimização de streaming de mídia para **O Azure CDN Standard da Akamai** é eficaz para meios de streaming ao vivo ou vídeo-on-demand que usam fragmentos de mídia individuais para entrega. Este processo é diferente de um único grande ativo transferido através de download progressivo ou usando pedidos byte-range. Para obter informações sobre este estilo de entrega de mídia, consulte a otimização de [ficheiros Large](cdn-large-file-optimization.md).

Os tipos gerais de entrega de mídia ou de fornecimento de meios de vídeo-on-demand usam um CDN com otimizações de back-end para fornecer ativos de mídia mais rapidamente. Também usam configurações para meios de comunicação com base nas melhores práticas aprendidas ao longo do tempo.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Configurar um ponto final da Akamai CDN para otimizar o streaming de meios de comunicação
 
Pode configurar o ponto final da sua rede de entrega de conteúdos (CDN) para otimizar a entrega de ficheiros grandes através do portal Azure. Também pode utilizar as APIs REST ou qualquer um dos SDKs do cliente para o fazer. Os seguintes passos mostram o processo através do portal Azure para um **Azure CDN Standard do** perfil da Akamai:

1. Para adicionar um novo ponto final, numa página de perfil da Akamai **CDN,** selecione **Endpoint**.
  
    ![Novo ponto final](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Na lista **Otimizada para** drop-down, selecione **Vídeo a pedido de streaming de meios** para ativos de vídeo a pedido. Se fizer uma combinação de streaming ao vivo e vídeo-on-demand, selecione **General media streaming**.

    ![Streaming selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Depois de criar o ponto final, aplica a otimização para todos os ficheiros que correspondam a determinados critérios. A secção seguinte descreve este processo. 

### <a name="caching"></a>Colocação em cache

Se **o Azure CDN Standard da Akamai** detetar que o ativo é um manifesto de streaming ou fragmento, utiliza diferentes tempos de validade do caching da entrega geral da web. (Ver a lista completa na tabela seguinte.) Como sempre, os cabeçalhos de cache ou expira enviados da origem são honrados. Se o ativo não for um ativo de mídia, ele caches usando os tempos de validade para a entrega geral da web.

O curto tempo negativo de caching é útil para o descarregamento de origem quando muitos utilizadores pedem um fragmento que ainda não existe. Um exemplo é um live stream onde os pacotes não estão disponíveis a partir da origem que segundo. O intervalo de caching mais longo também ajuda a descarregar pedidos da origem porque o conteúdo de vídeo não é tipicamente modificado.

| Colocação em cache  | Entrega geral da web | Streaming de meios de comunicação geral | Streaming de meios de vídeo a pedido  
|--- | --- | --- | ---
| Caching: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
| Caching: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhum | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Lidar com falha de origem  

A entrega geral dos meios de comunicação social e a entrega de meios de comunicação em vídeo-on-demand também têm intervalos de origem e um registo de retíria baseado nas melhores práticas para padrões de pedido típicos. Por exemplo, porque a entrega geral dos meios de comunicação social é para entrega de meios de comunicação ao vivo e vídeo-on-demand, utiliza um tempo de ligação mais curto devido à natureza sensível do tempo do streaming ao vivo.

Quando uma ligação acaba, o CDN retrição várias vezes antes de enviar um erro "504 - Gateway Timeout" ao cliente. 

Quando um ficheiro corresponde à lista de condições do tipo de ficheiro e do tamanho, o CDN utiliza o comportamento para o streaming de mídia. Caso contrário, utiliza a entrega geral da web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para otimização do streaming de meios de comunicação 

O quadro que se segue enumera o conjunto de critérios a satisfazer para a otimização do streaming de meios de comunicação: 
 
Tipos de streaming suportados | Extensões de ficheiros  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, chave, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>estrutura de URL de Seg-Frag <br> (regex correspondente: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, traço, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Streaming suave | /manifesto/, /QualityLevels/Fragmentos/
  
 
