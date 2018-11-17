---
title: Migrar a partir do Azure v2 para a v3 dos serviços de multimédia | Documentos da Microsoft
description: Este artigo descreve as alterações que foram introduzidas no Serviços de multimédia do Azure v3 e mostra as diferenças entre duas versões.
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
ms.date: 11/15/2018
ms.author: juliako
ms.openlocfilehash: 41ad4b26247fa8037de01ff956921146a2238abc
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823393"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientações de migração para mover de serviços de multimédia v2 para v3

Este artigo descreve as alterações que foram introduzidas no Serviços de multimédia do Azure v3, mostra as diferenças entre duas versões e fornece as orientações de migração.

Se tiver um serviço de vídeo desenvolvido hoje na parte superior dos [APIs de v2 de serviços de multimédia herdadas](../previous/media-services-overview.md), deve rever as seguintes diretrizes e as seguintes considerações antes de migrar para as APIs v3. Existem muitos benefícios e os novos recursos da API v3 que melhoram a experiência de desenvolvedor e recursos dos serviços de multimédia. No entanto, como o chamado no [problemas conhecidos do](#known-issues) secção deste artigo, também existem algumas limitações devido a alterações entre as versões de API. Esta página será mantida à medida que a equipe de serviços de multimédia faz melhorias contínuas para as APIs v3 e aborda as lacunas entre as versões. 

## <a name="benefits-of-media-services-v3"></a>Benefícios do suporte de dados dos serviços de v3

### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. Modelos Azure Resource Manager podem ser utilizados para criar e implementar as transformações, pontos finais de transmissão em fluxo, LiveEvents e muito mais.
* [Abra a API (também conhecido como Swagger) especificação](https://aka.ms/ams-v3-rest-sdk) documento.
    Expõe o esquema para todos os componentes de serviço, incluindo a codificação baseada em ficheiros.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [node. js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [ir](https://aka.ms/ams-v3-go-ref)e Ruby.
* [CLI do Azure](https://aka.ms/ams-v3-cli-ref) integração para o suporte a script simple.

### <a name="new-features"></a>Novos recursos

* Para o trabalho baseados em ficheiros de processamento, pode utilizar um URL de HTTP (S) como entrada.
    Não é necessário ter o conteúdo já armazenado no Azure, nem precisa criar recursos.
* Apresenta o conceito de [transforma](transforms-jobs-concept.md) para processamento de tarefas baseada em ficheiros. Uma transformação pode ser utilizada para configurações de compilação reutilizáveis, para criar modelos do Azure Resource Manager e isolar as definições de processamento entre vários clientes ou inquilinos.
* Pode ter um elemento [StreamingLocators vários](streaming-locators-concept.md) cada uma com diferentes configurações de empacotamento dinâmico e a encriptação dinâmica.
* [Proteção de conteúdo](content-key-policy-concept.md) oferece suporte a recursos com múltiplos principais.
* Pode transmitir em fluxo eventos em direto que estão até 24 horas longas quando utilizar os serviços de mídia à transcodificação uma contribuição de velocidade de transmissão única para alimentar num fluxo de saída que tenha múltiplas velocidades de transmissão.
* Novo baixa latência em direto a suporte de transmissão em fluxo LiveEvents.
* Pré-visualização do LiveEvent suporta o empacotamento dinâmico e a encriptação dinâmica. Isto permite que a proteção de conteúdo no empacotamento de pré-visualização, bem como DASH e HLS.
* LiveOutput é mais simples de usar do que a entidade de programa nas v2 APIs. 
* Tem o controlo de acesso baseado em funções (RBAC) sobre as entidades. 

## <a name="changes-from-v2"></a>Alterações do v2

* Para os recursos criados com v3, serviços de multimédia só suporta o [encriptação do armazenamento do lado do servidor de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Pode usar v3 APIs com recursos que criou com APIs de v2 que tinham [encriptação do armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pelos serviços de multimédia.
    * Não é possível criar novos recursos com o AES 256 legado [encriptação do armazenamento](../previous/media-services-rest-storage-encryption.md) através de v3 APIs.
* Os SDKs de v3 agora estão desassociados do SDK de armazenamento, o que dá-lhe mais controlo sobre a versão do SDK de armazenamento que pretende utilizar e evita problemas de controle de versão. 
* Nas v3 APIs, todas as taxas de bits de codificação são em bits por segundo. Isto é diferente de v2 que predefine Media Encoder Standard. Por exemplo, a velocidade de transmissão no v2 teria de ser especificada como 128 (kbps), mas na v3 seria 128000 (bits por segundo). 
* Entidades AssetFiles AccessPolicies e IngestManifests não existem na v3.
* A propriedade IAsset.ParentAssets não existe na v3.
* ContentKeys já não é uma entidade, agora é uma propriedade do StreamingLocator.
* Suporte a eventos Grid substitui NotificationEndpoints.
* As seguintes entidades foram renomeadas
    * JobOutput substitui a tarefa e agora é parte de uma tarefa.
    * StreamingLocator substitui o localizador.
    * LiveEvent substitui o canal.<br/>LiveEvents a faturação baseia-se a medidores de canal em direto. Para obter mais informações, consulte [descrição geral de transmissão em direto](live-streaming-overview.md#billing) e [preços](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput substitui o programa.
* LiveOutputs não têm de ser iniciados explicitamente, iniciar a criação e parar quando eliminado. Programas de forma diferente se trabalhou nas v2 APIs, eles tinham que ser iniciado após a criação.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Intervalos de funcionalidades em relação a v2 APIs

A API de v3 tem os seguintes intervalos de funcionalidades em relação a API v2. Fechar as lacunas é o trabalho em curso.

* O [codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e o legado [processadores de análise de multimédia](../previous/media-services-analytics-overview.md) (pré-visualização do Azure Media Services indexador 2, Editor de rostos, etc.) não estão acessíveis por meio de v3.<br/>Os clientes que pretendem migrar a partir de 1 de indexador de multimédia ou 2 preview imediatamente podem utilizar o AudioAnalyzer predefinir na v3 API.  Esta configuração predefinida nova contém mais recursos do que o indexador 1 mais antigas do suporte de dados ou 2. 
* Muitas das funcionalidades avançadas do Media Encoder Standard no v2 APIs não estão atualmente disponíveis na v3, tais como:
    * Recorte (para cenários sob demanda e em direto)
    * Clips de ativos
    * Sobreposições
    * Corte
    * Sprites em miniatura
* LiveEvents com transcodificação atualmente não suportam stream médio de inserção de imagem fixa, configurações predefinidas personalizadas ou inserção de marcadores de publicidade através de uma chamada API. 

> [!NOTE]
> . Este artigo de marcador e manter a verificação de atualizações.

## <a name="code-differences"></a>Diferenças de código

A tabela seguinte mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|V2 API|V3 API|
|---|---|---|
|Criar um elemento e carregar um ficheiro |[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET de v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Submeter uma tarefa|[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET de v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro uma transformação e, em seguida, submeter uma tarefa.|
|Publicar um elemento com encriptação AES |1. Criar ContentKeyAuthorizationPolicyOption<br/>2. Criar ContentKeyAuthorizationPolicy<br/>3. Criar AssetDeliveryPolicy<br/>4. Criar elemento e carregar conteúdo ou submeter tarefa e utilizar recursos de saída<br/>5. Associe AssetDeliveryPolicy com elemento<br/>6. Criar ContentKey<br/>7. Anexar ContentKey ativo<br/>8. Criar AccessPolicy<br/>9. Criar o localizador<br/><br/>[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar política de chave de conteúdo<br/>2. Criar recurso<br/>3. Carregar conteúdo ou utilizar recursos como JobOutput<br/>4. Criar StreamingLocator<br/><br/>[exemplo de .NET de v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, não é possível utilizar o portal do Azure para gerir os recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-sdk), CLI, ou um dos SDKs suportados.
* Terá de aprovisionar unidades reservadas de multimédia (MRUs) na sua conta para controlar a simultaneidade e o desempenho das suas tarefas, especialmente aqueles envolvendo vídeo ou áudio de análise. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Pode gerir os MRUs usando [CLI 2.0 para serviços de multimédia v3](media-reserved-units-cli-how-to.md), utilizando o [portal do Azure](../previous/media-services-portal-scale-media-processing.md), ou utilizando o[ v2 APIs](../previous/media-services-dotnet-encoding-units.md). Terá de aprovisionar MRUs, se estiver a utilizar os serviços de multimédia v2 ou v3 APIs.
* Entidades de serviços de suporte de dados criadas com a v3 que API não pode ser gerida pela v2 API.  
* Não é recomendado para gerir as entidades que foram criadas com as APIs do v2 por meio das APIs v3. Seguem-se exemplos das diferenças que tornam as entidades em duas versões incompatíveis:   
    * Trabalhos e tarefas que criou no v2 não aparecem na v3 à medida que não estiverem associados uma transformação. Recomenda-se mudar para a v3 transformações e tarefas. Haverá um período de tempo relativamente curto da necessidade de monitor a v2 em utilização tarefas durante a alternância de.
    * Canais e programas criados com o v2 (que são mapeados para LiveEvents e LiveOutputs na v3) não é possível continuar a ser geridas com v3. Recomenda-se mudar para v3 LiveEvents e LiveOutputs num conveniente parar de canal.<br/>Atualmente, não é possível migrar canais continuamente em execução.  

> [!NOTE]
> . Este artigo de marcador e manter a verificação de atualizações.

## <a name="next-steps"></a>Passos Seguintes

Para ver como é fácil começar a codificar e a transmitir ficheiros de vídeo, veja [Stream files](stream-files-dotnet-quickstart.md) (Transmitir ficheiros). 

