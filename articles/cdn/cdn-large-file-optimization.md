---
title: Otimização de descarregamento de ficheiros com CDN Azure
description: Este artigo explica como grandes downloads de ficheiros podem ser otimizados.
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
ms.openlocfilehash: 4fe72985a799595908a0ff6bceb1a73dca823c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593790"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Otimização de descarregamento de ficheiros com CDN Azure

Os tamanhos de ficheiros dos conteúdos entregues através da internet continuam a crescer devido a uma funcionalidade melhorada, gráficos melhorados e conteúdos de mídia ricos. Este crescimento é impulsionado por muitos fatores: penetração de banda larga, dispositivos de armazenamento mais baratos, aumento generalizado de vídeo de alta definição e dispositivos ligados à Internet (IoT). Um mecanismo de entrega rápido e eficiente para ficheiros grandes é fundamental para garantir uma experiência suave e agradável do consumidor.

A entrega de ficheiros grandes tem vários desafios. Em primeiro lugar, o tempo médio para descarregar um ficheiro grande pode ser significativo porque as aplicações podem não descarregar todos os dados sequencialmente. Em alguns casos, as aplicações podem descarregar a última parte de um ficheiro antes da primeira parte. Quando apenas uma pequena quantidade de um ficheiro é solicitado ou um utilizador faz uma pausa no download, o download pode falhar. O download também pode ser adiado até que a rede de entrega de conteúdos (CDN) recupere todo o ficheiro do servidor de origem. 

Em segundo lugar, a latência entre a máquina de um utilizador e o ficheiro determina a velocidade a que podem ver o conteúdo. Além disso, o congestionamento da rede e os problemas de capacidade também afetam a entrada. Maiores distâncias entre servidores e utilizadores criam oportunidades adicionais para a perda de pacotes, o que reduz a qualidade. A redução da qualidade causada pela entrada limitada e pelo aumento da perda de pacotes pode aumentar o tempo de espera para que um download de ficheiros termine. 

Em terceiro lugar, muitos ficheiros grandes não são entregues na sua totalidade. Os utilizadores podem cancelar um download a meio ou assistir apenas aos primeiros minutos de um longo vídeo mp4. Portanto, as empresas de software e entrega de mídia querem entregar apenas a parte de um ficheiro que é solicitado. A distribuição eficiente das porções solicitadas reduz o tráfego de saída do servidor de origem. A distribuição eficiente também reduz a memória e a pressão de E/S no servidor de origem. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Otimizar para entrega de grandes ficheiros com CDN Azure da Microsoft

**O Azure CDN Standard a partir de** pontos finais da Microsoft fornece ficheiros grandes sem tampa no tamanho do ficheiro. As funcionalidades adicionais são ligadas por padrão para tornar a entrega de ficheiros grandes mais rapidamente.

### <a name="object-chunking"></a>Pedaço de objeto 

**O Azure CDN Standard da Microsoft** utiliza uma técnica chamada chunking de objetos. Quando um ficheiro grande é solicitado, o CDN recupera pedaços menores do ficheiro da origem. Depois de o servidor CDN POP receber um pedido de ficheiro completo ou byte-range, o servidor de borda CDN solicita o ficheiro a partir da origem em pedaços de 8 MB. 

Depois que o pedaço chega à borda do CDN, é emcache e imediatamente servido ao utilizador. O CDN então prebusca o próximo pedaço em paralelo. Esta prefetch garante que o conteúdo fica um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todas as gamas de byte estão disponíveis (se solicitado), ou o cliente termina a ligação. 

Para obter mais informações sobre o pedido de byte-range, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

O CDN caches quaisquer pedaços que sejam recebidos. Todo o ficheiro não precisa de ser colocado na cache da CDN. Os pedidos subsequentes para o ficheiro ou gama de bytes são servidos a partir da cache CDN. Se nem todos os pedaços estiverem em cache no CDN, a prefetch é usada para solicitar pedaços da origem. Esta otimização baseia-se na capacidade do servidor de origem para suportar pedidos de alcance byte; se o servidor de origem não suportar pedidos de alcance byte, esta otimização não é eficaz. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros de grande dimensão
As grandes funcionalidades de otimização de ficheiros para **o Azure CDN Standard da Microsoft** são ligadas por padrão quando utiliza o tipo geral de otimização da entrega web. Não há limites para o tamanho máximo do ficheiro.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Otimizar para entrega de grandes ficheiros com CDN Azure da Verizon

