---
title: Azure Media Services divulga notas | Microsoft Docs
description: Este artigo fala sobre as notas de lançamento v2 da Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 019f0bc98de45fa1fe6f9b8c72ef74beb50ea2c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017363"
---
# <a name="azure-media-services-release-notes"></a>Notas de lançamento da Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Estas notas de lançamento para a Azure Media Services resumem as alterações das versões anteriores e dos problemas conhecidos.

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Queremos ouvir os nossos clientes para que possamos focar-nos na resolução de problemas que o afetam. Para reportar um problema ou fazer perguntas, envie um post no [Fórum MSDN dos Media Media Services]. 

## <a name="known-issues"></a><a name="issues"></a>Questões conhecidas
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Questões gerais dos Serviços de Comunicação Social

| Problema | Description |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na API REST. |Se desenvolver aplicações de Serviços de Mídia utilizando a API REST, descobre que alguns campos comuns de cabeçalho HTTP (incluindo O ID DO CLIENTE-PEDIDO, ID de PEDIDO e RETORNO-CLIENTE-PEDIDO-ID) não são suportados. Os cabeçalhos serão adicionados numa futura atualização. |
| Não é permitido codificar por cento. |Os Serviços de Comunicação Social utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Por esta razão, a codificação por cento não é permitida. O valor da propriedade Name não pode ter nenhum dos [seguintes caracteres reservados por cento](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'(;:@&=+$,/?%#[]". Além disso, só pode haver um "." para a extensão do nome do ficheiro. |
| O método ListBlobs que faz parte da versão 3.x do Azure Storage SDK falha. |Os Media Services geram URLs SAS com base na versão [2012-02-12.](/rest/api/storageservices/version-2012-02-12) Se pretender utilizar o SDK de armazenamento para listar as bolhas num recipiente de bolhas, utilize o método [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) que faz parte da versão 2.x do Storage SDK. |
| O mecanismo de estrangulamento dos Serviços de Comunicação Social restringe o uso de recursos para aplicações que fazem pedidos excessivos ao serviço. O serviço poderá devolver o código de estado "Serviço Indisponível" 503 HTTP. |Para mais informações, consulte a descrição do código de estado 503 HTTP nos [códigos de erro dos Serviços de Comunicação Social.](media-services-encoding-error-codes.md) |
| Quando se consulta as entidades, é devolvido um limite de 1.000 entidades de uma só vez porque a versão 2 do REST do público limita os resultados da consulta a 1.000 resultados. |Utilize Skip and Take (.NET)/top (REST) conforme descrito [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [este exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem encontrar um problema de marcação repetida no manifesto de Streaming Suave. |Para mais informações, consulte [esta secção.](media-services-deliver-content-overview.md#known-issues) |
| Os objetos Net SDK não podem ser serializados e, como resultado, não funcionam com a Azure Cache para redis. |Se tentar serializar o objeto SDK AssetCollection para o adicionar ao Azure Cache para Redis, é lançada uma exceção. |
|A API REST responde com uma mensagem de erro a dizer "O filtro não pode ser acedido por esta versão do REST Api" ao tentar obter um filtro de nível de Ativo ou Conta.|O filtro foi criado ou modificado com uma versão API mais recente do que está a ser usado para tentar obter o filtro. Isto pode acontecer se duas versões API estiverem a ser utilizadas por código ou ferramentas que estão a ser utilizadas pelo cliente.  A melhor solução aqui é atualizar o código ou ferramentas para usar as versões mais recentes ou as duas versões API.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Rest API versão história
Para obter informações sobre o histórico da versão API dos Serviços de Mídia REST, consulte a referência API dos [Serviços de Mídia Azure].

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API e SDKs anúncio de depreciação

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Atualize os seus Serviços de Media Azure REST API e SDKs para v3 até 29 de fevereiro de 2024

Como a versão 3 do Azure Media Services REST API e os Clientes SDKs para .NET e Java oferecem mais capacidades do que a versão 2, estamos a retirar a versão 2 do Azure Media Services REST API e os SDKs clientes para .NET e Java. Encorajamo-lo a fazer a troca mais cedo para obter os benefícios mais ricos da versão 3 da Azure Media Services REST API e dos Clientes SDKs para .NET e Java.
A versão 3 fornece:
 
- Suporte ao evento ao vivo 24x7
- ARM REST APIs, SDKs cliente para .NET core, Node.js, Python, Java, Go e Ruby.
- Chaves geridas pelo cliente, integração de armazenamento confiável, suporte de ligação privada, e [muito mais](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Ação necessária:

Para minimizar a perturbação das suas cargas de trabalho, reveja o guia de [migração](https://go.microsoft.com/fwlink/?linkid=2149150&clcid=0x409) para transitar o seu código da versão 2 API e SDKs para a versão 3 API e SDK antes de 29 de fevereiro de 2024.
**Depois de 29 de fevereiro de 2024,** a Azure Media Services deixará de aceitar o tráfego na versão 2 REST API, na versão API de gestão de conta ARM 2015-10-01, ou na versão 2 .NET client SDKs. Isto inclui qualquer cliente de código aberto de 3ª parte SDKS que pode ligar para a versão 2 API.  

Veja o anúncio oficial do [Azure Updates](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>Setembro de 2020

As seguintes propriedades v2 deixarão de ser preenchidas com dados históricos de progresso do trabalho:

* [Eventos Históricos](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Para obter o histórico de tarefas, deve utilizar as notificações de emprego v2 através de webhooks ou mensagens de fila utilizando Pontos Finais de Notificação. Para obter mais informações, consulte:

* [Use o armazenamento da Azure Queue para monitorizar as notificações de emprego dos Media Services](media-services-dotnet-check-job-progress-with-queues.md)
* [Use webhooks Azure para monitorizar notificações de emprego dos Media Services](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Fevereiro de 2020

Alguns processadores de mídia analíticos serão reformados. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

## <a name="september-2019"></a>Setembro de 2019

### <a name="deprecation-of-media-processors"></a>Depreciação dos processadores de meios de comunicação

Estamos a anunciar a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. [O Azure Media Services Video Indexer](../video-indexer/index.yml) substitui estes processadores de mídia legado.

Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md)

Consulte também [a Migração do Azure Media Indexer e do Azure Media Indexer 2 para o Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

### <a name="deprecation-of-media-processors"></a>Depreciação dos processadores de meios de comunicação

Estamos a anunciar a depreciação dos processadores de mídia *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md)

Para mais detalhes, consulte [a Migração WAME para Media Encoder Standard](./migrate-windows-azure-media-encoder.md) e [Migrar AME para Media Encoder Standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>Março de 2019

A funcionalidade de pré-visualização de Hiperlapse media da Azure Media Services foi depreciada.

## <a name="december-2018"></a>Dezembro de 2018

Em breve, a funcionalidade de Pré-visualização de Hiperlapse media da Azure Media Services será retirada. A partir de 19 de dezembro de 2018, os Serviços de Media deixarão de fazer alterações ou melhorias no Hyperlapse dos Media. No dia 29 de março de 2019, será reformado e deixará de estar disponível.

## <a name="october-2018"></a>Outubro de 2018

### <a name="cmaf-support"></a>Suporte cmaf

CMAF e suporte de encriptação 'cbcs' para os jogadores Apple HLS (iOS 11+) e MPEG-DASH que suportam a CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprites de miniatura Web VTT

Agora pode utilizar os Serviços de Mídia para gerar sprites de miniatura Web VTT usando as nossas APIs v2. Para obter mais informações, consulte [Gerar um sprite de miniatura.](generate-thumbnail-sprite.md)

## <a name="july-2018"></a>Julho de 2018

Com a mais recente versão do serviço, existem pequenas alterações de formatação nas mensagens de erro devolvidas pelo serviço quando um Trabalho falha, no que diz respeito à forma como é dividido em duas ou mais linhas.

## <a name="may-2018"></a>Maio de 2018 

A partir de 12 de maio de 2018, os canais ao vivo deixarão de suportar o protocolo de transmissão rtp/MPEG-2. Por favor, migrar de PROTOCOLOs de ingestão RTP/MPEG-2 para RTMP ou MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Versão de outubro de 2017
> [!IMPORTANT] 
> Os Serviços de Comunicação Social estão a depreciar o suporte para as chaves de autenticação do Serviço de Controlo de Acesso Azure. No dia 22 de junho de 2018, já não é possível autenticar com os Serviços de Comunicação Social por código, utilizando as teclas do Serviço de Controlo de Acesso. Tem de atualizar o seu código para utilizar o Azure Ative Directory (Azure AD) por [autenticação baseada em Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Atenção a avisos sobre esta mudança no portal Azure.

### <a name="updates-for-october-2017"></a>Atualizações para outubro de 2017
#### <a name="sdks"></a>SDKs
* O .NET SDK foi atualizado para suportar a autenticação Azure AD. O suporte à autenticação do Serviço de Controlo de Acesso foi removido da mais recente .NET SDK em Nuget.org para incentivar uma migração mais rápida para a Azure AD. 
* O JAVA SDK foi atualizado para apoiar a autenticação Azure AD. O suporte à autenticação AD AZure foi adicionado ao Java SDK. Para obter informações sobre como utilizar o Java SDK com media services, consulte [Começar com o cliente Java SDK para Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificação baseada em ficheiros
* Agora pode utilizar o Codificador Premium para codificar o seu conteúdo para o código de codificação de vídeo de alta eficiência H.265 (HEVC). Não há impacto nos preços se escolher H.265 em vez de outros códigos, como H.264. Para obter informações sobre licenças de patentes HEVC, consulte [os Termos dos Serviços Online.](https://azure.microsoft.com/support/legal/)
* Para vídeos de origem codificados com o código de vídeo H.265 (HEVC), como o vídeo capturado através do iOS11 ou GoPro Hero 6, pode agora utilizar o Codificador Premium ou o Standard Encoder para codificar esses vídeos. Para obter informações sobre licenças de patentes, consulte [os Termos dos Serviços Online.](https://azure.microsoft.com/support/legal/)
* Para conteúdos que contenham múltiplas faixas de áudio de idioma, os valores linguísticos devem ser corretamente rotulados de acordo com a especificação correspondente do formato de ficheiro (por exemplo, ISO MP4). Em seguida, pode utilizar o Standard Encoder para codificar o conteúdo para streaming. O localizador de streaming resultante lista as línguas de áudio disponíveis.
* O Standard Encoder suporta agora duas novas predefinições do sistema apenas áudio, "AAC Audio" e "AAC Good Quality Audio". Ambos produzem uma produção de codificação de áudio estéreo avançada (AAC), a taxas de bit de 128 Kbps e 192 Kbps, respectivamente.
* O Premium Encoder suporta agora os formatos de ficheiro QuickTime/MOV como entrada. O código de vídeo deve ser um dos [tipos apple ProRes listados neste artigo do GitHub](./media-services-media-encoder-standard-formats.md). O áudio deve ser modulação do código de pulso AAC ou de pulso (PCM). O Premium Encoder não suporta, por exemplo, o vídeo DVC/DVCPro embrulhado em ficheiros QuickTime/MOV como entrada. O Standard Encoder suporta estes códigos de vídeo.
* As seguintes correções de erros foram feitas em codificadores:

    * Pode agora submeter empregos utilizando um ativo de entrada. Após o fim destes trabalhos, pode modificar o ativo (por exemplo, adicionar, excluir ou renomear ficheiros dentro do ativo) e submeter empregos adicionais.
    * A qualidade das miniaturas JPEG produzidas pelo Standard Encoder é melhorada.
    * O Standard Encoder lida melhor com metadados de entrada e geração de miniaturas em vídeos de muito curta duração.
    * Melhorias no descodificador H.264 usado no Codificadores Padrão eliminam certos artefactos raros. 

#### <a name="media-analytics"></a>Análise de Multimédia
Disponibilidade geral do Azure Media Redator: Este processador de media realiza anonimização desfocando os rostos de indivíduos selecionados e é ideal para ser utilizado em cenários de segurança pública e media de notícias. 

Para uma visão geral deste novo processador, consulte [este post de blog](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter informações sobre documentação e configurações, consulte [as faces do Redact com a Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Lançamento de junho de 2017

Os Serviços de Comunicação Social suportam agora [a autenticação baseada em Azure.](media-services-use-aad-auth-to-access-ams-api.md)

> [!IMPORTANT]
> Atualmente, os Serviços de Comunicação suportam o modelo de autenticação do Serviço de Controlo de Acesso. A autorização do Serviço de Controlo de Acesso será depreendida no dia 1 de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="march-2017-release"></a>Lançamento de março de 2017

Agora pode utilizar o Standard Encoder para [gerar automaticamente uma escada de bitrate](media-services-autogen-bitrate-ladder-with-mes.md) especificando a cadeia predefinida "Adaptive Streaming" quando criar uma tarefa de codificação. Para codificar um vídeo para streaming com serviços de mídia, utilize a predefinição "Adaptive Streaming". Para personalizar uma predefinição codificante para o seu cenário específico, pode começar com [estas predefinições](media-services-mes-presets-overview.md).

Agora pode utilizar o Media Encoder Standard ou o Media Encoder Premium Workflow para [criar uma tarefa de codificação que gere pedaços de fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Lançamento de fevereiro de 2017

A partir de 1 de abril de 2017, qualquer registo de emprego na sua conta com mais de 90 dias é automaticamente eliminado, juntamente com os seus registos de tarefas associados. A supressão ocorre mesmo que o número total de registos seja inferior ao contingente máximo. Para arquivar as informações de trabalho/tarefa, pode utilizar o código descrito em [Gerir ativos e entidades relacionadas com os Serviços de Comunicação Social .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Lançamento de janeiro de 2017

Nos Serviços de Media, um ponto final de streaming representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de cliente ou a uma rede de entrega de conteúdos (CDN) para posterior distribuição. Os Serviços de Mídia também proporcionam uma integração perfeita da Rede de Entrega de Conteúdos Azure. O stream de saída de um serviço StreamingEndpoint pode ser um stream ao vivo, um vídeo a pedido ou um download progressivo do seu ativo na sua conta de Media Services. Cada conta de Serviços de Mídia inclui um ponto final de streaming padrão. Os pontos finais de streaming adicionais podem ser criados na conta. 

Existem duas versões de streaming de pontos finais, 1.0 e 2.0. A partir de 10 de janeiro de 2017, quaisquer contas de Media Services recém-criadas incluem o ponto final de streaming padrão da versão 2.0. Os pontos finais de streaming adicionais que adiciona a esta conta também são a versão 2.0. Esta alteração não afeta as contas existentes. Os pontos finais de streaming existentes são a versão 1.0 e podem ser atualizados para a versão 2.0. Há comportamentos, faturações e mudanças de funcionalidade com esta mudança. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

Começando pela versão 2.15, os Media Services adicionaram as seguintes propriedades à entidade de ponta de streaming:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Para obter mais informações sobre estas propriedades, consulte [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Lançamento de dezembro de 2016

 Agora pode utilizar os Serviços de Comunicação Social para aceder a dados de telemetria/métricas para os seus serviços. Pode utilizar a versão atual dos Media Services para recolher dados de telemetria para canais ao vivo, streaming de endpoint e entidades de arquivo. Para mais informações, consulte [a telemetria dos Serviços de Comunicação Social.](media-services-telemetry-overview.md)

## <a name="july-2016-release"></a><a name="july_changes16"></a>Lançamento de julho de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações para o ficheiro manifesto (*. ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação é submetida à Media Encoder Standard ou Media Encoder Premium, a tarefa de codificação gera um [ficheiro manifesto de streaming](media-services-deliver-content-overview.md) (*.ism) no ativo de saída. Com a mais recente versão do serviço, a sintaxe deste ficheiro manifesto de streaming foi atualizada.

> [!NOTE]
> A sintaxe do ficheiro manifesto de streaming (.ism) está reservada para uso interno. Está sujeito a mudanças em futuras libertações. Não modifique nem manipule o conteúdo deste ficheiro.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. O ficheiro ISMC) é gerado no ativo de saída quando uma tarefa de codificação produz um ou mais ficheiros MP4
Começando com a mais recente versão do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais ficheiros MP4, o ativo de saída também contém um ficheiro de manifesto de cliente de streaming (*.ismc). O ficheiro .ismc ajuda a melhorar o desempenho do streaming dinâmico. 

> [!NOTE]
> A sintaxe do ficheiro manifesto cliente (.ismc) está reservada para uso interno. Está sujeito a mudanças em futuras libertações. Não modifique nem manipule o conteúdo deste ficheiro.
> 
> 

Para mais informações, consulte [este blog.](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file)

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem encontrar um problema de marcação repetida no manifesto de Streaming Suave. Para mais informações, consulte [esta secção.](media-services-deliver-content-overview.md#known-issues)

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Lançamento de abril de 2016
### <a name="media-analytics"></a>Análise de Multimédia
 Os Media Services introduziram a Media Analytics para uma poderosa inteligência de vídeo. Para mais informações, consulte a [visão geral da Media Services Analytics](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (pré-visualização)
Agora pode utilizar os Media Services para encriptar dinamicamente o seu conteúdo HTTP Live Streaming (HLS) com o Apple FairPlay. Também pode utilizar o serviço de entrega de licenças media Services para entregar licenças FairPlay aos clientes. Para obter mais informações, consulte "Use a Azure Media Services para transmitir o seu conteúdo HLS protegido com o Apple FairPlay."

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Lançamento de fevereiro de 2016
A versão mais recente do Media Services SDK para .NET (3.5.3) contém uma correção de bugs relacionada com o Google Widevine. Era impossível reutilizar a AssetDeliveryPolicy para múltiplos ativos encriptados com a Widevine. Como parte desta correção de bugs, a seguinte propriedade foi adicionada ao SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Lançamento de janeiro de 2016
As unidades reservadas da codificação foram renomeadas para reduzir a confusão com nomes codificantes.

As unidades reservadas de codificação Básica, Standard e Premium foram renomeadas para unidades reservadas S1, S2 e S3, respectivamente. Os clientes que utilizam as unidades reservadas de codificação Básica hoje vêem o S1 como o rótulo no portal Azure (e na fatura). Os clientes que utilizam Standard e Premium vêem as etiquetas S2 e S3, respectivamente. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Lançamento de dezembro de 2015

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder anúncio de depreciação

 A Media Encoder será depreciada a partir de aproximadamente 12 meses a partir do lançamento da Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK for PHP (Azure SDK para PHP)
A equipa da Azure SDK publicou um novo lançamento do pacote [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) que contém atualizações e novas funcionalidades para os Media Services. Em particular, o Media Services SDK para PHP suporta agora as mais recentes funcionalidades [de proteção de conteúdos.](media-services-content-protection-overview.md) Estas funcionalidades são encriptação dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrições simbólicas. Também suporta unidades de [codificação](media-services-dotnet-encoding-units.md)de escala.

Para obter mais informações, consulte:

* As [seguintes amostras](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) de código ajudam-no a começar rapidamente:
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica AES-128 e o serviço de entrega de chaves. Baseia-se na amostra .NET explicada na [encriptação dinâmica Use AES-128 e no serviço de entrega de chaves](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Este ficheiro PHP mostra como utilizar a encriptação dinâmica PlayReady e o serviço de entrega de licenças. Baseia-se na amostra .NET explicada no [Use PlayReady e/ou widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Este ficheiro PHP mostra como escalar as unidades reservadas de codificação.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Lançamento de novembro de 2015
 Os Serviços de Comunicação Social oferecem agora o serviço de entrega de licenças Widevine na nuvem. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) Além disso, consulte [este tutorial](media-services-protect-with-playready-widevine.md) e o [repositório gitHub.](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 

Os serviços de entrega de licenças widevine fornecidos pela Media Services estão em pré-visualização. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Lançamento de outubro de 2015
Os Media Services estão agora em direto nos seguintes centros de dados: Brasil Sul, Índia Ocidental, Índia Sul e Índia Central. Pode agora utilizar o portal Azure para [criar contas do Media Service](media-services-portal-create-account.md) e executar várias tarefas descritas na página [de documentação dos Serviços de Comunicação](https://azure.microsoft.com/documentation/services/media-services/)Social. A Codificação ao Vivo não está ativada nestes centros de dados. Além disso, nem todos os tipos de unidades reservadas de codificação estão disponíveis nestes centros de dados.

* Brasil Sul: Estão disponíveis unidades reservadas standard e basic.
* Índia Oeste, Índia Sul e Índia Central: Apenas unidades reservadas de codificação básica estão disponíveis.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Lançamento de setembro de 2015
Os Media Services oferecem agora a capacidade de proteger tanto o vídeo a pedido como os streams ao vivo com tecnologia drm modular Widevine. Pode utilizar os seguintes parceiros de serviços de entrega para ajudá-lo a entregar licenças widevine:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)
  
Pode utilizar o [SDK .NET dos Serviços de Multimédia](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar o AssetDeliveryConfiguration para utilizar o Widevine. 
* Os Media Services adicionaram suporte aos vídeos do Apple ProRes. Agora pode carregar os seus ficheiros de vídeos de origem QuickTime que utilizam o Apple ProRes ou outros codecs. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)
* Agora pode utilizar o Media Encoder Standard para fazer a extração de arquivos subcrípder e vivo. Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)
* Foram efetuadas as seguintes atualizações de filtragem: 
  
  * Agora pode utilizar o formato Apple HLS com um filtro apenas de áudio. Pode utilizar esta atualização para remover uma faixa apenas de áudio especificando (apenas áudio=falso) no URL.
  * Quando define filtros para os seus ativos, pode agora combinar vários (até três) filtros num único URL.
    
    Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)
* Os Serviços de Comunicação Social suportam agora quadros I na versão 4 do HLS. O suporte i-frame otimiza operações de avanço rápido e rebobinar. Por predefinição, todas as saídas da versão HLS 4 incluem a lista de reprodução de quadro I (EXT-X-I-FRAME-STREAM-INF).
Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Lançamento de agosto de 2015
* O Media Services SDK para a versão Java 0.8.0 e novas amostras estão agora disponíveis. Para obter mais informações, consulte:
    
* O Azure Media Player foi atualizado com suporte de fluxo multi-áudio. Para mais informações, consulte [este post de blog.](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Lançamento de julho de 2015
* Foi anunciada a disponibilidade geral da Media Encoder Standard. Para mais informações, consulte [este post de blog.](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
  
    A Media Encoder Standard utiliza predefinições, conforme descrito [nesta secção](./media-services-mes-presets-overview.md). Quando utilizar uma pré-sintonia para codificações 4K, obtenha o tipo de unidade reservada Premium. Para obter mais informações, consulte [a codificação da Escala.](media-services-scale-media-processing-overview.md)
* As legendas em tempo real foram usadas com os Media Services e o Media Player. Para mais informações, consulte [este post de blog.](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Atualizações de Media Services .NET SDK
O Media Services .NET SDK é agora a versão 3.4.0.0. Foram efetuadas as seguintes atualizações: 

* O apoio foi implementado para o arquivo ao vivo. Não é possível descarregar um ativo que contenha um arquivo ao vivo.
* O suporte foi implementado para filtros dinâmicos.
* A funcionalidade foi implementada para que os utilizadores possam manter um recipiente de armazenamento enquanto apagam um ativo.
* As correções de bugs foram feitas relacionadas com políticas de relívia nos canais.
* O fluxo de trabalho premium da Media Encoder foi ativado.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Lançamento de junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações de Media Services .NET SDK
O Media Services .NET SDK é agora a versão 3.3.0.0. Foram efetuadas as seguintes atualizações: 

* O suporte foi adicionado para a especificação de descoberta OpenId Connect.
* Foi adicionado suporte para manusear a capotamento das chaves do lado do fornecedor de identidade.

Se utilizar um fornecedor de identidade que exponha um documento de descoberta OpenID Connect (como a Azure AD, Google e Salesforce fazem), pode instruir os Serviços de Comunicação Social a obter chaves de assinatura para validação de JSON Web Tokens (JWTs) a partir da especificação de descoberta OpenID Connect. 

Para obter mais informações, consulte [as teclas web JSON a partir da especificação de descoberta OpenID Connect para trabalhar com a autenticação JWT nos Serviços de Mídia](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Lançamento de maio de 2015
Foram anunciadas as seguintes novas funcionalidades:

* [Uma pré-estreia da codificação ao vivo com os Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Lançamento de abril de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços Gerais de Mídia
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) foi anunciado.
* A partir do Media Services REST 2.10, os canais configurados para ingerir um protocolo de mensagens Real-Time (RTMP) são criados com URLs de ingestão primária e secundária. Para obter mais informações, consulte [as configurações de ingestão de canais.](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* O Azure Media Indexer foi atualizado.
* Foi acrescentado o apoio à língua espanhola.
* Foi adicionada uma nova configuração para o formato XML.

Para mais informações, consulte [este blog.](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)

### <a name="media-services-net-sdk-updates"></a>Atualizações de Media Services .NET SDK
O Media Services .NET SDK é agora a versão 3.2.0.0. Foram efetuadas as seguintes atualizações:

* Breaking change: TokenRestrictionTemplate.Issuer and TokenRestrictionTemplate.Audience foram alterados para ser de um tipo de corda.
* Foram feitas atualizações relacionadas com a criação de políticas de relíndi personalizadas.
* As correções de bugs foram feitas relacionadas com o upload e descarregamento de ficheiros.
* A classe MediaServicesCredentials aceita agora pontos finais de controlo de acesso primário e secundário para autenticar contra.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Lançamento de março de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços Gerais de Mídia
* Os Serviços de Mídia proporcionam agora a integração da Rede de Entrega de Conteúdos. Para apoiar a integração, a propriedade CdnEnabled foi adicionada ao StreamingEndpoint. O CdnEnabled pode ser utilizado com APIs REST a começar pela versão 2.9. Para mais informações, consulte [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). O CdnEnabled pode ser utilizado com o .NET SDK a começar pela versão 3.1.0.2. Para mais informações, consulte [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* Foi anunciado o fluxo de trabalho premium da Media Encoder. Para mais informações, consulte [a introdução da codificação Premium nos Serviços de Meios de Comunicação Social Azure.](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Lançamento de fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços Gerais de Mídia
A API dos Serviços de Media REST é agora a versão 2.9. A partir desta versão, pode ativar a integração da Rede de Entrega de Conteúdos com os pontos finais de streaming. Para mais informações, consulte [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Lançamento de janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações dos Serviços Gerais de Mídia
Foi anunciada a disponibilidade geral de proteção de conteúdos com encriptação dinâmica. Para obter mais informações, consulte [os Serviços de Comunicação Social para melhorar a segurança do streaming com a disponibilidade geral de tecnologia DRM.](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)

### <a name="media-services-net-sdk-updates"></a>Atualizações de Media Services .NET SDK
O Media Services .NET SDK é agora a versão 3.1.0.1.

Esta versão marcou o padrão microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate construtor como obsoleto. O novo construtor toma TokenType como argumento.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Lançamento de dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações dos Serviços Gerais de Mídia
* Algumas atualizações e novas funcionalidades foram adicionadas ao Media Indexer. Para obter mais informações, consulte [as notas de lançamento do Azure Media Indexer 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Foi adicionada uma nova API REST que pode utilizar para atualizar as unidades reservadas de codificação. Para obter mais informações, consulte [EncodingReservedUnitType com REST](/rest/api/media/operations/encodingreservedunittype).
* O suporte CORS foi adicionado para o serviço de entrega chave.
* Foram introduzidas melhorias no desempenho para consultar as opções de política de autorização.
* No centro de dados da China, o [URL de entrega chave](/rest/api/media/operations/contentkey#get_delivery_service_url) é agora por cliente (assim como em outros centros de dados).
* A duração do alvo do HLS foi adicionada. Ao fazer streaming ao vivo, o HLS é sempre embalado dinamicamente. Por predefinição, os Serviços de Comunicação social calculam automaticamente a relação de embalagem do segmento HLS (FragmentsPerSegment) com base no intervalo do quadro-chave (KeyFrameInterval). Este método também é referido como um grupo de imagens (GOP) que é recebido do codificar ao vivo. Para mais informações, consulte [Work with Media Services em direto.](/previous-versions/azure/dn783466(v=azure.100))

### <a name="media-services-net-sdk-updates"></a>Atualizações de Media Services .NET SDK
O [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) é agora a versão 3.1.0.0. Foram efetuadas as seguintes atualizações:

* A dependência .NET SDK foi atualizada para o Quadro .NET 4.5.
* Foi adicionada uma nova API que pode utilizar para atualizar as unidades reservadas de codificação. Para obter mais informações, consulte [Update reserved unit type e aumentar a codificação de unidades reservadas através da utilização .NET](media-services-dotnet-encoding-units.md).
* Foi adicionado o suporte jWT para a autenticação simbólica. Para obter mais informações, consulte [a autenticação simbólica JWT nos Serviços de Mídia e encriptação dinâmica.](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* Foram adicionadas compensações relativas para BeginDate e ExpirationDate no modelo de licença PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Lançamento de novembro de 2014
* Agora pode utilizar os Serviços de Comunicação para ingerir conteúdo sonoro de Streaming Suave (fMP4) ao longo de uma ligação TLS. Para ingerir sobre TLS, certifique-se de atualizar o URL de ingestão para HTTPS. Atualmente, os Media Services não suportam TLS com domínios personalizados. Para obter mais informações sobre o streaming ao vivo, consulte [Work with Azure Media Services Live Streaming](/previous-versions/azure/dn783466(v=azure.100)).
* Atualmente, não é possível ingerir um fluxo de transmissão rtmp ao vivo através de uma ligação TLS.
* Só pode transmitir através do TLS se o ponto final de streaming a partir do qual entrega o seu conteúdo foi criado após 10 de setembro de 2014. Se os urLs de streaming forem baseados nos pontos finais de streaming criados após 10 de setembro de 2014, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam TLS. Se o seu URL estiver no formato antigo e pretender transmitir através do TLS, [crie um novo ponto final de streaming](media-services-portal-manage-streaming-endpoints.md). Para transmitir o seu conteúdo através de TLS, utilize URLs com base no novo ponto final de streaming.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>SDK .NET dos Serviços de Multimédia
O SDK dos Serviços de Comunicação social para extensões .NET é agora a versão 2.0.0.3.

O SDK dos Serviços de Comunicação Social para .NET é agora a versão 3.0.0.8. Foram efetuadas as seguintes atualizações:

* A refacagem foi implementada em aulas de política de recandidologia.
* Uma cadeia de agente de utilizador foi adicionada aos cabeçalhos de pedido HTTP.
* Um passo de construção de restauro NuGet foi adicionado.
* Os testes de cenário foram fixados para utilizar o cert x509 do repositório.
* Foram adicionadas definições de validação para quando o canal e a atualização final de streaming.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório do GitHub para acolher amostras de Serviços de Comunicação Social
As amostras estão nas amostras do [repositório GitHub dos Serviços de Comunicação Social.](https://github.com/Azure/Azure-Media-Services-Samples)

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Lançamento de setembro de 2014
Os metadados REST dos Serviços de Comunicação Social são agora a versão 2.7. Para obter mais informações sobre as últimas atualizações do REST, consulte a [referência API dos Serviços de Mídia](/rest/api/media/operations/azure-media-services-rest-api-reference)REST .

O SDK dos Serviços de Comunicação Social para .NET é agora a versão 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Alterações interruptivas
* A origem foi renomeada para [StreamingEndpoint.]
* Foi feita uma alteração no comportamento predefinido quando utiliza o portal Azure para codificar e publicar ficheiros MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Novas funcionalidades/cenários que fazem parte da versão geral da disponibilidade
* Foi introduzido o processador de media Media Indexer. Para obter mais informações, consulte [os ficheiros de mídia Indexar com o Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* Pode utilizar a entidade [StreamingEndpoint] para adicionar nomes de domínio personalizado (anfitrião).
  
    Para utilizar um nome de domínio personalizado como nome de ponto final de streaming dos Media Services, adicione nomes de anfitriões personalizados ao seu ponto final de streaming. Utilize os Serviços de Mídia REST APIs ou o .NET SDK para adicionar nomes de anfitriões personalizados.
  
    As seguintes considerações são aplicáveis:
  
  * Deve ter a propriedade do nome de domínio personalizado.
  * A propriedade do nome de domínio deve ser validada pelos Media Services. Para validar o domínio, crie um CName que mapeia o domínio parental mediaServicesAccountId para verificar a zona de mediaservices-dns DNS.
  * Tem de criar outro CName que mapeie o nome de anfitrião personalizado (por exemplo, sports.contoso.com) para o nome de anfitrião do StreamingEndpoint dos Serviços de Media (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para mais informações, consulte a propriedade CustomHostNames no artigo [streamingEndpoint.](/rest/api/media/operations/streamingendpoint)

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Novas funcionalidades/cenários que fazem parte do lançamento da pré-visualização pública
* Pré-visualização em direto. Para mais informações, consulte [Work with Media Services em direto.](/previous-versions/azure/dn783466(v=azure.100))
* Serviço de entrega de chaves. Para obter mais informações, consulte [a encriptação dinâmica AES-128 e o serviço de entrega de chaves](/previous-versions/azure/dn783457(v=azure.100)).
* Encriptação dinâmica da AES. Para obter mais informações, consulte [a encriptação dinâmica AES-128 e o serviço de entrega de chaves](/previous-versions/azure/dn783457(v=azure.100)).
* Serviço de entrega de licença PlayReady. 
* Encriptação dinâmica PlayReady. 
* Modelo de licença PlayReady de Serviços de Mídia. Para obter mais informações, consulte a visão geral do [modelo de licença playReady dos Media Services].
* Transmita os ativos encriptados de armazenamento. Para obter mais informações, consulte [o conteúdo encriptado de armazenamento do Stream](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Lançamento de agosto de 2014
Quando codifica um ativo, um ativo de saída é produzido quando o trabalho de codificação está terminado. Até esta divulgação, a Media Services Encoder produziu metadados sobre os ativos de produção. A partir desta versão, o codificadora também produz metadados sobre os ativos de entrada. Para obter mais informações, consulte [metadados de entrada] e [metadados de saída.]

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Lançamento de julho de 2014
Foram feitas as seguintes correções de bug para o Azure Media Services Packager e o Encryptor:

* Quando um ativo de arquivo ao vivo é transmitido ao HLS, apenas o áudio reproduz- se: Este problema foi corrigido, e agora tanto o áudio como o vídeo podem ser reproduzidos.
* Quando um ativo é embalado para encriptação de envelopes HLS e AES de 128 bits, os streams embalados não reproduzem-se em dispositivos Android: Este bug foi corrigido e o fluxo embalado reproduz-se em dispositivos Android que suportam o HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Lançamento de maio de 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Atualizações dos Serviços Gerais de Mídia
Agora pode utilizar [embalagens dinâmicas] para transmitir a versão 3 do HLS. Para transmitir a versão HLS 3, adicione o seguinte formato ao caminho do localizador de origem: * .ism/manifest (formato=m3u8-aapl-v3). Para mais informações, consulte [este fórum.](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)

A embalagem dinâmica agora também suporta a entrega de HLS (versão 3 e versão 4) encriptada com PlayReady baseado em Smooth Streaming estáticamente encriptado com PlayReady. Para obter informações sobre como encriptar o Smooth Streaming com o PlayReady, consulte [Protect Smooth Streaming com PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Atualizações de Media Services .NET SDK
O Media Services .NET SDK é agora a versão 3.0.0.5. Foram efetuadas as seguintes atualizações:

* Velocidade e resiliência são melhores quando você carrega e descarrega ativos de mídia.
* Foram introduzidas melhorias na lógica de retenção e no tratamento transitório de exceções: 
  
  * A lógica de deteção e reorientação de erros transitórios foram melhoradas para exceções que são causadas quando consulta, guarda alterações e carrega ou descarrega ficheiros. 
  * Quando obtém exceções na Web (por exemplo, durante um pedido de token do Serviço de Acesso), os erros fatais falham mais rapidamente agora.

Para obter mais informações, consulte [a lógica Retry no SDK dos Serviços de Comunicação Social para .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Lançamentos janeiro/fevereiro de 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Serviços de Mídia .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3
As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Foram corrigidas questões relacionadas com o uso de consultas linq com declarações OrderBy.
* As soluções de teste no [GitHub] foram divididas em testes baseados em unidades e testes baseados em cenários.

Para obter mais informações sobre as alterações, consulte os [serviços de comunicação social .NET SDK 3.0.0.1 e 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

As seguintes alterações foram efetuadas na versão 3.0.0.3:

* As dependências de armazenamento Azure foram atualizadas para utilizar a versão 3.0.3.0.
* Um problema de retrocompatibilidade foi fixado para 3.0. *.* liberta.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Lançamento de dezembro de 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Serviços de Mídia .NET SDK 3.0.0.0.0
> [!NOTE]
> Os desbloqueios de 3.0.x.x não são compatíveis com lançamentos de 2,4.x.x.
> 
> 

A versão mais recente do Media Services SDK é agora 3.0.0.0. Você pode baixar o mais recente pacote do NuGet ou obter as partes do [GitHub].

A partir da versão 3.0.0.0 do Serviço de Controlo de Acesso AD do Media Services, pode reutilizar os tokens do [Serviço de Controlo de Acesso AD Azure.](/previous-versions/azure/azure-services/hh147631(v=azure.100)) Para mais informações, consulte a secção "Reutilar fichas do Serviço de Controlo de Acesso" em [Connect to Media Services com o Media Services SDK for .NET](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Serviços de Mídia .NET Extensões SDK 2.0.0.0
 As extensões De Serviços de Mídia .NET SDK são um conjunto de métodos de extensão e funções auxiliares que simplificam o seu código e facilitam o desenvolvimento com os Media Services. Pode obter as últimas bits das [extensões Net SDK dos Media Services .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Lançamento de novembro de 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Alterações nos Serviços de Mídia .NET SDK
A partir desta versão, o Media Services SDK para .NET lida com erros transitórios de falha que podem ocorrer quando as chamadas são feitas para a camada API dos Serviços de Mídia REST.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Lançamento de agosto de 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Cmdlets PowerShell dos Serviços de Comunicação Social incluídos em ferramentas Azure SDK
Os seguintes cmdlets PowerShell dos Serviços de Comunicação Social estão agora incluídos em [ferramentas Azure SDK:](https://github.com/Azure/azure-sdk-tools)

* Get-AzureMediaServices 

    Por exemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Lançamento de junho de 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Alterações nos Serviços de Comunicação Social
As seguintes alterações mencionadas nesta secção são as atualizações incluídas nas versões dos Serviços de Comunicação de junho de 2013:

* Capacidade de ligar várias contas de armazenamento a uma conta de Media Service. 
    * StorageAccount
    * Asset.StorageAccountName e Asset.StorageAccount
* Capacidade de atualizar Job.Priority. 
* Entidades e propriedades relacionadas com a notificação: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Tarefa
* Ativo.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Alterações nos Serviços de Mídia .NET SDK
As seguintes alterações estão incluídas nas versões SDK dos Media Services de junho de 2013. Os mais recentes Media Services SDK estão disponíveis no GitHub.

* A partir da versão 2.3.0.0, o Media Services SDK suporta a ligação de várias contas de armazenamento a uma conta de Media Services. As seguintes APIs suportam esta característica:
  
    * IStorageAccount tipo
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Propriedade StorageAccount
    * ArmazenamentoSapoma propriedade
  
      Para obter mais informações, consulte [os ativos do Manage Media Services em várias contas de armazenamento.](/previous-versions/azure/dn271889(v=azure.100))
* APIs relacionados com notificação. A partir da versão 2.2.0.0,0, pode ouvir as notificações de armazenamento da Azure Queue. Para mais informações, consulte [as notificações de trabalho do Handle Media Services](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Dependência do cliente de armazenamento SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dependência do OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Lançamento de dezembro de 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Alterações nos Serviços de Mídia .NET SDK
* IntelliSense: A documentação intelliSense desaparecida foi adicionada para muitos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: Foi corrigido um problema em que o SDK ainda tinha uma dependência de uma versão antiga desta assembléia. O SDK refere-se agora à versão 5.1.1209.1 deste conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count: Esta contagem é agora reportada corretamente em novas interfaces IAsset depois de todos os localizadores serem eliminados.
* IAssetFile.ContentFileSize: Este valor está agora corretamente definido após um upload por IAssetFile.Upload (filepath).
* IAssetFile.ContentFileSize: Esta propriedade pode agora ser definida quando criar um ficheiro de ativos. Foi lido apenas anteriormente.
* IAssetFile.Upload (filepath): Foi corrigido um problema em que este método de upload sincronizado estava a lançar o seguinte erro quando vários ficheiros foram enviados para o ativo. O erro foi "O Servidor não autuou o pedido. Certifique-se de que o valor do cabeçalho de autorização é formado corretamente, incluindo a assinatura."
* IAssetFile.UploadAsync: Foi corrigido um problema que limitou o carregamento simultâneo de ficheiros a cinco ficheiros.
* IAssetFile.UploadProgressChanged: Este evento é agora fornecido pelo SDK.
* IAssetFile.DownloadAsync (string, BlobTransferClient, ILocator, CancelToken): Esta sobrecarga de método é agora fornecida.
* IAssetFile.DownloadAsync: Foi corrigido um problema que limitou o download simultâneo de ficheiros a cinco ficheiros.
* IAssetFile.Delete(): Uma questão foi corrigida quando a chamada delete pode lançar uma exceção se nenhum ficheiro foi carregado para o IAssetFile.
* Empregos: Foi corrigido um problema em que acorrentar uma "tarefa MP4 a Smooth Streams" com uma "Tarefa de Proteção PlayReady" utilizando um modelo de trabalho não criou nenhuma tarefa.
* EncryptionUtils.GetCertificateFromStore(): Este método já não lança uma exceção de referência nula devido a uma falha na descoberta do certificado com base em problemas de configuração de certificados.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Lançamento de novembro de 2012
As alterações mencionadas nesta secção foram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Estas alterações podem exigir que qualquer código escrito para a versão SDK de pré-visualização de junho de 2012 seja modificado ou reescrito.

* Elementos
  
    * IAsset.Create(nome de ativos) é a *única* função de criação de ativos. IAsset.Create já não faz upload de ficheiros como parte da chamada de método. Utilize o IAssetFile para o upload.
    * O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do Serviço SDK. Qualquer código que dependa deste valor deve ser reescrito.
* FileInfo
  
    * Esta aula foi removida e substituída pela IAssetFile.
  
* IAssetFiles
  
    * O IAssetFile substitui o FileInfo e tem um comportamento diferente. Para usá-lo, instantânea o objeto IAssetFiles, seguido de um upload de ficheiros, quer utilizando o SDK dos Serviços de Comunicação Social ou o SDK de armazenamento. Podem ser utilizadas as seguintes sobrecargas IAssetFile.Upload:
  
        * IAssetFile.Upload (filePath): Este método sincronizado bloqueia o fio, e recomendamos-o apenas quando carrega um único ficheiro.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, localizador, cancelamentoToken): Este método assíncronal é o mecanismo de upload preferido. 
    
            Bug conhecido: Se utilizar o token de cancelamento, o upload é cancelado. As tarefas podem ter muitos estados de cancelamento. Deve pegar e lidar com exceções.
* Localizadores
  
    * As versões específicas da origem foram removidas. O contexto específico da SAS. Localizadores.CreateSasLocator (ativo, accessPolicy) será marcado depreciado ou removido por disponibilidade geral. Consulte a secção "Localizadores" em "Nova funcionalidade" para obter um comportamento atualizado.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Lançamento de pré-visualização de junho de 2012
A seguinte funcionalidade foi nova no lançamento de novembro do SDK:

* Exclusão de entidades
  
    * Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy e IContentKey são agora eliminados ao nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma eliminação na Coleção, isto é, cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    * Os localizadores devem agora ser criados utilizando o método CreateLocator. Devem utilizar os valores de LocalizadorType.SAS ou LocalizadorType.OnDemandOrigin enum como argumento para o tipo específico de localizador que pretende criar.
    * Foram adicionadas novas propriedades aos localizadores para facilitar a obtenção de URIs utilizáveis para o seu conteúdo. Esta remodelação de localizadores proporciona mais flexibilidade para futuras extensibilidades de terceiros e aumenta a facilidade de utilização para aplicações de clientes de media.
* Suporte ao método assíncronos
  
    * O apoio assíncronos foi adicionado a todos os métodos.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Referência Azure Media Services REST API]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadados de entrada]: ./media-services-input-metadata-schema.md
[Metadados de saída]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Visão geral do modelo de licença playReady dos serviços de mídia]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Empacotamento dinâmico]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Redatória lógica no SDK dos Serviços de Media para .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)