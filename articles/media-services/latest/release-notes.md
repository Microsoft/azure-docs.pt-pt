---
title: Notas de lançamento da Azure Media Services v3 Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe as últimas atualizações sobre o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769757"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de lançamento da Azure Media Services v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Pode utilizar o [portal Azure](https://portal.azure.com/) para gerir o V3 [Live Events,](live-events-outputs-concept.md)ver v3 [Assets,](assets-concept.md)obter informações sobre o acesso a APIs. Para todas as outras tarefas de gestão (por exemplo, Transforms and Jobs), utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados .

Para mais informações, consulte [a orientação da Migração para a mudança dos Serviços de Media v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>Abril de 2020

### <a name="improvements-in-documentation"></a>Melhorias na documentação

Os docs do Azure Media Player foram migrados para a [documentação azure.](../azure-media-player/azure-media-player-overview.md)

## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Um suporte melhorado para fontes interligadas em Análise de Vídeo – esse conteúdo está agora desinterligado corretamente antes de ser enviado para motores de inferência.
- Ao gerar miniaturas com o modo "Best", o codificador procura agora mais de 30 segundos para selecionar uma moldura que não seja monocromática.

### <a name="azure-government-cloud-updates"></a>Atualizações na nuvem do Governo azure

Media Services GA'ed nas seguintes regiões do Governo Azure: *USGov Arizona* e *USGov Texas*.

## <a name="december-2019"></a>Dezembro de 2019

Adicionou suporte cdn para cabeçalhos *Prefetch Origin-Assist* para streaming ao vivo e vídeo a pedido; disponível para clientes que tenham contrato direto com a Akamai CDN. A funcionalidade De Assistência à Origem CDN-Prefetch envolve as seguintes trocas de cabeçalhos HTTP entre a origem da Akamai CDN e da Azure Media Services:

|Cabeçalho HTTP|Valores|Remetente|Recetor|Objetivo|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Habilitado | 1 (padrão) ou 0 |CDN|Origem|Para indicar que a CDN está ativada por prefetch|
|CDN-Origin-Assist-Prefetch-Path| Exemplo: <br/>Fragmentos (vídeo=140000000000,formato=mpd-time-cmaf)|Origem|CDN|Para fornecer caminho de prefetch para CDN|
|CDN-Origem-Pedido de Prefetch-Prefetch|1 (pedido de pré-rebusca) ou 0 (pedido regular)|CDN|Origem|Para indicar o pedido da CDN é uma prefetch|

Para ver parte da troca de cabeçalhoem em ação, pode experimentar os seguintes passos:

1. Utilize o Carteiro ou o curl para emitir um pedido à origem dos Serviços de Media para um segmento de áudio ou vídeo ou fragmento. Certifique-se de adicionar o cabeçalho CDN-Origin-Assist-Prefetch-Enabled: 1 no pedido.
2. Na resposta, deve ver o cabeçalho CDN-Origin-Assist-Prefetch-Path com um caminho relativo como valor.

## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Pré-visualização da transcrição ao vivo

A transcrição ao vivo está agora em pré-visualização pública e disponível para uso na região oeste dos EUA 2.

A transcrição ao vivo é projetada para trabalhar em conjunto com eventos ao vivo como uma capacidade de adição.  É suportado tanto em eventos ao vivo pass-through como Standard ou Premium.  Quando esta funcionalidade está ativada, o serviço utiliza a funcionalidade [Discurso-Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio que está a chegar em texto. Este texto é então disponibilizado para entrega juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS. A faturação baseia-se num novo medidor adicional que é custo adicional para o evento ao vivo quando está no estado de "Running".  Para mais detalhes sobre transcrição ao vivo e faturação, consulte [transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como uma funcionalidade de pré-visualização na região oeste dos EUA 2. Apoia a transcrição de palavras faladas em inglês (en-us) apenas neste momento.

### <a name="content-protection"></a>Proteção de conteúdo

O recurso *Token Replay Prevention* lançado em regiões limitadas em setembro já está disponível em todas as regiões.
Os clientes da Media Services podem agora estabelecer um limite no número de vezes que o mesmo símbolo pode ser usado para solicitar uma chave ou uma licença. Para mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros codificadores ao vivo recomendados

Suporte adicional para os seguintes codificadores de novos parceiros recomendados para o streaming ao vivo RTMP:

- [Cambria Ao Vivo 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Câmaras de ação GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Melhorias de codificação de ficheiros

- Um novo preconjunto de codificação de conteúdo consciente está agora disponível. Produz um conjunto de MP4 alinhados com gop utilizando codificação consciente do conteúdo. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada. Utiliza estes resultados para determinar o número ideal de camadas, a taxa de bits apropriada e as definições de resolução para entrega através de streaming adaptativo. Este preset é particularmente eficaz para vídeos de baixa complexidade e complexidade média, onde os ficheiros de saída estão a taxas mais baixas, mas a uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá ficheiros MP4 com vídeo e áudio interleaved. Para mais informações, consulte as [especificações abertas](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)da API .
- Melhor desempenho e multi-threading para o re-sizer em Standard Encoder. Em condições específicas, o cliente deve ver um aumento de desempenho entre 5-40% de codificação VOD. O conteúdo de baixa complexidade codificado em várias bit-rates verá os aumentos de desempenho mais elevados. 
- A codificação padrão mantém agora uma cadência gop regular para conteúdos de taxa de fotogramas variáveis (VFR) durante a codificação do VOD ao utilizar a definição de GOP baseada no tempo.  Isto significa que o cliente que submete conteúdo misto de taxa de fotogramas que varia entre 15-30 fps, por exemplo, deve agora ver distâncias GOP regulares calculadas na saída para ficheiros MP4 de streaming de bitrate adaptativo. Isto melhorará a capacidade de alternar perfeitamente entre as faixas ao entregar o HLS ou o DASH. 
-  Sincronização de AV melhorada para o conteúdo de origem da taxa de fotogramas variável (VFR)

### <a name="video-indexer-video-analytics"></a>Indexador de Vídeo, análise de vídeo

- Os quadros-chave extraídos utilizando o predefinido VideoAnalyzer estão agora na resolução original do vídeo em vez de serem redimensionados. A extração de quadros de alta resolução dá-lhe imagens de qualidade originais e permite-lhe utilizar os modelos de inteligência artificial baseados em imagem fornecidos pelos serviços Microsoft Computer Vision e Custom Vision para obter ainda mais informações a partir do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear ao vivo de eventos ao vivo

A Media Services v3 está a anunciar a pré-visualização de 24 horas x 365 dias de codificação linear ao vivo de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos a nunciando a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](../previous/legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui estes processadores de mídia legados.

Para mais informações, consulte [Migrate from Azure Media Indexer e Azure Media Indexer 2 para Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Dupla regional da África do Sul está aberta para serviços de media 

Os Serviços de Media estão agora disponíveis nas regiões do Norte e da África do Sul.

Para mais informações, consulte [Clouds e regiões em que existem serviços de mídia v3.](azure-clouds-regions.md)

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME), que estão a ser reformados. Para as datas de aposentadoria, consulte este tema de [componentes legados.](../previous/legacy-components.md)

Para mais detalhes, consulte [migrate WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [Migrar AME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição simbólica, os utilizadores finais precisam de obter um símbolo que seja enviado como parte do pedido de entrega chave. A funcionalidade *de Prevenção* de Repetição de Token permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo símbolo pode ser usado para solicitar uma chave ou uma licença. Para mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

A partir de julho, a funcionalidade de pré-visualização só estava disponível na Us Central e us West Central.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Subclipping de vídeo

Agora pode cortar ou subclipe um vídeo ao codificar-o com um [Job](https://docs.microsoft.com/rest/api/media/jobs). 

Esta funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construído utilizando os predefinições [BuiltInStandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou os predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Ver exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte do Monitor Azure para registos e métricas de diagnóstico dos Serviços de Media

Agora pode utilizar o Monitor Azure para visualizar os dados de telemetria emitidos pelos Media Services.

* Utilize os registos de diagnóstico do Monitor Azure para monitorizar os pedidos enviados pelo ponto final da entrega da chave dos serviços de mídia. 
* Monitorize as métricas emitidas pelos [pontos finais de streaming](streaming-endpoint-concept.md)de serviços de mídia .   

Para mais detalhes, consulte [as métricas do Monitor Media Services e os registos de diagnóstico.](media-services-metrics-diagnostic-logs.md)

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte de faixas de áudio multi-áudio em Embalagens Dinâmicas 

Ao transmitir Ativos que possuem múltiplas faixas de áudio com vários códigos e idiomas, a [Dynamic Packaging](dynamic-packaging-overview.md) suporta agora várias faixas de áudio para a saída hls (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Dupla regional da Coreia está aberta para os Serviços de Media 

Os Serviços de Media estão agora disponíveis nas regiões sul da Coreia e da Coreia. 

Para mais informações, consulte [Clouds e regiões em que existem serviços de mídia v3.](azure-clouds-regions.md)

### <a name="performance-improvements"></a>Melhorias no desempenho

Atualizações adicionadas que incluem melhorias no desempenho dos Serviços de Media.

* O tamanho máximo do ficheiro suportado para processamento foi atualizado. Vês, [Quotas e limites.](limits-quotas-constraints.md)
* [Codificar melhorias de velocidades.](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [O FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador incorporado.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado aos presets codificadores incorporados. Para mais informações, consulte a [codificação de conteúdos.](content-aware-encoding.md) 

## <a name="march-2019"></a>Março de 2019

A Dynamic Packaging suporta agora o Dolby Atmos. Para mais informações, consulte [Os códigos áudio suportados por embalagens dinâmicas](dynamic-packaging-overview.md#audio-codecs).

Pode agora especificar uma lista de filtros de ativos ou conta, que se aplicaria ao seu Localizador de Streaming. Para mais informações, consulte [Filtros Associados com Localizador de Streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

A Media Services v3 é agora apoiada nas nuvens nacionais de Azure. Nem todas as funcionalidades estão disponíveis em todas as nuvens ainda. Para mais detalhes, consulte Clouds e regiões nas quais existe o [Azure Media Services v3.](azure-clouds-regions.md)

O evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos schemas da Rede de Eventos Azure para serviços de media.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Ficheiros Media Encoder Standard e MPI 

Ao codificar com a Media Encoder Standard para produzir ficheiros MP4( s), é gerado um novo ficheiro .mpi e adicionado ao Ativo de saída. Este ficheiro MPI destina-se a melhorar o desempenho das [embalagens dinâmicas](dynamic-packaging-overview.md) e cenários de streaming.

Não deve modificar ou remover o ficheiro MPI, nem tomar qualquer dependência do seu serviço sobre a existência (ou não) de tal ficheiro.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações do lançamento da API da API v3 incluem:
       
* As propriedades **PresentationTimeRange** já não são 'necessárias' para **filtros** de ativos e **filtros**de conta . 
* As opções de consulta $top e $skip para **Jobs** and **Transforms** foram removidas e $orderby foi adicionada. Como parte da adição da nova funcionalidade de encomenda, descobriu-se que as opções $top e $skip tinham sido acidentalmente expostas anteriormente, mesmo que não fossem implementadas.
* A execção de enumeração foi reativada. Esta funcionalidade foi ativada nas versões de pré-visualização do SDK e foi acidentalmente desativada na versão GA.
* Duas políticas de streaming predefinidas foram renomeadas. **O SecureStreaming** é agora **MultiDrmCencStreaming**. **O SecureStreamingWithFairPlay** já está **a Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo CLI 2.0 já se encontra disponível para [o Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v.0.50.

### <a name="new-commands"></a>Novos comandos

- [az ams conta](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams conta-filtro](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams ativo](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams trabalho](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - permite-lhe gerir Unidades Reservadas de Media. Para mais informações, consulte [Unidades Reservadas](media-reserved-units-cli-how-to.md)scale Media .

### <a name="new-features-and-breaking-changes"></a>Novas funcionalidades e mudanças de rutura

#### <a name="asset-commands"></a>Comandos de ativos

- ```--storage-account```e ```--container``` argumentos adicionados.
- Valores predefinidos para o tempo de validade (Agora+23h) e permissões (Ler) no ```az ams asset get-sas-url``` comando adicionados.

#### <a name="job-commands"></a>Comandos de trabalho

- ```--correlation-data```e ```--label``` argumentos adicionados
- ```--output-asset-names```renomeado ```--output-assets```para . Agora aceita uma lista de ativos separados pelo espaço no formato 'assetName=label'. Um ativo sem rótulo pode ser enviado assim: 'assetName='.

#### <a name="streaming-locator-commands"></a>Comandos localizadores de streaming

- ```az ams streaming locator```comando base ```az ams streaming-locator```substituído por .
- ```--streaming-locator-id```e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument```argumento atualizado.
- ```--content-policy-name```renomeado ```--content-key-policy-name```para .

#### <a name="streaming-policy-commands"></a>Comandos de Política de Streaming

- ```az ams streaming policy```comando base ```az ams streaming-policy```substituído por .
- Suporte de parâmetros ```az ams streaming-policy create``` de encriptação adicionado.

#### <a name="transform-commands"></a>Transformar comandos

- ```--preset-names```argumento substituído ```--preset```por . Agora só pode definir 1 saída/predefinição de cada vez ```az ams transform output add```(para adicionar mais tem que correr). Além disso, pode definir standardEncoderPreset personalizado passando o caminho para o seu JSON personalizado.
- ```az ams transform output remove```pode ser realizado passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumentos adicionados ```az ams transform create``` ```az ams transform output add``` e comandos.

## <a name="october-2018---ga"></a>Outubro 2018 - GA

Esta secção descreve as atualizações de outubro do Azure Media Services (AMS).

### <a name="rest-v3-ga-release"></a>Libertação REST v3 GA

A [versão REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para filtros manifestos ao vivo, de conta/ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gestão de Recursos Azure 

O apoio à Gestão de Recursos Azure permite a gestão e operações unificadas API (agora tudo num só local).

A partir desta versão, pode utilizar modelos do Gestor de Recursos para criar Eventos Ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de ativos 

Foram introduzidas as seguintes melhorias:

- Ingestão de URLs DE ARMAZENAMENTO HTTP ou Azure Blob SAS.
- Especifique os nomes de contentores próprios para Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com funções Azure.

#### <a name="new-transform-object"></a>Novo objeto de transformação

O novo objeto **Transform** simplifica o modelo de codificação. O novo objeto facilita a criação e partilha de modelos e predefinições do Gestor de Recursos de codificação. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Diretório Ativo Azure e RBAC

A autenticação aditiva azure e o Controlo de Acesso baseado em funções (RBAC) permitem transformações seguras, LiveEvents, Políticas chave de conteúdo ou ativos por papel ou utilizadores em AD Azure.

#### <a name="client-sdks"></a>SDKs do Cliente  

Idiomas suportados em Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

São introduzidas as seguintes atualizações de codificação ao vivo:

- Novo modo de latência baixa para o vivo (10 segundos de ponta a ponta).
- Melhor suporte de RTMP (maior estabilidade e mais suporte codificador de fonte).
- RTMPS segura ingerir.

    Quando crias um Evento Ao Vivo, recebes 4 URLs ingerir. Os 4 URLs ingerir são quase idênticos, têm o mesmo token de streaming (AppId), apenas a parte número da porta é diferente. Dois dos URLs são primários e reserva para RTMPS. 
- Suporte de transcodificação 24 horas. 
- Melhor suporte de sinalização de anúncios em RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte melhorado da Grelha de Eventos

Pode ver as seguintes melhorias de suporte da Rede de Eventos:

- Integração da Grelha de Eventos Azure para facilitar o desenvolvimento com Aplicações Lógicas e Funções Azure. 
- Inscreva-se para eventos em Encoding, Live Channels e muito mais.

### <a name="cmaf-support"></a>Suporte cmaf

CmAF e suporte de encriptação 'cbcs' para os jogadores apple HLS (iOS 11+) e MPEG-DASH que suportam a CMAF.

### <a name="video-indexer"></a>Indexador de Vídeos

O lançamento do Video Indexer GA foi anunciado em agosto. Para novas informações sobre funcionalidades atualmente suportadas, consulte [o Que é o Indexante](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)de Vídeo . 

### <a name="plans-for-changes"></a>Planos para alterações

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure
 
O módulo Azure CLI 2.0 que inclui operações em todas as funcionalidades (incluindo Políticas de Chave de Conteúdo, Filtros de Conta/Ativos, Políticas de Streaming) está para breve. 

### <a name="known-issues"></a>Problemas conhecidos

Apenas os clientes que utilizaram a API de pré-visualização para Asset ou Filtros de Conta são impactados pelo seguinte problema.

Se criou Filtros de Ativos ou Contas entre 09/28 e 10/12 com Media Services v3 CLI ou APIs, precisa remover todos os Filtros de Ativos e Contas e recriá-los devido a um conflito de versão. 

## <a name="may-2018---preview"></a>Maio 2018 - Pré-visualização

### <a name="net-sdk"></a>SDK .NET

As seguintes funcionalidades estão presentes no .NET SDK:

* Transforma e **Jobs** para codificar ou analisar conteúdos **mediáticos.** Por exemplo, consulte [ficheiros Stream](stream-files-tutorial-with-api.md) e [Analise](analyze-videos-tutorial-with-api.md).
* **Localizadores** de streaming para publicação e streaming de conteúdos para dispositivos de utilizador final
* **Políticas de streaming** e **políticas de chave** de conteúdo para configurar a entrega de chaves e a proteção de conteúdos (DRM) ao entregar conteúdo.
* **Eventos ao vivo** e **saídas ao vivo** para configurar a ingestão e arquivamento de conteúdos de streaming ao vivo.
* **Ativos** para armazenar e publicar conteúdos de mídia no Armazenamento Azure. 
* **Streaming Endpoints** para configurar e escalar embalagens dinâmicas, encriptação e streaming para conteúdos de mídia ao vivo e a pedido.

### <a name="known-issues"></a>Problemas conhecidos

* Ao submeter um trabalho, pode especificar para ingerir o seu vídeo de origem utilizando URLs HTTPS, URLs SAS ou caminhos para ficheiros localizados no armazenamento do Azure Blob. Atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

- [Descrição Geral](media-services-overview.md)
- [Atualizações de documentação de Media Services v3](docs-release-notes.md)
- [Notas de lançamento v2 dos Media Services](../previous/media-services-release-notes.md)
