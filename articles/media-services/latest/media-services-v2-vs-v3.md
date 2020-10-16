---
title: Migrar do Azure Media Services v2 para v3
description: Este artigo descreve alterações que foram introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 23baa6ed92aa01251b4c7ccf34ef784910a0321f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019839"
---
# <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 vs. v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo descreve alterações que foram introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões.

## <a name="general-changes-from-v2"></a>Alterações gerais de v2

* Para alterações relacionadas com o ativo, consulte a secção [de alterações específicas](#asset-specific-changes) do Ativo que se segue.
* Os V3 SDKs estão agora dissociados do Storage SDK, o que lhe dá mais controlo sobre a versão do Storage SDK que pretende utilizar e evita problemas de versão. 
* Nas APIs v3, todas as taxas de codificação estão em bits por segundo. Isto é diferente dos predefinos V2 Media Encoder Standard. Por exemplo, o bitrate em v2 seria especificado como 128 (kbps), mas em v3 seria 128000 (bits/segundo). 
* As entidades AssetFiles, AccessPolicies e IngestManifests não existem na V3.
* O ContentKeys já não é uma entidade, é agora propriedade do Localizador de Streaming.
* O suporte da Grelha de Eventos substitui os pontos de final de notificação.
* As seguintes entidades foram renomeadas:

   * v3 JobOutput substitui v2 Task e agora faz parte de um Job. As entradas e saídas estão agora ao nível do Job. Para obter mais informações, consulte [Criar uma entrada de trabalho a partir de um ficheiro local.](job-input-from-local-file-how-to.md) 

       Para obter a história do progresso do trabalho, ouça os eventos EventGrid. Para obter mais informações, consulte [eventos de Grelha de Eventos de Gestão](reacting-to-media-services-events.md).
    * O localizador de streaming substitui o localizador.
    * Live Event substitui Channel.<br/>A faturação de Eventos Ao Vivo é baseada em medidores do Live Channel. Para mais informações, consulte [a faturação](live-event-states-billing.md) e [os preços.](https://azure.microsoft.com/pricing/details/media-services/)
    * A Saída Ao Vivo substitui o Programa.
* As Saídas Ao Vivo começam na criação e param quando apagadas. Os programas funcionaram de forma diferente nas APIs v2, tiveram de ser iniciados após a criação.
* Para obter informações sobre um trabalho, precisa saber o nome Transform sob o qual o trabalho foi criado. 
* Em v2, os ficheiros de [entrada](../previous/media-services-input-metadata-schema.md) e metadados de [saída](../previous/media-services-output-metadata-schema.md) XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. 
* Nos Serviços de Mídia v2, o vetor de inicialização (IV) pode ser especificado. Nos Serviços de Comunicação Social v3, o FairPlay IV não pode ser especificado. Embora não tenha impacto nos clientes que utilizam os Media Services tanto para a embalagem como para a entrega de licenças, pode ser um problema quando se utiliza um sistema de DRM de terceiros para entregar as licenças FairPlay (modo híbrido). Nesse caso, é importante saber que o FairPlay IV é derivado do ID da chave cbcs e pode ser recuperado usando esta fórmula:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    com o

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Para obter mais informações, consulte o [código Azure Functions C# para Serviços de Mídia v3 em modo híbrido para operações ao vivo e VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Reveja as convenções de nomeação que são aplicadas aos [recursos v3 dos Media Services.](media-services-apis-overview.md#naming-conventions) Reveja também [o nome de bolhas.](assets-concept.md#naming)

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Feature gaps with respect to v2 APIs (Diferenças de funcionalidades em relação a APIs v2)

A API v3 tem as seguintes lacunas de características no que diz respeito à API v2. Fechar as lacunas é trabalhar em andamento.

* O [Premium Encoder](../previous/media-services-encode-asset.md) e os [processadores de análise de mídia](../previous/legacy-components.md) legacy (Azure Media Services Indexer 2 Preview, Face Redator, etc.) não estão acessíveis via v3.<br/>Os clientes que desejem migrar da pré-visualização do Media Indexer 1 ou 2 podem utilizar imediatamente a predefinição AudioAnalyzer na API v3.  Esta nova predefinição contém mais funcionalidades do que o Indexer 1 ou 2 mais antigo. 
* Muitas das [funcionalidades avançadas da Norma Media Encoder em APIs v2](../previous/media-services-advanced-encoding-with-mes.md) não estão atualmente disponíveis em v3, tais como:
  
    * Costura de Ativos
    * Sobreposições
    * Corte
    * Sprites de miniatura
    * Inserção de uma faixa de áudio silenciosa quando a entrada não tem áudio
    * Inserir uma faixa de vídeo quando a entrada não tem vídeo
* Eventos ao vivo com transcoding atualmente não suportam inserção de ardósia a meio do fluxo e inserção de marcador de anúncios através da chamada API. 
* Consulte o `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` código de amostra para as melhores práticas e padrões relativos à utilização da API V2 REST em . NETCore SDK.

## <a name="asset-specific-changes"></a>Alterações específicas do ativo

* Para ativos criados com v3, os Serviços de Media suportam apenas a [encriptação de armazenamento do servidor do Azure Storage](../../storage/common/storage-service-encryption.md).
    * Pode utilizar APIs v3 com Ativos criados com APIs v2 que tinham [encriptação de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pela Media Services.
    * Não é possível criar novos Ativos com a [encriptação](../previous/media-services-rest-storage-encryption.md) de armazenamento AES 256 usando APIs v3.
* As propriedades do [Ativo](assets-concept.md)em v3 diferem de v2, veja [como as propriedades mapeiam.](#map-v3-asset-properties-to-v2)
* A propriedade IAsset.ParentAssets não existe em v3.

### <a name="map-v3-asset-properties-to-v2"></a>Mapear propriedades de ativos v3 para v2

A tabela seguinte mostra como as propriedades do [Ativo](/rest/api/media/assets/createorupdate#asset)no mapa v3 para as propriedades do Ativo em v2.

|v3 propriedades|v2 propriedades|
|---|---|
|`id` - (único) o percurso completo do Gestor de Recursos Azure, ver exemplos em [Ativo](/rest/api/media/assets/createorupdate)||
|`name` - (único) ver [convenções de nomeação](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` - (único) o valor começa com o `nb:cid:UUID:` prefixo.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opções de criação)|
|`type`||

### <a name="storage-side-encryption"></a>Encriptação lateral do armazenamento

Para proteger os seus Ativos em repouso, os ativos devem ser encriptados pela encriptação do lado do armazenamento. A tabela a seguir mostra como funciona a encriptação do lado do armazenamento nos Serviços de Media:

|Opção de encriptação|Description|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento de serviços de mídia|Encriptação AES-256, chave gerida pelos Media Services.|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](../../storage/common/storage-service-encryption.md)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportado|Suportado|
|[Encriptação Client-Side de armazenamento](../../storage/common/storage-client-side-encryption.md)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado|Não suportado|

<sup>1 Enquanto</sup> os Serviços de Mídia suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendado.

<sup>2</sup> Nos Serviços de Mídia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Serviços de Mídia v2. O que significa que a V3 funciona com os ativos encriptados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="code-differences"></a>Diferenças de código

A tabela seguinte mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|v2 API|v3 API|
|---|---|---|
|Criar um ativo e fazer upload de um ficheiro |[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Submeter um emprego|[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como primeiro criar uma Transform e depois submeter um Job.|
|Publique um ativo com encriptação AES |1. Criar ContentKeyAuthorizationPolicyOption<br/>2. Criar ContentKeyAuthorizationPolicy<br/>3. Criar AssetDeliveryPolicy<br/>4. Criar Ativo e carregar conteúdo OU Enviar trabalho e utilizar o ativo de saída<br/>5. Associar a AssetDeliveryPolicy com o Ativo<br/>6. Criar ContentKey<br/>7. Anexar contentKey ao Ativo<br/>8. Criar A Política de Acesso<br/>9. Criar Localizador<br/><br/>[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar política de chave de conteúdo<br/>2. Criar Ativo<br/>3. Carregar conteúdo ou utilizar o Ativo como JobOutput<br/>4. Criar localizador de streaming<br/><br/>[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes de emprego e gerir empregos |[Gerir empregos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerir empregos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Por favor, marque este artigo e continue a verificar se há novidades.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para passar dos Serviços de Comunicação v2 para v3](migrate-from-v2-to-v3.md)