**O Azure CDN Standard da Verizon** e **o Azure CDN Premium da Verizon** endpoints entregam ficheiros grandes sem tampa no tamanho do ficheiro. As funcionalidades adicionais são ligadas por padrão para tornar a entrega de ficheiros grandes mais rapidamente.

### <a name="complete-cache-fill"></a>Enchimento completo de cache

A função de preenchimento de cache completa por defeito permite ao CDN puxar um ficheiro para a cache quando um pedido inicial é abandonado ou perdido. 

O enchimento completo de cache é mais útil para grandes ativos. Normalmente, os utilizadores não os descarregam do início ao fim. Usam download progressivo. O comportamento padrão força o servidor de borda a iniciar uma busca de fundo do ativo a partir do servidor de origem. Depois, o ativo está na cache local do servidor de borda. Depois de o objeto completo estar na cache, o servidor de borda satisfaz pedidos de gama byte ao CDN para o objeto em cache.

O comportamento predefinido pode ser desativado através do motor de regras em **Azure CDN Premium da Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Cache de pares preenchem o arquivo quente

A função de preenchimento de cache por pares padrão utiliza um algoritmo proprietário sofisticado. Utiliza servidores de cache de borda adicionais baseados na largura de banda e métricas de pedidos agregados para satisfazer pedidos de clientes para objetos grandes e altamente populares. Esta funcionalidade evita uma situação em que um grande número de pedidos extras são enviados para o servidor de origem de um utilizador. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros de grande dimensão

As grandes funcionalidades de otimização de ficheiros para **o Azure CDN Standard da Verizon** e do **Azure CDN Premium da Verizon** são ligadas por padrão quando utiliza o tipo geral de otimização de entrega web. Não há limites para o tamanho máximo do ficheiro. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Otimizar para entrega de grandes ficheiros com o Azure CDN Standard da Akamai

**O Azure CDN Standard dos** pontos finais do perfil da Akamai oferece uma funcionalidade que fornece ficheiros grandes de forma eficiente aos utilizadores em todo o mundo em escala. A funcionalidade reduz as lateências porque reduz a carga nos servidores de origem.

A funcionalidade de otimização de ficheiros grande gira em otimizações e configurações de rede para entregar ficheiros grandes de forma mais rápida e responsiva. Entrega geral da Web com **O Padrão Azure CDN da Akamai** endpoints caches ficheiros apenas abaixo de 1,8 GB e pode fazer túneis (não cache) ficheiros até 150 GB. Grandes caches de otimização de ficheiros até 150 GB.

A otimização de ficheiros grandes é eficaz quando determinadas condições estão satisfeitas. As condições incluem o funcionamento do servidor de origem e os tamanhos e tipos dos ficheiros solicitados. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configure um ponto final da Akamai CDN para otimizar a entrega de grandes ficheiros

Pode configurar o seu **Padrão CDN Azure a partir do** ponto final da Akamai para otimizar a entrega de ficheiros grandes através do portal Azure. Também pode utilizar as APIs rest ou qualquer um dos SDKs do cliente para o fazer. Os seguintes passos mostram o processo através do portal Azure para um **Padrão CDN Azure a partir do** perfil akamai:

