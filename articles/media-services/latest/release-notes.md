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
ms.date: 10/07/2019
ms.author: juliako
ms.openlocfilehash: 34d68974366eb17704b4ea1291bd1d29a0eea95a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168623"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão do Azure Media Services V3

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), a CLI ou um dos SDKs com suporte.

Para obter mais informações, consulte [diretrizes de migração para mudar dos serviços de mídia v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear dinâmica de eventos ao vivo

Os serviços de mídia v3 estão anunciando a visualização de 24 horas x 365 dias de codificação linear dinâmica de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição da *Azure Media indexer* e da versão *prévia do Azure Media indexer 2*. O processador de mídia [Azure Media indexer](../previous/media-services-index-content.md) será desativado em 1º de outubro de 2020. Os processadores de mídia da versão [prévia do Azure Media indexer 2](../previous/media-services-process-content-with-indexer2.md) serão desativados em 1º de janeiro de 2020. Os [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui esses processadores de mídia herdados.

Para obter mais informações, consulte [migrar do Azure Media indexer e Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>O par regional da África do Sul está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis na África do Sul e nas regiões do oeste da África do Sul.

Para obter mais informações, consulte [nuvens e regiões nas quais os serviços de mídia v3 existem](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame), que estão sendo desativados em 30 de novembro de 2019.

Para obter detalhes, consulte [migrar WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [migrar ame para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição de token, os usuários finais precisam obter um token que é enviado como parte da solicitação de entrega de chave. O recurso de *prevenção de reprodução de token* permite que os clientes dos serviços de mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [prevenção de reprodução de token](content-protection-overview.md#token-replay-prevention).

Esse recurso está disponível no momento em Centro dos EUA e centro-oeste dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Subcorte de vídeo

Agora você pode cortar ou subcortar um vídeo ao codificá-lo usando um [trabalho](https://docs.microsoft.com/rest/api/media/jobs). 

Essa funcionalidade funciona com qualquer [transformação](https://docs.microsoft.com/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

Veja exemplos:

* [Subclipe um vídeo com o .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte Azure Monitor para métricas e logs de diagnóstico dos serviços de mídia

Agora você pode usar Azure Monitor para exibir os dados de telemetria emmited pelos serviços de mídia.

* Use os logs de diagnóstico Azure Monitor para monitorar solicitações enviadas pelo ponto de extremidade de entrega de chave dos serviços de mídia. 
* Monitore as métricas emitidas pelos [pontos de extremidade de streaming](streaming-endpoint-concept.md)dos serviços de mídia.   

Para obter detalhes, consulte [monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte a faixas de vários áudio no empacotamento dinâmico 

Ao transmitir ativos que têm várias faixas de áudio com vários codecs e linguagens, o [empacotamento dinâmico](dynamic-packaging-overview.md) agora dá suporte a faixas de vários áudio para a saída HLS (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>O par regional da Coreia está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis nas regiões da Coreia central e da Coreia do Sul. 

Para obter mais informações, consulte [nuvens e regiões nas quais os serviços de mídia v3 existem](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Melhorias de desempenho

Atualizações adicionadas que incluem melhorias de desempenho dos serviços de mídia.

* O tamanho máximo de arquivo com suporte para processamento foi atualizado. Confira, [cotas e limitações](limits-quotas-constraints.md).
* A [codificação acelera as melhorias](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador interno.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado às predefinições do codificador interno. Para obter mais informações, consulte [codificação com reconhecimento de conteúdo](cae-experimental.md). 

## <a name="march-2019"></a>Março de 2019

O empacotamento dinâmico agora dá suporte a Dolby Atmos. Para obter mais informações, consulte [codecs de áudio com suporte pelo empacotamento dinâmico](dynamic-packaging-overview.md#audio-codecs).

Agora você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. Para obter mais informações, consulte [associar filtros ao localizador de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

O Media Services V3 agora tem suporte nas nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens ainda. Para obter detalhes, consulte [nuvens e regiões nas quais os serviços de mídia do Azure v3 existem](azure-clouds-regions.md).

O evento [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos esquemas da grade de eventos do Azure para os serviços de mídia.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Arquivos Media Encoder Standard e MPI 

Ao codificar com Media Encoder Standard para produzir arquivos MP4, um novo arquivo. MPI é gerado e adicionado ao ativo de saída. Este arquivo MPI destina-se a melhorar o desempenho para cenários de streaming e [empacotamento dinâmico](dynamic-packaging-overview.md) .

Você não deve modificar ou remover o arquivo MPI ou assumir qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão GA da API v3 incluem:
       
* As propriedades **PresentationTimeRange** não são mais ' necessárias ' para **filtros de ativos** e **filtros de conta**. 
* As opções de consulta $top e $skip para **trabalhos** e **transformações** foram removidas e $OrderBy foi adicionado. Como parte da adição da nova funcionalidade de ordenação, foi descoberto que as opções de $top e $skip eram acidentalmente expostas anteriormente, embora não tenham sido implementadas.
* A extensibilidade de enumeração foi reabilitada. Este recurso foi habilitado nas versões de visualização do SDK e foi desabilitado acidentalmente na versão GA.
* Duas políticas de streaming predefinidas foram renomeadas. **SecureStreaming** agora é **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** agora é **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo CLI 2,0 agora está disponível para os [serviços de mídia do Azure v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [AZ conta AMS](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [AZ conta AMS – filtro](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [AZ Asset AMS](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ AMS Asset-Filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ AMS Content-Key-Policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [trabalho AZ AMS](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ AMS Live-Event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ AMS Live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ AMS streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ AMS streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -permite que você gerencie unidades reservadas de mídia. Para obter mais informações, consulte [dimensionar unidades reservadas de mídia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Novos recursos e alterações significativas

#### <a name="asset-commands"></a>Comandos de ativo

- argumentos ```--storage-account``` e ```--container``` adicionados.
- Valores padrão para tempo de expiração (Now + 23h) e permissões (leitura) no comando ```az ams asset get-sas-url``` adicionado.

#### <a name="job-commands"></a>Comandos de trabalho

- argumentos ```--correlation-data``` e ```--label``` adicionados
- ```--output-asset-names``` renomeado como ```--output-assets```. Agora ele aceita uma lista de ativos separados por espaços no formato ' assetname = rótulo '. Um ativo sem rótulo pode ser enviado da seguinte maneira: ' assetname = '.

#### <a name="streaming-locator-commands"></a>Comandos do localizador de streaming

- comando base ```az ams streaming locator``` substituído por ```az ams streaming-locator```.
- argumentos ```--streaming-locator-id``` e ```--alternative-media-id support``` adicionados.
- argumento ```--content-keys argument``` atualizado.
- ```--content-policy-name``` renomeado como ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de política de streaming

- comando base ```az ams streaming policy``` substituído por ```az ams streaming-policy```.
- Os parâmetros de criptografia dão suporte ao ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Comandos de transformação

- argumento ```--preset-names``` substituído por ```--preset```. Agora você só pode definir 1 saída/predefinição de cada vez (para adicionar mais, você precisa executar ```az ams transform output add```). Além disso, você pode definir StandardEncoderPreset personalizados passando o caminho para o JSON personalizado.
- ```az ams transform output remove``` pode ser executado passando o índice de saída para remover.
- argumentos ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` adicionados nos comandos ```az ams transform create``` e ```az ams transform output add```.

## <a name="october-2018---ga"></a>Outubro de 2018 – GA

Esta seção descreve as atualizações de outubro dos serviços de mídia do Azure (AMS).

### <a name="rest-v3-ga-release"></a>Versão de GA V3 do REST

A [versão do REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para filtros de manifesto de nível de conta/ativo, e suporte a DRM em tempo real.

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

### <a name="video-indexer"></a>Indexador de Vídeo

Video Indexer versão de GA foi anunciada em agosto. Para obter novas informações sobre os recursos com suporte no momento, consulte [o que é Video indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
O módulo CLI do Azure 2,0 que inclui operações em todos os recursos (incluindo políticas dinâmicas, de chave de conteúdo, filtros de conta/ativo, políticas de streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de visualização para ativos ou AccountFilters são afetados pelo seguinte problema.

Se você criou os ativos ou filtros de conta entre 09/28 e 10/12 com CLI ou APIs do Media Services V3, você precisa remover todos os ativos e AccountFilters e recriá-los devido a um conflito de versão. 

## <a name="may-2018---preview"></a>Maio de 2018-visualização

### <a name="net-sdk"></a>.NET SDK

Os seguintes recursos estão presentes no SDK do .NET:

* **Transformações** e **trabalhos** para codificar ou analisar conteúdo de mídia. Para obter exemplos, consulte [transmitir arquivos](stream-files-tutorial-with-api.md) e [analisar](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicação e streaming de conteúdo para dispositivos de usuário final
* **Políticas de streaming** e **políticas de chave de conteúdo** para configurar o fornecimento de chave e a proteção de conteúdo (DRM) ao fornecer conteúdo.
* **Eventos ao vivo** e **saídas ao vivo** para configurar a ingestão e o arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar conteúdo de mídia no armazenamento do Azure. 
* **Pontos de extremidade de streaming** para configurar e dimensionar empacotamento dinâmico, criptografia e streaming para conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar a ingestão do vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no armazenamento de BLOBs do Azure. Atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral](media-services-overview.md)
- [Notas de versão do Media Services v2](../previous/media-services-release-notes.md)
