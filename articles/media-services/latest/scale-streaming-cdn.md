---
title: Conteúdo de streaming com integração de CDN
titleSuffix: Azure Media Services
description: Saiba mais sobre o streaming de conteúdos com integração cdN, bem como prefetching e Origin-Assist CDN-Prefetch.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128057"
---
# <a name="stream-content-with-cdn-integration"></a>Conteúdo de streaming com integração de CDN

A Rede de Entrega de Conteúdos (CDN) oferece aos programadores uma solução global para o fornecimento rápido de conteúdo de largura de banda alta aos utilizadores, ao colocar em cache o respetivo conteúdo em nós físicos estrategicamente colocados em todo o mundo.  

CDN caches conteúdo transmitido a partir de um Media Services [Streaming Endpoint (origem)](streaming-endpoint-concept.md) por codec, por protocolo de streaming, por bitrate, por formato de contentor, e por encriptação/DRM. Para cada combinação de codec-streaming protocolo-container-bitrate-encryption, haverá uma cache CDN separada.

O conteúdo popular será servido diretamente a partir da cache CDN, desde que o fragmento de vídeo esteja em cache. É provável que o conteúdo ao vivo seja emcache porque normalmente tem muitas pessoas a ver exatamente a mesma coisa. O conteúdo a pedido pode ser um pouco mais complicado porque pode ter algum conteúdo que seja popular e outros que não são. Se você tem milhões de ativos de vídeo onde nenhum deles é popular (apenas um ou dois espectadores por semana) mas você tem milhares de pessoas assistindo todos os vídeos diferentes, o CDN torna-se muito menos eficaz.

Também precisa de considerar como funciona o streaming adaptativo. Cada fragmento de vídeo individual é cached como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é visto. Se o espectador saltar por aí assistindo apenas alguns segundos aqui e ali, apenas os fragmentos de vídeo associados ao que a pessoa viu ficam em cache em CDN. Com o streaming adaptativo, você normalmente tem 5 a 7 bitrates diferentes de vídeo. Se uma pessoa está a ver um bitrate e outra pessoa está a ver um bitrate diferente, então cada um está em cache separadamente no CDN. Mesmo que duas pessoas estejam a ver o mesmo bitrate, podem estar a transmitir protocolos diferentes. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é cached separadamente. Assim, cada bitrate e protocolo são cached separadamente e apenas os fragmentos de vídeo que foram solicitados são cached.

Ao decidir se permite ou não a CDN no [ponto final](streaming-endpoint-concept.md)de streaming dos Serviços de Media, considere o número de espectadores antecipados. O CDN só ajuda se estiver à espera de muitos espectadores para o seu conteúdo. Se a moeda máxima dos espectadores for inferior a 500, é recomendado desativar o CDN uma vez que a CDN melhor se baseia em moeda.

