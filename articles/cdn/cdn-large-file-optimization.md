---
title: Otimização de descarregamento de ficheiros grandes com Azure CDN
description: Saiba como os downloads de ficheiros grandes podem ser otimizados na Rede de Entrega de Conteúdos Azure. Este artigo inclui vários cenários.
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
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020726"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Otimização de descarregamento de ficheiros grandes com Azure CDN

Os tamanhos de ficheiros de conteúdos entregues através da internet continuam a crescer devido à funcionalidade melhorada, à melhoria dos gráficos e ao rico conteúdo dos meios de comunicação. Este crescimento é impulsionado por muitos fatores: penetração de banda larga, dispositivos de armazenamento mais baratos, aumento generalizado de vídeo de alta definição e dispositivos ligados à Internet (IoT). Um mecanismo de entrega rápido e eficiente para ficheiros grandes é fundamental para garantir uma experiência suave e agradável do consumidor.

A entrega de grandes ficheiros tem vários desafios. Em primeiro lugar, o tempo médio para descarregar um grande ficheiro pode ser significativo porque as aplicações podem não descarregar todos os dados sequencialmente. Em alguns casos, as aplicações podem descarregar a última parte de um ficheiro antes da primeira parte. Quando apenas uma pequena quantidade de um ficheiro é solicitado ou um utilizador faz uma pausa no download, o download pode falhar. O download também pode ser adiado até que a rede de entrega de conteúdos (CDN) recupere todo o ficheiro do servidor de origem. 

Em segundo lugar, a latência entre a máquina de um utilizador e o ficheiro determina a velocidade a que podem visualizar o conteúdo. Além disso, o congestionamento da rede e os problemas de capacidade também afetam a produção. Maiores distâncias entre servidores e utilizadores criam oportunidades adicionais para a perda de pacotes, o que reduz a qualidade. A redução da qualidade causada por uma produção limitada e pelo aumento da perda de pacotes pode aumentar o tempo de espera para um download de ficheiros terminar. 

Em terceiro lugar, muitos ficheiros grandes não são entregues na sua totalidade. Os utilizadores podem cancelar um download a meio ou assistir apenas aos primeiros minutos de um longo vídeo mp4. Portanto, as empresas de software e de entrega de meios de comunicação querem entregar apenas a parte de um ficheiro que é solicitado. Uma distribuição eficiente das porções solicitadas reduz o tráfego de saída do servidor de origem. Uma distribuição eficiente também reduz a memória e a pressão de E/S no servidor de origem. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Otimize para entrega de grandes ficheiros com Azure CDN da Microsoft

**O Azure CDN Standard dos** pontos finais da Microsoft fornece ficheiros grandes sem uma tampa no tamanho do ficheiro. As funcionalidades adicionais são ligadas por padrão para tornar a entrega de grandes ficheiros mais rapidamente.

### <a name="object-chunking"></a>Pedaço de objeto 

**O Azure CDN Standard da Microsoft** usa uma técnica chamada "chunking" de objetos. Quando um ficheiro grande é solicitado, o CDN recupera pedaços menores do ficheiro a partir da origem. Depois de o servidor CDN POP receber um pedido de ficheiro completo ou byte-range, o servidor de borda CDN solicita o ficheiro a partir da origem em pedaços de 8 MB. 

Depois de o pedaço chegar à borda do CDN, é em cache e imediatamente servido ao utilizador. Em seguida, o CDN prefeca o próximo pedaço em paralelo. Esta prefetch garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todos os intervalos byte estão disponíveis (se solicitado), ou o cliente termina a ligação. 

Para obter mais informações sobre o pedido byte-range, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

O CDN caches qualquer pedaço à medida que são recebidos. Todo o ficheiro não precisa de ser colocado na cache do CDN. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache CDN. Se nem todos os pedaços estiverem em cache no CDN, a prefetch é utilizada para solicitar pedaços da origem. Esta otimização baseia-se na capacidade do servidor de origem de suportar pedidos de alcance byte; se o servidor de origem não suportar pedidos de alcance byte, os pedidos de descarregamento de dados superiores a 8mb falharão. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros grandes
Não há limites no tamanho máximo do ficheiro.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Otimize para entrega de grandes ficheiros com Azure CDN da Verizon

