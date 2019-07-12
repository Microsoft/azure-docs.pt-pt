---
title: Suporte de dados de transmissão em fluxo de otimização com o CDN do Azure
description: Otimizar ficheiros de multimédia de transmissão em fluxo para uma entrega uniforme
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
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: c6ed546735058e330368151adb0df7323f943050
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593657"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Suporte de dados de transmissão em fluxo de otimização com o CDN do Azure 
 
Utilização do vídeo de alta definição está aumentando na internet, que cria dificuldades para entrega eficiente de ficheiros grandes. Os clientes esperam smooth reprodução de vídeo a pedido ou em direto de recursos de vídeo numa variedade de clientes e redes em todo o mundo. Um mecanismo de entrega rápida e eficiente para suporte de dados de ficheiros de transmissão em fluxo é fundamental para garantir uma experiência de consumidor uniforme e agradável.  

Suporte de dados de transmissão em fluxo em direto é especialmente difícil entregar devido a tamanhos grandes e o número de espetadores em simultâneo. Grandes atrasos fazer com que os utilizadores a sair. Uma vez transmissões em fluxo não podem ser colocado em cache antecipadamente e latências grandes não são aceitáveis para visualizadores, fragmentos de vídeo tem de ser entregue em tempo hábil. 

Os padrões de pedido de transmissão em fluxo também fornecem alguns novos desafios. Quando um fluxo em direto popular ou uma nova série for lançada para vídeo a pedido, milhares de milhões de espetadores podem pedir o fluxo ao mesmo tempo. Neste caso, a consolidação de pedido inteligente é vital para não sobrecarregar os servidores de origem, quando os recursos não estão em cache ainda.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Suporte de dados de transmissão em fluxo otimizações para CDN do Azure da Microsoft

**CDN Standard do Azure da Microsoft** pontos finais de fornecem recursos de suporte de dados de transmissão em fluxo diretamente, usando o tipo de otimização de entrega geral web. 

Suporte de dados de transmissão em fluxo para a otimização **CDN Standard do Microsoft Azure** está em vigor para em direto ou suporte de dados que utiliza os fragmentos de suporte de dados individuais para entrega de streaming de vídeo a pedido. Este processo é diferente de um único recurso de grandes transferido através de transferências progressivas ou através de pedidos de intervalo de bytes. Para informações sobre esse estilo de entrega de multimédia, veja [otimização de transferência de ficheiros grandes com a CDN do Azure](cdn-large-file-optimization.md).

Os suporte de dados gerais entrega ou de vídeo a pedido entrega otimização tipos de mídia utilizam a rede de entrega de conteúdos (CDN) com otimizações de back-end para fornecer recursos de suporte de dados mais rapidamente. Também utilizam as configurações para os elementos de multimédia com base nas práticas recomendadas aprendidas ao longo do tempo.

### <a name="partial-cache-sharing"></a>Cache parcial de partilha
Partilha de cache parcial permite que a CDN para servir conteúdo parcialmente em cache para novos pedidos. Por exemplo, se a primeira solicitação para a CDN resulta numa falha de acerto na cache, a solicitação é enviada para a origem. Embora este conteúdo incompleto é carregado para a cache CDN, outros pedidos para a CDN podem começar a obter estes dados. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Suporte de dados de transmissão em fluxo otimizações para CDN do Azure da Verizon

**CDN Standard do Azure da Verizon** e **CDN do Azure Premium da Verizon** pontos finais de fornecem recursos de suporte de dados de transmissão em fluxo diretamente, usando o tipo de otimização de entrega geral web. Alguns recursos na CDN ajudá-lo diretamente no fornecimento de ativos de mídia por padrão.

### <a name="partial-cache-sharing"></a>Cache parcial de partilha

Partilha de cache parcial permite que a CDN para servir conteúdo parcialmente em cache para novos pedidos. Por exemplo, se a primeira solicitação para a CDN resulta numa falha de acerto na cache, a solicitação é enviada para a origem. Embora este conteúdo incompleto é carregado para a cache CDN, outros pedidos para a CDN podem começar a obter estes dados. 

### <a name="cache-fill-wait-time"></a>Tempo de espera de preenchimento de cache

 A funcionalidade de tempo de espera de preenchimento de cache força o servidor de borda para conter todos os pedidos subsequentes para o mesmo recurso, até que chegam de cabeçalhos de resposta HTTP do servidor de origem. Se os cabeçalhos de resposta HTTP da origem chegam antes do timer expira, todos os pedidos que foram colocar em espera são fornecidos fora da cache de cada vez maior. Ao mesmo tempo, o cache é preenchida por dados a partir da origem. Por predefinição, o tempo de espera de preenchimento de cache está definido para 3 000 milissegundos. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Suporte de dados de transmissão em fluxo otimizações para CDN do Azure da Akamai
 
