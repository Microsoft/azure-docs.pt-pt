---
title: Otimização de transferência de ficheiros grandes com a CDN do Azure
description: Este artigo explica como grande arquivo downloads podem ser otimizados.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9793348b47763e6de10992b9a8a4606fc532cc4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636756"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Otimização de transferência de ficheiros grandes com a CDN do Azure

Tamanhos de ficheiro de conteúdo fornecida através da internet e continue a expandir devido à funcionalidade avançada, o gráfico e o conteúdo multimédia formatado. Esse crescimento é orientado por diversos fatores: penetração banda larga, maior dispositivos de armazenamento de baixo custo, aumento amplo de alta definição vídeo e ligado à internet dispositivos (IoT). Um mecanismo de entrega rápida e eficiente para ficheiros grandes é fundamental para garantir uma experiência de consumidor uniforme e agradável.

Entrega de ficheiros grandes tem vários desafios. Em primeiro lugar, o tempo médio para transferir um ficheiro grande pode ser significativo porque os aplicativos podem não transferir todos os dados sequencialmente. Em alguns casos, os aplicativos podem baixar a última parte de um ficheiro antes da primeira parte. Quando é solicitada a apenas uma pequena quantidade de um ficheiro ou um utilizador coloca em pausa um download, a transferência poderá falhar. O download também pode ser adiado até depois que a rede de entrega de conteúdos (CDN) recupera o arquivo inteiro do servidor de origem. 

Em segundo lugar, a latência entre um computador de usuário e o arquivo determina a velocidade a que eles podem visualizar o conteúdo. Além disso, problemas de congestionamento e a capacidade de rede também afetam o débito. Maior distâncias entre servidores e usuários criar outras oportunidades de perda de pacotes ocorrer, o que reduz a qualidade. A redução na qualidade causados pelo débito limitado e perda de pacotes maior pode aumentar o tempo de espera por um download de arquivo concluir. 

Terceiro, muitos ficheiros grandes não são entregues por inteiro. Os utilizadores podem cancelar um download no meio do caminho ou ver apenas os primeiros minutos de um vídeo MP4 longo. Por conseguinte, as empresas de entrega de software e suporte de dados desejam fornecer apenas a parte de um ficheiro que é solicitada. Distribuição eficiente das partes pedidas reduz o tráfego de saída do servidor de origem. Distribuição eficiente também reduz a memória e a pressão de e/s no servidor de origem. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Otimizar a entrega de ficheiros grandes, com a CDN do Azure da Microsoft

**CDN Standard do Azure da Microsoft** pontos finais de entregar os ficheiros grandes sem um limite no tamanho do ficheiro. Funcionalidades adicionais são ativadas por predefinição, para acelerar a entrega de ficheiros grandes.

### <a name="object-chunking"></a>Segmentação de objeto 

**CDN Standard do Azure da Microsoft** usa uma técnica chamada segmentação do objeto. Quando é solicitado um arquivo grande, a CDN obtém as partes mais pequenas do ficheiro da origem. Depois do servidor POP da CDN recebe um pedido de ficheiro completo ou intervalo de bytes, o servidor de borda CDN solicita o ficheiro a partir da origem em blocos de 8 MB. 

Depois do bloco chega na extremidade da CDN, tem em cache e servidos imediatamente para o utilizador. A CDN prefetches, em seguida, o próximo bloco em paralelo. Este obtenção prévia garante que o conteúdo permanece um trecho de um passo à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro é transferido (se requerido), todos os intervalos de byte estão disponíveis (se requerido), ou o cliente encerra a conexão. 

Para obter mais informações sobre a solicitação de intervalo de bytes, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN armazena em cache quaisquer partes à medida que estão a receber. Todo o arquivo não precisa ser colocado em cache na cache de CDN. Pedidos subsequentes para os intervalos de ficheiro ou bytes são servidos a partir da cache CDN. Se não todos os segmentos são colocadas em cache na CDN, obtenção prévia é utilizada para pedir segmentos da origem. Essa otimização depende da capacidade do servidor de origem para suportar pedidos de intervalo de bytes; Se o servidor de origem não suportar pedidos de intervalo de bytes, essa otimização não é eficaz. 