1. Para adicionar um novo ponto final, numa página de **perfil da Akamai CDN,** selecione **Endpoint**.

    ![Novo ponto final](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Na lista **Otimizada para** drop-down, selecione **Large file download**.

    ![Otimização de ficheiros grandes selecionada](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Depois de criar o ponto final do CDN, aplica as grandes otimizações de ficheiros para todos os ficheiros que correspondam a determinados critérios. A secção seguinte descreve este processo.

### <a name="object-chunking"></a>Pedaço de objeto 

A otimização de ficheiros com o **Azure CDN Standard da Akamai** usa uma técnica chamada chunking de objetos. Quando um ficheiro grande é solicitado, o CDN recupera pedaços menores do ficheiro da origem. Depois de o servidor CDN POP receber um pedido de ficheiro completo ou byte-range, verifica se o tipo de ficheiro é suportado para esta otimização. Também verifica se o tipo de ficheiro satisfaz os requisitos de tamanho do ficheiro. Se o tamanho do ficheiro for superior a 10 MB, o servidor de borda CDN solicita o ficheiro a partir da origem em pedaços de 2 MB. 

Depois que o pedaço chega à borda do CDN, é emcache e imediatamente servido ao utilizador. O CDN então prebusca o próximo pedaço em paralelo. Esta prefetch garante que o conteúdo fica um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todas as gamas de byte estão disponíveis (se solicitado), ou o cliente termina a ligação. 

Para obter mais informações sobre o pedido de byte-range, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

O CDN caches quaisquer pedaços que sejam recebidos. Todo o ficheiro não precisa de ser colocado na cache da CDN. Os pedidos subsequentes para o ficheiro ou gama de bytes são servidos a partir da cache CDN. Se nem todos os pedaços estiverem em cache no CDN, a prefetch é usada para solicitar pedaços da origem. Esta otimização baseia-se na capacidade do servidor de origem para suportar pedidos de alcance byte; se o servidor de origem não suportar pedidos de alcance byte, esta otimização não é eficaz.

### <a name="caching"></a>Colocação em cache
A otimização de ficheiros grandes utiliza diferentes prazos de expiração de cadercidade padrão a partir da entrega geral da Web. Diferencia entre o cache positivo e o cache negativo com base em códigos de resposta HTTP. Se o servidor de origem especificar um tempo de validade através de um controlo de cache ou expirar o cabeçalho na resposta, o CDN honra esse valor. Quando a origem não especifica e o ficheiro corresponde às condições do tipo e tamanho para este tipo de otimização, o CDN utiliza os valores predefinidos para uma grande otimização de ficheiros. Caso contrário, o CDN utiliza predefinições para a entrega geral da Web.


|    | Teia geral | Otimização de ficheiros grandes 
--- | --- | --- 
Caching: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
Caching: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo 

### <a name="deal-with-origin-failure"></a>Lidar com a falha de origem

O comprimento do tempo de leitura de origem aumenta de dois segundos para a entrega geral da web para dois minutos para o tipo de otimização de ficheiros. Este aumento explica os tamanhos de ficheiros maiores para evitar uma ligação de tempo indevido.

Quando uma ligação se esforça, o CDN tenta várias vezes antes de enviar um erro "504 - Gateway Timeout" ao cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros de grande dimensão

O quadro seguinte enumera o conjunto de critérios a satisfazer para a otimização de ficheiros de grande dimensão:

Condição | Valores 
--- | --- 
Tipos de ficheiro suportados | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamanho mínimo do arquivo | 10 MB 
Tamanho máximo do ficheiro | 150 GB 
Características do servidor de origem | Deve apoiar pedidos de alcance byte 

## <a name="additional-considerations"></a>Considerações adicionais

Considere os seguintes aspetos adicionais para este tipo de otimização:

- O processo de chunking gera pedidos adicionais para o servidor de origem. No entanto, o volume global de dados entregues a partir da origem é muito menor. A chunking resulta em melhores características de cache no CDN.

- A memória e a pressão de I/S são reduzidas na origem porque peças menores do ficheiro são entregues.

- Para os pedaços em cache no CDN, não há pedidos adicionais para a origem até que o conteúdo expire ou seja despejado da cache.

- Os utilizadores podem fazer pedidos de alcance para o CDN, que são tratados como qualquer ficheiro normal. A otimização só se aplica se for um tipo de ficheiro válido e a gama de bytes estiver entre 10 MB e 150 GB. Se o tamanho médio do ficheiro solicitado for inferior a 10 MB, utilize a entrega geral da web.

