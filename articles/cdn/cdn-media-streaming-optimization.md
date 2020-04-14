---
title: Otimização de streaming de mídia com Azure CDN
description: Otimizar ficheiros de meios de streaming para entrega suave
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
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 2931dffaaab2d06b2c06f03770a66d78d6466787
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260484"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Otimização de streaming de mídia com Azure CDN 
 
A utilização de vídeos de alta definição está a aumentar na internet, o que cria dificuldades para uma entrega eficiente de ficheiros grandes. Os clientes esperam uma reprodução suave de vídeo a pedido ou ativos de vídeo ao vivo em várias redes e clientes em todo o mundo. Um mecanismo de entrega rápido e eficiente para ficheiros de streaming de mídia é fundamental para garantir uma experiência suave e agradável do consumidor.  

Os meios de streaming ao vivo são especialmente difíceis de entregar devido aos grandes tamanhos e ao número de espectadores simultâneos. Atrasos prolongados fazem com que os utilizadores saiam. Como os streams ao vivo não podem ser protegidos antes do tempo e as grandes latenciências não são aceitáveis para os espectadores, os fragmentos de vídeo devem ser entregues em tempo útil. 

Os padrões de pedido de streaming também fornecem alguns novos desafios. Quando um popular live stream ou uma nova série é lançado para vídeo a pedido, milhares a milhões de espectadores podem solicitar o stream ao mesmo tempo. Neste caso, a consolidação de pedidos inteligentes é vital para não sobrecarregar os servidores de origem quando os ativos ainda não estão em cache.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Otimizações de streaming de mídia para CDN Azure da Microsoft

**O Azure CDN Standard a partir de** pontos finais da Microsoft fornece diretamente os ativos dos meios de streaming utilizando o tipo geral de otimização da entrega web. 

A otimização do streaming de meios de comunicação para **o Azure CDN Standard da Microsoft** é eficaz para meios de streaming ao vivo ou vídeo-on-demand que utilizam fragmentos de mídia individuais para entrega. Este processo é diferente de um único grande ativo transferido através de transferência progressiva ou utilizando pedidos de gama byte. Para obter informações sobre esse estilo de entrega de mídia, consulte a otimização de [descarregamento de ficheiros grande com o Azure CDN](cdn-large-file-optimization.md).

Os tipos gerais de entrega de mídia ou vídeo-on-demand usam a Rede de Entrega de Conteúdos Azure (CDN) com otimizações de back-end para fornecer ativos de mídia mais rapidamente. Também usam configurações para meios de comunicação baseados nas melhores práticas aprendidas ao longo do tempo.

### <a name="partial-cache-sharing"></a>Partilha parcial de cache
A partilha parcial de cache permite que o CDN sirva conteúdo parcialmente cacheado a novos pedidos. Por exemplo, se o primeiro pedido ao CDN resultar numa falha de cache, o pedido é enviado para a origem. Embora este conteúdo incompleto seja carregado na cache CDN, outros pedidos para o CDN podem começar a obter estes dados. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Otimizações de streaming de mídia para Azure CDN de Verizon

**O Azure CDN Standard da Verizon** e **do Azure CDN Premium da Verizon** endpoints fornecem ativos de streaming de mídia diretamente utilizando o tipo geral de otimização de entrega web. Algumas funcionalidades no CDN ajudam diretamente na entrega de ativos de mídia por defeito.

### <a name="partial-cache-sharing"></a>Partilha parcial de cache

A partilha parcial de cache permite que o CDN sirva conteúdo parcialmente cacheado a novos pedidos. Por exemplo, se o primeiro pedido ao CDN resultar numa falha de cache, o pedido é enviado para a origem. Embora este conteúdo incompleto seja carregado na cache CDN, outros pedidos para o CDN podem começar a obter estes dados. 

### <a name="cache-fill-wait-time"></a>Cache preencher tempo de espera

 A função de tempo de espera de enchimento de cache obriga o servidor de borda a reter quaisquer pedidos subsequentes para o mesmo recurso até que os cabeçalhos de resposta HTTP cheguem do servidor de origem. Se os cabeçalhos de resposta HTTP da origem chegarem antes do tempo expirar, todos os pedidos que foram colocados em espera são servidos fora da cache crescente. Ao mesmo tempo, a cache é preenchida por dados da origem. Por predefinição, o tempo de espera de enchimento da cache está definido para 3.000 milissegundos. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Otimizações de streaming de mídia para Azure CDN da Akamai
 