### <a name="conditions-for-large-file-optimization"></a>Condições para otimização de ficheiros grandes
Funcionalidades de otimização de ficheiros grandes para **CDN Standard do Microsoft Azure** são ativadas por predefinição quando utiliza o tipo de otimização de entrega geral web. Não há nenhum limite no tamanho máximo do ficheiro.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Otimizar a entrega de ficheiros grandes, com a CDN do Azure da Verizon

**CDN Standard do Azure da Verizon** e **CDN do Azure Premium da Verizon** pontos finais de entregar os ficheiros grandes sem um limite no tamanho do ficheiro. Funcionalidades adicionais são ativadas por predefinição, para acelerar a entrega de ficheiros grandes.

### <a name="complete-cache-fill"></a>Preenchimento de cache concluída

O recurso de preenchimento do padrão cache completo permite que a CDN transferir um ficheiro para a cache quando uma solicitação inicial é abandonada ou perdida. 

Preenchimento de cache completa é mais útil para recursos grandes. Normalmente, os utilizadores não transferem-los do início ao fim. Eles usam transferência progressiva. O comportamento padrão força o servidor de borda para iniciar uma obtenção de plano de fundo do elemento do servidor de origem. Depois disso, o elemento é na cache local do servidor de borda. Depois do objeto completo na cache, o servidor de borda atende pedidos de intervalo de bytes para a CDN para o objeto em cache.

O comportamento predefinido pode ser desabilitado por meio do mecanismo de regras em **CDN do Azure Premium da Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Arquivamento frequente de preenchimento de cache ponto a ponto

A funcionalidade de arquivamento frequente de preenchimento do padrão ponto a ponto cache utiliza um algoritmo proprietário sofisticado. Ele usa o edge adicional servidores com base na largura de banda de colocação em cache e agregado pede as métricas para satisfazer os pedidos de cliente para objetos grandes e altamente populares. Esta funcionalidade impede uma situação em que um grande número de pedidos adicionais é enviado para o servidor de origem de um utilizador. 

### <a name="conditions-for-large-file-optimization"></a>Condições para otimização de ficheiros grandes

Funcionalidades de otimização de ficheiros grandes para **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon** são ativadas por predefinição quando utiliza o tipo de otimização de entrega geral web. Não há nenhum limite no tamanho máximo do ficheiro. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Otimizar a entrega de ficheiros grandes, com a CDN do Azure Standard da Akamai

**CDN Standard do Azure da Akamai** pontos finais de perfil oferecem uma funcionalidade que proporciona arquivos grandes com eficiência aos utilizadores em todo o mundo em escala. O recurso reduz latências, dado que reduz a carga nos servidores de origem.

A funcionalidade de tipo de otimização de ficheiros grandes ativa otimizações de rede e configurações para entregar os ficheiros de grandes dimensões mais rápido e mais de forma reativa. Entrega geral web com **CDN do Azure Standard da Akamai** pontos finais coloca em cache de arquivos apenas abaixo de 1,8 GB e pode túnel (não cache) ficheiros até 150 GB. Caches de otimização de ficheiros grandes ficheiros até 150 GB.

Otimização de ficheiros grandes é eficaz quando determinadas condições são satisfeitas. As condições incluem como funciona o servidor de origem e os tamanhos e tipos de ficheiros que são solicitados. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurar um ponto de final de CDN da Akamai para otimizar a entrega de ficheiros grandes

Pode configurar seus **CDN do Azure Standard da Akamai** ponto final para otimizar a entrega de ficheiros grandes através do portal do Azure. Também pode utilizar as APIs REST ou qualquer um dos SDKs do cliente para fazer isso. Os passos seguintes mostram o processo através do portal do Azure para uma **CDN do Azure Standard da Akamai** perfil:

1. Para adicionar um novo ponto final de um Akamai **perfil da CDN** página, selecione **Endpoint**.

    ![Novo ponto final](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Na **otimizado para** na lista pendente, selecione **transferência de ficheiros grandes**.

    ![Otimização de ficheiros grandes selecionada](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Depois de criar o ponto final da CDN, aplica-se as otimizações de ficheiros grandes para todos os ficheiros que correspondem a determinados critérios. A secção seguinte descreve este processo.

### <a name="object-chunking"></a>Segmentação de objeto 

Otimização de ficheiros grandes com **CDN do Azure Standard da Akamai** usa uma técnica chamada segmentação do objeto. Quando é solicitado um arquivo grande, a CDN obtém as partes mais pequenas do ficheiro da origem. Depois do servidor POP da CDN recebe um pedido de ficheiro completo ou intervalo de bytes, ele verifica se o tipo de ficheiro é suportado para essa otimização. Ele também verifica se o tipo de ficheiro cumpre os requisitos de tamanho de ficheiro. Se o tamanho do ficheiro é maior do que 10 MB, o servidor de borda CDN solicita o ficheiro a partir da origem em blocos de 2 MB. 

Depois do bloco chega na extremidade da CDN, tem em cache e servidos imediatamente para o utilizador. A CDN prefetches, em seguida, o próximo bloco em paralelo. Este obtenção prévia garante que o conteúdo permanece um trecho de um passo à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro é transferido (se requerido), todos os intervalos de byte estão disponíveis (se requerido), ou o cliente encerra a conexão. 

Para obter mais informações sobre a solicitação de intervalo de bytes, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN armazena em cache quaisquer partes à medida que estão a receber. Todo o arquivo não precisa ser colocado em cache na cache de CDN. Pedidos subsequentes para os intervalos de ficheiro ou bytes são servidos a partir da cache CDN. Se não todos os segmentos são colocadas em cache na CDN, obtenção prévia é utilizada para pedir segmentos da origem. Essa otimização depende da capacidade do servidor de origem para suportar pedidos de intervalo de bytes; Se o servidor de origem não suportar pedidos de intervalo de bytes, essa otimização não é eficaz.

### <a name="caching"></a>Colocação em cache
Otimização de ficheiros grandes utiliza tempos de expiração de colocação em cache predefinido diferente desde a entrega geral web. Ele faz distinção entre os colocação em cache positiva e negativa colocação em cache com base nos códigos de resposta HTTP. Se o servidor de origem Especifica um prazo de expiração através de uma cache-control ou expire cabeçalho na resposta, a CDN honra esse valor. Quando não especifica a origem e o ficheiro corresponde as condições de tipo e o tamanho para este tipo de otimização, a CDN utiliza os valores predefinidos para otimização de ficheiros grandes. Caso contrário, a CDN utiliza as predefinições para a entrega geral web.


|    | Geral web | Otimização de ficheiros grandes 
--- | --- | --- 
Colocação em cache: positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
Colocação em cache: negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhuma | 1 segundo 

### <a name="deal-with-origin-failure"></a>Lidar com falha de origem

O comprimento de leitura-tempo limite de origem aumenta de dois segundos para entrega geral web até dois minutos para o tipo de otimização de ficheiros grandes. Este aumento de contas para os tamanhos de ficheiro maior evitar uma ligação de tempo limite prematura.

Quando uma ligação exceder o tempo limite, a CDN tentará novamente várias vezes antes de enviar um erro de "504 - tempo limite do Gateway" para o cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para otimização de ficheiros grandes

A tabela seguinte lista o conjunto de critérios de ser cumpridos para otimização de ficheiros grandes:

Condição | Valores 
--- | --- 
Tipos de ficheiro suportados | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, e webp, wma, wmv, zip  
Tamanho mínimo do ficheiro | 10 MB 
Tamanho máximo do ficheiro | 150 GB 
Características do servidor de origem | Tem de suportar pedidos de intervalo de bytes 

## <a name="additional-considerations"></a>Considerações adicionais

Considere os seguintes aspetos adicionais para este tipo de otimização:

- O processo de criação de blocos gera pedidos adicionais para o servidor de origem. No entanto, o volume global de entregue a partir da origem de dados é muito menor. Criação de blocos resulta em melhores características de colocação em cache no CDN.

- Memória e a pressão de e/s são reduzidos na origem porque as partes mais pequenas do arquivo são entregues.

- Para os segmentos em cache no CDN, existem não existem pedidos adicionais para a origem até que o conteúdo expira ou ele forem expulsos da cache.

- Os utilizadores podem fazer solicitações na faixa para a CDN, que são tratados como qualquer arquivo normal. Otimização aplica-se apenas se for um tipo de ficheiro válido e o intervalo de bytes é entre 10 MB e 150 GB. Se o tamanho do arquivo média solicitado é menor do que 10 MB, use entrega geral web.

