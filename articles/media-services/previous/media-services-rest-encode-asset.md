---
title: Como codificar um ativo Azure utilizando o Media Encoder Standard [ Media Encoder Standard] Microsoft Docs
description: Saiba como utilizar o Media Encoder Standard para codificar conteúdos de mídia nos Serviços De Mídia Azure. As amostras de código usam a API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774925"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Como codificar um ativo utilizando o Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Visão geral

Para entregar vídeo digital através da Internet, deve comprimir os meios de comunicação. Os ficheiros de vídeo digitais são grandes e podem ser demasiado grandes para serem entregues através da Internet, ou para que os dispositivos dos seus clientes apresentem corretamente. A codificação é o processo de compressão de vídeo e áudio para que os seus clientes possam ver os seus meios de comunicação.

Os postos de trabalho de codificação são uma das operações de processamento mais comuns nos Serviços De Mídia Azure. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Quando codificar, pode utilizar o codificador incorporado dos Serviços de Media (Media Encoder Standard). Também pode utilizar um codificador fornecido por um parceiro de Serviços de Media. Os codificadores de terceiros estão disponíveis através do Mercado Azure. Pode especificar os detalhes das tarefas de codificação utilizando cordas predefinidas definidas para o seu codificador, ou utilizando ficheiros de configuração predefinidos. Para ver os tipos de predefinições disponíveis, consulte [Predefinições de Tarefas para Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que pretende realizar. Através da API REST, você pode criar empregos e suas tarefas relacionadas de uma de duas maneiras:

* As tarefas podem ser definidas inline através da propriedade de navegação Tasks em entidades De trabalho.
* Utilize o processamento do lote OData.

Recomendamos que codifique sempre os seus ficheiros de origem num conjunto de MP4 bitrate adaptativo e, em seguida, converta o conjunto para o formato desejado utilizando [embalagens dinâmicas](media-services-dynamic-packaging-overview.md).

Se o seu ativo de saída estiver encriptado, tem de configurar a política de entrega de ativos. Para mais informações, consulte [a política de entrega de ativos configurando.](media-services-rest-configure-asset-delivery-policy.md)

## <a name="considerations"></a>Considerações

Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md).

