---
title: Migrar dos Serviços De Mídia Azure v2 para v3
description: Este artigo descreve as alterações introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087826"
---
# <a name="media-services-v2-vs-v3"></a>Serviços de Media v2 vs. v3

Este artigo descreve as alterações introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões.

## <a name="general-changes-from-v2"></a>Alterações gerais a partir da v2

* Para ativos criados com v3, os Media Services suportam apenas a encriptação do lado do [servidor de armazenamento Do Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Pode utilizar apis v3 com Ativos criados com APIs v2 que tinham [encriptação](../previous/media-services-rest-storage-encryption.md) de armazenamento (AES 256) fornecida pela Media Services.
    * Não é possível criar novos Ativos com a [encriptação](../previous/media-services-rest-storage-encryption.md) de armazenamento AES 256 com recurso a V3 APIs.
* As propriedades do [Ativo](assets-concept.md)em v3 diferem de v2, veja [como as propriedades mapeiam](#map-v3-asset-properties-to-v2).
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

    Para mais informações, consulte o [código Azure Functions C# para Media Services v3 em modo híbrido para operações ao vivo e vod](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Reveja as convenções de nomeação que são aplicadas aos [recursos v3 dos Media Services.](media-services-apis-overview.md#naming-conventions) Também reveja [as bolhas de nome.](assets-concept.md#naming)

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Feature gaps with respect to v2 APIs (Diferenças de funcionalidades em relação a APIs v2)

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
 
## <a name="asset-specific-changes"></a>Alterações específicas do ativo

### <a name="map-v3-asset-properties-to-v2"></a>Mapa v3 propriedades de ativos para v2

A tabela seguinte mostra como as propriedades do [Ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)no mapa v3 para as propriedades do Ativo em v2.

|propriedades v3|propriedades v2|
|---|---|
|`id`- (único) o caminho completo do Gestor de Recursos Azure, ver exemplos em [Ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (único) ver convenções de [nomeação](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- o valor (único) começa com o `nb:cid:UUID:` prefixo.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(opções de criação)|
|`type`||

### <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus Ativos em repouso, os ativos devem ser encriptados pela encriptação do lado do armazenamento. A tabela que se segue mostra como funciona a encriptação do lado do armazenamento nos Serviços de Media:

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento de serviços de mídia|Encriptação AES-256, chave gerida pela Media Services.|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportado|Suportado|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado|Não suportado|

<sup>1</sup> Enquanto os Serviços de Media suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendado fazê-lo.

<sup>2</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Media Services v2. O que significa que a V3 trabalha com os ativos encriptados de armazenamento existentes, mas não permite a criação de novos.

## <a name="code-differences"></a>Diferenças de código

A tabela que se segue mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|V2 API|V3 API|
|---|---|---|
|Criar um ativo e carregar um ficheiro |[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Submeter um emprego|[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro um Transform e depois submeter um Trabalho.|
|Publicar um ativo com encriptação AES |1. Criar opção contentkeyauthorizationPolicyOption<br/>2. Criar política de autorização de conteúdo<br/>3. Criar política de entrega de ativos<br/>4. Criar Ativo e carregar conteúdo OU Enviar trabalho e utilizar ativo de saída<br/>5. Política de Entrega de Ativos Associados com Ativo<br/>6. Criar a Chave de Conteúdo<br/>7. Anexar a Chave de Conteúdo ao Ativo<br/>8. Criar política de acesso<br/>9. Criar localizador<br/><br/>[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar política chave de conteúdo<br/>2. Criar Ativo<br/>3. Carregar conteúdo ou utilizar o Ativo como JobOutput<br/>4. Criar localizador de streaming<br/><br/>[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes de emprego e gerir empregos |[Gerir empregos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerir empregos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Por favor, marque este artigo e continue a verificar se há novidades.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para a mudança dos Serviços de Media v2 para v3](migrate-from-v2-to-v3.md)
