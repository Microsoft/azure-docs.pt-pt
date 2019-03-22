---
title: Notas de versão dos serviços de multimédia | Documentos da Microsoft
description: Notas de versão dos serviços de multimédia
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8e602cc28026b4b6660c90eaeb529e549dbba5f5
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295441"
---
# <a name="azure-media-services-release-notes"></a>Notas de versão dos serviços de multimédia do Azure

Estas notas de versão para serviços de multimédia do Azure resumir as alterações de versões anteriores e problemas conhecidos.

> [!NOTE]
> Não existem novos recursos ou funcionalidades estão a ser adicionados para serviços de multimédia v2. 

Queremos ouvir os nossos clientes, para que possa concentrar no corrigir problemas que afetam. Para reportar um problema ou fazer perguntas, submeter uma postagem no [Fórum MSDN dos serviços de multimédia do Azure]. 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Problemas conhecidos atualmente
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Problemas gerais de serviços de multimédia

| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na REST API. |Se desenvolver aplicativos de serviços de multimédia com a API REST, achar que alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, ID de pedido e retorno-CLIENT-REQUEST-ID) não são suportados. Os cabeçalhos serão adicionados numa atualização futura. |
| Percentagem de codificação não é permitida. |Serviços de multimédia utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para o conteúdo de transmissão em fluxo (por exemplo, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Por esse motivo, a percentagem de codificação não é permitida. O valor da propriedade do nome não pode ter qualquer um dos seguintes [por cento de codificação-reservados carateres](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # ". Além disso, pode haver apenas um "." para a extensão de nome de ficheiro. |
| O método ListBlobs que faz parte da falha de 3.x de versão do SDK de armazenamento do Azure. |Serviços de multimédia gera URLs SAS com base na [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) versão. Se pretender utilizar o SDK de armazenamento para listar os blobs num contentor de BLOBs, utilize o [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) método que faz parte da versão do SDK de armazenamento 2.x. |
| O mecanismo de limitação os serviços de multimédia restringe a utilização de recursos para aplicativos que efetuam pedidos excessivos para o serviço. O serviço pode devolver o código de estado HTTP "Serviço indisponível" 503. |Para obter mais informações, consulte a descrição do código de estado HTTP 503 na [códigos de erro de serviços de multimédia](media-services-encoding-error-codes.md). |
| Quando consultar entidades, um limite de 1000 entidades é devolvido ao mesmo tempo porque a versão 2 REST pública limita os resultados da consulta para 1000 resultados. |Utilizar Skip e tirar (.NET) / principais (REST), conforme descrito em [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem se deparou com um problema de repetições de etiqueta no manifesto de transmissão em fluxo uniforme. |Para obter mais informações, consulte [esta secção](media-services-deliver-content-overview.md#known-issues). |
| Objetos de SDK .NET dos Media Services não não possível serializar e assim não funcionam com o Azure Cache de Redis. |Se tentar serializar o objeto de SDK AssetCollection para adicioná-lo para a Cache do Azure para Redis, é emitida uma exceção. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Histórico de versões de REST API
Para obter informações sobre o histórico de versões de API de REST dos serviços de suporte de dados, consulte a [Referência da API de REST dos serviços de multimédia do Azure].

## <a name="december-2018"></a>Dezembro de 2018

O [Hyperlapse de suporte de dados pré-visualização](media-services-hyperlapse-content.md) em breve será retirada a funcionalidade dos serviços de multimédia do Azure. A partir de 19 de Dezembro de 2018, os serviços de multimédia já não fará alterações ou melhorias no Hyperlapse de multimédia. 29 de Março de 2019 será extinto e já não está disponível.

## <a name="october-2018"></a>Outubro de 2018

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de encriptação CMAF e 'cbcs' para (iOS 11 +) do Apple HLS e MPEG-DASH jogadores que suportam CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprites em miniatura do Web VTT

Agora, pode utilizar os serviços de multimédia para gerar os sprites de miniatura Web VTT com as nossas APIs de v2. Para obter mais informações, consulte [gerar um miniatura sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Julho de 2018

Com a versão mais recente do serviço, existem pequenas alterações para as mensagens de erro retornadas pelo serviço quando uma tarefa falhar, em relação à forma como ele é dividido em duas ou mais linhas de formatação.

## <a name="may-2018"></a>Maio de 2018 

A partir de 12 de Maio de 2018, os canais em direto será já não suporte o fluxo de transporte RTP/MPEG-2 protocolo de ingestão. Migre de RTP/MPEG-2 para RTMP ou MP4 fragmentado (Smooth Streaming) protocolos de ingestão.

## <a name="october-2017-release"></a>Versão de Outubro de 2017
> [!IMPORTANT] 
> Serviços de suporte de dados está a descontinuar o suporte para as chaves de autenticação do serviço de controle de acesso do Azure. 22 de Junho de 2018, já não pode autenticar com os serviços de suporte de dados back-end por meio do código com chaves de serviço de controlo de acesso. Tem de atualizar seu código para utilizar o Azure Active Directory (Azure AD) por [autenticação baseada no AD do Azure](media-services-use-aad-auth-to-access-ams-api.md). Procure avisos sobre esta alteração no portal do Azure.

### <a name="updates-for-october-2017"></a>Atualizações de Outubro de 2017
#### <a name="sdks"></a>SDKs
* O SDK de .NET foi atualizado para suportar a autenticação do Azure AD. Suporte para a autenticação do serviço de controlo de acesso foi removido do SDK mais recente do .NET em Nuget.org para incentivar a migração mais rápida para o Azure AD. 
* O SDK de JAVA foi atualizado para suportar a autenticação do Azure AD. Foi adicionado suporte para autenticação do Azure AD para o SDK de Java. Para obter informações sobre como utilizar o SDK de Java com os Media Services, consulte [introdução ao SDK de cliente Java para serviços de multimédia do Azure](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>A codificação baseada em ficheiros
* Agora, pode utilizar o codificador Premium para codificar o conteúdo para o vídeo de alta eficiência H.265 codificação codec de vídeo (HEVC). Não há nenhum impacto no preço, se escolher H.265 ao invés de outros codecs, como o H.264. Para obter informações sobre as licenças de patentes HEVC, consulte [termos dos Online Services](https://azure.microsoft.com/support/legal/).
* Vídeo de origem que está codificado com o codec de vídeo H.265 (HEVC), como vídeo capturado, usando o iOS11 ou GoPro Hero 6, agora, pode utilizar o codificador Premium ou o codificador Standard para codificar esses vídeos. Para obter informações sobre as licenças de patentes, consulte [termos dos Online Services](https://azure.microsoft.com/support/legal/).
* Para conteúdo que contém várias faixas de áudio de linguagem, os valores de idioma devem ser corretamente rotulado como, de acordo com a especificação de formato de ficheiro (por exemplo, o ISO MP4) correspondente. Em seguida, pode usar o codificador Standard para codificar o conteúdo de transmissão em fluxo. O localizador de transmissão em fluxo resultante lista os idiomas de áudio disponíveis.
* O codificador Standard suporta agora duas novas só de áudio predefinições do sistema, "Áudio AAC" e "AAC boa qualidade áudio". Ambos produzem estéreo advanced áudio codificação de saída (AAC), às taxas de bits de 128 Kbps e 192 Kbps, respectivamente.
* O codificador Premium suporta agora formatos de arquivo do QuickTime/MOV como entrada. O codec de vídeo tem de ser o [tipos de Apple ProRes apresentadas neste artigo do GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). O áudio tem de ser de qualquer AAC ou pulse modulação de código (PCM). O codificador Premium não suporta, por exemplo, vídeo DVC/DVCPro encapsulado em arquivos do QuickTime/MOV como entrada. O codificador Standard suporta este codecs de vídeo.
* As seguintes correções de bugs foram feitas no codificadores:

    * Agora pode submeter tarefas ao utilizar um elemento de entrada. Depois de concluir estas tarefas, pode modificar o recurso (por exemplo, adicionar, eliminar ou mudar o nome dos arquivos dentro do elemento) e submeter as tarefas adicionais.
    * A qualidade das miniaturas JPEG produzidos pelo codificador Standard foi aprimorada.
    * O codificador Standard processa dos metadados de entrada e de geração de miniaturas melhor nos vídeos de duração muito curto.
    * Melhorias ao Decodificador H.264 utilizado do codificador Standard eliminam determinados artefatos raros. 

#### <a name="media-analytics"></a>Análise de Multimédia
Disponibilidade geral do Redator de multimédia do Azure: Este processador de multimédia realiza anonimização ao desfocar os rostos de indivíduos selecionados e é ideal para utilização em cenários de segurança e multimédia para notícias públicos. 

Para uma descrição geral sobre este novo processador, consulte [nesta mensagem de blogue](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter informações sobre a documentação e as definições, consulte [edite rostos com análise de multimédia do Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versão de Junho de 2017

Serviços de multimédia agora suporta [autenticação baseada no AD do Azure](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso. Autorização de serviço de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="march-2017-release"></a>Versão de Março de 2017

Agora, pode utilizar o codificador Standard a [gerar automaticamente uma escala de bits](media-services-autogen-bitrate-ladder-with-mes.md) , especificando o "transmissão em fluxo adaptável" Predefinir a cadeia de caracteres quando cria uma tarefa de codificação. Para codificar um vídeo para transmissão em fluxo com os serviços de multimédia, utilize a configuração predefinida de "Transmissão em fluxo adaptável". Para personalizar uma codificação na configuração predefinida para o seu cenário específico, pode começar a usar [estas predefinições](media-services-mes-presets-overview.md).

Agora pode utilizar o Media Encoder Standard ou o Media Encoder Premium Workflow para [criar uma tarefa de codificação que gera segmentos fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Versão de Fevereiro de 2017

A partir de 1 de Abril de 2017, qualquer registo de tarefa na sua conta mais de 90 dias é eliminado automaticamente, juntamente com os seus registos de tarefa associada. A eliminação ocorre mesmo se o número total de registos for inferior à quota máxima. Para arquivar as informações de tarefas, pode utilizar o código descrito em [gerir ativos e entidades relacionadas com o SDK .NET dos Media Services](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versão de Janeiro de 2017

Nos Media Services, um ponto de final de transmissão em fluxo representa um serviço de transmissão em fluxo que pode entregar conteúdo diretamente a uma aplicação de leitor cliente ou a uma rede de entrega de conteúdos (CDN) para uma maior distribuição. Serviços de multimédia também fornecem integração perfeita de rede de entrega de conteúdos do Azure. O fluxo de saída de um serviço de StreamingEndpoint pode ser uma transmissão em direto, um vídeo a pedido ou uma transferência progressiva de seus recursos na sua conta de Media Services. Cada conta de Media Services inclui um ponto final de transmissão em fluxo de predefinido. Pontos finais de transmissão em fluxo adicionais podem ser criados sob a conta. 

Existem duas versões de transmissão em fluxo de pontos de extremidade, 1.0 e 2.0. A partir de 10 de Janeiro de 2017, as contas de serviços de multimédia recém-criado incluem o padrão da versão 2.0 ponto final de transmissão em fluxo. Transmissão em fluxo pontos finais adicionais que adicionar a esta conta também têm a versão 2.0. Esta alteração não afeta as contas existentes. Pontos finais de transmissão em fluxo existentes são a versão 1.0 e podem ser atualizados para a versão 2.0. Existem alterações de funcionalidade com esta alteração, faturação e comportamento. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

A partir da versão 2,15, serviços de multimédia adicionadas as seguintes propriedades para a entidade de ponto final de transmissão em fluxo:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Para obter mais informações sobre estas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versão de Dezembro de 2016

 Agora, pode utilizar os serviços de multimédia para aceder a dados de telemetria/métricas para seus serviços. Pode utilizar a versão atual dos serviços de multimédia para recolher dados de telemetria para o canal em direto, o ponto final de transmissão em fluxo e arquivar entidades. Para obter mais informações, consulte [telemetria de serviços de multimédia](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Versão de Julho de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações para o arquivo de manifesto (*. O ISM) gerados por tarefas de codificação
Quando uma tarefa de codificação foi submetida para o Media Encoder Standard ou Premium do codificador de multimédia, a tarefa de codificação gera uma [arquivo de manifesto de transmissão em fluxo](media-services-deliver-content-overview.md) (*. ISM) no recurso de saída. Com a versão mais recente do serviço, a sintaxe deste ficheiro de manifesto de transmissão em fluxo foi atualizada.

> [!NOTE]
> A sintaxe do arquivo de manifesto (. ISM) transmissão em fluxo é reservada para utilização interna. Ele está sujeita a alterações em versões futuras. Não modifique ou manipular o conteúdo deste ficheiro.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. Ficheiro ISMC) é gerado no recurso de saída quando uma tarefa de codificação produz um ou mais ficheiros MP4
A partir da versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais ficheiros MP4, o elemento de saída contém também um arquivo de manifesto (*.ismc) de cliente transmissão em fluxo. O ficheiro de .ismc ajuda a melhorar o desempenho de dinâmica de transmissão em fluxo. 

> [!NOTE]
> A sintaxe do arquivo de manifesto (.ismc) do cliente está reservada para utilização interna. Ele está sujeita a alterações em versões futuras. Não modifique ou manipular o conteúdo deste ficheiro.
> 
> 

Para obter mais informações, veja [este blogue](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem se deparou com um problema de repetições de etiqueta no manifesto de transmissão em fluxo uniforme. Para obter mais informações, consulte [esta secção](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Versão de Abril de 2016
### <a name="media-analytics"></a>Análise de Multimédia
 Serviços de multimédia introduziu a análise de multimédia para inteligência poderosa de vídeo. Para obter mais informações, consulte [descrição geral de análise de serviços de multimédia](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (pré-visualização)
Agora pode utilizar os serviços de multimédia para encriptar dinamicamente o HTTP Live Streaming (HLS) conteúdo com o Apple FairPlay. Também pode utilizar o serviço de entrega de licença de serviços de multimédia para entregar licenças do FairPlay para clientes. Para obter mais informações, consulte "Utilização dos serviços de multimédia do Azure para transmitir o conteúdo HLS protegido com o Apple FairPlay."

## <a id="feb_changes16"></a>Versão de Fevereiro de 2016
A versão mais recente do SDK de serviços de multimédia para .NET (3.5.3) contém uma correção de erros relacionados com a Google Widevine. Foi impossível reutilizar AssetDeliveryPolicy para vários recursos de encriptado com Widevine. Como parte desta correção de erro, a seguinte propriedade foi adicionada para o SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Versão de Janeiro de 2016
Unidades de Encoding reservadas foram renomeadas para reduzir a confusão com nomes de codificador.

Os básico, Standard e Premium unidades de encoding reservadas foram renomeadas para S1, S2, e S3 unidades reservadas de, respectivamente. Os clientes que utilizam atualmente básicas unidades de encoding reservadas S1, consulte como a etiqueta no portal do Azure (e na fatura). Os clientes que utilizam o Standard e Premium verem as etiquetas de S2 e S3, respectivamente. 

## <a id="dec_changes_15"></a>Versão de Dezembro de 2015

### <a name="media-encoder-deprecation-announcement"></a>Anúncio de preterição do codificador de multimédia

 Codificador de multimédia vão ser preterido a partir de aproximadamente 12 meses a partir da versão do Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>SDK do Azure para PHP
A equipe do SDK do Azure publicada uma nova versão dos [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) pacote que contém as atualizações e novas funcionalidades para serviços de multimédia. Em particular, o SDK de serviços de multimédia para PHP suporta agora a versão mais recente [proteção de conteúdo](media-services-content-protection-overview.md) funcionalidades. Estas funcionalidades são a encriptação dinâmica com o AES e o DRM (PlayReady e Widevine) com e sem restrições de token. Também suporta dimensionamento [unidades de codificação](media-services-dotnet-encoding-units.md).

Para obter mais informações, consulte:

* O seguinte procedimento [exemplos de código](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) ajudá-lo a começar rapidamente:
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave. Baseia-se no exemplo de .NET explicado [encriptação dinâmica de utilizar AES-128 e o serviço de entrega de chave](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica do PlayReady e o serviço de entrega de licença. Baseia-se no exemplo de .NET explicado [utilização PlayReady e/ou Widevine encriptação comum dinâmica](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Este ficheiro PHP mostra como dimensionar as unidades de encoding reservadas.

## <a id="nov_changes_15"></a>Versão de Novembro de 2015
 Serviços de multimédia oferecem agora o serviço de entrega de licença do Widevine na cloud. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Além disso, veja [deste tutorial](media-services-protect-with-playready-widevine.md) e o [repositório do GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Serviços de entrega de licença do Widevine fornecidos pelos serviços de multimédia estão em pré-visualização. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versão de Outubro de 2015
Serviços de multimédia está agora em direto nos centros de dados seguintes: Sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. Agora, pode utilizar o portal do Azure para [criar contas de serviço de multimédia](media-services-portal-create-account.md) e realizar várias tarefas descritas no [página Web de documentação dos serviços de multimédia](https://azure.microsoft.com/documentation/services/media-services/). O Live Encoding não está ativado nestes centros de dados. Além disso, nem todos os tipos de unidades de codificação reservadas estão disponíveis nestes centros de dados.

* Sul do Brasil:                                          Apenas Standard e Basic unidades de codificação reservadas estão disponíveis.
* Índia Ocidental, Sul da Índia e Índia Central:             Apenas o básicas unidades de encoding reservadas estão disponíveis.

## <a id="september_changes_15"></a>Versão de Setembro de 2015
Serviços de multimédia oferece agora a capacidade de proteger o vídeo a pedido e transmissões em direto com tecnologia do Widevine modular DRM. Pode utilizar os seguintes parceiros de serviços de entrega para o ajudar a fornecer licenças do Widevine:
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Pode utilizar o [SDK .NET dos Serviços de Multimédia](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar o AssetDeliveryConfiguration para utilizar o Widevine. 
* Serviços de multimédia foi adicionado suporte para vídeos ProRes da Apple. Agora, pode carregar os ficheiros de vídeos de origem QuickTime que utilizam o Apple ProRes ou outros codecs. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Pode agora utilizar Media Encoder Standard para fazer a extração de arquivo subclipping e em direto. Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Foram efetuadas as seguintes atualizações de filtragem: 
  
  * Agora, pode utilizar o formato de Apple HLS com um filtro de só de áudio. Pode utilizar esta atualização para remover um Roteiro de só de áudio, especificando (só de áudio = false) no URL.
  * Ao definir filtros para os seus ativos, agora pode combinar vários filtros (até três) numa única URL.
    
    Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Serviços de multimédia suportam agora eu-quadros no HLS versão 4. Suporte de eu fotograma otimiza as operações de avanço rápido e retroceder. Por predefinição, todos os resultados da versão 4 de HLS incluem a lista de reprodução do eu-quadro (EXT-X-I-FRAME-STREAM-INF).
Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Versão de Agosto de 2015
* O SDK dos serviços de suporte de dados para o lançamento de versão 0.8.0 de Java e novos exemplos estão agora disponíveis. Para obter mais informações, consulte:
    
* O leitor de multimédia do Azure foi atualizado com o suporte de transmissão de áudio com múltiplos. Para obter mais informações, consulte [nesta mensagem de blogue](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Versão de Julho de 2015
* A disponibilidade geral do Media Encoder Standard foi anunciada. Para obter mais informações, consulte [nesta mensagem de blogue](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Codificador de multimédia Standard utiliza configurações predefinidas, conforme descrito em [esta secção](https://go.microsoft.com/fwlink/?LinkId=618336). Quando utiliza uma configuração predefinida para 4K codifica, obter o tipo de unidade Premium reservado. Para obter mais informações, consulte [codificação de escala](media-services-scale-media-processing-overview.md).
* Legendas de áudio em tempo real em direto foram utilizadas com os serviços de multimédia e o Media Player. Para obter mais informações, consulte [nesta mensagem de blogue](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK .NET dos Media Services
O SDK .NET dos Media Services agora é a versão 3.4.0.0. Foram efetuadas as seguintes atualizações: 

* O suporte foi implementado para o arquivo em direto. Não é possível transferir um elemento que contenha um arquivo em direto.
* O suporte foi implementado para filtros dinâmicos.
* Funcionalidade foi implementada para que os usuários podem ter um contentor de armazenamento, enquanto que eliminar um recurso.
* Correções de bugs foram feitas relacionadas para repetir as políticas em canais.
* Media Encoder Premium Workflow foi ativada.

## <a id="june_changes_15"></a>Versão de Junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK .NET dos Media Services
O SDK .NET dos Media Services agora é a versão 3.3.0.0. Foram efetuadas as seguintes atualizações: 

* Foi adicionado suporte para a especificação de deteção do OpenId Connect.
* Foi adicionado suporte para a manipulação de rollover de chaves no lado do fornecedor de identidade.

Se usar um fornecedor de identidade que expõe um documento de deteção do OpenID Connect (como o Azure AD, Google e o Salesforce fazem), pode instruir o serviços de multimédia para obter as chaves de assinatura para a validação de JSON Web Tokens (JWTs) da especificação de deteção do OpenID Connect. 

Para obter mais informações, consulte [chaves de web de JSON de utilização da especificação de deteção do OpenID Connect para funcionar com a autenticação do JWT nos serviços de multimédia](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Versão de Maio de 2015
Os seguintes novos recursos foram anunciados:

* [Uma pré-visualização da codificação em tempo real com os serviços de multimédia](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
* [Uma pré-visualização do processador de multimédia Hyperlapse de multimédia do Azure](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Versão de Abril de 2015
### <a name="general-media-services-updates"></a>As atualizações dos serviços de suporte de dados gerais
* [O leitor de multimédia](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) foi anunciado.
* Começando com o 2.10 de REST de serviços de multimédia, canais que estão configurados para a ingestão de um protocolo de mensagens de em tempo real (RTMP) são criados com o primário e secundário URLs de inserção. Para obter mais informações, consulte [configurações de ingestão do canal](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* O indexador de multimédia do Azure foi atualizado.
* Foi adicionado suporte para idioma espanhol.
* Foi adicionada uma nova configuração para o formato XML.

Para obter mais informações, veja [este blogue](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK .NET dos Media Services
O SDK .NET dos Media Services agora é a versão 3.2.0.0. Foram efetuadas as seguintes atualizações:

* Alteração significativa: TokenRestrictionTemplate.Issuer e TokenRestrictionTemplate.Audience foram alterados para ter um tipo de cadeia de caracteres.
* As atualizações foram feitas relacionadas à criação de políticas de repetição personalizada.
* Correções de bugs foram feitas relacionadas para carregar e descarregar arquivos.
* A classe MediaServicesCredentials aceita agora pontos finais de controlo de acesso primária e secundária para autenticar.

## <a id="march_changes_15"></a>Versão de Março de 2015
### <a name="general-media-services-updates"></a>As atualizações dos serviços de suporte de dados gerais
* Os Media Services agora fornecem integração de rede de entrega de conteúdos. Para suportar a integração, a propriedade CdnEnabled foi adicionada ao StreamingEndpoint. CdnEnabled pode ser utilizado com as APIs REST a partir da versão 2.9. Para obter mais informações, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled pode ser utilizado com o SDK do .NET a partir da versão 3.1.0.2. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* O Media Encoder Premium Workflow foi anunciado. Para obter mais informações, consulte [encoding Premium apresentando nos serviços de multimédia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Versão de Fevereiro de 2015
### <a name="general-media-services-updates"></a>As atualizações dos serviços de suporte de dados gerais
A API de REST de serviços de suporte de dados está agora a versão 2.9. Começando com esta versão, pode ativar a integração de rede de entrega de conteúdos com pontos finais de transmissão em fluxo. Para obter mais informações, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Versão de Janeiro de 2015
### <a name="general-media-services-updates"></a>As atualizações dos serviços de suporte de dados gerais
A disponibilidade geral da proteção de conteúdo com encriptação dinâmica foi anunciada. Para obter mais informações, consulte [dos serviços de multimédia melhora a segurança de transmissão em fluxo com a disponibilidade geral da tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK .NET dos Media Services
O SDK .NET dos Media Services agora é a versão 3.1.0.1.

Esta versão marcadas como o construtor de Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate padrão como obsoleto. O novo construtor aceita TokenType como argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versão de Dezembro de 2014
### <a name="general-media-services-updates"></a>As atualizações dos serviços de suporte de dados gerais
* Algumas atualizações e novas funcionalidades foram adicionadas para o indexador de multimédia. Para obter mais informações, consulte [notas de versão do indexador de multimédia do Azure versão 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Uma nova API de REST foi adicionada a que pode utilizar para atualizar as unidades de encoding reservadas. Para obter mais informações, consulte [EncodingReservedUnitType com REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Foi adicionado suporte CORS para o serviço de entrega de chave.
* Foram efetuadas melhorias de desempenho para consultar as opções de política de autorização.
* No Centro de dados na China, o [URL de entrega da chave](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) é agora por cliente (tal como nos outros centros de dados).
* Duração de destino do HLS automática foi adicionada. Ao realizar a transmissão em fluxo em direto, HLS é sempre empacotado dinamicamente. Por predefinição, os serviços de multimédia calcula automaticamente a HLS segmento empacotamento proporção (FragmentsPerSegment) com base no intervalo de quadro-chave (KeyFrameInterval). Este método é também referido como um grupo de imagens (GOP) que é recebido do codificador em direto. Para obter mais informações, consulte [transmissão em direto de trabalho com serviços de multimédia](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK .NET dos Media Services
O [SDK .NET dos Media Services](https://www.nuget.org/packages/windowsazure.mediaservices/) está agora a versão 3.1.0.0. Foram efetuadas as seguintes atualizações:

* A dependência do SDK do .NET foi atualizada para o .NET 4.5 Framework.
* Foi adicionada uma nova API que pode utilizar para atualizar as unidades de encoding reservadas. Para obter mais informações, consulte [atualização reservados de tipo de unidade e aumento de unidades de codificação reservadas com o .NET](media-services-dotnet-encoding-units.md).
* Foi adicionado suporte JWT para autenticação de token. Para obter mais informações, consulte [autenticação de token JWT nos serviços de suporte de dados e a encriptação dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Foram adicionados desvios relativos para BeginDate e ExpirationDate no modelo de licença PlayReady.

## <a id="november_changes_14"></a>Versão de Novembro de 2014
* Agora pode utilizar os serviços de multimédia para ingestão de conteúdos de transmissão em fluxo uniforme (fMP4) em direto através de uma ligação SSL. Ingerir através de SSL, certifique-se atualizar o URL de inserção para HTTPS. Atualmente, os serviços de multimédia não suporta SSL com domínios personalizados. Para obter mais informações sobre a transmissão em fluxo em direto, consulte [trabalhar com o Azure Media Services transmissão em direto](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Atualmente, não pode ingerir um fluxo em direto de RTMP através de uma ligação SSL.
* Pode transmitir em fluxo através de SSL apenas se o ponto final de transmissão em fluxo do que forneça o conteúdo tiver sido criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo baseiam-se os pontos finais de transmissão em fluxo criados após 10 de Setembro de 2014, o URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de transmissão em fluxo que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o URL está no formato antigo e quer transmitir através de SSL, [criar um novo ponto de final de transmissão em fluxo](media-services-portal-manage-streaming-endpoints.md). Para transmitir o seu conteúdo através de SSL, use URLs com base no novo ponto de final de transmissão em fluxo.

## <a id="october_changes_14"></a>Versão de Outubro de 2014
### <a id="new_encoder_release"></a>Versão do codificador de serviços de multimédia
 A nova versão do codificador de multimédia do Media Services do Azure foi anunciada. Com o codificador de multimédia mais recentes, é-lhe cobrado apenas para GBs de saída. Caso contrário, o novo codificador é compatível com o codificador anterior de funcionalidade. Para obter mais informações, consulte [Detalhes de preços dos Serviços de Multimédia (Media Services pricing details)].

### <a id="oct_sdk"></a>.NET SDK dos serviços de multimédia
O SDK de Media Services para .NET extensions está agora a versão 2.0.0.3.

O SDK de serviços de multimédia para .NET está agora a versão 3.0.0.8. Foram efetuadas as seguintes atualizações:

* Refatoração foi implementada nas classes de política de repetição.
* Uma cadeia de caracteres do agente de utilizador foi adicionada para os cabeçalhos de pedido HTTP.
* Foi adicionada uma etapa de compilação de restauro do NuGet.
* Testes de cenário foram corrigidas para utilizar x509 cert do repositório.
* Foram adicionadas definições de validação para quando atualizar o canal e o final de transmissão em fluxo.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório do GitHub para exemplos de serviços de multimédia do anfitrião
Os exemplos são no [repositório do GitHub de exemplos de serviços de multimédia](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Versão de Setembro de 2014
Os metadados de REST de serviços de suporte de dados estão agora a versão 2.7. Para obter mais informações sobre as atualizações mais recentes do REST, consulte a [referência da API de REST de serviços de multimédia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

O SDK de serviços de multimédia para .NET está agora a versão 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Alterações recentes
* Origem nome foi mudada para [StreamingEndpoint].
* Foi efetuada uma alteração no comportamento predefinido ao utilizar o portal do Azure para codificar e, em seguida, publicar ficheiros MP4.

### <a id="sept_14_GA_changes"></a>Novos recursos/cenários que fazem parte da versão de disponibilidade geral
* Foi introduzido o processador de multimédia do indexador de multimédia. Para obter mais informações, consulte [indexar os arquivos de suporte de dados com o indexador de multimédia](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Pode utilizar o [StreamingEndpoint] entidade para adicionar nomes de domínio personalizado (anfitrião).
  
    Para utilizar um nome de domínio personalizado como o nome do ponto final de transmissão em fluxo dos serviços de multimédia, adicione os nomes de anfitrião personalizado para o ponto final de transmissão em fluxo. Utilize as APIs de REST de serviços de suporte de dados ou o SDK de .NET para adicionar nomes de anfitrião personalizado.
  
    As seguintes considerações aplicam-se:
  
  * Tem de ter a propriedade de nome de domínio personalizado.
  * A propriedade de nome de domínio têm de ser validada pelos serviços de multimédia. Para validar o domínio, crie um CName que mapeia o domínio principal MediaServicesAccountId para verificar mediaservices-dns-zona DNS.
  * Tem de criar outro CName que mapeia o nome de anfitrião personalizado (por exemplo, sports.contoso.com) para o seu nome de anfitrião StreamingEndpoint de serviços de multimédia (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para obter mais informações, consulte a propriedade CustomHostNames na [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) artigo.

### <a id="sept_14_preview_changes"></a>Novos recursos/cenários que fazem parte da versão de pré-visualização pública
* Em direto a pré-visualização de transmissão em fluxo. Para obter mais informações, consulte [transmissão em direto de trabalho com serviços de multimédia](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Serviço de entrega de chave. Para obter mais informações, consulte [encriptação dinâmica de utilizar AES-128 e o serviço de entrega de chave](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Encriptação dinâmica AES. Para obter mais informações, consulte [encriptação dinâmica de utilizar AES-128 e o serviço de entrega de chave](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Serviço de entrega de licença PlayReady. 
* PlayReady a encriptação dinâmica. 
* Modelo de licença de PlayReady de serviços de multimédia. Para obter mais informações, consulte a [Descrição geral do modelo de licença do PlayReady dos Serviços de Multimédia].
* Ativos de encriptação de armazenamento de Stream. Para obter mais informações, consulte [Stream conteúdo encriptado com o armazenamento](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Versão de Agosto de 2014
Ao codificar um elemento, um elemento de saída é produzido quando a tarefa de codificação é concluída. Até esta versão, o codificador de serviços de suporte de dados produzidos metadados sobre recursos de saída. Começando com esta versão, o codificador produz também metadados sobre os recursos de entrada. Para obter mais informações, consulte [metadados de entrada] e [metadados de saída].

## <a id="july_changes_14"></a>Versão de Julho de 2014
As seguintes correções de bugs foram feitas para o empacotador de serviços de multimédia do Azure e o encriptador:

* Quando um recurso de arquivo live é transmitido para HLS, áudio apenas reproduz: Este problema foi corrigido, e agora podem reproduzir áudio e vídeo.
* Quando um recurso é empacotado para encriptação de envelope HLS e AES de 128 bits, os fluxos em pacote não reproduzir em dispositivos Android: Esse bug foi corrigido, e o fluxo em pacote reproduz em dispositivos Android que suportam HLS.

## <a id="may_changes_14"></a>Versão de Maio de 2014
### <a id="may_14_changes"></a>As atualizações dos serviços de suporte de dados gerais
Agora, pode utilizar [empacotamento dinâmico] fluxo HLS versão 3. Para HLS versão de fluxo de 3, adicione o seguinte formato para o caminho de localização de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte [Este fórum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Também empacotamento dinâmico agora suporta o fornecimento HLS (versão 3 e 4 de versão) encriptado com PlayReady com base na transmissão em fluxo uniforme estaticamente criptografado com PlayReady. Para obter informações sobre como encriptar a transmissão em fluxo uniforme com PlayReady, consulte [proteger Smooth Streaming com PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Atualizações do SDK .NET dos Media Services
O SDK .NET dos Media Services agora é a versão 3.0.0.5. Foram efetuadas as seguintes atualizações:

* Velocidade e a resiliência são melhores quando carregar e transferir os ativos de mídia.
* Foram efetuadas melhorias na manipulação de exceção de repetição lógica e transitórias: 
  
  * Lógica de deteção e volte a tentar erro transitório foram melhorados para exceções causados quando consultar, guarde as alterações e carregar ou transferir os ficheiros. 
  * Quando receber exceções da web (por exemplo, durante um pedido de token de serviço de controlo de acesso), erros fatais falharem mais rapidamente agora.

Para obter mais informações, consulte [lógica de repetição no SDK de serviços de multimédia para .NET].

## <a id="april_changes_14"></a>Versão do codificador de Abril de 2014
### <a name="april_14_enocer_changes"></a>Atualizações do codificador de serviços de multimédia
* Foi adicionado suporte para ingerir ficheiros AVI, que são criados com o editor de não-linear de grama Valley EDIUS. Nesse processo, o vídeo é apenas superficialmente comprimido com o codec de grama Valley HQ/HQX. Para obter mais informações, consulte [grama Valley anuncia 7 EDIUS transmissão em fluxo através da cloud].
*  Foi adicionado suporte para especificar a Convenção de nomenclatura para os arquivos gerados pelo codificador de serviços de multimédia. Para obter mais informações, consulte [nomes de ficheiro de saída do codificador de serviços de suporte de dados de controle](https://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Foi adicionado suporte para sobreposições de vídeo e/ou áudio. Para obter mais informações, consulte [criar sobreposições](https://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Foi adicionado suporte para reunir vários segmentos de vídeo. Para obter mais informações, consulte [reunir os segmentos de vídeo](https://msdn.microsoft.com/library/azure/dn640504.aspx).
* Foi corrigido um erro que estava relacionado a transcodificação MP4s onde o áudio codificado com MPEG-1 3 da camada de áudio (também conhecido como MP3).

## <a id="jan_feb_changes_14"></a>Versões de Janeiro/Fevereiro de 2014
### <a name="jan_fab_14_donnet_changes"></a>SDK de .NET 3.0.0.1, 3.0.0.2 e 3.0.0.3 dos serviços de multimédia
As alterações no 3.0.0.1 e 3.0.0.2 incluem:

* Problemas relacionados com a utilização de consultas do LINQ com as instruções de OrderBy foram corrigidos.
* Testar soluções na [GitHub] foram divididos em testes de unidade e testes com base no cenário.

Para obter mais informações sobre as alterações, consulte a [versões do SDK de .NET para serviços de multimédia 3.0.0.1 e 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

As seguintes alterações foram feitas na versão 3.0.0.3:

* Dependências de armazenamento do Azure foram atualizadas para utilizar a versão 3.0.3.0.
* Um problema de compatibilidade com versões anteriores, foi corrigido para 3.0. *.* versões.

## <a id="december_changes_13"></a>Versão de Dezembro de 2013
### <a name="dec_13_donnet_changes"></a>Serviços de multimédia .NET SDK 3.0.0.0
> [!NOTE]
> As versões de 3.0.x.x não são compatíveis com versões anteriores com as versões de 2.4.x.x.
> 
> 

A versão mais recente do SDK de serviços de suporte de dados está agora 3.0.0.0. Pode transferir o pacote mais recente do NuGet ou obtenha os bits da [GitHub].

Começando com o SDK de Media Services versão 3.0.0.0, é possível reutilizar os [serviço de controlo de acesso do Azure AD](https://msdn.microsoft.com/library/hh147631.aspx) tokens. Para obter mais informações, consulte a secção "Tokens do serviço de controle de acesso de reutilização" em [ligar aos Media Services com o SDK de Media Services para .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK .NET dos Media Services 2.0.0.0
 As extensões do SDK de .NET de serviços de multimédia são um conjunto de métodos de extensão e funções auxiliares que simplificam o seu código e torná-lo mais fácil desenvolver com os Media Services. Pode obter os bits mais recentes do [extensões do SDK .NET dos Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Versão de Novembro de 2013
### <a name="nov_13_donnet_changes"></a>Alterações do SDK .NET dos Media Services
Começando com esta versão, o SDK de Media Services para .NET identificadores de falhas transitórias erros que possam ocorrer quando chamadas são feitas para a camada de API de REST dos serviços de multimédia.

## <a id="august_changes_13"></a>Versão de Agosto de 2013
### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell de serviços de suporte de dados incluídos nas ferramentas do SDK do Azure
Os seguintes cmdlets PowerShell de serviços de suporte de dados agora estão incluídos no [ferramentas SDK do Azure](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Por exemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Versão de Junho de 2013
### <a name="june_13_general_changes"></a>Alterações de serviços de multimédia
As seguintes alterações mencionadas nesta seção são as atualizações incluídas nas versões dos serviços de multimédia de Junho de 2013:

* Capacidade de ligar várias contas de armazenamento a uma conta de serviço de multimédia. 
    * StorageAccount
    * Asset.StorageAccountName e Asset.StorageAccount
* Capacidade de atualizar Job.Priority. 
* Entidades relacionadas com a notificação e propriedades: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Tarefa
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do SDK .NET dos Media Services
As seguintes alterações são incluídas em Junho de 2013 da versões do SDK de Media Services. O SDK de serviços de multimédia mais recente está disponível no GitHub.

* O SDK de Media Services oferece suporte à, vários de armazenamento de ligação a partir da versão 2.3.0.0, contas para uma conta de Media Services. As seguintes APIs de suportar esta funcionalidade:
  
    * Tipo de IStorageAccount
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * StorageAccount propriedade
    * Propriedade de StorageAccountName
  
      Para obter mais informações, consulte [ativos de gerir os serviços de multimédia em várias contas de armazenamento](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* APIs relacionadas a notificação. A partir da versão 2.2.0.0, pode ouvir para notificações de armazenamento de filas do Azure. Para obter mais informações, consulte [notificações da tarefa de lidar com os serviços de multimédia](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint type
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type
    * Tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Dependência de cliente de armazenamento SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dependência do OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Versão de Dezembro de 2012
### <a name="dec_12_dotnet_changes"></a>Alterações do SDK .NET dos Media Services
* IntelliSense: Falta de documentação do IntelliSense foi adicionado para muitos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: Um problema foi corrigido em que o SDK ainda tinha uma dependência de uma versão antiga desse assembly. O SDK agora faz referência a versão 5.1.1209.1 tohoto sestavení.

Correções para problemas encontrados em Novembro de 2012 SDK:

* IAsset.Locators.Count: Esta contagem agora é reportada corretamente no novas interfaces de IAsset depois de todos os localizadores são eliminados.
* IAssetFile.ContentFileSize: Este valor está agora corretamente definido após um carregamento por IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Esta propriedade agora pode ser definida quando cria um ficheiro de elemento. Ele foi anteriormente só de leitura.
* IAssetFile.Upload(filepath): Um problema foi corrigido em que este método de carregamento síncronos estava gerando o erro seguinte quando vários ficheiros foram carregados para o elemento. O erro foi "Falha ao autenticar o pedido de servidor. Certifique-se de que o valor de cabeçalho de autorização está formado corretamente, incluindo a assinatura."
* IAssetFile.UploadAsync: Um problema foi corrigido que limitou o simultâneo carregamento de ficheiros a cinco ficheiros.
* IAssetFile.UploadProgressChanged: Este evento é agora fornecido pelo SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Essa sobrecarga do método agora é fornecida.
* IAssetFile.DownloadAsync: Um problema foi corrigido que limitou o download simultâneo de ficheiros a cinco ficheiros.
* IAssetFile.Delete(): Um problema foi corrigido onde chamada delete pode gerar uma exceção se nenhum arquivo foi carregado para o IAssetFile.
* Tarefas: Um problema foi corrigido onde encadeamento um "MP4 a tarefa de fluxos suave" com uma "tarefa de proteção de PlayReady" ao utilizar um modelo de tarefa não tiver criado quaisquer tarefas de todo.
* EncryptionUtils.GetCertificateFromStore(): Esse método já não lança uma exceção de referência nula devido a uma falha em encontrar o certificado com base em problemas de configuração de certificado.

## <a id="november_changes_12"></a>Versão de Novembro de 2012
As alterações mencionadas nesta seção foram atualizações incluídas em Novembro de 2012 (versão 2.0.0.0) SDK. Estas alterações podem exigir qualquer código escrito para a pré-visualização de Junho de 2012 de versão do SDK para ser modificado ou reescrito.

* Elementos
  
    * IAsset.Create(assetName) é o *apenas* função de criação do recurso. IAsset.Create já não carrega ficheiros como parte da chamada do método. Utilize IAssetFile para carregar.
    * O método IAsset.Publish e o valor de enumeração de AssetState.Publish foram removidas do SDK de serviços. Qualquer código que conta com este valor tem de ser reescrito.
* FileInfo
  
    * Esta classe foi removida e substituída por IAssetFile.
  
* IAssetFiles
  
    * IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-lo, instancie o objeto de IAssetFiles, seguido de um carregamento de ficheiros através da utilização do SDK de Media Services ou o SDK de armazenamento. Podem ser utilizadas as sobrecargas de IAssetFile.Upload seguintes:
  
        * IAssetFile.Upload(filePath): Este método síncrono bloqueia o thread e recomendamos apenas quando carregar um único ficheiro.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Este método assíncrono é o mecanismo de carregamento preferido. 
    
            Erro conhecido: Se utilizar o token de cancelamento, o carregamento foi cancelado. As tarefas podem ter vários Estados de cancelamento. Corretamente tem de detetar e lidar com exceções.
* Localizadores
  
    * As versões específicas de origem foram removidas. O contexto específico do SAS. Locators.CreateSasLocator (asset, accessPolicy) serão marcados preteridas ou removidas pela disponibilidade geral. Consulte a secção "Localizadores" em "Nova funcionalidade" para o comportamento de atualizado.

## <a id="june_changes_12"></a>Versão de pré-visualização de Junho de 2012
A seguinte funcionalidade foi nova na versão de Novembro do SDK:

* A eliminar entidades
  
    * Objetos IAsset, IAssetFile, ILocator, IAccessPolicy e IContentKey agora são eliminados ao nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão da coleção, ou seja, cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    * Agora os localizadores devem ser criados usando o método CreateLocator. Terão de utilizar os valores de enum LocatorType.SAS ou LocatorType.OnDemandOrigin como um argumento para o tipo específico de localizador de que pretende criar.
    * Novas propriedades foram adicionadas para os localizadores para que seja mais fácil de obter os URIs utilizável para o seu conteúdo. Essa reformulação de localizadores fornece mais flexibilidade para futura extensibilidade de terceiros e aumenta a facilidade de utilização para aplicativos de clientes do suporte de dados.
* Suporte de método assíncrono
  
    * Foi adicionado suporte assíncrono para todos os métodos.

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum MSDN dos serviços de multimédia do Azure]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência da API de REST dos serviços de multimédia do Azure]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Detalhes de preços dos Serviços de Multimédia (Media Services pricing details)]: https://azure.microsoft.com/pricing/details/media-services/
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
[Lógica de repetição no SDK de serviços de multimédia para .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grama Valley anuncia 7 EDIUS transmissão em fluxo através da cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
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