**O Azure CDN Standard da Akamai** oferece uma funcionalidade que fornece ativos de streaming de mídia de forma eficiente aos utilizadores em todo o mundo em escala. A funcionalidade reduz as lateências porque reduz a carga nos servidores de origem. Esta funcionalidade está disponível com o nível padrão de preços da Akamai. 

A otimização do streaming de mídia para **o Azure CDN Standard da Akamai** é eficaz para meios de streaming ao vivo ou vídeo-on-demand que usam fragmentos de mídia individuais para entrega. Este processo é diferente de um único grande ativo transferido através de transferência progressiva ou utilizando pedidos de gama byte. Para obter informações sobre esse estilo de entrega de mídia, consulte a otimização de [ficheiros grandes](cdn-large-file-optimization.md).

Os tipos gerais de entrega de mídia ou vídeo-on-demand fornecem tipos de encriptação usam um CDN com otimizações de back-end para fornecer ativos de mídia mais rapidamente. Também usam configurações para meios de comunicação baseados nas melhores práticas aprendidas ao longo do tempo.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Configure um ponto final da Akamai CDN para otimizar o streaming de meios de comunicação
 
Pode configurar o ponto final da sua rede de entrega de conteúdos (CDN) para otimizar a entrega de ficheiros grandes através do portal Azure. Também pode utilizar as APIs rest ou qualquer um dos SDKs do cliente para o fazer. Os seguintes passos mostram o processo através do portal Azure para um **Padrão CDN Azure a partir do** perfil akamai:

1. Para adicionar um novo ponto final, numa página de **perfil da Akamai CDN,** selecione **Endpoint**.
  
    ![Novo ponto final](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Na lista **Otimizada para** drop-down, selecione **Video on demand media streaming** para ativos de vídeo-on-demand. Se fizer uma combinação de streaming ao vivo e vídeo-on-demand, selecione o streaming de **meios de comunicação gerais**.

    ![Streaming selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Depois de criar o ponto final, aplica a otimização para todos os ficheiros que correspondam a determinados critérios. A secção seguinte descreve este processo. 

### <a name="caching"></a>Colocação em cache

Se o **Azure CDN Standard da Akamai** detetar que o ativo é um manifesto ou fragmento de streaming, utiliza diferentes prazos de expiração de caderndez a partir da entrega geral da Web. (Ver a lista completa na tabela seguinte.) Como sempre, os cabeçalhos de controlo de cache ou expirados enviados da origem são honrados. Se o ativo não for um ativo de mídia, ele caches utilizando os prazos de validade para entrega geral da Web.

O curto tempo negativo de cache é útil para o descarregamento de origem quando muitos utilizadores pedem um fragmento que ainda não existe. Um exemplo é um live stream onde os pacotes não estão disponíveis a partir da origem que a segunda. O intervalo de cache mais longo também ajuda a descarregar pedidos da origem porque o conteúdo de vídeo não é tipicamente modificado.
 

|   | Entrega geral da web | Transmissão geral dos meios de comunicação | Streaming de mídia vídeo-on-demand  
--- | --- | --- | ---
Caching: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
Caching: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Lidar com a falha de origem  

A entrega geral dos meios de comunicação social e a entrega de meios de vídeo a pedido também têm tempo de origem e um registo de retry baseado nas melhores práticas para padrões típicos de pedidos. Por exemplo, como a entrega geral dos meios de comunicação é para entrega de meios de comunicação ao vivo e vídeo-on-demand, ele usa um tempo de ligação mais curto devido à natureza sensível ao tempo do streaming ao vivo.

Quando uma ligação se esforça, o CDN tenta várias vezes antes de enviar um erro "504 - Gateway Timeout" ao cliente. 

Quando um ficheiro corresponde à lista de condições do tipo de ficheiro e tamanho, o CDN utiliza o comportamento para o streaming de meios de comunicação. Caso contrário, utiliza a entrega geral da web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para a otimização do streaming de mídia 

A tabela que se segue enumera o conjunto de critérios a satisfazer para a otimização do streaming de meios de comunicação: 
 
Tipos de streaming suportados | Extensões de ficheiros  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, chave, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Estrutura url seg-frag <br> (regex correspondente: ^(/.*)Seq(\d+)-Frag(\d+)
TRAÇO | mpd, traço, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Streaming suave | /manifesto/, /QualityLevels/Fragmentos/
  
 