**CDN Standard do Azure da Akamai** oferece uma funcionalidade que fornece recursos de suporte de dados de transmissão em fluxo com eficiência para os utilizadores em todo o mundo em escala. O recurso reduz latências, dado que reduz a carga nos servidores de origem. Esta funcionalidade está disponível com o escalão de preço standard da Akamai. 

Suporte de dados de transmissão em fluxo para a otimização **CDN do Azure Standard da Akamai** está em vigor para em direto ou suporte de dados que utiliza os fragmentos de suporte de dados individuais para entrega de streaming de vídeo a pedido. Este processo é diferente de um único recurso de grandes transferido através de transferências progressivas ou através de pedidos de intervalo de bytes. Para informações sobre esse estilo de entrega de multimédia, veja [otimização de ficheiros grandes](cdn-large-file-optimization.md).

Os suporte de dados gerais entrega ou de vídeo a pedido entrega otimização tipos de mídia utilizam um CDN com otimizações de back-end para apresentar os ativos de mídia mais rapidamente. Também utilizam as configurações para os elementos de multimédia com base nas práticas recomendadas aprendidas ao longo do tempo.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Configurar um ponto de final de CDN da Akamai para otimizar o suporte de dados de transmissão em fluxo
 
Pode configurar o ponto final de entrega de conteúdos (CDN) de rede para otimizar a entrega de ficheiros grandes através do portal do Azure. Também pode utilizar as APIs REST ou qualquer um dos SDKs do cliente para fazer isso. Os passos seguintes mostram o processo através do portal do Azure para uma **CDN do Azure Standard da Akamai** perfil:

1. Para adicionar um novo ponto final de um Akamai **perfil da CDN** página, selecione **Endpoint**.
  
    ![Novo ponto final](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Na **otimizado para** na lista pendente, selecione **vídeo de transmissão de multimédia a pedido** para ativos de vídeo a pedido. Se fizer uma combinação de em direto e transmissão em fluxo de vídeo a pedido, selecione **geral de multimédia de transmissão em fluxo**.

    ![Transmissão em fluxo selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Depois de criar o ponto de extremidade, ele se aplica a otimização para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve este processo. 

### <a name="caching"></a>Colocação em cache

Se **CDN do Azure Standard da Akamai** Deteta que o elemento é um manifesto de transmissão em fluxo ou fragmento, ele usa diferentes horas de expiração de colocação em cache, desde a entrega geral web. (Consulte a lista completa na tabela a seguir). Como sempre, são honrados cache-control ou os cabeçalhos de Expires enviados a partir da origem. Se o elemento não é um elemento de multimédia, armazena em cache ao utilizar as horas de expiração para entrega geral web.

O tempo de colocação em cache negativo curto é útil para a descarga de origem quando muitos utilizadores pedem um fragmento que ainda não existe. Um exemplo é uma transmissão em direto onde os pacotes não estão disponíveis a partir da origem esse segundo. O intervalo já está a colocação em cache também ajuda a descarregar pedidos a partir da origem, porque o conteúdo de vídeo, normalmente, não é modificado.
 

|   | Entrega geral web | Transmissão geral de multimédia | Transmissão de multimédia de vídeo a pedido  
--- | --- | --- | ---
Colocação em cache: positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
Colocação em cache: negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Lidar com falha de origem  

Entrega geral de multimédia e entrega de multimédia de vídeo a pedido também têm tempos limite de origem e um registo de repetição com base nas melhores práticas para padrões de pedido comuns. Por exemplo, uma vez que a entrega de multimédia gerais destina-se em direto e entrega de multimédia de vídeo a pedido, ele usa um menor tempo limite da ligação devido à natureza sensível ao tempo de transmissão em direto.

Quando uma ligação exceder o tempo limite, a CDN tentará novamente várias vezes antes de enviar um erro de "504 - tempo limite do Gateway" para o cliente. 

Quando um ficheiro corresponde à lista de condições de tipo e o tamanho de ficheiro, a CDN utiliza o comportamento para suporte de dados de transmissão em fluxo. Caso contrário, ele usa a entrega geral web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para otimização de transmissão em fluxo de multimédia 

A tabela seguinte lista o conjunto de critérios de ser cumpridos para otimização de transmissão em fluxo de dados: 
 
Tipos de transmissão em fluxo suportados | Extensões de ficheiro  
--- | ---  
Apple HLS | Format=m3u8, m3u, m3ub, chave, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Estrutura do URL de seg Frag <br> (correspondência de regex: ^(/.*)Seq(\d+)-Frag(\d+)
TRAÇO | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Transmissão em fluxo uniforme | /manifest/, /QualityLevels/Fragments/
  
 
