---
title: Notas de versão dos serviços de mídia | Microsoft Docs
description: Notas de versão dos serviços de mídia
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 6fea7b7d3d3ef3b1a46aeeff0bab8fef2a9bf3ad
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860347"
---
# <a name="azure-media-services-release-notes"></a>Notas de versão dos serviços de mídia do Azure

Estas notas de versão dos serviços de mídia do Azure resumem as alterações de versões anteriores e problemas conhecidos.

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Queremos ouvir nossos clientes para que possamos nos concentrar na correção de problemas que afetam você. Para relatar um problema ou fazer perguntas, envie uma postagem no [Fórum do MSDN dos serviços de mídia do Azure]. 

## <a name="a-idissuesknown-issues"></a><a id="issues"/>Problemas conhecidos
### <a name="a-idgeneral_issuesmedia-services-general-issues"></a><a id="general_issues"/>Problemas gerais dos serviços de mídia

| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na API REST. |Se você desenvolver aplicativos de serviços de mídia usando a API REST, verá que alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID) não têm suporte. Os cabeçalhos serão adicionados em uma atualização futura. |
| A codificação por porcentagem não é permitida. |Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao compilar URLs para o conteúdo de streaming (por `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`exemplo,). Por esse motivo, a codificação por porcentagem não é permitida. O valor da propriedade Name não pode ter nenhum dos seguintes [caracteres reservados para codificação de porcentagem](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ & = + $,/?% # [] ". Além disso, pode haver apenas um "." para a extensão de nome de arquivo. |
| O método ListBlobs que faz parte do SDK do armazenamento do Azure versão 3. x falha. |Os serviços de mídia geram URLs SAS com base na versão [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) . Se você quiser usar o SDK de armazenamento para listar BLOBs em um contêiner de BLOB, use o método [CloudBlobContainer. ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) que faz parte do SDK de armazenamento versão 2. x. |
| O mecanismo de limitação dos serviços de mídia restringe o uso de recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP 503 de "Serviço indisponível". |Para obter mais informações, consulte a descrição do código de status HTTP 503 nos [códigos de erro dos serviços de mídia](media-services-encoding-error-codes.md). |
| Quando você consulta entidades, um limite de 1.000 entidades é retornado ao mesmo tempo porque o REST público versão 2 limita os resultados da consulta para 1.000 resultados. |Use Skip e Take (.NET)/Top (REST), conforme descrito neste exemplo de [.net](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem vir por um problema de marca de repetição no manifesto Smooth Streaming. |Para obter mais informações, consulte [esta seção](media-services-deliver-content-overview.md#known-issues). |
| Os objetos do SDK do .NET dos serviços de mídia não podem ser serializados e, como resultado, não funcionam com o cache do Azure para Redis. |Se você tentar serializar o objeto Assetcollection do SDK para adicioná-lo ao cache do Azure para Redis, uma exceção será lançada. |
|A API REST responde com uma mensagem de erro dizendo "o filtro não pode ser acessado por esta versão da API REST" ao tentar obter um filtro de nível de conta ou ativo.|O filtro foi criado ou modificado com uma versão de API mais recente do que está sendo usado para tentar obter o filtro. Isso pode acontecer se duas versões de API estiverem sendo usadas pelo código ou por ferramentas que estão sendo usadas pelo cliente.  A melhor solução aqui é atualizar o código ou as ferramentas para usar as duas versões de API mais recentes.|

## <a name="a-idrest_version_historyrest-api-version-history"></a><a id="rest_version_history"/>Histórico de versão da API REST
Para obter informações sobre o histórico de versão da API REST dos serviços de mídia, consulte a [referência de API REST de serviços de mídia do Azure].

## <a name="august-2019"></a>Agosto de 2019

### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame), que estão sendo desativados em 30 de novembro de 2019.

Para obter detalhes, consulte [migrar WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [migrar ame para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Março de 2019

O recurso de visualização de hiperlapsação de mídia dos serviços de mídia do Azure foi preterido.

## <a name="december-2018"></a>Dezembro de 2018

O recurso de visualização de hiperlapsação de mídia dos serviços de mídia do Azure será desativado em breve. A partir de 19 de dezembro de 2018, os serviços de mídia não farão mais alterações ou melhorias na hiperlapsação de mídia. Em 29 de março de 2019, ele será desativado e não estará mais disponível.

## <a name="october-2018"></a>Outubro de 2018

### <a name="cmaf-support"></a>Suporte do CMAF

Suporte à criptografia CMAF e ' CBCs ' para os players Apple HLS (iOS 11 +) e MPEG-DASH que dão suporte a CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprites da miniatura do VTT da Web

Agora você pode usar os serviços de mídia para gerar sprites de miniaturas de VTT da Web usando nossas APIs v2. Para obter mais informações, consulte [gerar uma miniatura Sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Julho de 2018

Com a versão mais recente do serviço, há alterações de formatação secundárias nas mensagens de erro retornadas pelo serviço quando um trabalho falha, com relação a como ele é dividido em duas ou mais linhas.

## <a name="may-2018"></a>Maio de 2018 

A partir de 12 de maio de 2018, os canais ao vivo não serão mais compatíveis com o protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Migre do RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="october-2017-release"></a>Versão de outubro de 2017
> [!IMPORTANT] 
> Os serviços de mídia estão preterindo o suporte para chaves de autenticação do serviço de controle de acesso do Azure. Em 22 de junho de 2018, você não pode mais autenticar com o back-end dos serviços de mídia por meio de código usando as chaves do serviço de controle de acesso. Você deve atualizar seu código para usar o Azure Active Directory (AD do Azure) por [autenticação baseada no Azure ad](media-services-use-aad-auth-to-access-ams-api.md). Observe os avisos sobre essa alteração na portal do Azure.

### <a name="updates-for-october-2017"></a>Atualizações de outubro de 2017
#### <a name="sdks"></a>SDKs
* O SDK do .NET foi atualizado para dar suporte à autenticação do Azure AD. O suporte para autenticação do serviço de controle de acesso foi removido do SDK do .NET mais recente no Nuget.org para incentivar a migração mais rápida para o Azure AD. 
* O SDK do JAVA foi atualizado para dar suporte à autenticação do Azure AD. O suporte à autenticação do Azure AD foi adicionado ao SDK do Java. Para obter informações sobre como usar o SDK do Java com os serviços de mídia, consulte Introdução [ao SDK do cliente Java para os serviços de mídia do Azure](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificação baseada em arquivo
* Agora você pode usar o codificador Premium para codificar seu conteúdo para o codec de vídeo HEVC (codificação de vídeo de alta eficiência) H. 265. Não haverá impacto no preço se você escolher H. 265 sobre outros codecs, como H. 264. Para obter informações sobre licenças de patente HEVC, consulte [termos de serviços online](https://azure.microsoft.com/support/legal/).
* Para o vídeo de origem codificado com o codec de vídeo H. 265 (HEVC), como vídeo capturado usando iOS11 ou GoPro Hero 6, agora você pode usar o codificador Premium ou o codificador padrão para codificar esses vídeos. Para obter informações sobre licenças de patente, consulte [termos de serviços online](https://azure.microsoft.com/support/legal/).
* Para conteúdo que contém várias faixas de áudio de idioma, os valores de idioma devem ser rotulados corretamente de acordo com a especificação de formato de arquivo correspondente (por exemplo, ISO MP4). Em seguida, você pode usar o codificador padrão para codificar o conteúdo para streaming. O localizador de streaming resultante lista os idiomas de áudio disponíveis.
* O codificador padrão agora dá suporte a duas novas predefinições de sistema somente áudio, "áudio AAC" e "áudio AAC de boa qualidade". Ambos produzem saída de AAC (codificação avançada de áudio) estéreo, taxas de bits de 128 kbps e 192 kbps, respectivamente.
* O codificador Premium agora dá suporte aos formatos de arquivo QuickTime/MOV como entrada. O codec de vídeo deve ser um dos [tipos ProRes da Apple listados neste artigo do GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). O áudio deve ser AAC ou a modulação de código de pulso (PCM). O codificador Premium não dá suporte a, por exemplo, vídeo DVC/DVCPro encapsulado em arquivos QuickTime/MOV como entrada. O codificador padrão oferece suporte a esses codecs de vídeo.
* As seguintes correções de bugs foram feitas em codificadores:

    * Agora você pode enviar trabalhos usando um ativo de entrada. Após a conclusão desses trabalhos, você pode modificar o ativo (por exemplo, adicionar, excluir ou renomear arquivos dentro do ativo) e enviar trabalhos adicionais.
    * A qualidade das miniaturas JPEG produzidas pelo codificador Standard foi aprimorada.
    * O codificador padrão manipula os metadados de entrada e a geração de miniatura melhor em vídeos com duração muito curta.
    * Melhorias no decodificador H. 264 usado no codificador Standard eliminam determinados artefatos raros. 

#### <a name="media-analytics"></a>Análise de Multimédia
Disponibilidade geral do Azure Media Redactor: Esse processador de mídia executa a anonimato, desfocando os rostos de indivíduos selecionados e é ideal para uso em cenários de mídia de notícias e segurança pública. 

Para obter uma visão geral sobre esse novo processador, consulte [esta postagem no blog](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter informações sobre documentação e configurações, consulte [redação faces com análise de mídia do Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versão de junho de 2017

Os serviços de mídia agora dão suporte à [autenticação baseada no Azure ad](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Atualmente, os serviços de mídia oferecem suporte ao modelo de autenticação do serviço de controle de acesso. A autorização do serviço de controle de acesso será preterida em 1º de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="march-2017-release"></a>Versão de março de 2017

Agora você pode usar o codificador padrão para [gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md) especificando a cadeia de caracteres de predefinição "streaming adaptável" ao criar uma tarefa de codificação. Para codificar um vídeo para streaming com os serviços de mídia, use a predefinição "streaming adaptável". Para personalizar uma predefinição de codificação para seu cenário específico, você pode começar com [essas predefinições](media-services-mes-presets-overview.md).

Agora você pode usar Media Encoder Standard ou Media Encoder Premium Workflow para [criar uma tarefa de codificação que gera partes fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Versão de fevereiro de 2017

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias será excluído automaticamente, juntamente com seus registros de tarefa associados. A exclusão ocorre mesmo que o número total de registros esteja abaixo da cota máxima. Para arquivar as informações de trabalho/tarefa, você pode usar o código descrito em [gerenciar ativos e entidades relacionadas com o SDK do .net dos serviços de mídia](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versão de janeiro de 2017

Nos serviços de mídia, um ponto de extremidade de streaming representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo) para distribuição posterior. Os serviços de mídia também fornecem integração direta de rede de distribuição de conteúdo do Azure. O fluxo de saída de um serviço StreamingEndpoint pode ser uma transmissão ao vivo, um vídeo sob demanda ou um download progressivo de seu ativo na sua conta de serviços de mídia. Cada conta dos serviços de mídia inclui um ponto de extremidade de streaming padrão. Pontos de extremidade de streaming adicionais podem ser criados na conta. 

Há duas versões de pontos de extremidade de streaming, 1,0 e 2,0. A partir de 10 de janeiro de 2017, quaisquer contas de serviços de mídia recém-criadas incluem o ponto de extremidade de streaming padrão da versão 2,0. Os pontos de extremidade de streaming adicionais que você adicionar a essa conta também são da versão 2,0. Essa alteração não afeta as contas existentes. Os pontos de extremidade de streaming existentes são da versão 1,0 e podem ser atualizados para a versão 2,0. Há alterações de comportamento, cobrança e recursos com essa alteração. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

A partir da versão 2,15, os serviços de mídia adicionaram as seguintes propriedades à entidade de ponto de extremidade de streaming:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Para obter mais informações sobre essas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versão de dezembro de 2016

 Agora você pode usar os serviços de mídia para acessar dados de telemetria/métricas para seus serviços. Você pode usar a versão atual dos serviços de mídia para coletar dados de telemetria para o canal ao vivo, ponto de extremidade de streaming e entidades de arquivamento. Para obter mais informações, consulte [telemetria dos serviços de mídia](media-services-telemetry-overview.md).

## <a name="a-idjuly_changes16july-2016-release"></a><a id="july_changes16"/>Versão de julho de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações para o arquivo de manifesto (*. ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação é enviada para Media Encoder Standard ou o Media Encoder Premium, a tarefa de codificação gera um [arquivo de manifesto de streaming](media-services-deliver-content-overview.md) (*. ISM) no ativo de saída. Com a versão mais recente do serviço, a sintaxe desse arquivo de manifesto de streaming foi atualizada.

> [!NOTE]
> A sintaxe do arquivo de manifesto de streaming (. ISM) é reservada para uso interno. Está sujeito a alterações em versões futuras. Não modifique nem manipule o conteúdo deste arquivo.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. ISMC) é gerado no ativo de saída quando uma tarefa de codificação gera um ou mais arquivos MP4
A partir da versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais arquivos MP4, o ativo de saída também contém um arquivo de manifesto do cliente de streaming (*. ismc). O arquivo. ismc ajuda a melhorar o desempenho do streaming dinâmico. 

> [!NOTE]
> A sintaxe do arquivo de manifesto do cliente (. ismc) é reservada para uso interno. Está sujeito a alterações em versões futuras. Não modifique nem manipule o conteúdo deste arquivo.
> 
> 

Para obter mais informações, veja [este blogue](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem vir por um problema de marca de repetição no manifesto Smooth Streaming. Para obter mais informações, consulte [esta seção](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Versão de abril de 2016
### <a name="media-analytics"></a>Análise de Multimédia
 Os serviços de mídia introduziram Análise de Mídia para inteligência de vídeo avançada. Para obter mais informações, consulte [visão geral da análise de serviços de mídia](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (versão prévia)
Agora você pode usar os serviços de mídia para criptografar dinamicamente seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay. Você também pode usar o serviço de entrega de licença dos serviços de mídia para fornecer licenças do FairPlay para os clientes. Para obter mais informações, consulte "usar os serviços de mídia do Azure para transmitir seu conteúdo do HLS protegido com o Apple FairPlay".

## <a id="feb_changes16"></a>Versão de fevereiro de 2016
A versão mais recente do SDK dos serviços de mídia para .NET (3.5.3) contém uma correção de bug relacionada ao Google Widevine. Era impossível reutilizar o AssetDeliveryPolicy para vários ativos criptografados com o Widevine. Como parte dessa correção de bug, a seguinte propriedade foi adicionada ao SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Versão de janeiro de 2016
As unidades reservadas para codificação foram renomeadas para reduzir a confusão com nomes de codificador.

As unidades reservadas para codificação básica, Standard e Premium foram renomeadas para unidades reservadas S1, S2 e S3, respectivamente. Os clientes que usam unidades reservadas de codificação básica atualmente veem S1 como o rótulo no portal do Azure (e na fatura). Os clientes que usam Standard e Premium veem os rótulos S2 e S3, respectivamente. 

## <a id="dec_changes_15"></a>Versão de dezembro de 2015

### <a name="media-encoder-deprecation-announcement"></a>Comunicado de substituição do codificador de mídia

 O codificador de mídia será preterido a partir de aproximadamente 12 meses a partir da versão do Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>SDK do Azure para PHP
A equipe do SDK do Azure publicou uma nova versão do pacote [SDK do Azure para php](https://github.com/Azure/azure-sdk-for-php) que contém atualizações e novos recursos para os serviços de mídia. Em particular, o SDK dos serviços de mídia para PHP agora dá suporte aos recursos de [proteção de conteúdo](media-services-content-protection-overview.md) mais recentes. Esses recursos são criptografia dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrições de token. Ele também dá suporte a [unidades de codificação](media-services-dotnet-encoding-units.md)de dimensionamento.

Para obter mais informações, consulte:

* Os [exemplos de código](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) a seguir ajudam você a começar rapidamente:
  * **vodworkflow_aes.php**: Este arquivo PHP mostra como usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves. Ele é baseado no exemplo do .NET explicado em [usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Este arquivo PHP mostra como usar a criptografia dinâmica do PlayReady e o serviço de entrega de licença. Ele é baseado no exemplo do .NET explicado em [usar a criptografia comum dinâmica do PlayReady e/ou do Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Este arquivo PHP mostra como dimensionar unidades reservadas para codificação.

## <a id="nov_changes_15"></a>Versão de novembro de 2015
 Os serviços de mídia agora oferecem o serviço de entrega de licença do Widevine na nuvem. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Além disso, consulte [este tutorial](media-services-protect-with-playready-widevine.md) e o [repositório GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Os serviços de entrega de licença do Widevine fornecidos pelos serviços de mídia estão em versão prévia. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versão de outubro de 2015
Os serviços de mídia agora estão ativos nos seguintes data centers: Sul do Brasil, Índia ocidental, sul da Índia e Índia central. Agora você pode usar o portal do Azure para [criar contas de serviço de mídia](media-services-portal-create-account.md) e executar várias tarefas descritas na [página da Web documentação dos serviços de mídia](https://azure.microsoft.com/documentation/services/media-services/). A codificação ativa não está habilitada nesses data centers. Além disso, nem todos os tipos de unidades reservadas para codificação estão disponíveis nesses data centers.

* Sul do Brasil:                                          Somente as unidades reservadas para codificação standard e básico estão disponíveis.
* Índia ocidental, sul da Índia e Índia central:             Somente unidades reservadas para codificação básica estão disponíveis.

## <a id="september_changes_15"></a>Versão de setembro de 2015
Os serviços de mídia agora oferecem a capacidade de proteger vídeos sob demanda e transmissões ao vivo com a tecnologia DRM modular Widevine. Você pode usar os seguintes parceiros de serviços de entrega para ajudá-lo a fornecer licenças Widevine:
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Pode utilizar o [SDK .NET dos Serviços de Multimédia](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar o AssetDeliveryConfiguration para utilizar o Widevine. 
* Os serviços de mídia adicionaram suporte para vídeos do Apple ProRes. Agora você pode carregar seus arquivos de vídeos de origem do QuickTime que usam o Apple ProRes ou outros codecs. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Agora você pode usar Media Encoder Standard para fazer o subcorte e a extração de arquivo ao vivo. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Foram feitas as seguintes atualizações de filtragem: 
  
  * Agora você pode usar o formato Apple HLS com um filtro somente de áudio. Você pode usar essa atualização para remover uma faixa somente de áudio especificando (áudio-somente = falso) na URL.
  * Ao definir filtros para seus ativos, agora você pode combinar vários filtros (até três) em uma única URL.
    
    Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Os serviços de mídia agora dão suporte a I-frames no HLS versão 4. O suporte a I-frame otimiza operações de avanço e retrocesso. Por padrão, todas as saídas do HLS versão 4 incluem a lista de reprodução de I-frame (EXT-X-I-FRAME-STREAM-INF).
Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Versão de agosto de 2015
* O SDK dos serviços de mídia para a versão 0.8.0 do Java e novos exemplos agora estão disponíveis. Para obter mais informações, consulte:
    
* O Player de Mídia do Azure foi atualizado com suporte a fluxo de vários áudios. Para obter mais informações, consulte [esta postagem no blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Versão de julho de 2015
* A disponibilidade geral do Media Encoder Standard foi anunciada. Para obter mais informações, consulte [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard usa predefinições, conforme descrito nesta [seção](https://go.microsoft.com/fwlink/?LinkId=618336). Quando você usa uma predefinição para codificações de 4K, obtenha o tipo de unidade reservada Premium. Para obter mais informações, consulte [dimensionar codificação](media-services-scale-media-processing-overview.md).
* As legendas em tempo real são usadas com os serviços de mídia e o Media Player. Para obter mais informações, consulte [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos serviços de mídia
O SDK do .NET dos serviços de mídia agora está na versão 3.4.0.0. As seguintes atualizações foram feitas: 

* O suporte foi implementado para arquivamento dinâmico. Não é possível baixar um ativo que contenha um arquivo ativo.
* O suporte foi implementado para filtros dinâmicos.
* A funcionalidade foi implementada para que os usuários possam manter um contêiner de armazenamento durante a exclusão de um ativo.
* Correções de bugs foram feitas relacionadas a políticas de repetição em canais.
* Media Encoder Premium Workflow foi habilitado.

## <a id="june_changes_15"></a>Versão de junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos serviços de mídia
O SDK do .NET dos serviços de mídia agora está na versão 3.3.0.0. As seguintes atualizações foram feitas: 

* Foi adicionado suporte para a especificação de descoberta do OpenId Connect.
* Foi adicionado suporte para lidar com a substituição de chaves no lado do provedor de identidade.

Se você usar um provedor de identidade que expõe um documento de descoberta do OpenID Connect (como o Azure AD, o Google e o Salesforce), poderá instruir os serviços de mídia para obter chaves de assinatura para validação de tokens da Web JSON (JWTs) da especificação de descoberta do OpenID Connect. 

Para obter mais informações, consulte [usar chaves da Web JSON da especificação de descoberta do OpenID Connect para trabalhar com a autenticação JWT nos serviços de mídia](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Versão de maio de 2015
Os novos recursos a seguir foram anunciados:

* [Uma visualização da codificação ativa com os serviços de mídia](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)

## <a id="april_changes_15"></a>Versão de abril de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos serviços de mídia
* O [player de mídia](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) foi anunciado.
* A partir do REST 2,10 dos serviços de mídia, os canais configurados para ingerir um protocolo RTMP são criados com URLs de ingestão primária e secundária. Para obter mais informações, consulte [configurações de ingestão de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer foi atualizado.
* Foi adicionado suporte para idioma espanhol.
* Foi adicionada uma nova configuração para o formato XML.

Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos serviços de mídia
O SDK do .NET dos serviços de mídia agora está na versão 3.2.0.0. As seguintes atualizações foram feitas:

* Alteração interruptiva: TokenRestrictionTemplate. emissor e TokenRestrictionTemplate. Audience foram alterados para serem de um tipo de cadeia de caracteres.
* Foram feitas atualizações relacionadas à criação de políticas de repetição personalizadas.
* Correções de bugs foram feitas relacionadas ao carregamento e download de arquivos.
* A classe MediaServicesCredentials agora aceita pontos de extremidade de controle de acesso primários e secundários nos quais se autenticar.

## <a id="march_changes_15"></a>Versão de março de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos serviços de mídia
* Os serviços de mídia agora fornecem integração de rede de distribuição de conteúdo. Para dar suporte à integração, a propriedade CdnEnabled foi adicionada a StreamingEndpoint. CdnEnabled pode ser usado com APIs REST a partir da versão 2,9. Para obter mais informações, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled pode ser usado com o SDK do .NET a partir da versão 3.1.0.2. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* O Media Encoder Premium Workflow foi anunciado. Para obter mais informações, consulte [introdução à codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Versão de fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos serviços de mídia
A API REST dos serviços de mídia agora é a versão 2,9. A partir desta versão, você pode habilitar a integração da rede de distribuição de conteúdo com pontos de extremidade de streaming. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Versão de janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos serviços de mídia
A disponibilidade geral da proteção de conteúdo com criptografia dinâmica foi anunciada. Para obter mais informações, consulte os [serviços de mídia aprimoram a segurança de streaming com a disponibilidade geral da tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos serviços de mídia
O SDK do .NET dos serviços de mídia agora está na versão 3.1.0.1.

Esta versão marcou o construtor padrão Microsoft. WindowsAzure. Mediaservices. Client. ContentKeyAuthorization. TokenRestrictionTemplate como obsoleto. O novo construtor usa TokenType como um argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versão de dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações gerais dos serviços de mídia
* Algumas atualizações e novos recursos foram adicionados ao indexador de mídia. Para obter mais informações, consulte as [notas de versão do Azure Media indexer versão 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Foi adicionada uma nova API REST que você pode usar para atualizar as unidades reservadas para codificação. Para obter mais informações, consulte [EncodingReservedUnitType com REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Foi adicionado suporte a CORS para o serviço de distribuição de chaves.
* Foram feitas melhorias de desempenho para consultar as opções de política de autorização.
* No data center da China, a [URL de entrega de chave](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) agora é por cliente (assim como em outros data centers).
* A duração de destino automática HLS foi adicionada. Ao fazer a transmissão ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os serviços de mídia calculam automaticamente a taxa de empacotamento de segmento HLS (FragmentsPerSegment) com base no intervalo de quadro-chave (KeyFrameInterval). Esse método também é conhecido como um grupo de imagens (GOP) que é recebido do codificador ao vivo. Para obter mais informações, consulte [trabalhar com o Live streaming dos serviços de mídia](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos serviços de mídia
O [SDK do .net dos serviços de mídia](https://www.nuget.org/packages/windowsazure.mediaservices/) agora está na versão 3.1.0.0. As seguintes atualizações foram feitas:

* A dependência do SDK do .NET foi atualizada para a estrutura do .NET 4,5.
* Uma nova API que você pode usar para atualizar unidades reservadas para codificação foi adicionada. Para obter mais informações, consulte [atualizar o tipo de unidade reservada e aumentar a codificação de unidades reservadas usando o .net](media-services-dotnet-encoding-units.md).
* Foi adicionado suporte a JWT para autenticação de token. Para obter mais informações, consulte [autenticação de token JWT nos serviços de mídia e criptografia dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Foram adicionados deslocamentos relativos para BeginDate e ExpirationDate no modelo de licença do PlayReady.

## <a id="november_changes_14"></a>Versão de novembro de 2014
* Agora você pode usar os serviços de mídia para ingerir conteúdo de Smooth Streaming (fMP4) ao vivo em uma conexão SSL. Para ingerir sobre SSL, certifique-se de atualizar a URL de ingestão para HTTPS. Atualmente, os serviços de mídia não dão suporte a SSL com domínios personalizados. Para obter mais informações sobre a transmissão ao vivo, consulte [trabalhar com o Live streaming dos serviços de mídia do Azure](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* No momento, não é possível ingerir uma transmissão ao vivo RTMP em uma conexão SSL.
* Você poderá transmitir por SSL somente se o ponto de extremidade de streaming do qual você entregar seu conteúdo tiver sido criado após 10 de setembro de 2014. Se suas URLs de streaming forem baseadas nos pontos de extremidade de streaming criados após 10 de setembro de 2014, a URL conterá "streaming.mediaservices.windows.net" (o novo formato). As URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não oferecem suporte a SSL. Se a URL estiver no formato antigo e você quiser transmitir por SSL, [crie um novo ponto de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md). Para transmitir seu conteúdo por SSL, use URLs com base no novo ponto de extremidade de streaming.

### <a id="oct_sdk"></a>SDK do .NET dos serviços de mídia
O SDK dos serviços de mídia para extensões .NET agora está na versão 2.0.0.3.

O SDK dos serviços de mídia para .NET agora está na versão 3.0.0.8. As seguintes atualizações foram feitas:

* A refatoração foi implementada em classes de política de repetição.
* Uma cadeia de caracteres do agente do usuário foi adicionada aos cabeçalhos de solicitação HTTP.
* Foi adicionada uma etapa de compilação de restauração do NuGet.
* Os testes de cenário foram corrigidos para usar o certificado X509 do repositório.
* Foram adicionadas configurações de validação para quando o canal e o End de streaming são atualizados.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório GitHub para hospedar exemplos de serviços de mídia
Os exemplos estão no [repositório GitHub de exemplos dos serviços de mídia](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Versão de setembro de 2014
Os metadados REST dos serviços de mídia agora estão na versão 2,7. Para obter mais informações sobre as atualizações REST mais recentes, consulte a [referência da API REST dos serviços de mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

O SDK dos serviços de mídia para .NET agora está na versão 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Alterações recentes
* A origem foi renomeada para [StreamingEndpoint].
* Foi feita uma alteração no comportamento padrão quando você usa o portal do Azure para codificar e depois publicar arquivos MP4.

### <a id="sept_14_GA_changes"></a>Novos recursos/cenários que fazem parte da versão de disponibilidade geral
* O processador de mídia do indexador de mídia foi introduzido. Para obter mais informações, consulte [indexar arquivos de mídia com o indexador de mídia](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Você pode usar a entidade [StreamingEndpoint] para adicionar nomes de domínio (host) personalizados.
  
    Para usar um nome de domínio personalizado como o nome do ponto de extremidade de streaming dos serviços de mídia, adicione nomes de host personalizados ao ponto de extremidade de streaming. Use as APIs REST dos serviços de mídia ou o SDK do .NET para adicionar nomes de host personalizados.
  
    As seguintes considerações se aplicam:
  
  * Você deve ter a propriedade do nome de domínio personalizado.
  * A propriedade do nome de domínio deve ser validada pelos serviços de mídia. Para validar o domínio, crie um CName que mapeie o domínio pai MediaServicesAccountId para verificar o DNS mediaservices-DNS-Zone.
  * Você deve criar outro CName que mapeie o nome de host personalizado (por exemplo, sports.contoso.com) para o nome de host do StreamingEndpoint dos serviços de mídia (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para obter mais informações, consulte a propriedade CustomHostNames no artigo [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) .

### <a id="sept_14_preview_changes"></a>Novos recursos/cenários que fazem parte da versão de visualização pública
* Visualização de transmissão ao vivo. Para obter mais informações, consulte [trabalhar com o Live streaming dos serviços de mídia](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Serviço de distribuição de chaves. Para obter mais informações, consulte [usar criptografia dinâmica AES-128 e o serviço de distribuição de chaves](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Criptografia dinâmica AES. Para obter mais informações, consulte [usar criptografia dinâmica AES-128 e o serviço de distribuição de chaves](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Serviço de entrega de licença do PlayReady. 
* Criptografia dinâmica do PlayReady. 
* Modelo de licença do PlayReady dos serviços de mídia. Para obter mais informações, consulte a [Descrição geral do modelo de licença do PlayReady dos Serviços de Multimédia].
* Transmita ativos criptografados pelo armazenamento. Para obter mais informações, consulte [transmitir conteúdo criptografado pelo armazenamento](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Versão de agosto de 2014
Quando você codifica um ativo, um ativo de saída é produzido quando o trabalho de codificação é concluído. Até esta versão, o codificador dos serviços de mídia produziu metadados sobre os ativos de saída. A partir desta versão, o codificador também produz metadados sobre ativos de entrada. Para obter mais informações, consulte [metadados de entrada] e metadados de [Metadados de saída].

## <a id="july_changes_14"></a>Versão de julho de 2014
As seguintes correções de bugs foram feitas para o empacotador e o criptografador dos serviços de mídia do Azure:

* Quando um ativo de arquivo ativo é transmitido para HLS, apenas o áudio é reproduzido: Esse problema foi corrigido e agora o áudio e o vídeo podem ser reproduzidos.
* Quando um ativo é empacotado para HLS e criptografia de envelope AES de 128 bits, os fluxos empacotados não são reproduzidos em dispositivos Android: Esse bug foi corrigido e o fluxo empacotado é reproduzido em dispositivos Android que dão suporte a HLS.

## <a id="may_changes_14"></a>Versão de maio de 2014
### <a id="may_14_changes"></a>Atualizações gerais dos serviços de mídia
Agora você pode usar o [empacotamento dinâmico] para transmitir HLS versão 3. Para transmitir HLS versão 3, adicione o seguinte formato ao caminho do localizador de origem: *. ISM/manifest (Format = M3U8-AAPL-v3). Para obter mais informações, consulte [este fórum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

O empacotamento dinâmico agora também dá suporte ao fornecimento de HLS (versão 3 e versão 4) criptografados com PlayReady com base em Smooth Streaming criptografados estaticamente com PlayReady. Para obter informações sobre como criptografar Smooth Streaming com o PlayReady, consulte [proteger Smooth streaming com PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Atualizações do SDK do .NET dos serviços de mídia
O SDK do .NET dos serviços de mídia agora está na versão 3.0.0.5. As seguintes atualizações foram feitas:

* Velocidade e resiliência são melhores quando você carrega e baixa ativos de mídia.
* Melhorias foram feitas na lógica de repetição e no tratamento de exceções transitórias: 
  
  * A detecção de erro transitória e a lógica de repetição foram aprimoradas para exceções que são causadas ao consultar, salvar alterações e carregar ou baixar arquivos. 
  * Quando você obtém exceções da Web (por exemplo, durante uma solicitação de token de serviço de controle de acesso), os erros fatais falham mais rapidamente agora.

Para obter mais informações, consulte [lógica de repetição no SDK dos serviços de mídia para .net].

## <a id="jan_feb_changes_14"></a>Versões de janeiro/fevereiro de 2014
### <a name="jan_fab_14_donnet_changes"></a>SDK do .NET dos serviços de mídia 3.0.0.1, 3.0.0.2 e 3.0.0.3
As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Foram corrigidos problemas relacionados ao uso de consultas LINQ com instruções OrderBy.
* As soluções de teste no [GitHub] foram divididas em testes baseados em unidade e testes baseados em cenários.

Para obter mais informações sobre as alterações, consulte as [versões 3.0.0.1 e 3.0.0.2 do SDK do .net dos serviços de mídia](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

As seguintes alterações foram feitas na versão 3.0.0.3:

* As dependências do armazenamento do Azure foram atualizadas para usar a versão 3.0.3.0.
* Um problema de compatibilidade com versões anteriores foi corrigido para 3,0. *.* pela.

## <a id="december_changes_13"></a>Versão de dezembro de 2013
### <a name="dec_13_donnet_changes"></a>SDK do .NET dos serviços de mídia 3.0.0.0
> [!NOTE]
> As versões 3.0. x. x não são compatíveis com versões 2.4. x. x.
> 
> 

A versão mais recente do SDK dos serviços de mídia agora é 3.0.0.0. Você pode baixar o pacote mais recente do NuGet ou obter os bits do [GitHub].

A partir da versão 3.0.0.0 do SDK dos serviços de mídia, você pode reutilizar os tokens do [serviço de controle de acesso do Azure ad](https://msdn.microsoft.com/library/hh147631.aspx) . Para obter mais informações, consulte a seção "reutilizar tokens de serviço de controle de acesso" em [conectar-se aos serviços de mídia com o SDK dos serviços de mídia para .net](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK do .NET dos serviços de mídia 2.0.0.0
 As extensões do SDK do .NET dos serviços de mídia são um conjunto de métodos de extensão e funções auxiliares que simplificam seu código e facilitam o desenvolvimento com os serviços de mídia. Você pode obter os bits mais recentes das [extensões do SDK do .net dos serviços de mídia](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Versão de novembro de 2013
### <a name="nov_13_donnet_changes"></a>Alterações do SDK do .NET dos serviços de mídia
A partir desta versão, o SDK dos serviços de mídia para .NET trata erros transitórios que podem ocorrer quando são feitas chamadas para a camada da API REST dos serviços de mídia.

## <a id="august_changes_13"></a>Versão de agosto de 2013
### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell dos serviços de mídia incluídos nas ferramentas do SDK do Azure
Os seguintes cmdlets do PowerShell dos serviços de mídia agora estão incluídos nas [Ferramentas do SDK do Azure](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Por exemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Versão de junho de 2013
### <a name="june_13_general_changes"></a>Alterações dos serviços de mídia
As seguintes alterações mencionadas nesta seção são atualizações incluídas nas versões de junho de 2013 de serviços de mídia:

* Capacidade de vincular várias contas de armazenamento a uma conta de serviço de mídia. 
    * StorageAccount
    * Asset. StorageAccountName e Asset. StorageAccount
* Capacidade de atualizar o trabalho. prioridade. 
* Entidades e propriedades relacionadas à notificação: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Tarefa
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do SDK do .NET dos serviços de mídia
As seguintes alterações estão incluídas nas versões do SDK dos serviços de mídia de junho de 2013. O SDK dos serviços de mídia mais recente está disponível no GitHub.

* A partir da versão 2.3.0.0, o SDK dos serviços de mídia dá suporte à vinculação de várias contas de armazenamento a uma conta dos serviços de mídia. As seguintes APIs oferecem suporte a esse recurso:
  
    * Tipo de IStorageAccount
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * Propriedade StorageAccount
    * Propriedade StorageAccountName
  
      Para obter mais informações, consulte [gerenciar ativos de serviços de mídia em várias contas de armazenamento](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* APIs relacionadas à notificação. A partir da versão 2.2.0.0, você pode escutar notificações de armazenamento de filas do Azure. Para obter mais informações, consulte [tratar notificações de trabalho dos serviços de mídia](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property
    * Tipo Microsoft. WindowsAzure. Mediaservices. Client. INotificationEndPoint
    * Tipo Microsoft. WindowsAzure. Mediaservices. Client. IJobNotificationSubscription
    * Tipo Microsoft. WindowsAzure. Mediaservices. Client. NotificationEndPointCollection
    * Tipo Microsoft. WindowsAzure. Mediaservices. Client. NotificationEndPointType
* Dependência do SDK do cliente de armazenamento 2,0 (Microsoft. WindowsAzure. StorageClient. dll)
* Dependência do OData 5,5 (Microsoft. Data. OData. dll)

## <a id="december_changes_12"></a>Versão de dezembro de 2012
### <a name="dec_12_dotnet_changes"></a>Alterações do SDK do .NET dos serviços de mídia
* IntelliSense A documentação do IntelliSense ausente foi adicionada para muitos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: Foi corrigido um problema em que o SDK ainda tinha uma dependência para uma versão antiga desse assembly. O SDK agora faz referência à versão 5.1.1209.1 deste assembly.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset. Locators. contagem: Essa contagem agora é relatada corretamente em novas interfaces IAsset depois que todos os localizadores são excluídos.
* IAssetFile.ContentFileSize: Esse valor agora é definido corretamente após um upload pelo IAssetFile. upload (FilePath).
* IAssetFile.ContentFileSize: Essa propriedade agora pode ser definida quando você cria um arquivo de ativo. Ele era somente leitura anteriormente.
* IAssetFile.Upload(filepath): Foi corrigido um problema em que esse método de upload síncrono estava lançando o erro a seguir quando vários arquivos foram carregados para o ativo. O erro era "o servidor falhou ao autenticar a solicitação. Verifique se o valor do cabeçalho de autorização está formado corretamente, incluindo a assinatura. "
* IAssetFile.UploadAsync: Foi corrigido um problema que limitava o carregamento simultâneo de arquivos a cinco arquivos.
* IAssetFile.UploadProgressChanged: Esse evento agora é fornecido pelo SDK.
* IAssetFile. DownloadAsync (cadeia de caracteres, BlobTransferClient, ILocator, CancellationToken): Agora, essa sobrecarga de método é fornecida.
* IAssetFile.DownloadAsync: Foi corrigido um problema que limitava o download simultâneo de arquivos a cinco arquivos.
* IAssetFile.Delete(): Foi corrigido um problema em que chamar delete pode lançar uma exceção se nenhum arquivo foi carregado para o IAssetFile.
* Sejam Foi corrigido um problema em que o encadeamento de uma "tarefa de MP4 para Smooth streams" com uma "tarefa de proteção do PlayReady" usando um modelo de trabalho não criou nenhuma tarefa.
* EncryptionUtils.GetCertificateFromStore(): Esse método não gera mais uma exceção de referência nula devido a uma falha na localização do certificado com base em problemas de configuração de certificado.

## <a id="november_changes_12"></a>Versão de novembro de 2012
As alterações mencionadas nesta seção foram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Essas alterações podem exigir que qualquer código escrito para a versão do SDK de visualização de junho de 2012 seja modificado ou reescrito.

* Elementos
  
    * IAsset. Create (assetname) é a *única* função de criação de ativo. IAsset. Create não carrega mais arquivos como parte da chamada do método. Use IAssetFile para carregar.
    * O método IAsset. publish e o valor de enumeração Assetstate. publish foram removidos do SDK de serviços. Qualquer código que dependa desse valor deve ser reescrito.
* FileInfo
  
    * Essa classe foi removida e substituída por IAssetFile.
  
* IAssetFiles
  
    * IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-lo, crie uma instância do objeto IAssetFiles, seguido por um upload de arquivo usando o SDK dos serviços de mídia ou o SDK de armazenamento. As sobrecargas de IAssetFile. upload a seguir podem ser usadas:
  
        * IAssetFile.Upload(filePath): Esse método síncrono bloqueia o thread e é recomendável apenas quando você carrega um único arquivo.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, Locator, cancellationToken): Esse método assíncrono é o mecanismo de upload preferencial. 
    
            Bug conhecido: Se você usar o token de cancelamento, o carregamento será cancelado. As tarefas podem ter muitos Estados de cancelamento. Você deve capturar e manipular exceções corretamente.
* Localizadores
  
    * As versões específicas de origem foram removidas. O contexto específico de SAS. Locators. CreateSasLocator (ativo, accessPolicy) será marcado como preterido ou removido por disponibilidade geral. Consulte a seção "localizadores" em "nova funcionalidade" para obter o comportamento atualizado.

## <a id="june_changes_12"></a>Versão de visualização de junho de 2012
A funcionalidade a seguir foi nova na versão de novembro do SDK:

* Excluindo entidades
  
    * Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy e IContentKey agora são excluídos no nível do objeto, ou seja, IObject. Delete (), em vez de exigir uma exclusão na coleção, ou seja, cloudMediaContext. ObjCollection. Delete (objInstance).
* Localizadores
  
    * Os localizadores agora devem ser criados usando o método CreateLocator. Eles devem usar os valores de enumeração Locatortype. SAS ou Locatortype. OnDemandOrigin como um argumento para o tipo específico de localizador que você deseja criar.
    * Novas propriedades foram adicionadas aos localizadores para facilitar a obtenção de URIs utilizáveis para seu conteúdo. Essa reestruturação de localizadores fornece mais flexibilidade para extensibilidade de terceiros futura e aumenta a facilidade de uso para aplicativos cliente de mídia.
* Suporte a método assíncrono
  
    * O suporte assíncrono foi adicionado a todos os métodos.

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum do MSDN dos serviços de mídia do Azure]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência de API REST de Serviços de Mídia do Azure]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadados de entrada]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Descrição geral do modelo de licença do PlayReady dos Serviços de Multimédia]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Empacotamento dinâmico]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de repetição no SDK dos serviços de mídia para .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

