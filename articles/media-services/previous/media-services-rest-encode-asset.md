---
title: Como codificar um recurso do Azure com o Media Encoder Standard | Documentos da Microsoft
description: Saiba como utilizar o Media Encoder Standard para codificar o conteúdo de multimédia nos serviços de multimédia do Azure. Exemplos de código utilizam a REST API.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 608abd09227014513a0ad358d7a2c225b5ab2b97
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863377"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Como codificar um recurso com o Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Descrição geral

Para distribuir vídeo digital através da Internet, tem de comprimir a multimédia. Ficheiros de vídeo digital são grandes e podem ser demasiado grandes para entregar através da Internet ou para os dispositivos dos seus clientes para serem exibidos corretamente. Codificação é o processo de compactação de vídeo e áudio para que os clientes podem ver os seus suportes de dados.

Tarefas de codificação são uma das operações de processamento mais comuns nos serviços de multimédia do Azure. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Ao codificar, pode usar o codificador incorporado de serviços de multimédia (Media Encoder Standard). Também pode usar um codificador fornecido por um parceiro de serviços de multimédia. Codificadores de terceiros estão disponíveis através do Azure Marketplace. Pode especificar os detalhes das tarefas de codificação, utilizando cadeias predefinidas, definidas para seu codificador ou com arquivos de configuração predefinida. Para ver os tipos de configurações predefinidas que estão disponíveis, consulte [predefinições de tarefas para o Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Cada tarefa pode ter uma ou mais tarefas, dependendo do tipo de processamento que deseja realizar. Através da API REST, pode criar tarefas e suas tarefas relacionadas em uma das seguintes formas:

* As tarefas podem ser definidos inline por meio da propriedade de navegação de tarefas em entidades de tarefa.
* Utilize o processamento em lote OData.

Recomendamos que sempre codificar seus arquivos de origem para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto para o formato desejado usando [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

Se o elemento de saída estiver armazenamento encriptado, tem de configurar a política de entrega de elementos. Para obter mais informações, consulte [configurar a política de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Considerações

Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).

Antes de começar a fazer referência a processadores de multimédia, certifique-se de que tem o suporte de dados correto ID do processador. Para obter mais informações, consulte [obter processadores de multimédia](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Criar uma tarefa com uma única tarefa de codificação

> [!NOTE]
> Quando está trabalhando com a API de REST de serviços de multimédia, aplicam-se as seguintes considerações:
>
> Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST dos serviços de multimédia](media-services-rest-how-to-use.md).
>
> Quando usar o JSON e especificando a utilizar o **__metadata** palavra-chave no pedido (por exemplo, para um objeto vinculado de referência), tem de definir o **Accept** cabeçalho para [formato verboso de JSON](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.
>
>

O exemplo seguinte mostra-lhe como criar e publicar uma tarefa com uma tarefa definida como codificar um vídeo numa resolução específico e qualidade. Quando codifica com o Media Encoder Standard, pode utilizar predefinições de configuração de tarefas especificadas [aqui](https://msdn.microsoft.com/library/mt269960).

Pedido:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Definir nome do elemento de saída
O exemplo seguinte mostra como definir o atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Considerações
* Propriedades de TaskBody tem de utilizar literal XML para definir o número de entrada ou saída ativos que são utilizados pela tarefa. O artigo de tarefas contém a definição do esquema XML para o XML.
* O valor na definição do TaskBody, para cada interna <inputAsset> e <outputAsset> tem de ser definido como JobInputAsset(value) ou JobOutputAsset(value).
* Uma tarefa pode ter vários recursos de saída. Um JobOutputAsset(x) só pode ser utilizado uma vez como uma saída de uma tarefa numa tarefa.
* Pode especificar JobInputAsset ou JobOutputAsset como um recurso de entrada de uma tarefa.
* Tarefas não têm formam um ciclo.
* O parâmetro de valor que passar para JobInputAsset ou JobOutputAsset representa o valor de índice para um recurso. Os recursos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets sobre a definição de entidade de tarefa.
* Como os serviços de multimédia é baseado em OData v3, os recursos individuais nas coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um "__metadata: uri" par nome / valor.
* InputMediaAssets mapeia para uma ou mais recursos que criou nos serviços de multimédia. OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um recurso existente.
* OutputMediaAssets podem ser nomeados usando o atributo assetName. Se este atributo não estiver presente, o nome do OutputMediaAsset é seja qual for o valor de texto interno do <outputAsset> elemento é com um sufixo do valor do nome da tarefa ou o valor de Id da tarefa (no caso em que a propriedade de nome não está definida). Por exemplo, se definir um valor para assetName para "Exemplo", em seguida, a propriedade de nome de OutputMediaAsset é definida como "Exemplo". No entanto, se não define um valor para assetName, mas definir o nome da tarefa para "NewJob", em seguida, o nome de OutputMediaAsset seria "_NewJob JobOutputAsset (valor)."

## <a name="create-a-job-with-chained-tasks"></a>Criar uma tarefa com tarefas em cadeia
Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos Media Services, pode criar uma série de tarefas em cadeia. Cada tarefa executa os passos de processamento diferente e pode utilizar processadores de multimédia diferentes. As tarefas em cadeia podem passá um elemento de uma tarefa para outro, executar uma sequência de tarefas de linear no ativo. No entanto, as tarefas executadas numa tarefa não têm de ser numa sequência. Quando cria uma tarefa em cadeia, o encadeados **ITask** objetos são criados num único **IJob** objeto.

> [!NOTE]
> Não existe atualmente um limite de 30 tarefas por tarefa. Se precisar de mais de 30 tarefas de encadear, crie mais do que uma tarefa para conter as tarefas.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
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
Para ativar o encadeamento de tarefa:

* Uma tarefa tem de ter, pelo menos, duas tarefas.
* Tem de existir pelo menos uma tarefa, cujos comentários são o resultado de outra tarefa no trabalho.

## <a name="use-odata-batch-processing"></a>Utilizar o processamento de lotes de OData
O exemplo seguinte mostra como utilizar o processamento de lotes de OData para criar um trabalho e tarefas. Para obter informações sobre o processamento em lotes, consulte [Open Data Protocol (OData) de processamento de Batch](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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
    x-ms-version: 2.17
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
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Criar uma tarefa com um JobTemplate
Quando processar vários recursos, utilizando um conjunto comum de tarefas, utilize um JobTemplate para especificar as predefinições de tarefas padrão, ou para definir a ordem das tarefas.

O exemplo seguinte mostra como criar um JobTemplate com um TaskTemplate que é definidos inline. O TaskTemplate utiliza o Media Encoder Standard como o MediaProcessor para codificar o ficheiro de elemento. No entanto, outros MediaProcessors pode ser usado também.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Ao contrário de outras entidades de serviços de multimédia, tem de definir um novo identificador GUID para cada TaskTemplate e colocá-lo no taskTemplateId e a propriedade do Id no seu corpo de pedido. O esquema de identificação de conteúdo tem de seguir o esquema descrito em identificar entidades de serviços de multimédia do Azure. Além disso, não é possível atualizar JobTemplates. Em vez disso, tem de criar um novo com as suas alterações atualizadas.
>
>

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


O exemplo seguinte mostra como criar uma tarefa que faça referência a um Id de JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Funcionalidades de codificação avançadas para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Recortar vídeos durante a codificação](media-services-crop-video.md)
* [Personalizando as configurações predefinidas de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobrepor ou um vídeo com uma imagem de marca d'água](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como criar uma tarefa para codificar um recurso, consulte [como verificar o progresso da tarefa com os Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Consulte também
[Obter processadores de multimédia](media-services-rest-get-media-processor.md)
