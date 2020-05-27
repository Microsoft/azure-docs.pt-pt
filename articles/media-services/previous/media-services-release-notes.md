---
title: Notas de lançamento da Azure Media Services Microsoft Docs
description: Este artigo fala sobre as notas de lançamento v2 do Microsoft Azure Media Services.
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
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: edea04e15fe5b844654f250a22a05a753f0df123
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836401"
---
# <a name="azure-media-services-release-notes"></a>Notas de lançamento da Azure Media Services

Estas notas de lançamento para a Azure Media Services resumem as alterações de lançamentos anteriores e questões conhecidas.

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Queremos ouvir os nossos clientes para que possamos focar-nos na resolução de problemas que o afetam. Para relatar um problema ou fazer perguntas, submeta um post no [Fórum MSDN da Azure Media Services]. 

## <a name="known-issues"></a><a id="issues"/>Questões conhecidas
### <a name="media-services-general-issues"></a><a id="general_issues"/>Questões gerais dos Serviços de Media

| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos http comuns não são fornecidos na API REST. |Se desenvolver aplicações de Serviços de Media utilizando a API REST, verifica que alguns campos comuns de cabeçalho http (incluindo CLIENTE-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID) não são suportados. Os cabeçalhos serão adicionados numa futura atualização. |
| Por centérico não é permitido. |A Media Services utiliza o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Por esta razão, não é permitida a codificação por cento. O valor da propriedade Name não pode ter nenhum dos seguintes [caracteres reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)por cento : !*'(:@&=+$,/?%#[]". Além disso, só pode haver um "." para a extensão do nome do ficheiro. |
| O método ListBlobs que faz parte da versão 3.x do Azure Storage SDK falha. |A Media Services gera URLs SAS com base na versão [2012-02-12.](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) Se pretender utilizar o SDK de armazenamento para listar bolhas num recipiente de bolha, utilize o método [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) que faz parte da versão 2.x do Storage SDK. |
| O mecanismo de estrangulamento dos Serviços de Media restringe o uso de recursos para aplicações que fazem pedidos excessivos ao serviço. O serviço pode devolver o código de estado "Service Inavailable" 503 HTTP. |Para mais informações, consulte a descrição do código de estado 503 HTTP nos códigos de erro dos [Serviços de Media](media-services-encoding-error-codes.md). |
| Quando se consultam entidades, um limite de 1.000 entidades é devolvido de uma só vez porque a versão 2 do REST público limita os resultados da consulta a 1.000 resultados. |Use Skip and Take (.NET)/top (REST) conforme descrito [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem encontrar um problema de etiqueta seletiva no manifesto smooth streaming. |Para mais informações, consulte [esta secção](media-services-deliver-content-overview.md#known-issues). |
| Os ficheiros Media Services .NET SDK não podem ser serializados e, como resultado, não funcionam com o Azure Cache para redis. |Se tentar serializar o objeto SDK AssetCollection para adicioná-lo ao Azure Cache para Redis, é lançada uma exceção. |
|A API REST responde com uma mensagem de erro dizendo "O filtro não pode ser acedido por esta versão do REST Api" ao tentar obter um filtro de nível de Ativo ou Conta.|O filtro foi criado ou modificado com uma versão API mais recente do que está a ser usado para tentar obter o filtro. Isto pode acontecer se duas versões API estiverem a ser utilizadas por código ou ferramentas que estão a ser utilizadas pelo cliente.  A melhor solução aqui é atualizar o código ou ferramentas para usar as versões Mais recentes ou as duas API.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>História da versão REST API
Para obter informações sobre o histórico da versão DaPI do Media Services REST, consulte a referência da API do [Azure Media Services REST .]

## <a name="february-2020"></a>Fevereiro de 2020

Alguns processadores de meios de comunicação de análise serão retirados. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

## <a name="september-2019"></a>Setembro de 2019

### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos a nunciando a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui estes processadores de mídia legados.

Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md)

Consulte também [a Migrate do Azure Media Indexer e do Azure Media Indexer 2 para o Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md)

Para mais detalhes, consulte [migrate WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [Migrar AME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Março de 2019

A funcionalidade de pré-visualização de Media Hyperlapse da Azure Media Services foi depreciada.

## <a name="december-2018"></a>Dezembro de 2018

A funcionalidade de pré-visualização de Media Hyperlapse da Azure Media Services em breve será reformada. A partir de 19 de dezembro de 2018, os Serviços de Media deixarão de fazer alterações ou melhorias no Hyperlapse dos Media. No dia 29 de março de 2019, será reformado e já não está disponível.

## <a name="october-2018"></a>Outubro de 2018

### <a name="cmaf-support"></a>Suporte cmaf

CmAF e suporte de encriptação 'cbcs' para os jogadores apple HLS (iOS 11+) e MPEG-DASH que suportam a CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprites de miniatura Web VTT

Agora pode utilizar os Serviços de Media para gerar sprites de miniatura Web VTT usando as nossas APIs v2. Para mais informações, consulte [Gerar um sprite de miniatura](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Julho de 2018

Com a mais recente versão do serviço, existem pequenas alterações de formatação nas mensagens de erro devolvidas pelo serviço quando um Trabalho falha, no que diz respeito à forma como é dividido em duas ou mais linhas.

## <a name="may-2018"></a>Maio de 2018 

A partir de 12 de maio de 2018, os canais ao vivo deixarão de apoiar o protocolo de transporte sintetizador da RTP/MPEG-2. Por favor, emigrar de PROTOCOLOS de ingerir RTP/MPEG-2 para RTMP ou MP4 fragmentados (Smooth Streaming).

## <a name="october-2017-release"></a>Versão de outubro de 2017
> [!IMPORTANT] 
> A Media Services está a depreciar o suporte às chaves de autenticação do Serviço de Controlo de Acesso Azure. No dia 22 de junho de 2018, já não é possível autenticar com os Serviços de Media a final através do código utilizando as chaves do Serviço de Controlo de Acesso. Tem de atualizar o seu código para utilizar o Azure Ative Directory (Azure AD) por [autenticação baseada em AD Azure](media-services-use-aad-auth-to-access-ams-api.md). Tenha em alerta sonos sobre esta mudança no portal Azure.

### <a name="updates-for-october-2017"></a>Atualizações para outubro de 2017
#### <a name="sdks"></a>SDKs
* O .NET SDK foi atualizado para apoiar a autenticação da AD Azure. O suporte para a autenticação do Serviço de Controlo de Acesso foi removido do mais recente .NET SDK na Nuget.org para incentivar uma migração mais rápida para a AD Azure. 
* O JAVA SDK foi atualizado para apoiar a autenticação da AD Azure. O suporte para a autenticação Azure AD foi adicionado ao Java SDK. Para obter informações sobre como usar o Java SDK com serviços de mídia, consulte [Iniciar com o cliente Java SDK para Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificação baseada em ficheiros
* Agora pode utilizar o Codificador Premium para codificar o seu conteúdo para o código de vídeo de alta eficiência H.265 (HEVC). Não há impacto de preços se escolher H.265 em vez de outros códigos, como h.264. Para obter informações sobre as licenças de patente do HEVC, consulte [Os Termos dos Serviços Online](https://azure.microsoft.com/support/legal/).
* Para um vídeo de origem codificado com o código de vídeo H.265 (HEVC), como o vídeo captado através do iOS11 ou goPro Hero 6, pode agora utilizar o Codificador Premium ou o Codificador Padrão para codificar esses vídeos. Para obter informações sobre licenças de patente, consulte [Os Termos dos Serviços Online](https://azure.microsoft.com/support/legal/).
* Para conteúdos que contenham várias faixas áudio linguísticas, os valores linguísticos devem ser corretamente rotulados de acordo com a especificação correspondente do formato de ficheiro (por exemplo, ISO MP4). Em seguida, pode utilizar o Codificador Standard para codificar o conteúdo para streaming. O localizador de streaming resultante lista as línguas áudio disponíveis.
* O Standard Encoder suporta agora duas novas predefinições de sistema de áudio, "AAC Audio" e "AAC Good Quality Audio". Ambos produzem uma saída de codificação de áudio avançada (AAC), a taxas bit de 128 Kbps e 192 Kbps, respectivamente.
* O Premium Encoder suporta agora os formatos de ficheiroQuickTime/MOV como entrada. O código de vídeo deve ser um dos tipos apple [ProRes listados neste artigo do GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). O áudio deve ser a modulação do Código De pulso ou o código de pulso (PCM). O Encoder Premium não suporta, por exemplo, o vídeo DVC/DVCPro embrulhado em ficheiros QuickTime/MOV como entrada. O Standard Encoder suporta estes códigos de vídeo.
* As seguintes correções de bugs foram feitas em codificadores:

    * Agora pode submeter empregos utilizando um ativo de entrada. Após o acabamento destes trabalhos, pode modificar o ativo (por exemplo, adicionar, eliminar ou renomear ficheiros dentro do ativo) e apresentar trabalhos adicionais.
    * A qualidade das miniaturas JPEG produzidas pelo Standard Encoder é melhorada.
    * O Standard Encoder lida melhor com metadados de entrada e geração de miniaturas em vídeos de muito curta duração.
    * As melhorias no descodificador H.264 utilizado no Codificador Padrão eliminam certos artefactos raros. 

#### <a name="media-analytics"></a>Análise de Multimédia
Disponibilidade geral do Redator Azure Media: Este processador de mídia realiza anoinização desfocando os rostos de indivíduos selecionados e é ideal para ser usado em cenários de segurança pública e meios de comunicação. 

Para uma visão geral deste novo processador, consulte [esta publicação de blog](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter informações sobre documentação e configurações, consulte [a Redact faces com o Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Lançamento de junho de 2017

A Media Services apoia agora a [autenticação baseada em Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)

> [!IMPORTANT]
> Atualmente, a Media Services suporta o modelo de autenticação do Serviço de Controlo de Acesso. A autorização do Serviço de Controlo de Acesso será deprecada no dia 1 de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="march-2017-release"></a>Lançamento de março de 2017

Agora pode utilizar o Standard Encoder para [gerar automaticamente uma escada bitrate,](media-services-autogen-bitrate-ladder-with-mes.md) especificando a cadeia predefinida "Adaptive Streaming" quando criar uma tarefa de codificação. Para codificar um vídeo para streaming com os Media Services, utilize o predefinido "Adaptive Streaming". Para personalizar um predefinição de codificação para o seu cenário específico, pode começar com [estas predefinições](media-services-mes-presets-overview.md).

Pode agora utilizar o Media Encoder Standard ou Media Encoder Premium Workflow para criar uma tarefa de [codificação que gere pedaços fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Lançamento de fevereiro de 2017

A partir de 1 de abril de 2017, qualquer registo de emprego na sua conta com mais de 90 dias é automaticamente apagado, juntamente com os seus registos de tarefas associados. A eliminação ocorre mesmo que o número total de registos esteja abaixo da quota máxima. Para arquivar a informação de trabalho/tarefa, pode utilizar o código descrito em [Gerir ativos e entidades relacionadas com os Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Lançamento de janeiro de 2017

Nos Serviços de Media, um ponto final de streaming representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de jogador de cliente ou a uma rede de entrega de conteúdos (CDN) para posterior distribuição. A Media Services também fornece uma integração perfeita da Rede de Entrega de Conteúdos Azure. O fluxo de saída de um serviço StreamingEndpoint pode ser um live stream, um vídeo a pedido ou um download progressivo do seu ativo na sua conta de Media Services. Cada conta de Media Services inclui um ponto final de streaming predefinido. Os pontos finais de streaming adicionais podem ser criados sob a conta. 

Existem duas versões de pontos finais de streaming, 1.0 e 2.0. A partir de 10 de janeiro de 2017, quaisquer contas recém-criadas dos Media Services incluem o ponto final de streaming padrão da versão 2.0. Os pontos finais de streaming adicionais que adiciona a esta conta também são a versão 2.0. Esta mudança não afeta as contas existentes. Os pontos finais de streaming existentes são da versão 1.0 e podem ser atualizados para a versão 2.0. Há comportamentos, faturação e mudanças de recursos com esta mudança. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

A partir da versão 2.15, a Media Services adicionou as seguintes propriedades à entidade de ponto final de streaming:

* CdnProvider 
* CdnProfile
* Tempo de fim de prazo gratuito 
* Versão streamingEndpoint 

Para obter mais informações sobre estas propriedades, consulte [streamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Lançamento de dezembro de 2016

 Agora pode utilizar os Serviços de Comunicação Social para aceder a dados de telemetria/métricas para os seus serviços. Pode utilizar a versão atual dos Media Services para recolher dados de telemetria para canais ao vivo, ponto final de streaming e entidades de arquivo. Para mais informações, consulte [a telemetria dos Serviços de Media.](media-services-telemetry-overview.md)

## <a name="july-2016-release"></a><a id="july_changes16"/>Lançamento de julho de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações do ficheiro manifesto (*. ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação é submetida ao Media Encoder Standard ou Media Encoder Premium, a tarefa de codificação gera um [ficheiro manifesto de streaming](media-services-deliver-content-overview.md) (*.ism) no ativo de saída. Com o mais recente lançamento do serviço, a sintaxe deste ficheiro manifesto de streaming foi atualizada.

> [!NOTE]
> A sintaxe do ficheiro de streaming manifesto (.ism) está reservada para uso interno. Está sujeito a mudanças em futuros lançamentos. Não modifique nem manipule o conteúdo deste ficheiro.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. O ficheiro ISMC é gerado no ativo de saída quando uma tarefa de codificação produz um ou mais ficheiros MP4
Começando com a mais recente versão do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais ficheiros MP4, o ativo de saída também contém um ficheiro de manifesto de cliente de streaming (*.ismc). O ficheiro .ismc ajuda a melhorar o desempenho do streaming dinâmico. 

> [!NOTE]
> A sintaxe do ficheiro manifesto do cliente (.ismc) está reservada para uso interno. Está sujeito a mudanças em futuros lançamentos. Não modifique nem manipule o conteúdo deste ficheiro.
> 
> 

Para mais informações, consulte [este blog.](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/)

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem encontrar um problema de etiqueta seletiva no manifesto smooth streaming. Para mais informações, consulte [esta secção](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Lançamento de abril de 2016
### <a name="media-analytics"></a>Análise de Multimédia
 A Media Services introduziu o Media Analytics para poderosa inteligência em vídeo. Para mais informações, consulte a [visão geral do Media Services Analytics.](media-services-analytics-overview.md)

### <a name="apple-fairplay-preview"></a>Apple FairPlay (pré-visualização)
Agora pode utilizar os Serviços de Media para encriptar dinamicamente o conteúdo do HTTP Live Streaming (HLS) com o Apple FairPlay. Também pode utilizar o serviço de entrega de licenças dos Media Services para entregar licenças FairPlay aos clientes. Para mais informações, consulte "Use Azure Media Services para transmitir o seu conteúdo HLS protegido com apple FairPlay."

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Lançamento de fevereiro de 2016
A versão mais recente do Media Services SDK para .NET (3.5.3) contém uma correção de bugs relacionada com o Google Widevine. Era impossível reutilizar o AssetDeliveryPolicy para vários ativos encriptados com a Widevine. Como parte desta correção de bugs, a seguinte propriedade foi adicionada ao SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Lançamento de janeiro de 2016
A codificação de unidades reservadas foi renomeada para reduzir a confusão com nomes codificadores.

As unidades reservadas de codificação Básica, Standard e Premium foram renomeadas para unidades reservadas S1, S2 e S3, respectivamente. Os clientes que utilizam unidades reservadas de codificação Básica vêem hoje o S1 como o rótulo no portal Azure (e na conta). Os clientes que utilizam a Standard e premium vêem as etiquetas S2 e S3, respectivamente. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Lançamento de dezembro de 2015

### <a name="media-encoder-deprecation-announcement"></a>Comunicado de depreciação do Media Encoder

 O Media Encoder será depreciado a partir de aproximadamente 12 meses a partir do lançamento do Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK for PHP (Azure SDK para PHP)
A equipa azure SDK publicou um novo lançamento do [Pacote Azure SDK para php](https://github.com/Azure/azure-sdk-for-php) que contém atualizações e novas funcionalidades para os Serviços de Media. Em particular, o Media Services SDK para PHP suporta agora as mais recentes funcionalidades de proteção de [conteúdos.](media-services-content-protection-overview.md) Estas funcionalidades são encriptação dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrições simbólicas. Também suporta unidades de codificação de [escalonamento.](media-services-dotnet-encoding-units.md)

Para obter mais informações, consulte:

* As [seguintes amostras de código](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) ajudam-no a começar rapidamente:
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica AES-128 e o serviço de entrega de chaves. Baseia-se na amostra .NET explicada na [encriptação dinâmica AES-128 e no serviço](media-services-protect-with-aes128.md)de entrega de chaves.
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica PlayReady e o serviço de entrega de licenças. Baseia-se na amostra .NET explicada na [encriptação comum dinâmica Use PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)dinâmica .
  * **scale_encoding_units.php**: Este ficheiro PHP mostra como dimensionar as unidades reservadas de codificação.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Lançamento de novembro de 2015
 A Media Services oferece agora o serviço de entrega de licenças Widevine na nuvem. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) Consulte [também este tutorial](media-services-protect-with-playready-widevine.md) e o [repositório GitHub.](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 

Os serviços de entrega de licenças Widevine fornecidos pela Media Services estão em pré-visualização. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Lançamento de outubro de 2015
A Media Services vive agora nos seguintes centros de dados: Brasil Sul, Índia Oeste, Índia Sul e Índia Central. Agora pode utilizar o portal Azure para [criar contas do Media Service](media-services-portal-create-account.md) e executar várias tarefas descritas na página web da documentação dos [Serviços de Media.](https://azure.microsoft.com/documentation/services/media-services/) A Live Encoding não está ativada nestes centros de dados. Além disso, nem todos os tipos de unidades reservadas de codificação estão disponíveis nestes centros de dados.

* Brasil Sul: Apenas estão disponíveis unidades reservadas de codificação Standard e Basic.
* Índia Oeste, Índia Sul e Índia Central: Apenas unidades reservadas de codificação básica estão disponíveis.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Lançamento de setembro de 2015
A Media Services oferece agora a capacidade de proteger tanto o vídeo a pedido como os streams ao vivo com a tecnologia modular de DRM da Widevine. Pode utilizar os seguintes parceiros de serviços de entrega para o ajudar a entregar licenças Widevine:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)
  
Pode utilizar o [SDK .NET dos Serviços de Multimédia](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar o AssetDeliveryConfiguration para utilizar o Widevine. 
* Os Media Services adicionaram suporte para vídeos apple ProRes. Agora pode fazer o upload dos ficheiros de vídeos de origem QuickTime que utilizam ProRes Apple ou outros códigos. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)
* Agora pode utilizar o Media Encoder Standard para fazer a extração de arquivos e subctilantes ao vivo. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)
* Foram feitas as seguintes atualizações de filtragem: 
  
  * Agora pode utilizar o formato Apple HLS com um filtro só para áudio. Pode utilizar esta atualização para remover uma faixa só de áudio especificando (apenas áudio=falso) no URL.
  * Quando define filtros para os seus ativos, agora pode combinar vários filtros (até três) num único URL.
    
    Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)
* A Media Services suporta agora quadros I na versão 4 do HLS. O suporte i-frame otimiza operações de avanço rápido e de rebobinar. Por predefinição, todas as saídas da versão 4 do HLS incluem a lista de reprodução de quadros I (EXT-X-I-FRAME-STREAM-INF).
Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Lançamento de agosto de 2015
* O Media Services SDK para a versão Java 0.8.0 e novas amostras já estão disponíveis. Para obter mais informações, consulte:
    
* O Azure Media Player foi atualizado com suporte a fluxo sonoro multi-áudio. Para mais informações, consulte [esta publicação de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Lançamento de julho de 2015
* Foi anunciada a disponibilidade geral da Media Encoder Standard. Para mais informações, consulte [esta publicação de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard utiliza predefinições, conforme descrito [nesta secção](https://go.microsoft.com/fwlink/?LinkId=618336). Quando utilizar um predefinido para codificações 4K, obtenha o tipo de unidade reservado Premium. Para mais informações, consulte [a codificação de escala](media-services-scale-media-processing-overview.md).
* Legendas em tempo real foram usadas com os Media Services e o Media Player. Para mais informações, consulte [esta publicação de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK atualiza
O Media Services .NET SDK é agora a versão 3.4.0.0. Foram feitas as seguintes atualizações: 

* O apoio foi implementado para o arquivo ao vivo. Não pode descarregar um ativo que contenha um arquivo ao vivo.
* Foi implementado suporte para filtros dinâmicos.
* A funcionalidade foi implementada para que os utilizadores possam manter um recipiente de armazenamento enquanto apagam um ativo.
* As correções de bugs foram feitas relacionadas com políticas de retry nos canais.
* Media Encoder Premium Workflow foi ativado.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Lançamento de junho de 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK atualiza
O Media Services .NET SDK é agora a versão 3.3.0.0. Foram feitas as seguintes atualizações: 

* O suporte foi adicionado para a especificação de descoberta OpenId Connect.
* Foi adicionado apoio para manusear as chaves do lado do fornecedor de identidade.

Se utilizar um fornecedor de identidade que exponha um documento de descoberta OpenID Connect (como o Azure AD, Google e Salesforce fazem), pode instruir os Serviços de Media a obter em troca de chaves de assinatura para validação de JSON Web Tokens (JWTs) a partir da especificação de descoberta OpenID Connect. 

Para mais informações, consulte [Utilize as teclas web JSON da especificação de descoberta OpenID Connect para trabalhar com a autenticação JWT em Serviços de Media](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Lançamento de maio de 2015
Foram anunciadas as seguintes novidades:

* [Uma pré-visualização da codificação ao vivo com os Serviços de Media](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Lançamento de abril de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços De Media Gerais
* [O Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) foi anunciado.
* A partir do Media Services REST 2.10, os canais configurados para ingerir um Protocolo de Mensagens em Tempo Real (RTMP) são criados com URLs primários e secundários. Para mais informações, consulte [as configurações de ingestão do Canal](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* O Azure Media Indexer foi atualizado.
* Foi acrescentado o apoio à língua espanhola.
* Foi adicionada uma nova configuração para o formato XML.

Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK atualiza
O Media Services .NET SDK é agora a versão 3.2.0.0. Foram feitas as seguintes atualizações:

* Alteração de rutura: TokenRestrictionTemplate.Emitente e TokenRestrictionTemplate.O público foi alterado para ser de um tipo de corda.
* Foram feitas atualizações relacionadas com a criação de políticas de retry personalizadas.
* As correções de bugs foram feitas relacionadas com o upload e o download de ficheiros.
* A classe MediaServicesCredenciais aceita agora pontos finais de controlo de acesso primário e secundário para autenticar contra.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Lançamento de março de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços De Media Gerais
* A Media Services fornece agora integração da Rede de Entrega de Conteúdos. Para apoiar a integração, a propriedade CdnEnabled foi adicionada ao StreamingEndpoint. O CdnEnabled pode ser utilizado com APIs REST a partir da versão 2.9. Para mais informações, consulte [o StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). O CdnEnabled pode ser utilizado com o SDK .NET a começar pela versão 3.1.0.2. Para mais informações, consulte [o StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* O Media Encoder Premium Workflow foi anunciado. Para mais informações, consulte [A Introdução da codificação Premium nos Serviços De Mídia Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Lançamento de fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços De Media Gerais
O Media Services REST API é agora a versão 2.9. A partir desta versão, pode ativar a integração da Rede de Entrega de Conteúdos com pontos finais de streaming. Para mais informações, consulte [o StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Lançamento de janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços De Media Gerais
Foi anunciada a disponibilidade geral de proteção de conteúdos com encriptação dinâmica. Para mais informações, consulte [os Serviços de Media, aumentando a segurança de streaming com a disponibilidade geral da tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK atualiza
O Media Services .NET SDK é agora a versão 3.1.0.1.

Esta versão marcou o padrão Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate constructor como obsoleto. O novo construtor toma tokenType como argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Lançamento de dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações dos Serviços De Media Gerais
* Algumas atualizações e novas funcionalidades foram adicionadas ao Media Indexer. Para mais informações, consulte as notas de [lançamento do Azure Media Indexer 1.1.6.7.](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)
* Foi adicionada uma nova API REST que pode utilizar para atualizar as unidades reservadas de codificação. Para mais informações, consulte [A CodificaçãoReservedUnitType com REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* O suporte cors foi adicionado para o serviço de entrega chave.
* Foram introduzidas melhorias no desempenho na consulta de opções de política de autorização.
* No centro de dados da China, o [URL de entrega chave](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) é agora por cliente (tal como em outros centros de dados).
* Foi adicionada a duração do alvo automático hls. Ao fazer streaming ao vivo, o HLS é sempre embalado dinamicamente. Por predefinição, os Serviços de Media calculam automaticamente a relação de embalagem do segmento HLS (FragmentsPerSegment) com base no intervalo do quadro-chave (KeyFrameInterval). Este método também é referido como um grupo de imagens (GOP) que é recebida do codificador ao vivo. Para mais informações, consulte [Trabalhar com serviços de media em streaming ao vivo.](https://msdn.microsoft.com/library/azure/dn783466.aspx)

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK atualiza
O [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) é agora a versão 3.1.0.0. Foram feitas as seguintes atualizações:

* A dependência do SDK .NET foi atualizada para o quadro .NET 4.5.
* Foi adicionada uma nova API que pode utilizar para atualizar as unidades reservadas de codificação. Para mais informações, consulte o tipo de [unidade reservado de Atualização e aumente a codificação das unidades reservadas utilizando .NET](media-services-dotnet-encoding-units.md).
* Foi adicionado o suporte jWT para autenticação simbólica. Para mais informações, consulte a autenticação simbólica [jWT nos Serviços de Media e encriptação dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Foram adicionadas compensações relativas para O Início Data e ExpiraçãoData no modelo de licença PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Lançamento de novembro de 2014
* Agora pode utilizar os Serviços de Media para ingerir conteúdo de Smooth Streaming (fMP4) ao vivo sobre uma ligação TLS. Para ingerir sobre tLS, certifique-se de atualizar o URL ingerir para HTTPS. Atualmente, a Media Services não suporta TLS com domínios personalizados. Para mais informações sobre streaming ao vivo, consulte [Trabalhar com o Azure Media Services Live Streaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Atualmente, não é possível ingerir um fluxo de vídeo RTMP através de uma ligação TLS.
* Só pode transmitir através do TLS se o ponto final de streaming a partir do qual entrega o seu conteúdo for criado após 10 de setembro de 2014. Se os seus URLs de streaming forem baseados nos pontos finais de streaming criados após 10 de setembro de 2014, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam TLS. Se o seu URL estiver no formato antigo e quiser transmitir através de TLS, [crie um novo ponto final](media-services-portal-manage-streaming-endpoints.md)de streaming . Para transmitir o seu conteúdo através de TLS, utilize URLs com base no novo ponto final de streaming.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>SDK .NET dos Serviços de Multimédia
O Media Services SDK para extensões .NET é agora versão 2.0.0.3.

O Media Services SDK para .NET é agora a versão 3.0.0.8. Foram feitas as seguintes atualizações:

* A refactoring foi implementada em aulas políticas de retry.
* Uma corda de agente de utilizador foi adicionada aos cabeçalhos de pedido HTTP.
* Foi adicionado um passo de construção de restauro NuGet.
* Os testes de cenário foram fixados para usar x509 cert do repositório.
* As definições de validação foram adicionadas para quando o canal e a atualização final de streaming.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório GitHub para acolher amostras dos Media Services
As amostras constam das amostras de serviços de [comunicação gitHub repositório](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Lançamento de setembro de 2014
Os metadados do Media Services REST são agora a versão 2.7. Para mais informações sobre as últimas atualizações do REST, consulte a referência da [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

O Media Services SDK para .NET é agora a versão 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Alterações interruptivas
* A origem foi renomeada para [StreamingEndpoint].
* Foi feita uma alteração no comportamento predefinido quando se utiliza o portal Azure para codificar e depois publicar ficheiros MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Novas funcionalidades/cenários que fazem parte do lançamento da disponibilidade geral
* O processador de media Media Indexer foi introduzido. Para mais informações, consulte os [ficheiros de media do Indexante do Índice de Meios de Comunicação Social](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Pode utilizar a entidade [StreamingEndpoint] para adicionar nomes de domínio personalizado (anfitrião).
  
    Para utilizar um nome de domínio personalizado como o nome final do media services, adicione os nomes personalizados do anfitrião ao seu ponto final de streaming. Utilize as APIs DE REPOUSO dos Serviços de Mídia ou o .NET SDK para adicionar nomes personalizados de anfitriões.
  
    As seguintes considerações são aplicáveis:
  
  * Deve ter a propriedade do nome de domínio personalizado.
  * A propriedade do nome de domínio deve ser validada pela Media Services. Para validar o domínio, crie um CName que mapeie o domínio dos pais do MediaServicesAccountId para verificar a zona dNS mediaservices-dns..
  * Deve criar outro CName que mapeie o nome de anfitrião personalizado (por exemplo, sports.contoso.com) para o nome do anfitrião do Media Services StreamingEndpoint (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para mais informações, consulte a propriedade CustomHostNames no artigo [streamingEndpoint.](https://msdn.microsoft.com/library/azure/dn783468.aspx)

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Novas funcionalidades/cenários que fazem parte do lançamento da pré-visualização pública
* Pré-visualização de streaming ao vivo. Para mais informações, consulte [Trabalhar com serviços de media em streaming ao vivo.](https://msdn.microsoft.com/library/azure/dn783466.aspx)
* Serviço de entrega chave. Para mais informações, consulte [Utilize encriptação dinâmica AES-128 e o serviço de entrega de chaves.](https://msdn.microsoft.com/library/azure/dn783457.aspx)
* Encriptação dinâmica da AES. Para mais informações, consulte [Utilize encriptação dinâmica AES-128 e o serviço de entrega de chaves.](https://msdn.microsoft.com/library/azure/dn783457.aspx)
* Serviço de entrega de licença PlayReady. 
* Encriptação dinâmica PlayReady. 
* Modelo de licença PlayReady de Serviços de Mídia. Para mais informações, consulte a visão geral do modelo de licença PlayReady dos [Serviços de Media.]
* Transmita ativos encriptados de armazenamento. Para mais informações, consulte [o conteúdo encriptado](https://msdn.microsoft.com/library/azure/dn783451.aspx)de armazenamento do Stream .

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Lançamento de agosto de 2014
Quando codifica um ativo, um ativo de saída é produzido quando o trabalho de codificação está terminado. Até esta versão, o Media Services Encoder produziu metadados sobre ativos de produção. A partir desta versão, o codificador também produz metadados sobre ativos de entrada. Para mais informações, consulte [metadados] de entrada e [metadados de saída].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Lançamento de julho de 2014
Foram feitas as seguintes correções de bugs para o Pacote e Encriptador de Serviços de Mídia Azure:

* Quando um ativo de arquivo ao vivo é transmitido para hls, apenas o áudio reproduz: Este problema foi corrigido, e agora tanto o áudio como o vídeo podem ser reproduzidos.
* Quando um ativo é embalado para encriptação de envelopes HLS e AES de 128 bits, os streams embalados não rejogam nos dispositivos Android: Este bug foi corrigido e o stream embalado reproduz-se em dispositivos Android que suportam hls.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Lançamento de maio de 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Atualizações dos Serviços De Media Gerais
Agora pode utilizar [embalagens dinâmicas] para transmitir a versão 3 do HLS. Para transmitir a versão 3 do HLS, adicione o seguinte formato ao caminho do localizador de origem: * .ism/manifesto (formato=m3u8-aapl-v3). Para mais informações, consulte [este fórum.](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)

A embalagem dinâmica agora também suporta a entrega de HLS (versão 3 e versão 4) encriptada com PlayReady com base no Smooth Streaming encriptado estáticamente com o PlayReady. Para obter informações sobre como encriptar o Streaming Liso com o PlayReady, consulte [Protect Smooth Streaming com PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK atualiza
O Media Services .NET SDK é agora a versão 3.0.0.5. Foram feitas as seguintes atualizações:

* A velocidade e a resiliência são melhores quando faz o upload e o download de meios de comunicação.
* Foram introduzidas melhorias na lógica de retry e no tratamento transitório da exceção: 
  
  * A lógica transitória de deteção e retentativa de erros foi melhorada para exceções que são causadas quando se consulta, guarda alterações e upload ou download de ficheiros. 
  * Quando obtém exceções à web (por exemplo, durante um pedido de ficha do Serviço de Controlo de Acesso), erros fatais falham mais rapidamente agora.

Para mais informações, consulte a [lógica de Retry no Media Services SDK para .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Lançamentos de janeiro/fevereiro de 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3
As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Foram corrigidas questões relacionadas com a utilização de consultas LINQ com declarações OrderBy.
* As soluções de teste no [GitHub] foram divididas em testes baseados em unidades e testes baseados em cenários.

Para mais informações sobre as alterações, consulte os serviços de [media .NET SDK 3.0.0.1 e 3.0.0.2.](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)

As seguintes alterações foram efetuadas na versão 3.0.0.3:

* As dependências de armazenamento azure foram atualizadas para utilizar a versão 3.0.3.0.
* Uma questão de retrocompatibilidade foi fixada para 3.0. *.* liberta.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Lançamento de dezembro de 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Serviços de Media .NET SDK 3.0.0.0.0
> [!NOTE]
> Os lançamentos de 3.0.x não são compatíveis para trás com lançamentos de 2.4.x.x.
> 
> 

A versão mais recente do Media Services SDK é agora 3.0.0.0. Você pode baixar o pacote mais recente do NuGet ou obter os bits do [GitHub].

A partir da versão 3.0.0.0.0 do Media Services SDK, pode reutilizar as fichas do Serviço de Controlo de [Acesso AD Azure.](https://msdn.microsoft.com/library/hh147631.aspx) Para mais informações, consulte a secção "Fichas do Serviço de Controlo de Acesso de Reutilização" em [Connect to Media Services com o Media Services SDK para .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK extensões 2.0.0.0
 As extensões Media Services .NET SDK são um conjunto de métodos de extensão e funções auxiliares que simplificam o seu código e facilitam o desenvolvimento com os Serviços de Media. Pode obter as últimas partes das [extensões Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Lançamento de novembro de 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services .NET SDK alterações
A partir desta versão, o Media Services SDK para .NET lida com erros de falha transitórios que podem ocorrer quando as chamadas são feitas para a camada de API do Media Services REST.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Lançamento de agosto de 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services PowerShell cmdlets incluídos em ferramentas Azure SDK
Os seguintes cmdlets De Media Services PowerShell estão agora incluídos nas [ferramentas Azure SDK:](https://github.com/Azure/azure-sdk-tools)

* Get-AzureMediaServices 

    Por exemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Lançamento de junho de 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Alterações nos Serviços de Media
As seguintes alterações mencionadas nesta secção são atualizações incluídas nos comunicados de junho de 2013 dos Serviços de Media:

* Capacidade de ligar várias contas de armazenamento a uma conta de Serviço de Media. 
    * StorageAccount
    * Asset.StorageAccountName and Asset.StorageAccount
* Capacidade de atualizar Job.Priority. 
* Entidades e propriedades relacionadas com a notificação: 
    * Subscrição de Notificação de Emprego
    * Ponto final de notificação
    * Tarefa
* Ativo.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services .NET SDK alterações
As seguintes alterações estão incluídas nos lançamentos do SDK de junho de 2013. O mais recente Media Services SDK está disponível no GitHub.

* Começando pela versão 2.3.0.0, o Media Services SDK suporta a ligação de várias contas de armazenamento a uma conta de Media Services. As seguintes APIs suportam esta funcionalidade:
  
    * Tipo IStorageAccount
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Propriedade StorageAccount
    * Propriedade StorageAccountName
  
      Para mais informações, consulte [os ativos da Manage Media Services através de várias contas de armazenamento.](https://msdn.microsoft.com/library/azure/dn271889.aspx)
* APIs relacionados com notificação. A partir da versão 2.2.0.0, pode ouvir as notificações de armazenamento da Fila Azure. Para mais informações, consulte [as notificações](https://msdn.microsoft.com/library/azure/dn261241.aspx)de emprego da Handle Media Services .
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.Job.Job.JobNotificationSubscriptions property
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationEscreva tipo
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection tipo
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType tipo
* Dependência do cliente de armazenamento SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dependência do OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Lançamento de dezembro de 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services .NET SDK alterações
* IntelliSense: A documentação intelliSense em falta foi adicionada para muitos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: Foi corrigido um problema em que o SDK ainda tinha uma dependência de uma versão antiga deste conjunto. O SDK refere agora a versão 5.1.1209.1 deste conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count: Esta contagem é agora corretamente reportada em novas interfaces IAsset após a eliminação de todos os localizadores.
* IAssetFile.ContentFileSize: Este valor é agora corretamente definido após um upload por IAssetFile.Upload (filepath).
* IAssetFile.ContentFileSize: Esta propriedade pode agora ser definida quando cria um ficheiro de ativo. Foi lido apenas anteriormente.
* IAssetFile.Upload (filepath): Foi corrigido um problema onde este método de upload sincronizado estava a lançar o seguinte erro quando vários ficheiros foram enviados para o ativo. O erro foi "O Servidor não conseguiu autenticar o pedido. Certifique-se de que o valor do cabeçalho de autorização é formado corretamente, incluindo a assinatura."
* IAssetFile.UploadAsync: Foi corrigido um problema que limitou o envio simultâneo de ficheiros a cinco ficheiros.
* IAssetFile.UploadProgressChanged: Este evento é agora fornecido pelo SDK.
* IAssetFile.DownloadAsync (string, BlobTransferClient, ILocator, CancelToken): Esta sobrecarga de método supor agora.
* IAssetFile.DownloadAsync: Foi corrigido um problema que limitou o download simultâneo de ficheiros a cinco ficheiros.
* IAssetFile.Delete(): Um problema foi corrigido onde a chamada eliminar poderia lançar uma exceção se nenhum ficheiro fosse enviado para o IAssetFile.
* Empregos: Foi corrigido um problema em que a correntar uma tarefa "MP4 to Smooth Streams" com uma "PlayReady Protection Task" utilizando um modelo de trabalho não criou nenhuma tarefa.
* EncriptaçãoUtilizas.GetCertificateFromStore(): Este método já não lança uma exceção de referência nula devido a uma falha na procura do certificado com base em problemas de configuração do certificado.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Lançamento de novembro de 2012
As alterações mencionadas nesta secção foram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Estas alterações podem exigir que qualquer código escrito para a versão SDK de pré-visualização de junho de 2012 seja modificado ou reescrito.

* Elementos
  
    * IAsset.Create(assetName) é a *única* função de criação de ativos. IAsset.Create já não carrega ficheiros como parte da chamada do método. Utilize o IAssetFile para fazer o upload.
    * O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK de Serviços. Qualquer código que dependa deste valor deve ser reescrito.
* FileInfo
  
    * Esta classe foi removida e substituída pelo IAssetFile.
  
* IAssetFiles
  
    * O IAssetFile substitui o FileInfo e tem um comportamento diferente. Para usá-lo, instantaneamente o objeto IAssetFiles, seguido de um upload de ficheiro susco utilizando o SDK de Serviços de Media ou o SDK de Armazenamento. O seguinte IAssetFile.Carregar sobrecargas podem ser usados:
  
        * IAssetFile.Upload (filePath): Este método sincronizado bloqueia a linha, e recomendamos apenas quando faz o upload de um único ficheiro.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, localizador, cancelamentoToken): Este método assíncrono é o mecanismo de upload preferido. 
    
            Bug conhecido: Se utilizar o símbolo de cancelamento, o upload é cancelado. As tarefas podem ter muitos estados de cancelamento. Deve apanhar e lidar corretamente com exceções.
* Localizadores
  
    * As versões específicas da origem foram removidas. O contexto específico da SAS. Os localizadores.CreateSasLocator (ativo, accessPolicy) serão marcados deprecedos ou removidos pela disponibilidade geral. Consulte a secção "Localizadores" em "Nova funcionalidade" para comportamento atualizado.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Lançamento de pré-visualização de junho de 2012
A seguinte funcionalidade foi nova no lançamento de novembro do SDK:

* Entidades de desisminagem
  
    * IAsset, IAssetFile, ILocator, IAccessPolicy e iContentKey os objetos são agora eliminados ao nível do objeto, isto é, iObject.Delete(), em vez de exigir um desisuso na Coleção, isto é, cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    * Os localizadores devem agora ser criados utilizando o método CreateLocator. Devem utilizar os valores de enum do LocatorType.SAS ou do LocatorType.OnDemandOrigin como argumento para o tipo específico de localizador que pretende criar.
    * Foram adicionadas novas propriedades aos localizadores para facilitar a obtenção de URIs utilizáveis para o seu conteúdo. Esta remodelação de localizadores proporciona mais flexibilidade para a extibilidade futura de terceiros e aumenta a facilidade de utilização para aplicações de clientes de mídia.
* Suporte asíncrono
  
    * O apoio assíncrono foi adicionado a todos os métodos.

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: https://docs.microsoft.com/answers/topics/azure-media-services.html
[Referência da Azure Media Services REST API]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
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
[Visão geral do modelo de licença PlayReady de serviços de mídia]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Empacotamento dinâmico]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Redelógica de retry no SDK de Serviços de Media para .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
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

