---
title: Migrar dos serviços de mídia do Azure v2 para v3 | Microsoft Docs
description: Este artigo descreve as alterações que foram introduzidas nos serviços de mídia do Azure v3 e mostra as diferenças entre duas versões. O artigo também fornece diretrizes de migração para a mudança dos serviços de mídia v2 para v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 901542e2a69d2c7880825d76c1d69d3795713ed2
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231168"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Diretrizes de migração para mudar dos serviços de mídia v2 para v3

Este artigo descreve as alterações que foram introduzidas nos serviços de mídia do Azure v3, mostra as diferenças entre duas versões e fornece as diretrizes de migração.

Se você tiver um serviço de vídeo desenvolvido hoje na parte superior das [APIs do Legacy Media Services v2](../previous/media-services-overview.md), analise as diretrizes e considerações a seguir antes de migrar para as APIs v3. Há muitos benefícios e novos recursos na API v3 que melhoram a experiência do desenvolvedor e os recursos dos serviços de mídia. No entanto, como foi chamado na seção [problemas conhecidos](#known-issues) deste artigo, também há algumas limitações devido a alterações entre as versões da API. Essa página será mantida à medida que a equipe de serviços de mídia fizer melhorias contínuas nas APIs v3 e solucionar as lacunas entre as versões. 

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="benefits-of-media-services-v3"></a>Benefícios dos serviços de mídia v3
  
### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. Azure Resource Manager modelos podem ser usados para criar e implantar transformações, pontos de extremidade de streaming, eventos ao vivo e muito mais.
* Documento [de especificação openapi (anteriormente chamado de Swagger)](https://aka.ms/ams-v3-rest-sdk) .
    Expõe o esquema para todos os componentes de serviço, incluindo a codificação baseada em arquivo.
* SDKs disponíveis para [.net](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [node. js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [go](https://aka.ms/ams-v3-go-ref)e Ruby.
* [CLI do Azure](https://aka.ms/ams-v3-cli-ref) integração para suporte a scripts simples.

### <a name="new-features"></a>Novos recursos

* Para o processamento de trabalho baseado em arquivo, você pode usar uma URL HTTP (S) como a entrada.<br/>Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar ativos.
* Apresenta o conceito de [](transforms-jobs-concept.md) transformações para processamento de trabalho baseado em arquivo. Uma transformação pode ser usada para compilar configurações reutilizáveis, criar Azure Resource Manager modelos e isolar configurações de processamento entre vários clientes ou locatários.
* Um ativo pode ter vários [localizadores de streaming](streaming-locators-concept.md) , cada um com diferentes configurações de criptografia dinâmica e de [empacotamento dinâmico](dynamic-packaging-overview.md) .
* A [proteção de conteúdo](content-key-policy-concept.md) dá suporte a recursos de várias chaves.
* Você pode transmitir eventos ao vivo que têm até 24 horas de duração ao usar os serviços de mídia para transcodificar um feed de contribuição de taxa de bits única em um fluxo de saída com várias taxas de bits.
* Novo suporte de streaming ao vivo de baixa latência em eventos ao vivo. Para obter mais informações, consulte [latência](live-event-latency.md).
* A visualização de eventos ao vivo dá suporte ao [empacotamento dinâmico](dynamic-packaging-overview.md) e à criptografia dinâmica. Isso habilita a proteção de conteúdo na visualização, bem como o empacotamento de DASH e HLS.
* A saída ao vivo é mais simples de usar do que a entidade do programa nas APIs v2. 
* Suporte a RTMP melhorado (maior estabilidade e mais suporte a codificador de origem).
* Ingestão segura do RTMPS.<br/>Ao criar um evento ao vivo, você obtém 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId), apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS.   
* Você tem o RBAC (controle de acesso baseado em função) em suas entidades. 

## <a name="changes-from-v2"></a>Alterações da V2

* Para ativos criados com v3, os serviços de mídia oferecem suporte apenas à [criptografia de armazenamento do lado do servidor do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Você pode usar APIs v3 com ativos criados com APIs v2 que tinham [criptografia de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecidas pelos serviços de mídia.
    * Você não pode criar novos ativos com a criptografia de [armazenamento](../previous/media-services-rest-storage-encryption.md) do AES 256 herdada usando APIs v3.
* As propriedades do [ativo](assets-concept.md)na v3 diferem de v2, veja [como as propriedades](assets-concept.md#map-v3-asset-properties-to-v2)são mapeadas.
* Os SDKs do v3 agora são dissociados do SDK de armazenamento, que oferece mais controle sobre a versão do SDK de armazenamento que você deseja usar e evita problemas de controle de versão. 
* Nas APIs v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições de Media Encoder Standard v2. Por exemplo, a taxa de bits em v2 seria especificada como 128 (Kbps), mas, em v3, seria 128000 (bits/segundo). 
* As entidades AssetFiles, AccessPolicies e os ingestmanifests não existem em v3.
* A propriedade IAsset. ParentAssets não existe em v3.
* ContentKeys não é mais uma entidade, agora é uma propriedade do localizador de streaming.
* O suporte à grade de eventos substitui NotificationEndpoints.
* As seguintes entidades foram renomeadas
    * A saída do trabalho substitui a tarefa e agora faz parte de um trabalho.
    * O localizador de streaming substitui o localizador.
    * O evento ao vivo substitui o canal.<br/>A cobrança de eventos ao vivo é baseada em medidores de canal ao vivo. Para obter mais informações, consulte [cobrança](live-event-states-billing.md) e [preços](https://azure.microsoft.com/pricing/details/media-services/).
    * O Live output substitui o programa.
* As saídas ao vivo começam na criação e param quando excluídas. Os programas funcionaram de maneira diferente nas APIs v2, eles precisavam ser iniciados após a criação.
*  Para obter informações sobre um trabalho, você precisa saber o nome da transformação sob a qual o trabalho foi criado. 

> [!NOTE]
> Examine as convenções de nomenclatura que são aplicadas aos [recursos dos serviços de mídia v3](media-services-apis-overview.md#naming-conventions). Examine também os blobs de [nomenclatura](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Lacunas de recursos em relação às APIs v2

A API v3 tem as lacunas de recursos a seguir em relação à API v2. Fechar as lacunas é o trabalho em andamento.

* O [codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e os [processadores de análise de mídia](../previous/media-services-analytics-overview.md) herdados (visualização do indexador do Azure Media Services 2, edição facial etc.) não estão acessíveis por meio de v3.<br/>Os clientes que desejam migrar do indexador de mídia 1 ou 2 Preview podem usar imediatamente a predefinição AudioAnalyzer na API v3.  Essa nova predefinição contém mais recursos do que o indexador de mídia 1 ou 2 mais antigo. 
* Muitos dos [recursos avançados do Media Encoder Standard em APIs v2](../previous/media-services-advanced-encoding-with-mes.md) não estão disponíveis atualmente na v3, como:
  
    * Costura de ativos
    * Sobreposições
    * Corte
    * Sprites de miniatura
    * Inserindo uma faixa de áudio silenciosa quando a entrada não tem áudio
    * Inserindo uma faixa de vídeo quando a entrada não tem vídeo
* Eventos ao vivo com transcodificação atualmente não dão suporte à inserção de Slate MID-Stream e inserção de marcador de anúncio via chamada à API. 

> [!NOTE]
> Marque este artigo e continue verificando as atualizações.
 
## <a name="code-differences"></a>Diferenças de código

A tabela a seguir mostra as diferenças de código entre V2 e V3 para cenários comuns.

|Cenário|API V2|API V3|
|---|---|---|
|Criar um ativo e carregar um arquivo |[exemplo do .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Enviar um trabalho|[exemplo do .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como primeiro criar uma transformação e, em seguida, enviar um trabalho.|
|Publicar um ativo com criptografia AES |1. Criar ContentKeyAuthorizationPolicyOption<br/>2. Criar ContentKeyAuthorizationPolicy<br/>3. Criar AssetDeliveryPolicy<br/>4. Criar ativo e carregar conteúdo ou enviar trabalho e usar ativo de saída<br/>5. Associar o AssetDeliveryPolicy ao ativo<br/>6. Criar ContentKey<br/>7. Anexar ContentKey ao ativo<br/>8. Criar AccessPolicy<br/>9. Criar localizador<br/><br/>[exemplo do .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar política de chave de conteúdo<br/>2. Criar ativo<br/>3. Carregar conteúdo ou usar ativo como JobOutput<br/>4. Criar localizador de streaming<br/><br/>[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes do trabalho e gerenciar trabalhos |[Gerenciar trabalhos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerenciar trabalhos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), a CLI ou um dos SDKs com suporte.
* Você precisa provisionar unidades reservadas de mídia (MRUs) em sua conta para controlar a simultaneidade e o desempenho de seus trabalhos, especialmente aqueles que envolvem análise de vídeo ou áudio. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Você pode gerenciar o MRUs usando a [CLI 2,0 para os serviços de mídia v3](media-reserved-units-cli-how-to.md), usando o [portal do Azure](../previous/media-services-portal-scale-media-processing.md)ou usando as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa provisionar MRUs, esteja usando as APIs dos serviços de mídia v2 ou v3.
* As entidades dos serviços de mídia criadas com a API v3 não podem ser gerenciadas pela API v2.  
* Não é recomendável gerenciar entidades que foram criadas com APIs v2 por meio de APIs v3. Veja a seguir exemplos das diferenças que tornam as entidades em duas versões incompatíveis:   
    * Trabalhos e tarefas criados na v2 não aparecem na v3, pois não estão associados a uma transformação. A recomendação é alternar para transformações e trabalhos v3. Haverá um período de tempo relativamente curto para a necessidade de monitorar os trabalhos do em andamento v2 durante a comutação.
    * Os canais e programas criados com v2 (que são mapeados para eventos ao vivo e saídas ao vivo no v3) não podem continuar sendo gerenciados com v3. A recomendação é mudar para eventos ao vivo v3 e saídas ao vivo em uma parada de canal conveniente.<br/>Atualmente, você não pode migrar canais em execução contínua.  

> [!NOTE]
> Essa página será mantida à medida que a equipe de serviços de mídia fizer melhorias contínuas nas APIs v3 e solucionar as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos Seguintes

Para ver como é fácil começar a codificar e a transmitir ficheiros de vídeo, veja [Stream files](stream-files-dotnet-quickstart.md) (Transmitir ficheiros). 

