---
title: Migrar dos Serviços de Mídia Azure v2 para v3  Microsoft Docs
description: Este artigo descreve as alterações introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões. O artigo também fornece orientação de migração para a mudança dos Serviços de Media v2 para v3.
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
ms.date: 02/28/2020
ms.author: juliako
ms.openlocfilehash: 2a670c7bce113de8854b33e407c7de2236edd794
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197866"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para a mudança dos Serviços de Media v2 para v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` no leitor de feed RSS.

Este artigo descreve as alterações introduzidas no Azure Media Services v3, mostra diferenças entre duas versões e fornece a orientação de migração.

Se tiver um serviço de vídeo desenvolvido hoje em cima do [legado Media Services v2 APIs,](../previous/media-services-overview.md)deve rever as seguintes diretrizes e considerações antes de migrar para as APIs v3. Existem muitos benefícios e novas funcionalidades na API v3 que melhoram a experiência e as capacidades do desenvolvedor dos Media Services. No entanto, tal como foi chamado na secção [Questões Conhecidas](#known-issues) deste artigo, existem também algumas limitações devido a alterações entre as versões API. Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões. 

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="benefits-of-media-services-v3"></a>Benefícios dos Serviços de Media v3
  
### <a name="api-is-more-approachable"></a>API é mais acessível

*  A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. Os modelos do Gestor de Recursos Azure podem ser usados para criar e implementar Transforms, Streaming Endpoints, Eventos Ao Vivo e muito mais.
* Documento de [especificação OpenAPI (anteriormente chamado Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Expõe o esquema para todos os componentes do serviço, incluindo codificação baseada em ficheiros.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js,](/javascript/api/overview/azure/mediaservices/management) [Python,](https://aka.ms/ams-v3-python-ref) [Java,](https://aka.ms/ams-v3-java-ref) [Go](https://aka.ms/ams-v3-go-ref)e Ruby.
* [Integração Azure CLI](https://aka.ms/ams-v3-cli-ref) para suporte simples de scripts.

### <a name="new-features"></a>Novos recursos

* Para o processamento de trabalho baseado em ficheiros, pode utilizar um URL HTTP(S) como entrada.<br/>Não precisa de ter conteúdos já armazenados no Azure, nem precisa de criar Ativos.
* Introduz o conceito de [Transforms](transforms-jobs-concept.md) para processamento de trabalho baseado em ficheiros. Um Transform pode ser usado para construir configurações reutilizáveis, para criar modelos de gestor de recursos Azure, e isolar configurações de processamento entre vários clientes ou inquilinos.
* Um Ativo pode ter [vários localizadores](streaming-locators-concept.md) de streaming cada um com diferentes configurações de [Embalagem Dinâmica](dynamic-packaging-overview.md) e Encriptação Dinâmica.
* [A proteção de conteúdos](content-key-policy-concept.md) suporta funcionalidades multi-teclas.
* Você pode transmitir Eventos ao vivo que têm até 24 horas de duração quando usar os Serviços de Media para transcodificar um único feed de contribuição bitrate para um fluxo de saída que tem várias bitrates.
* Novo suporte de streaming ao vivo de Low Latency em Eventos Ao Vivo. Para mais informações, consulte [a latência.](live-event-latency.md)
* A Pré-visualização de eventos ao vivo suporta [a Embalagem Dinâmica](dynamic-packaging-overview.md) e a Encriptação Dinâmica. Isto permite a proteção de conteúdos na pré-visualização, bem como na embalagem DASH e HLS.
* A Saída Ao Vivo é mais simples de usar do que a entidade do Programa nas APIs v2. 
* Melhor suporte de RTMP (maior estabilidade e mais suporte codificador de fonte).
* RTMPS segura ingerir.<br/>Quando se cria um Evento Ao Vivo, obtém-se 4 URLs ingerir. Os 4 URLs ingerir são quase idênticos, têm o mesmo token de streaming (AppId), apenas a parte número da porta é diferente. Dois dos URLs são primários e reserva para RTMPS.   
* Tem um controlo de acesso baseado em papéis (RBAC) sobre as suas entidades. 

## <a name="changes-from-v2"></a>Alterações a partir de v2

* Para ativos criados com v3, os Media Services suportam apenas a encriptação do lado do [servidor de armazenamento Do Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Pode utilizar apis v3 com Ativos criados com APIs v2 que tinham [encriptação](../previous/media-services-rest-storage-encryption.md) de armazenamento (AES 256) fornecida pela Media Services.
    * Não é possível criar novos Ativos com a [encriptação](../previous/media-services-rest-storage-encryption.md) de armazenamento AES 256 com recurso a V3 APIs.
* As propriedades do [Ativo](assets-concept.md)em v3 diferem de v2, veja [como as propriedades mapeiam](assets-concept.md#map-v3-asset-properties-to-v2).
* Os SDKs v3 estão agora dissociados do SDK de Armazenamento, o que lhe dá mais controlo sobre a versão do Storage SDK que pretende utilizar e evita problemas de versão. 
* Nas V3 APIs, todas as taxas de codificação de bits estão em bits por segundo. Isto é diferente dos predefinições padrão do Codificador de Mídia v2. Por exemplo, o bitrate em v2 seria especificado como 128 (kbps), mas em v3 seria 128000 (bits/segundo). 
* Entidades AssetFiles, AccessPolicies e IngestManifests não existem na v3.
* A propriedade IAsset.ParentAssets não existe em v3.
* ContentKeys já não é uma entidade, é agora uma propriedade do Localizador de Streaming.
* O suporte da Rede de Eventos substitui os Pontos final da Notificação.
* As seguintes entidades foram renomeadas
    * A Job Output substitui a Task, e agora faz parte de um Trabalho.
    * Localizador de streaming substitui locator.
    * Live Event substitui Canal.<br/>A faturação de Eventos Ao Vivo baseia-se em contadores do Canal ao Vivo. Para mais informações, consulte [faturação](live-event-states-billing.md) e [preços.](https://azure.microsoft.com/pricing/details/media-services/)
    * Live Output substitui o Programa.
* As Saídas ao Vivo começam na criação e param quando eliminadas. Os programas funcionavam de forma diferente nas APIs v2, tinham de ser iniciados após a criação.
* Para obter informações sobre um trabalho, precisa saber o nome Transform em que o trabalho foi criado. 
* Em v2, os ficheiros de [entrada](../previous/media-services-input-metadata-schema.md) e [de saída](../previous/media-services-output-metadata-schema.md) de metadados XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. 
* Nos Serviços de Media v2, o vetor de inicialização (IV) pode ser especificado. Nos Serviços de Media v3, o FairPlay IV não pode ser especificado. Embora não tenha impacto nos clientes que utilizam os Serviços de Media tanto para a embalagem como para a entrega de licenças, pode ser um problema quando se utiliza um sistema DEDR De terceiros para entregar as licenças FairPlay (modo híbrido). Nesse caso, é importante saber que o FairPlay IV é derivado da chave CBCS ID e pode ser recuperado usando esta fórmula:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    por

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Para mais informações, consulte o [código funções Do Azure para C# serviços de mídia v3 em modo híbrido para operações ao vivo e vod](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Reveja as convenções de nomeação que são aplicadas aos [recursos v3 dos Media Services.](media-services-apis-overview.md#naming-conventions) Também reveja [as bolhas de nome.](assets-concept.md#naming)

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Lacunas de recurso no que diz respeito a APIs v2

A V3 API tem as seguintes lacunas de características no que diz respeito à API v2. Colmatar as lacunas está a decorrer.

* O [Premium Encoder](../previous/media-services-premium-workflow-encoder-formats.md) e os [processadores](../previous/media-services-analytics-overview.md) de análise de mídia (Azure Media Services Indexer 2 Preview, Face Redator, etc.) não estão acessíveis via v3.<br/>Os clientes que desejem migrar da pré-visualização do Media Indexer 1 ou 2 podem utilizar imediatamente o predefinido AudioAnalyzer na V3 API.  Este novo preset contém mais funcionalidades do que o indexante 1 ou 2 mais antigo. 
* Muitas das [funcionalidades avançadas da Norma Media Encoder em V2](../previous/media-services-advanced-encoding-with-mes.md) APIs não estão atualmente disponíveis em v3, tais como:
  
    * Costura de Ativos
    * Sobreposições
    * Colheita
    * Miniatura Sprites
    * Inserindo uma faixa de áudio silenciosa quando a entrada não tem áudio
    * Inserir uma pista de vídeo quando a entrada não tem vídeo
* Eventos ao vivo com transcodificação atualmente não suportam inserção de ardósia a meio do fluxo e inserção de marcador de anúncios através da chamada API. 

> [!NOTE]
> Por favor, marque este artigo e continue a verificar se há novidades.
 
## <a name="code-differences"></a>Diferenças de código

A tabela que se segue mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|V2 API|V3 API|
|---|---|---|
|Criar um ativo e carregar um ficheiro |[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Submeter um emprego|[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro um Transform e depois submeter um Trabalho.|
|Publicar um ativo com encriptação AES |1. Criar opção contentkeyauthorizationPolicyOption<br/>2. Criar política de autorização de conteúdo<br/>3. Criar política de entrega de ativos<br/>4. Criar Ativo e carregar conteúdo OU Enviar trabalho e utilizar ativo de saída<br/>5. Política de Entrega de Ativos Associados com Ativo<br/>6. Criar a Chave de Conteúdo<br/>7. Anexar a Chave de Conteúdo ao Ativo<br/>8. Criar política de acesso<br/>9. Criar localizador<br/><br/>[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar política chave de conteúdo<br/>2. Criar Ativo<br/>3. Carregar conteúdo ou utilizar o Ativo como JobOutput<br/>4. Criar localizador de streaming<br/><br/>[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes de emprego e gerir empregos |[Gerir empregos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerir empregos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize o [REST API,](https://aka.ms/ams-v3-rest-sdk)CLI ou um dos SDKs suportados.
* Você precisa fornecer Media Reserved Units (MRUs) na sua conta para controlar a conmoeda e desempenho dos seus Empregos, particularmente aqueles que envolvam Análise de Vídeo ou Áudio. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Pode gerir as MrUs utilizando o [CLI 2.0 para media services v3,](media-reserved-units-cli-how-to.md)utilizando o [portal Azure,](../previous/media-services-portal-scale-media-processing.md)ou utilizando as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa fornecer MRUs, quer esteja usando Media Services v2 ou v3 APIs.
* As entidades de Serviços de Media criadas com a V3 API não podem ser geridas pela API v2.  
* Nem todas as entidades da API V2 aparecem automaticamente na API V3.  Seguem-se exemplos de entidades nas duas versões que são incompatíveis:  
    * Os empregos e tarefas criados na v2 não aparecem na v3, uma vez que não estão associados a uma Transform. A recomendação é mudar para v3 Transforms and Jobs. Haverá um período de tempo relativamente curto para monitorizar os postos de trabalho v2 de bordo durante a transição.
    * Canais e Programas criados com v2 (que são mapeados para Eventos Ao Vivo e Saídas ao Vivo em v3) não podem continuar a ser geridos com v3. A recomendação é mudar para v3 Live Events e Live Outputs em uma paragem conveniente do Canal.<br/>Atualmente, não é possível migrar continuamente a correr canais.  

> [!NOTE]
> Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

Para ver como é fácil começar a codificar e a transmitir ficheiros de vídeo, veja [Stream files](stream-files-dotnet-quickstart.md) (Transmitir ficheiros). 