**O Azure CDN Standard da Verizon** e **a Azure CDN Premium dos** pontos finais da Verizon entregam ficheiros grandes sem uma tampa no tamanho do ficheiro. As funcionalidades adicionais são ligadas por padrão para tornar a entrega de grandes ficheiros mais rapidamente.

### <a name="complete-cache-fill"></a>Preenchimento completo da cache

A função de preenchimento de cache predefinido permite ao CDN puxar um ficheiro para a cache quando um pedido inicial é abandonado ou perdido. 

O enchimento completo da cache é mais útil para grandes ativos. Normalmente, os utilizadores não os descarregam do início ao fim. Usam downloads progressivos. O comportamento predefinido força o servidor de borda a iniciar uma busca de fundo do ativo a partir do servidor de origem. Depois, o ativo está na cache local do servidor de borda. Depois de o objeto completo estar na cache, o servidor de borda satisfaz os pedidos de alcance byte ao CDN para o objeto em cache.

O comportamento predefinido pode ser desativado através do motor de regras em **Azure CDN Premium da Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Cache de pares preenchem o arquivo quente

A cache de pares predefinido preenche a funcionalidade de arquivo quente usa um algoritmo proprietário sofisticado. Utiliza servidores adicionais de caching de bordas baseados em largura de banda e pedidos agregados para satisfazer pedidos de clientes para objetos grandes e altamente populares. Esta funcionalidade impede uma situação em que um grande número de pedidos extra são enviados para o servidor de origem de um utilizador. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros grandes

As grandes funcionalidades de otimização de ficheiros para **O Azure CDN Standard da Verizon** e **Azure CDN Premium da Verizon** são ligadas por padrão quando utiliza o tipo geral de otimização de entrega web. Não há limites no tamanho máximo do ficheiro. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Otimizar para entrega de grandes ficheiros com Azure CDN Standard da Akamai

**O Azure CDN Standard dos** pontos finais de perfil da Akamai oferece uma funcionalidade que fornece grandes ficheiros de forma eficiente aos utilizadores em todo o mundo em escala. A funcionalidade reduz as latências porque reduz a carga nos servidores de origem.

A funcionalidade de otimização de ficheiros grande liga-se a otimizações de rede e configurações para fornecer ficheiros grandes de forma mais rápida e responsiva. Entrega geral na Web com **Azure CDN Standard a partir de** ficheiros caches de pontos finais da Akamai apenas abaixo de 1,8 GB e pode fazer túneis (não cache) ficheiros até 150 GB. Grandes caches de otimização de ficheiros até 150 GB.

A otimização de ficheiros grandes é eficaz quando certas condições são satisfeitas. As condições incluem o funcionamento do servidor de origem e os tamanhos e tipos dos ficheiros que são solicitados. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurar um ponto final da Akamai CDN para otimizar a entrega de grandes ficheiros

Pode configurar o seu **Azure CDN Standard do** ponto final da Akamai para otimizar a entrega de ficheiros grandes através do portal Azure. Também pode utilizar as APIs REST ou qualquer um dos SDKs do cliente para o fazer. Os seguintes passos mostram o processo através do portal Azure para um **Azure CDN Standard do** perfil da Akamai:

1. Para adicionar um novo ponto final, numa página de perfil da Akamai **CDN,** selecione **Endpoint**.

    ![Novo ponto final](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Na lista **Otimizada para** drop-down, selecione **Grande download de ficheiros**.

    ![Grande otimização de ficheiros selecionados](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Depois de criar o ponto final cdn, aplica as grandes otimizações de ficheiros para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve este processo.

### <a name="object-chunking"></a>Pedaço de objeto 

Grande otimização de ficheiros com **Azure CDN Standard da Akamai** usa uma técnica chamada de chunking de objeto. Quando um ficheiro grande é solicitado, o CDN recupera pedaços menores do ficheiro a partir da origem. Depois de o servidor CDN POP receber um pedido de ficheiro completo ou byte-range, verifica se o tipo de ficheiro é suportado para esta otimização. Também verifica se o tipo de ficheiro satisfaz os requisitos de tamanho do ficheiro. Se o tamanho do ficheiro for superior a 10 MB, o servidor de borda CDN solicita o ficheiro a partir da origem em pedaços de 2 MB. 

Depois de o pedaço chegar à borda do CDN, é em cache e imediatamente servido ao utilizador. Em seguida, o CDN prefeca o próximo pedaço em paralelo. Esta prefetch garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todos os intervalos byte estão disponíveis (se solicitado), ou o cliente termina a ligação. 

Para obter mais informações sobre o pedido byte-range, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

O CDN caches qualquer pedaço à medida que são recebidos. Todo o ficheiro não precisa de ser colocado na cache do CDN. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache CDN. Se nem todos os pedaços estiverem em cache no CDN, a prefetch é utilizada para solicitar pedaços da origem. Esta otimização baseia-se na capacidade do servidor de origem de suportar pedidos de alcance byte; se o servidor de origem não suporta pedidos de alcance byte, esta otimização não é eficaz.

### <a name="caching"></a>Colocação em cache
A otimização de ficheiros grandes utiliza diferentes tempos de validade da validade da entrega geral da web. Diferencia entre caching positivo e caching negativo com base em códigos de resposta HTTP. Se o servidor de origem especificar um tempo de validade através de um cache-controle ou expirar o cabeçalho na resposta, o CDN honra esse valor. Quando a origem não especifica e o ficheiro corresponde às condições de tipo e tamanho para este tipo de otimização, o CDN utiliza os valores predefinidos para a otimização de ficheiros de grande dimensão. Caso contrário, o CDN utiliza predefinições para a entrega geral da web.

| Colocação em cache  | Teia geral | Otimização de ficheiros grandes 
--- | --- | --- 
Caching: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
Caching: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhum | 1 segundo 

### <a name="deal-with-origin-failure"></a>Lidar com falha de origem

O comprimento do tempo de leitura de origem aumenta de dois segundos para a entrega geral da web para dois minutos para o tipo de otimização de ficheiros grandes. Este aumento explica os tamanhos de ficheiro maiores para evitar uma ligação prematura do tempo limite.

Quando uma ligação acaba, o CDN retrição várias vezes antes de enviar um erro "504 - Gateway Timeout" ao cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de ficheiros grandes

O quadro que se segue enumera o conjunto de critérios a satisfazer para a otimização de ficheiros de grande enquanto:

Condição | Valores 
--- | --- 
Tipos de ficheiros suportados | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, alcatrão, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamanho mínimo do ficheiro | 10 MB 
Tamanho máximo do ficheiro | 150 GB 
Características do servidor de origem | Deve apoiar pedidos byte-range 

## <a name="additional-considerations"></a>Considerações adicionais

Considere os seguintes aspetos adicionais para este tipo de otimização:

- O processo de chunking gera pedidos adicionais para o servidor de origem. No entanto, o volume global de dados entregues a partir da origem é muito menor. A chunking resulta em melhores características de caching no CDN.

- A memória e a pressão de E/S são reduzidas na origem porque peças menores do ficheiro são entregues.

- Para os pedaços em cache na CDN, não há pedidos adicionais para a origem até que o conteúdo expire ou seja despejado da cache.

- Os utilizadores podem fazer pedidos de alcance para o CDN, que são tratados como qualquer ficheiro normal. A otimização só se aplica se for um tipo de ficheiro válido e a gama byte estiver entre 10 MB e 150 GB. Se o tamanho médio do ficheiro solicitado for inferior a 10 MB, utilize a entrega geral da web.

