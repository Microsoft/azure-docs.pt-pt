---
title: Notas de versão do Azure Media Services V3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: b3d5e1f814a8eb083ab01623051f1b5b3723a9f1
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049636"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão do Azure Media Services V3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` no leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* Versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize o [REST API,](https://aka.ms/ams-v3-rest-sdk)CLI ou um dos SDKs suportados.

Para mais informações, consulte [a orientação da Migração para a mudança dos Serviços de Media v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Um suporte melhorado para fontes interligadas em Análise de Vídeo – esse conteúdo está agora desinterligado corretamente antes de ser enviado para motores de inferência.
- Ao gerar miniaturas com o modo "Best", o codificador procura agora mais de 30 segundos para selecionar uma moldura que não seja monocromática.
 
## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Visualização de transcrição ao vivo

A transcrição ao vivo agora está em visualização pública e disponível para uso na região oeste dos EUA 2.

A transcrição ao vivo foi projetada para trabalhar em conjunto com eventos ao vivo como um recurso complementar.  Há suporte para eventos ao vivo de codificação de passagem e Standard ou Premium.  Quando esta funcionalidade está ativada, o serviço utiliza a funcionalidade [Discurso-Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio que está a chegar em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS. A cobrança é baseada em um novo medidor de complemento que é custo adicional para o evento ao vivo quando ele está no estado "em execução".  Para mais detalhes sobre transcrição ao vivo e faturação, consulte [transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como um recurso de visualização na região oeste dos EUA 2. Ele dá suporte à transcrição de palavras faladas em inglês (en-US) somente no momento.

### <a name="content-protection"></a>Proteção de conteúdo

O recurso *Token Replay Prevention* lançado em regiões limitadas em setembro já está disponível em todas as regiões.
Os clientes dos serviços de mídia agora podem definir um limite no número de vezes que o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros de codificador dinâmico recomendados

Adição de suporte para os seguintes novos codificadores de parceiros recomendados para streaming ao vivo RTMP:

- [Cambria Ao Vivo 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Câmaras de ação GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Aprimoramentos de codificação de arquivo

- Uma nova predefinição de codificação com reconhecimento de conteúdo está disponível agora. Ele produz um conjunto de MP4s alinhado a GOP usando a codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada. Ele usa esses resultados para determinar o número ideal de camadas, a taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de baixa complexidade e de complexidade média, em que os arquivos de saída têm taxas de bits menores, mas com uma qualidade que ainda oferece uma boa experiência aos visualizadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para mais informações, consulte as [especificações abertas](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)da API .
- Melhor desempenho e multithreading para o redimensionador em Media Encoder Standard. Em condições específicas, o cliente deve ver um aumento de desempenho entre 5-40% de codificação VOD. O conteúdo de baixa complexidade codificado em várias taxas de bits verá o maior desempenho aumenta. 
- A codificação padrão agora mantém uma cadência GOP regular para conteúdo de VFR (taxa de quadros variável) durante a codificação VOD ao usar a configuração de GOP baseada em tempo.  Isso significa que o cliente que envia conteúdo de taxa de quadros misto que varia entre 15-30 fps, por exemplo, agora deve ver as distâncias GOP regulares calculadas na saída para arquivos MP4 de streaming de taxa de bits adaptável. Isso melhorará a capacidade de alternar diretamente entre as faixas ao entregar HLS ou DASH. 
-  Sincronização antivírus aprimorada para conteúdo de origem de taxa de quadros variável (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, análise de vídeo

- Os quadros-chave extraídos usando a predefinição VideoAnalyzer agora estão na resolução original do vídeo em vez de serem redimensionados. A extração de quadro-chave de alta resolução fornece imagens de qualidade original e permite que você use os modelos de inteligência artificial baseados em imagem fornecidos pelo Microsoft Pesquisa Visual Computacional e Visão Personalizada Services para obter ainda mais informações do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear dinâmica de eventos ao vivo

Os serviços de mídia v3 estão anunciando a visualização de 24 horas x 365 dias de codificação linear dinâmica de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos a nunciando a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](../previous/legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui estes processadores de mídia legados.

Para mais informações, consulte [Migrate from Azure Media Indexer e Azure Media Indexer 2 para Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>O par regional da África do Sul está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis na África do Sul e nas regiões do oeste da África do Sul.

Para mais informações, consulte [Clouds e regiões em que existem serviços de mídia v3.](azure-clouds-regions.md)

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME), que estão a ser reformados. Para as datas de aposentadoria, consulte este tema de [componentes legados.](../previous/legacy-components.md)

Para mais detalhes, consulte [migrate WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [Migrar AME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição de token, os usuários finais precisam obter um token que é enviado como parte da solicitação de entrega de chave. A funcionalidade *de Prevenção* de Repetição de Token permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo símbolo pode ser usado para solicitar uma chave ou uma licença. Para mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

A partir de julho, o recurso de visualização só estava disponível em Centro dos EUA e centro-oeste dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Subcorte de vídeo

Agora pode cortar ou subclipe um vídeo ao codificar-o com um [Job](https://docs.microsoft.com/rest/api/media/jobs). 

Esta funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construído utilizando os predefinições [BuiltInStandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou os predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Veja exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte Azure Monitor para métricas e logs de diagnóstico dos serviços de mídia

Agora pode utilizar o Monitor Azure para visualizar os dados de telemetria emitidos pelos Media Services.

* Use os logs de diagnóstico Azure Monitor para monitorar solicitações enviadas pelo ponto de extremidade de entrega de chave dos serviços de mídia. 
* Monitorize as métricas emitidas pelos [pontos finais de streaming](streaming-endpoint-concept.md)de serviços de mídia .   

Para mais detalhes, consulte [as métricas do Monitor Media Services e os registos de diagnóstico.](media-services-metrics-diagnostic-logs.md)

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte a faixas de vários áudio no empacotamento dinâmico 

Ao transmitir Ativos que possuem múltiplas faixas de áudio com vários códigos e idiomas, a [Dynamic Packaging](dynamic-packaging-overview.md) suporta agora várias faixas de áudio para a saída hls (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>O par regional da Coreia está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis nas regiões da Coreia central e da Coreia do Sul. 

Para mais informações, consulte [Clouds e regiões em que existem serviços de mídia v3.](azure-clouds-regions.md)

### <a name="performance-improvements"></a>Melhorias no desempenho

Atualizações adicionadas que incluem melhorias de desempenho dos serviços de mídia.

* O tamanho máximo de arquivo com suporte para processamento foi atualizado. Vês, [Quotas e limitações.](limits-quotas-constraints.md)
* [Codificar melhorias de velocidades.](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [O FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador incorporado.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado aos presets codificadores incorporados. Para mais informações, consulte a [codificação de conteúdos.](content-aware-encoding.md) 

## <a name="march-2019"></a>Março de 2019

O empacotamento dinâmico agora dá suporte a Dolby Atmos. Para mais informações, consulte [Os códigos áudio suportados por embalagens dinâmicas](dynamic-packaging-overview.md#audio-codecs).

Agora você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. Para mais informações, consulte [Filtros Associados com Localizador de Streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

O Media Services V3 agora tem suporte nas nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens ainda. Para mais detalhes, consulte Clouds e regiões nas quais existe o [Azure Media Services v3.](azure-clouds-regions.md)

O evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos schemas da Rede de Eventos Azure para serviços de media.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Arquivos Media Encoder Standard e MPI 

Ao codificar com Media Encoder Standard para produzir arquivos MP4, um novo arquivo. MPI é gerado e adicionado ao ativo de saída. Este ficheiro MPI destina-se a melhorar o desempenho das [embalagens dinâmicas](dynamic-packaging-overview.md) e cenários de streaming.

Você não deve modificar ou remover o arquivo MPI ou assumir qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão GA da API v3 incluem:
       
* As propriedades **PresentationTimeRange** já não são 'necessárias' para **filtros** de ativos e **filtros**de conta . 
* As opções de consulta $top e $skip para **Jobs** and **Transforms** foram removidas e $orderby foi adicionada. Como parte da adição da nova funcionalidade de ordenação, foi descoberto que as opções de $top e $skip eram acidentalmente expostas anteriormente, embora não tenham sido implementadas.
* A extensibilidade de enumeração foi reabilitada. Este recurso foi habilitado nas versões de visualização do SDK e foi desabilitado acidentalmente na versão GA.
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

### <a name="new-features-and-breaking-changes"></a>Novos recursos e alterações significativas

#### <a name="asset-commands"></a>Comandos de ativo

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores predefinidos para o tempo de validade (Agora+23h) e permissões (Ler) no comando ```az ams asset get-sas-url``` adicionados.

#### <a name="job-commands"></a>Comandos de trabalho

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` renomeado para ```--output-assets```. Agora ele aceita uma lista de ativos separados por espaços no formato ' assetname = rótulo '. Um ativo sem rótulo pode ser enviado da seguinte maneira: ' assetname = '.

#### <a name="streaming-locator-commands"></a>Comandos do localizador de streaming

- ```az ams streaming locator``` comando base substituído por ```az ams streaming-locator```.
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` renomeado para ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de política de streaming

- ```az ams streaming policy``` comando base substituído por ```az ams streaming-policy```.
- Suporte de parâmetros de encriptação em ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Comandos de transformação

- ```--preset-names``` argumento substituído por ```--preset```. Agora só pode definir 1 saída/predefinição de cada vez (para adicionar mais tem que executar ```az ams transform output add```). Além disso, você pode definir StandardEncoderPreset personalizados passando o caminho para o JSON personalizado.
- ```az ams transform output remove``` pode ser realizada passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados aos comandos ```az ams transform create``` e ```az ams transform output add```.

## <a name="october-2018---ga"></a>Outubro de 2018 – GA

Esta seção descreve as atualizações de outubro dos serviços de mídia do Azure (AMS).

### <a name="rest-v3-ga-release"></a>Versão de GA V3 do REST

A [versão REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para filtros manifestos ao vivo, de conta/ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gerenciamento de recursos do Azure 

O suporte para o gerenciamento de recursos do Azure permite o gerenciamento unificado e a API de operações (agora tudo em um único lugar).

A partir desta versão, você pode usar modelos do Resource Manager para criar eventos ao vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria de operações de ativo 

Os seguintes aprimoramentos foram introduzidos:

- Ingestão de URLs de HTTP ou de URLs de SAS do armazenamento de BLOBs do Azure.
- Especifique seus próprios nomes de contêiner para ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com Azure Functions.

#### <a name="new-transform-object"></a>Novo objeto de transformação

O novo objeto **Transform** simplifica o modelo de codificação. O novo objeto torna fácil criar e compartilhar modelos e predefinições do Resource Manager de codificação. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação de Azure Active Directory e RBAC

A autenticação do Azure AD e o RBAC (controle de acesso baseado em função) permitem transformações seguras, LiveEvents, políticas de chave de conteúdo ou ativos por função ou usuários no Azure AD.

#### <a name="client-sdks"></a>SDKs do Cliente  

Idiomas com suporte nos serviços de mídia V3: .NET Core, Java, Node. js, Ruby, typescript, Python, go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

As seguintes atualizações de codificação ao vivo são introduzidas:

- Novo modo de baixa latência para Live (10 segundos de ponta a ponta).
- Suporte a RTMP melhorado (maior estabilidade e mais suporte a codificador de origem).
- Ingestão segura do RTMPS.

    Quando você cria um evento ao vivo, agora recebe 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId), apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS. 
- suporte de transcodificação de 24 horas. 
- Suporte de sinalização de AD aprimorado em RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado à grade de eventos

Você pode ver os seguintes aprimoramentos de suporte da grade de eventos:

- Integração da grade de eventos do Azure para facilitar o desenvolvimento com aplicativos lógicos e Azure Functions. 
- Assine eventos sobre codificação, canais ao vivo e muito mais.

### <a name="cmaf-support"></a>Suporte do CMAF

Suporte à criptografia CMAF e ' CBCs ' para os players Apple HLS (iOS 11 +) e MPEG-DASH que dão suporte a CMAF.

### <a name="video-indexer"></a>Indexador de Vídeos

Video Indexer versão de GA foi anunciada em agosto. Para novas informações sobre funcionalidades atualmente suportadas, consulte [o Que é o Indexante](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)de Vídeo . 

### <a name="plans-for-changes"></a>Planos para que as alterações

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure
 
O módulo CLI do Azure 2,0 que inclui operações em todos os recursos (incluindo políticas dinâmicas, de chave de conteúdo, filtros de conta/ativo, políticas de streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de visualização para ativos ou AccountFilters são afetados pelo seguinte problema.

Se você criou os ativos ou filtros de conta entre 09/28 e 10/12 com CLI ou APIs do Media Services V3, você precisa remover todos os ativos e AccountFilters e recriá-los devido a um conflito de versão. 

## <a name="may-2018---preview"></a>Maio de 2018-visualização

### <a name="net-sdk"></a>SDK .NET

Os seguintes recursos estão presentes no SDK do .NET:

* Transforma e **Jobs** para codificar ou analisar conteúdos **mediáticos.** Por exemplo, consulte [ficheiros Stream](stream-files-tutorial-with-api.md) e [Analise](analyze-videos-tutorial-with-api.md).
* **Localizadores** de streaming para publicação e streaming de conteúdos para dispositivos de utilizador final
* **Políticas de streaming** e **políticas de chave** de conteúdo para configurar a entrega de chaves e a proteção de conteúdos (DRM) ao entregar conteúdo.
* **Eventos ao vivo** e **saídas ao vivo** para configurar a ingestão e arquivamento de conteúdos de streaming ao vivo.
* **Ativos** para armazenar e publicar conteúdos de mídia no Armazenamento Azure. 
* **Streaming Endpoints** para configurar e escalar embalagens dinâmicas, encriptação e streaming para conteúdos de mídia ao vivo e a pedido.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar a ingestão do vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no armazenamento de BLOBs do Azure. Atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral](media-services-overview.md)
- [Notas de lançamento v2 dos Media Services](../previous/media-services-release-notes.md)
