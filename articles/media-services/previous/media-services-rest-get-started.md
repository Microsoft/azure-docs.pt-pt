---
title: Introdução à entrega de conteúdos a pedido com REST | Documentos da Microsoft
description: Este tutorial explica-lhe os passos para implementar uma aplicação de entrega de conteúdos a pedido com os serviços de multimédia do Azure com a REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d1453a558032e458e89c724e30472cc06f9cf33c
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295135"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introdução à entrega de conteúdos a pedido com REST  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este guia de introdução explica os passos para implementar uma aplicação de entrega de conteúdos de vídeo a pedido (VoD) utilizando as APIs REST do Azure Media Services (AMS).

O tutorial apresenta o fluxo de trabalho básico dos Media Services, os objetos de programação mais comuns e as tarefas necessárias para o desenvolvimento dos Media Services. Após a conclusão do tutorial, é possível transmitir ou transferir progressivamente um ficheiro de suporte de dados de exemplo que carregou, codificou e transferiu.

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver aplicações VoD contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários para começar a desenvolver com os serviços de multimédia com as APIs REST.

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).
* Compreensão de como programar com a API de REST dos serviços de multimédia. Para obter mais informações, consulte [descrição geral da API de REST de serviços de multimédia](media-services-rest-how-to-use.md).
* Uma aplicação à sua escolha, que pode enviar solicitações e respostas HTTP. Este tutorial utiliza [Fiddler](https://www.telerik.com/download/fiddler).

As seguintes tarefas são apresentadas neste guia de introdução.

1. Iniciar pontos finais de transmissão em fluxo (com o portal do Azure).
2. Ligar à conta de serviços de multimédia com a REST API.
3. Criar um novo elemento e carregar um ficheiro de vídeo com a REST API.
4. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável com a REST API.
5. Publique o elemento e obter transmissão em fluxo de transferência progressiva e URLs com a REST API.
6. Reproduzir os conteúdos.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver a utilizar sempre os mesmos dias / permissões, por exemplo, políticas para localizadores que pretendam permanecem em vigor durante muito tempo (políticas de não carregamento) de acesso. Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Para obter detalhes sobre as entidades de REST do AMS usadas neste artigo, consulte [referência da API do REST do Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Além disso, veja [conceitos de Media Services do Azure](media-services-concepts.md).

>[!NOTE]
>Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos finais de transmissão em fluxo com o portal do Azure

Ao trabalhar com os serviços de multimédia do Azure, um dos cenários mais comuns é a entrega de vídeo através de transmissão em fluxo de velocidade de transmissão adaptável. Os Serviços de Multimédia fornecem um empacotamento dinâmico, o que lhe permite entregar os seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Serviços de Multimédia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter de armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Para iniciar o ponto final de transmissão em fluxo, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Na janela Definições, clique em Pontos finais de transmissão em fluxo.
3. Clique no ponto final de transmissão em fluxo predefinido.

    A janela DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO é apresentada.

4. Clique no ícone Início.
5. Clique no botão Guardar para guardar as alterações.

## <a id="connect"></a>Ligar à conta de serviços de multimédia com a REST API

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Criar um novo elemento e carregar um ficheiro de vídeo com a REST API

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. O **Asset** entidade pode conter vídeo, áudio, imagens, coleções de miniaturas, texto faixas e legendagem de áudio ficheiros (e os metadados relativos a esses ficheiros.)  Assim que os ficheiros são carregados para o elemento, o conteúdo estiver armazenado em segurança na cloud para processamento adicional e transmissão em fluxo.

É um dos valores que tem de fornecer durante a criação de um recurso de opções de criação do elemento. O **opções** propriedade é um valor de enumeração que descreve as opções de encriptação que um recurso pode ser criado com. Um valor válido é um dos valores da lista abaixo, não uma combinação de valores desta lista:

* **NONE** = **0** -sem encriptação é utilizada. Ao utilizar esta opção não está protegido seu conteúdo em trânsito ou inativos no armazenamento.
    Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção.
* **StorageEncrypted** = **1** - criptografa o seu conteúdo transparente localmente utilizando a encriptação de AES-256 bits e, em seguida, carrega-o para o armazenamento do Azure onde estão armazenados encriptados em inatividade. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
* **CommonEncryptionProtected** = **2** -Utilize esta opção se estiver a carregar o conteúdo que já tenha sido encriptado e protegido com encriptação comum ou PlayReady DRM (por exemplo, transmissão em fluxo uniforme protegido com o PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Os ficheiros tem de ter sido codificados e encriptados pelo Gestor de transformação.

### <a name="create-an-asset"></a>Criar um elemento
Um recurso é um contentor para vários tipos ou conjuntos de objetos nos serviços de multimédia, incluindo o vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e ficheiros de legendagem de áudio. Na API do REST, a criação de um recurso requer enviar o pedido POST para serviços de multimédia e colocar todas as informações sobre o recurso de propriedade no corpo do pedido.

O exemplo seguinte mostra como criar um elemento.

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Criar um AssetFile
O [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entidade representa um ficheiro de vídeo ou áudio que é armazenado num contentor de Blobs. Um ficheiro de elemento é sempre associado a um elemento e um elemento pode conter um ou muitos AssetFiles. A tarefa de codificador de serviços de multimédia falha se um objeto de ficheiro de recurso não está associado um ficheiro digital num contentor de Blobs.

Depois de carregar o ficheiro de multimédia digital para um contentor de BLOBs, utilize o **intercalar** pedido HTTP para atualizar o AssetFile com informações sobre seu arquivo de mídia (conforme mostrado posteriormente neste tópico).

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criando o AccessPolicy com permissão de escrita
Antes de carregar todos os ficheiros no armazenamento de BLOBs, definiu o acesso a direitos de política para escrever para um elemento. Para fazê-lo, PUBLIQUE uma solicitação HTTP para o conjunto de entidades AccessPolicies. Definir um valor de DurationInMinutes após a criação ou receber uma mensagem de erro de servidor interno 500 postback em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

O exemplo seguinte mostra como criar um AccessPolicy:

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Resposta de HTTP**

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obter o URL de carregamento

Para receber o URL de carregamento real, crie um localizador SAS. Os localizadores definem a hora de início e o tipo de ponto final de ligação para clientes que desejam acessar arquivos num elemento. Pode criar várias entidades de localizador para um determinado par AccessPolicy e ativo processar pedidos de cliente diferentes e necessidades. Cada um desses localizadores usa o valor de StartTime e o valor de DurationInMinutes do AccessPolicy para determinar o período de tempo que pode ser utilizado um URL. Para obter mais informações, consulte [localizador](https://docs.microsoft.com/rest/api/media/operations/locator).

Um URL de SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

São aplicáveis algumas considerações:

* Não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 
* Se precisar de carregar os ficheiros imediatamente, deverá definir o valor de StartTime para cinco minutos antes da hora atual. Isso é porque pode haver relógio distorção entre o computador cliente e os serviços de multimédia. Além disso, o valor de StartTime tem de estar no seguinte formato de DateTime: AAAA-MM-: ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um segundo 30-40 atrasar depois de um localizador é criado para quando estiver disponível para utilização. Este problema aplica-se a ambos [URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) e localizadores de origem.

O exemplo seguinte mostra como criar um localizador de URL de SAS, conforme definido pela propriedade de tipo no corpo do pedido ("1" para um localizador SAS) e "2" para um localizador de origem a pedido. O **caminho** propriedade devolvida contém o URL que tem de utilizar para carregar o ficheiro.

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta de HTTP**

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregue um ficheiro para um contentor de armazenamento de BLOBs
Assim que tiver o AccessPolicy e localizador definido, o arquivo real é carregado para um contentor de armazenamento de Blobs do Azure com as APIs de REST de armazenamento do Azure. Tem de carregar os ficheiros como blobs de blocos. Blobs de páginas não são suportados pelos serviços de multimédia do Azure.  

> [!NOTE]
> Tem de adicionar o nome de ficheiro para o ficheiro que pretende carregar para o localizador **caminho** valor recebido na secção anterior. Por exemplo, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API de REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Atualizar o AssetFile
Agora que carregar o ficheiro, Atualize as informações de FileAsset tamanho (e outros). Por exemplo:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Elimine o localizador e AccessPolicy
**Pedido de HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte:

    HTTP/1.1 204 No Content
    ...

**Pedido de HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável

Após a ingestão de que ativos para os serviços de multimédia, suporte de dados podem ser codificados, transmuxed, digital e assim por diante, antes que é entregue aos clientes. Estas atividades são agendadas e executadas em várias instâncias de função de segundo plano para assegurar um elevado desempenho e disponibilidade. Estas atividades são denominadas tarefas, e cada uma é composta por tarefas atómicas que fazem o trabalho real no ficheiro de elemento (para obter mais informações, consulte [tarefa](https://docs.microsoft.com/rest/api/media/operations/job), [tarefa](https://docs.microsoft.com/rest/api/media/operations/task) descrições).

Como foi mencionado anteriormente, ao trabalhar com suporte de dados do Azure, serviços de um dos cenários mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Serviços de multimédia podem empacotar dinamicamente um conjunto de ficheiros MP4 de velocidade de transmissão adaptável dos seguintes formatos: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A seção a seguir mostra como criar uma tarefa que contém uma tarefa de codificação. A tarefa Especifica a transcodificação do ficheiro de mezanino num conjunto de MP4s de velocidade de transmissão adaptável utilizando **Media Encoder Standard**. A secção também mostra como monitorizar o progresso do processamento de trabalho. Quando a tarefa estiver concluída, seria capaz de criar os localizadores que são necessários para obter acesso a seus ativos.

### <a name="get-a-media-processor"></a>Obter um processador de multimédia
Nos Media Services, um processador de multimédia é um componente que processa uma tarefa de processamento específico, como codificação, conversão de formato, encriptar ou desencriptar conteúdos de multimédia. Para a tarefa de codificação mostrada neste tutorial, vamos usar o Media Encoder Standard.

O código a seguir pedidos de id do codificador.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta de HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Criar uma tarefa
Cada tarefa pode ter uma ou mais tarefas, dependendo do tipo de processamento que deseja realizar. Através da API REST, pode criar tarefas e suas tarefas relacionadas em uma das seguintes formas: As tarefas podem ser definidos inline por meio da propriedade de navegação de tarefas em entidades de tarefa ou por meio do processamento em lote OData. O SDK dos serviços de suporte de dados utiliza o processamento em lotes. No entanto, para a legibilidade dos exemplos de código neste artigo, as tarefas são definidos inline. Para obter informações sobre o processamento em lotes, consulte [Open Data Protocol (OData) de processamento de Batch](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

O exemplo seguinte mostra-lhe como criar e publicar uma tarefa com uma que tarefa definida para codificar um vídeo numa resolução específico e qualidade. A seguinte secção de documentação contém a lista de todos os [suas configurações predefinidas de tarefas](https://msdn.microsoft.com/library/mt269960) suportada pelo processador do Media Encoder Standard.  

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Resposta de HTTP**

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Existem algumas coisas importantes a ter em conta em qualquer pedido de tarefa:

* Propriedades de TaskBody tem de utilizar literal XML para definir o número de entrada ou saída ativos que são utilizados pela tarefa. O artigo de tarefas contém a definição do esquema XML para o XML.
* O valor na definição do TaskBody, para cada interna <inputAsset> e <outputAsset> tem de ser definido como JobInputAsset(value) ou JobOutputAsset(value).
* Uma tarefa pode ter vários recursos de saída. Um JobOutputAsset(x) só pode ser utilizado uma vez como uma saída de uma tarefa numa tarefa.
* Pode especificar JobInputAsset ou JobOutputAsset como um recurso de entrada de uma tarefa.
* Tarefas não têm formam um ciclo.
* O parâmetro de valor que passar para JobInputAsset ou JobOutputAsset representa o valor de índice para um recurso. Os recursos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets sobre a definição de entidade de tarefa.

> [!NOTE]
> Como os serviços de multimédia é baseado em OData v3, os ativos individuais em coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um "__metadata: uri" par nome / valor.
>
>

* InputMediaAssets mapeia para uma ou mais recursos que criou nos serviços de multimédia. OutputMediaAssets são criados pelo sistema. Eles não fazem referência um recurso existente.
* OutputMediaAssets podem ser nomeados usando o atributo assetName. Se este atributo não estiver presente, o nome do OutputMediaAsset é seja qual for o valor de texto interno do <outputAsset> elemento é com um sufixo do valor do nome da tarefa ou o valor de Id da tarefa (no caso em que a propriedade de nome não está definida). Por exemplo, se definir um valor para assetName para "Exemplo", a propriedade de nome de OutputMediaAsset seria definida para "Exemplo". No entanto, se não definiu um valor para assetName, mas definir o nome da tarefa para "NewJob", em seguida, o nome de OutputMediaAsset seria "_NewJob JobOutputAsset (valor)".

    O exemplo seguinte mostra como definir o atributo assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Para ativar o encadeamento de tarefa:

  * Uma tarefa tem de ter, pelo menos, duas tarefas
  * Tem de existir pelo menos uma tarefa, cujos comentários são o resultado de outra tarefa no trabalho.

Para obter mais informações, consulte [criação de uma tarefa de codificação com a API de REST de serviços de multimédia](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitor de progresso do processamento
Pode recuperar o estado da tarefa, utilizando a propriedade de estado, conforme mostrado no exemplo a seguir:

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta de HTTP**

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar uma tarefa
Serviços de multimédia permite-lhe cancelar tarefas em execução por meio da função de CancelJob. Esta chamada devolve um código de erro 400 se tentar cancelar uma tarefa quando o estado é cancelado, cancelar, erro ou foi concluída.

O exemplo seguinte mostra como chamar CancelJob.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Se tiver êxito, é devolvido um código de 204 resposta com nenhum corpo de mensagem.

> [!NOTE]
> Deve codificar o URL a id da tarefa (normalmente nb:jid:UUID: somevalue) ao passá-lo como um parâmetro para CancelJob.
>
>

### <a name="get-the-output-asset"></a>Obter elemento de saída
O código seguinte mostra como pedir o recurso de saída de ID.

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta de HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Publique o elemento e obter transmissão em fluxo de transferência progressiva e URLs com a REST API

Para transmitir ou transferir um elemento, primeiro tem de o "publicar" através da criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: Ondemandorigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou transmissão em fluxo uniforme) e localizadores de assinatura de acesso (SAS), utilizados para transferir ficheiros de suporte de dados. 

Depois de criar os localizadores, pode criar os URLs que são utilizados para transmitir ou transferir os ficheiros.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Um URL de transmissão em fluxo para uma Transmissão em Fluxo Uniforme tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um URL de transmissão em fluxo para HLS tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um URL de transmissão em fluxo para MPEG DASH tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Um URL de SAS utilizado para transferir ficheiros tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta secção mostra como realizar as seguintes tarefas necessárias para seus ativos de "publicar".  

* Criar o AccessPolicy com permissão de leitura
* Criação de um URL de SAS para transferir conteúdo
* Criação de um URL de origem para o conteúdo de transmissão em fluxo

### <a name="creating-the-accesspolicy-with-read-permission"></a>Criar o AccessPolicy com permissão de leitura
Antes de transferir ou qualquer conteúdo multimédia de transmissão em fluxo, primeiro defina um AccessPolicy com permissões de leitura e criar a entidade de localizador apropriada que especifica o tipo de mecanismo de entrega de que pretende ativar para seus clientes. Para obter mais informações sobre as propriedades disponíveis, consulte [propriedades de entidade AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

O exemplo seguinte mostra como especificar AccessPolicy para as permissões de leitura para um determinado recurso.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Se tiver êxito, um código de 201 êxito é retornado que descreve a entidade de AccessPolicy que criou. Em seguida, utilize o Id de AccessPolicy juntamente com o Id de recurso do recurso que contém o ficheiro que pretende enviar (por exemplo, um elemento de saída) para criar a entidade de localizador.

> [!NOTE]
> Este fluxo de trabalho básico é o mesmo que o carregamento de um ficheiro quando ingestão de um recurso (como foi discutido no início deste tópico). Além disso, como carregar ficheiros, se (ou seus clientes) tem de aceder aos seus ficheiros imediatamente, defina o valor de StartTime para cinco minutos antes da hora atual. Esta ação é necessária porque pode haver relógio distorção entre o cliente e os serviços de multimédia. O valor de StartTime tem de estar no seguinte formato de DateTime: AAAA-MM-: ssZ (por exemplo, "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Criação de um URL de SAS para transferir conteúdo
O código seguinte mostra como obter um URL que pode ser utilizado para transferir um ficheiro de suporte de dados criado e carregado anteriormente. O AccessPolicy tem o conjunto de permissões de leitura e o caminho de localizador refere-se para um URL de transferência SAS.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Retornado **caminho** propriedade contém o URL de SAS.

> [!NOTE]
> Se baixar conteúdo do armazenamento encriptado, tem manualmente decifrá-la antes de processá-lo, ou utilizar o MediaProcessor de desencriptação de armazenamento numa tarefa de processamento de ficheiros processados de forma a um OutputAsset de saída e, em seguida, transferir a partir desse ativo. Para obter mais informações sobre o processamento, consulte a criação de uma tarefa de codificação com a API de REST de serviços de multimédia. Além disso, os localizadores de URL de SAS não pode ser atualizados depois de terem sido criados. Por exemplo, não é possível reutilizar o localizador mesmo com um valor de StartTime atualizado. Trata-se por conta da forma que são criados os URLs de SAS. Se desejar acessar um recurso para download, depois de um localizador expirou, tem de criar um novo com um novo StartTime.
>
>

### <a name="download-files"></a>Transferir ficheiros
Assim que tiver o AccessPolicy e localizador de definida, pode baixar arquivos usando as APIs de REST de armazenamento do Azure.  

> [!NOTE]
> Tem de adicionar o nome de ficheiro para o ficheiro que pretende transferir para o localizador **caminho** valor recebido na secção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API de REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Como resultado da tarefa de codificação que realizou anteriormente (de codificação num MP4 adaptável conjunto), que tem múltiplos ficheiros de MP4 que pode transferir progressivamente. Por exemplo:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Criar um URL de transmissão em fluxo para transmissão em fluxo conteúdo
O código seguinte mostra como criar um localizador de URL de transmissão em fluxo:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Se tiver êxito, é devolvida a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir um URL de origem de transmissão em fluxo uniforme num leitor de multimédia de transmissão em fluxo, tem de anexar o caminho de propriedade com o nome de Smooth Streaming manifesto do arquivo, seguido por "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir em fluxo HLS, acrescente (formato Format=m3u8-aapl) depois do "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir em fluxo MPEG DASH, acrescente (formato = mpd-time-csf) depois do "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproduza o seu conteúdo
Para transmitir o seu vídeo, utilize o [Leitor dos Media Services do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar as transferências progressivas, cole um URL num browser (por exemplo, IE, Chrome e Safari).

## <a name="next-steps-media-services-learning-paths"></a>Passos Seguintes: Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