Este tema discute a possibilidade de [integração do CDN.](#enable-azure-cdn-integration) Também explica a prefetching (cache ativo) e o conceito [De Origem-Assistência CDN-Prefetch.](#origin-assist-cdn-prefetch)

## <a name="considerations"></a>Considerações

* O [ponto](streaming-endpoint-concept.md) `hostname` final de streaming e o URL de streaming permanecem os mesmos, quer ative ou não o CDN.
* Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, crie outro ponto final de streaming que não esteja ativado pela CDN.

## <a name="enable-azure-cdn-integration"></a>Permitir a integração do CDN azure

> [!IMPORTANT]
> Não pode permitir a CDN para julgamento ou contas de estudante azure.
>
> A integração do CDN é ativada em todos os centros de dados azure, exceto governo federal e regiões da China.

Depois de um ponto final de streaming ser aprovisionado com CDN ativado, há um tempo de espera definido nos Serviços de Media antes que a atualização do DNS seja feita para mapear o ponto final de streaming para o ponto final da CDN.

Se mais tarde pretender desativar/ativar o CDN, o seu ponto final de streaming deve estar no estado **de paragem.** Pode levar até duas horas para que a integração do CDN Azure seja ativada e que as alterações sejam ativas em todos os POPs da CDN. No entanto, pode iniciar o seu ponto final de streaming e transmitir sem interrupções do ponto final de streaming. Uma vez concluída a integração, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, o seu ponto final de streaming estará no estado **de partida** e poderá observar um desempenho degradado.

Quando o ponto final de streaming Standard é criado, é configurado por padrão com standard Verizon. Pode configurar fornecedores Premium Verizon ou Standard Akamai utilizando APIs REST.

A integração da Azure Media Services com o Azure CDN é implementada no **Azure CDN da Verizon** para pontos finais de streaming padrão. Os pontos finais de streaming premium podem ser configurados utilizando todos os níveis e fornecedores de **preços Do CDN do Azure.**

> [!NOTE]
> Para mais detalhes sobre o Azure CDN, consulte a visão geral do [CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Determine se foi feita uma alteração dNS

Pode determinar se a alteração do DNS foi feita num ponto final de streaming (o <https://www.digwebinterface.com>tráfego está a ser direcionado para o Azure CDN) utilizando . Se vir azureedge.net nomes de domínio nos resultados, o tráfego está agora a ser apontado para o CDN.

## <a name="origin-assist-cdn-prefetch"></a>Assistência de Origem CDN-Prefetch

O cache cDN é um processo reativo. Se a CDN puder prever o que o próximo objeto será solicitado, a CDN pode solicitar e cache proactivamente o próximo objeto. Com este processo, você pode obter um cache-hit para todos (ou a maioria) dos objetos, o que melhora o desempenho.

O conceito de prefetching esforça-se por posicionar objetos na "borda da internet" em antecipação de que estes serão solicitados pelo jogador iminentemente, reduzindo assim o tempo para entregar esse objeto ao jogador.

Para atingir este objetivo, um ponto final de streaming (origem) e CDN precisam trabalhar de mãos dadas de várias maneiras:

- A origem dos Serviços de Media precisa de ter a "inteligência" (Origin-Assist) para informar a CDN do próximo objeto à prefetch.
- O CDN faz a prefetch e o cache (parte CDN-prefetch). A CDN também precisa de ter a "inteligência" para informar a origem, seja uma prefetch ou uma busca regular, lidar com as respostas de 404, e uma maneira de evitar um loop prefetch interminável.

### <a name="benefits"></a>Benefícios

Os benefícios da funcionalidade *Origin-Assist CDN-Prefetch* incluem:

- A Prefetch melhora a qualidade da reprodução de vídeo ao posicionar segmentos de vídeo antecipados na borda durante a reprodução, reduzindo a latência ao espectador e melhorando os tempos de descarregamento do segmento de vídeo. Isto resulta num tempo de arranque de vídeo mais rápido e em ocorrências de retamponamento mais baixas.
- Este conceito é aplicável ao cenário geral de origem CDN e não se limita aos meios de comunicação.
- A Akamai adicionou esta funcionalidade à [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Esta funcionalidade ainda não se aplica ao Akamai CDN integrado com o ponto final de streaming dos Media Services. No entanto, está disponível para clientes da Media Services que têm um contrato pré-existente da Akamai e exigem integração personalizada entre a Akamai CDN e a origem dos Media Services.

### <a name="how-it-works"></a>Como funciona

O suporte da `Origin-Assist CDN-Prefetch` CDN para os cabeçalhos (para streaming ao vivo e vídeo a pedido) está disponível para clientes que tenham contrato direto com a Akamai CDN. A funcionalidade envolve as seguintes trocas de cabeçalhos HTTP entre a Akamai CDN e a origem dos Serviços de Media:

|Cabeçalho HTTP|Valores|Remetente|Recetor|Objetivo|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (padrão) ou 0 |CDN|Origem|Para indicar que o CDN está ativado por prefetch.|
|`CDN-Origin-Assist-Prefetch-Path`| Exemplo: <br/>Fragmentos (vídeo=140000000000,formato=mpd-time-cmaf)|Origem|CDN|Para fornecer caminho de prefetch para CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (pedido de pré-rebusca) ou 0 (pedido regular)|CDN|Origem|Para indicar o pedido da CDN é uma prefetch.|

Para ver parte da troca de cabeçalhoem em ação, pode experimentar os seguintes passos:

1. Utilize o Carteiro ou cURL para emitir um pedido à origem dos Serviços de Media para um segmento de áudio ou vídeo ou fragmento. Certifique-se de `CDN-Origin-Assist-Prefetch-Enabled: 1` adicionar o cabeçalho no pedido.
2. Na resposta, deve ver `CDN-Origin-Assist-Prefetch-Path` o cabeçalho com um caminho relativo como o seu valor.

### <a name="supported-streaming-protocols"></a>Protocolos de streaming suportados

A `Origin-Assist CDN-Prefetch` funcionalidade suporta os seguintes protocolos de streaming para streaming ao vivo e a pedido:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Streaming suave

### <a name="faqs"></a>FAQs

* E se um URL de caminho prefetch for inválido para que a prefetch cDN obtenha um 404?

    O CDN só cache uma resposta de 404 durante 10 segundos (ou outro valor configurado).

* Suponha que tenha um vídeo a pedido. Se a CDN-prefetch estiver ativada, esta funcionalidade implica que uma vez que um cliente solicite o primeiro segmento de vídeo, a prefetch iniciará um loop para pré-rebuscar todos os segmentos de vídeo subsequentes no mesmo bitrate?

    Não, a CDN-prefetch só é feita após um pedido/resposta iniciado pelo cliente. A CDN-prefetch nunca é acionada por uma pré-fetch, para evitar um loop de pré-fetch.

* A funcionalidade CDN-Prefetch de Assistência de Origem está sempre ligado? Como pode ser ligado/desligado?

    Esta funcionalidade está desligada por defeito. Os clientes precisam ligá-lo via Akamai API.

* Para streaming ao vivo, o que aconteceria ao Origin-Assist se o próximo segmento ou fragmento ainda não estiver disponível?

    Neste caso, a origem dos Serviços de Media não fornecerá `CDN-Origin-Assist-Prefetch-Path` cabeçalho e a CDN-prefetch não ocorrerá.

* Como `Origin-Assist CDN-Prefetch` funciona com filtros de manifesto dinâmicos?

    Esta função funciona independentemente do filtro manifesto. Quando o próximo fragmento sair de uma janela de filtro, o seu URL ainda será localizado olhando para o manifesto bruto do cliente e depois devolvido como cabeçalho de resposta prefetch CDN. Assim, o CDN receberá o URL de um fragmento que é filtrado do manifesto DASH/HLS/Smooth. No entanto, o jogador nunca fará um pedido get à CDN para obter esse fragmento, porque esse fragmento não está incluído no manifesto DASH/HLS/Smooth detido pelo leitor (o jogador não sabe a existência desse fragmento).

* A lista de reprodução DASH MPD/HLS/Manifesto liso pode ser prebuscada?

    Não, DASH MPD, lista de reprodução master HLS, lista de reprodução de variantes HLS ou URL de manifesto liso não é adicionado ao cabeçalho prefetch.

* Os URLs de prefetch são relativos ou absolutos?

    Enquanto a Akamai CDN permite ambos, a origem dos Serviços de Media apenas fornece URLs relativos para o caminho de prefetch porque não há nenhum benefício aparente na utilização de URLs absolutos.

* Esta funcionalidade funciona com conteúdos protegidos pela DRM?

    Sim, uma vez que esta funcionalidade funciona a nível HTTP, não descodifica nem analisa qualquer segmento/fragmento. Não importa se o conteúdo está encriptado ou não.

* Esta funcionalidade funciona com a Inserção de Anúncios Laterais do Servidor (SSAI)?
    
    Funciona para o conteúdo original/principal (o conteúdo original de vídeo antes da inserção de anúncios), uma vez que o SSAI não altera o carimbo de tempo do conteúdo de origem a partir da origem dos Serviços de Media. Se esta funcionalidade funciona com conteúdo de anúncios depende se a origem do anúncio suporta o Origin-Assist. Por exemplo, se os conteúdos de anúncios também estiverem hospedados nos Serviços De Mídia Azure (mesma ou origem separada), os conteúdos de anúncios também serão prebuscados.

* Esta funcionalidade funciona com conteúdo UHD/HEVC?

    Sim.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

* Certifique-se de rever o documento [Streaming Endpoint (origem).](streaming-endpoint-concept.md)
* A amostra [deste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto final de streaming predefinido com .NET.
