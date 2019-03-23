---
title: Notas de versão da v3 de serviços de multimédia do Azure | Documentos da Microsoft
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes nos serviços de multimédia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: a6746fa193331aff66b8726da1cb3afe49fdc9bf
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351563"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão da v3 de serviços de multimédia do Azure

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* Versões mais recentes
* Problemas conhecidos
* Correções de erros
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Atualmente, não é possível utilizar o portal do Azure para gerir os recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-sdk), CLI, ou um dos SDKs suportados.

Para obter mais informações, consulte [orientações de migração para mover de serviços de multimédia v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="march-2019"></a>Março de 2019

Empacotamento dinâmico agora suporta Dolby Atmos. Para obter mais informações, consulte [codecs de áudio suportados pelo empacotamento dinâmico](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

## <a name="february-2019"></a>Fevereiro de 2019

Serviços de multimédia v3 é agora suportada nas clouds nacionais do Azure. Nem todas as funcionalidades estão disponíveis em todas as nuvens ainda. Para obter detalhes, consulte [nuvens e regiões nos quais serviços de multimédia do Azure v3 existe](azure-clouds-regions.md).

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) eventos foi adicionado para os esquemas de Azure Event Grid para os serviços de multimédia.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Ficheiros Media Encoder Standard e MPI 

Ao codificar com o Media Encoder Standard para produzir ficheiros MP4, um novo ficheiro de .mpi é gerado e adicionado à saída de ativo. Este ficheiro MPI destina-se para melhorar o desempenho para [empacotamento dinâmico](dynamic-packaging-overview.md) e cenários de transmissão em fluxo.

Não deve modificar ou remover o arquivo MPI ou fazer qualquer dependência no seu serviço na existência (ou não) de um arquivo desse tipo.

## <a name="december-2018"></a>Dezembro de 2018

Atualizações da versão de disponibilidade geral da V3 API incluem:
       
* O **PresentationTimeRange** propriedades deixam de ser "obrigatórios" para **Asset filtros** e **filtros de conta**. 
* O $top e $skip consultar as opções para **trabalhos** e **transforma** foram removidas e $orderby foi adicionado. Como parte de adicionar a nova funcionalidade de ordenação, foi descoberto que as opções de $top e $skip acidentalmente tinham foi expostas anteriormente, mesmo que não estão implementados.
* Extensibilidade de enumeração foi reativada. Esta funcionalidade foi ativada nas versões de pré-visualização do SDK e acidentalmente foi desativada na versão GA.
* Duas políticas predefinidas de transmissão em fluxo foram alteradas. **SecureStreaming** agora é **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** agora é **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

Está agora disponível para o módulo de CLI 2.0 [dos serviços de multimédia do Azure v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [conta de AZ ams mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -permite-lhe gerir unidades reservadas de multimédia. Para obter mais informações, consulte [unidades reservadas de multimédia de escala](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Novas funcionalidades e alterações de última hora

#### <a name="asset-commands"></a>Comandos de recurso

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores predefinidos para o tempo de expiração (agora + 23h) e permissões (leitura) no ```az ams asset get-sas-url``` comando adicionado.

#### <a name="job-commands"></a>Comandos da tarefa

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` o nome mudado para ```--output-assets```. Agora, ela aceita uma lista separada por espaço dos ativos em "assetName = etiqueta ' formato. Um recurso sem etiqueta pode ser enviado como este: ' assetName ='.

#### <a name="streaming-locator-commands"></a>Comandos de localizador de transmissão em fluxo

- ```az ams streaming locator``` comando base substituídos por ```az ams streaming-locator```.
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` o nome mudado para ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de política de transmissão em fluxo

- ```az ams streaming policy``` comando base substituídos por ```az ams streaming-policy```.
- Suportam a parâmetros de criptografia em ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Transformar os comandos

- ```--preset-names``` argumento substituídos por ```--preset```. Agora pode apenas definir 1/predefinição de saída ao mesmo tempo (para adicionar mais precisa executar ```az ams transform output add```). Além disso, pode definir StandardEncoderPreset personalizada, passando o caminho para o seu JSON personalizado.
- ```az ams transform output remove``` pode ser executada, passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados ```az ams transform create``` e ```az ams transform output add``` comandos.

## <a name="october-2018---ga"></a>Outubro de 2018 - GA

Esta secção descreve as atualizações de Outubro de serviços (AMS) de multimédia do Azure.

### <a name="rest-v3-ga-release"></a>Versão de disponibilidade geral do REST v3

O [versão de disponibilidade geral do REST v3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs do Live, filtros de manifestos de nível de conta/ativo e o suporte DRM.

#### <a name="azure-resource-management"></a>Gestão de Recursos do Azure 

Suporte do Azure Resource Management permite gerenciamento unificado e operações de API (agora tudo num único local).

Começando com esta versão, pode utilizar modelos do Resource Manager para criar eventos em direto.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de recurso 

Foram introduzidas as seguintes melhorias:

- Ingestão de URLs de HTTP (s) ou URLs SAS do armazenamento de Blobs do Azure.
- Especifique a os nomes dos contentores próprios para ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com as funções do Azure.

#### <a name="new-transform-object"></a>Novo objeto de transformação

A nova **transformar** objeto simplifica o modelo de codificação. O novo objeto torna mais fácil criar e partilhar modelos de codificação do Resource Manager e suas configurações predefinidas. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Active Directory e RBAC do Azure

Autenticação do Azure AD e o controlo de acesso baseado em funções (RBAC) permitem seguro transformações, LiveEvents, diretivas de chave de conteúdo ou recursos por função ou utilizadores no Azure AD.

#### <a name="client-sdks"></a>SDKs do Cliente  

Idiomas com suporte no Serviços de multimédia v3: .NET Core, Java, node. js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação do Live

Foram introduzidas as seguintes atualizações de codificação em direto:

- Novo modo de baixa latência para live (10 segundos ponto-a-ponto).
- Suporte RTMP aprimorado (maior estabilidade e mais suporte do codificador de origem).
- Ingerir RTMPS seguro.

    Quando cria um evento em direto, agora obter 4 URLs de inserção. O 4 ingerir URLs são quase idênticos, ter o mesmo token de transmissão em fluxo (AppId), apenas a parte do número de porta é diferente. Dois dos URLs são principais e cópia de segurança para RTMPS. 
- suporte de transcodificação de 24 horas. 
- Suporte aprimorado a sinalização do ad no RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado do Event Grid

Pode ver o Event Grid seguintes melhorias de suporte:

- Integração do Azure Event Grid para o desenvolvimento mais fácil com o Logic Apps e as funções do Azure. 
- Inscreva-se para eventos no Encoding, canais em direto e muito mais.

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de encriptação CMAF e 'cbcs' para (iOS 11 +) do Apple HLS e MPEG-DASH jogadores que suportam CMAF.

### <a name="video-indexer"></a>Indexador de Vídeos

Versão de disponibilidade geral do indexador vídeo foi anunciado em Agosto. Para obter novas informações sobre as funcionalidades atualmente suportadas, consulte [o que é o Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos para que as alterações

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure
 
O módulo de CLI 2.0 do Azure que estão incluídas operações com todas as funcionalidades (incluindo em direto, diretivas de chave de conteúdo, filtros de conta/ativo, políticas de transmissão em fluxo) estará disponível brevemente. 

### <a name="known-issues"></a>Problemas conhecidos

Apenas os clientes que utilizada a API de pré-visualização para ativo ou AccountFilters são afetados pelo problema seguinte.

Se criou o ativos ou filtros de conta entre 09/28 e 10/12 com serviços de multimédia v3 CLI ou APIs, terá de remover todos os ativos e AccountFilters e voltar a criá-los devido a um conflito de versão. 

## <a name="may-2018---preview"></a>Maio de 2018 - pré-visualização

### <a name="net-sdk"></a>SDK .NET

As seguintes funcionalidades estão incluídas no SDK do .NET:

* **Transforma** e **tarefas** codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Stream arquivos](stream-files-tutorial-with-api.md) e [Analyze](analyze-videos-tutorial-with-api.md).
* **Os localizadores de transmissão em fluxo** para publicar e conteúdo para os dispositivos de utilizador final de transmissão em fluxo
* **Políticas de transmissão em fluxo** e **políticas de chaves de conteúdo** para configurar a entrega de chave e a proteção de conteúdo (DRM) quando a entrega de conteúdos.
* **Eventos em direto** e **saídas Live** para configurar a ingestão e o arquivamento de conteúdo de transmissão em fluxo em direto.
* **Ativos** para armazenar e publicar conteúdo de mídia no armazenamento do Azure. 
* **Pontos finais de transmissão em fluxo** a configurar e dimensionar o empacotamento dinâmico, encriptação e transmissão em fluxo em direto e a pedido conteúdos de multimédia.

### <a name="known-issues"></a>Problemas conhecidos

* Quando submeter uma tarefa, pode especificar para ingerir o vídeo de origem utilizar HTTPS URLs, SAS URLs ou caminhos para ficheiros localizados no armazenamento de Blobs do Azure. Atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral](media-services-overview.md)