Antes de começar a fazer referência aos processadores de mídia, verifique se tem o ID correto do processador de mídia. Para mais informações, consulte [Obter processadores de mídia](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ligue-se aos Serviços Multimédia

Para obter informações sobre como se conectar à AMS API, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Criar um trabalho com uma única tarefa de codificação

> [!NOTE]
> Quando está a trabalhar com a Media Services REST API, aplicam-se as seguintes considerações:
>
> Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para media services REST API desenvolvimento](media-services-rest-how-to-use.md).
>
> Ao utilizar o JSON e especificar utilizar a palavra-chave **__metadata** no pedido (por exemplo, para referir um objeto ligado), deve definir o cabeçalho **Aceitar** o [formato JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Aceitar: aplicação/json;odata=verbose.
>
>

O exemplo que se segue mostra como criar e publicar um trabalho com uma tarefa definida para codificar um vídeo numa resolução e qualidade específicas. Quando codificar com o Media Encoder Standard, pode utilizar predefinições de configuração de [tarefas](https://msdn.microsoft.com/library/mt269960)aqui especificadas .

Pedido:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Desmarca o nome do ativo de saída
O exemplo que se segue mostra como definir o atributo do nome do ativo:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Considerações
* As propriedades do TaskBody devem usar o XML literal para definir o número de entrada, ou ativos de saída que são utilizados pela tarefa. O artigo de tarefa contém a Definição XML Schema para o XML.
* Na definição TaskBody, cada valor interno para `<inputAsset>` e `<outputAsset>` deve ser definido como JobInputAsset(valor) ou JobOutputAsset (valor).
* Uma tarefa pode ter vários ativos de produção. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa num trabalho.
* Pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
* As tarefas não devem formar um ciclo.
* O parâmetro de valor que passa para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição da entidade de trabalho.
* Uma vez que os Serviços de Media são construídos com base no OData v3, os ativos individuais nas coleções de propriedades de navegação InputMediaAssets e OutputMediaAssets são referenciados através de um par de nomes "__metadata: uri".
* O InputMediaAssets mapeia um ou mais ativos que criou nos Serviços de Media. OutputMediaAssets são criados pelo sistema. Não referenciam um ativo existente.
* OutputMediaAssets pode ser nomeado usando o atributo assetName. Se este atributo não estiver presente, então o nome do OutputMediaAsset é qualquer que seja o valor de texto interno do elemento `<outputAsset>` com um sufixo do valor do Nome de Trabalho, ou o valor do Id de Trabalho (no caso em que a propriedade Nome não está definida). Por exemplo, se definir um valor para o nome do ativo para "Sample", então a propriedade OutputMediaAsset Name está definida para "Sample". No entanto, se não definisse um valor para o assetName, mas definisse o nome de trabalho para "NewJob", então o nome OutputMediaAsset seria "JobOutputAsset(valor)_NewJob".

## <a name="create-a-job-with-chained-tasks"></a>Criar um trabalho com tarefas acorrentadas
Em muitos cenários de aplicação, os desenvolvedores querem criar uma série de tarefas de processamento. Nos Serviços de Media, pode criar uma série de tarefas acorrentadas. Cada tarefa executa diferentes etapas de processamento e pode utilizar diferentes processadores de mídia. As tarefas acorrentadas podem entregar um ativo de uma tarefa para outra, executando uma sequência linear de tarefas sobre o ativo. No entanto, as tarefas executadas num trabalho não são necessárias para estar em sequência. Quando se cria uma tarefa acorrentada, os objetos **ITask** acorrentados são criados num único objeto **IJob.**

> [!NOTE]
> Existe atualmente um limite de 30 tarefas por trabalho. Se precisar de acorrentar mais de 30 tarefas, crie mais do que um trabalho para conter as tarefas.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Considerações
Para permitir o acorrentamento de tarefas:

* Um trabalho deve ter pelo menos duas tarefas.
* Deve haver pelo menos uma tarefa cujo contributo é a saída de outra tarefa no trabalho.

## <a name="use-odata-batch-processing"></a>Utilizar o processamento de lote OData
O exemplo que se segue mostra como usar o processamento de lotes OData para criar um trabalho e tarefas. Para obter informações sobre o processamento de lotes, consulte [o Processamento de Lote de Dados Abertos (OData).](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Criar um trabalho usando um Modelo de Emprego
Quando processar vários ativos utilizando um conjunto comum de tarefas, utilize um Modelo de Trabalho para especificar as predefinições de tarefas predefinidas ou para definir a ordem das tarefas.

O exemplo seguinte mostra como criar um Modelo de Trabalho com um Modelo de Tarefa que é definido inline. O Modelo de Tarefas utiliza o Media Encoder Standard como o MediaProcessor para codificar o ficheiro de ativo. No entanto, outros Processadores de Media também podem ser utilizados.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Ao contrário de outras entidades de Serviços de Media, deve definir um novo identificador GUID para cada Modelo de Tarefa e colocá-lo na propriedade taskTemplateId e Id no seu organismo de pedido. O regime de identificação de conteúdos deve seguir o esquema descrito nas Entidades de Serviços De Comunicação Social Da Identificação. Além disso, os Modelos de Emprego não podem ser atualizados. Em vez disso, tem de criar um novo com as suas alterações atualizadas.
>
>

Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


O exemplo que se segue mostra como criar um trabalho que referencia um Id do Modelo de Emprego:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Se for bem sucedido, devolve-se a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Funcionalidades avançadas de codificação para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Vídeos de colheita durante a codificação](media-services-crop-video.md)
* [Personalizando predefinições de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobreposição ou marcação de água um vídeo com uma imagem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como criar um emprego para codificar um ativo, veja como verificar o progresso do trabalho com os [Media Services.](media-services-rest-check-job-progress.md)

## <a name="see-also"></a>Ver também
[Obtenha processadores de mídia](media-services-rest-get-media-processor.md)
