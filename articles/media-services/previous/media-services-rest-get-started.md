---
title: Introdução ao fornecimento de conteúdo sob demanda usando REST | Microsoft Docs
description: Este tutorial orienta você pelas etapas de implementação de um aplicativo de distribuição de conteúdo sob demanda com os serviços de mídia do Azure usando a API REST.
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
ms.openlocfilehash: f0f9b2c974c0a095719973b1c6173d682718dbbf
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69014862"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introdução ao fornecimento de conteúdo sob demanda usando o REST  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este guia de início rápido orienta você pelas etapas de implementação de um aplicativo de distribuição de conteúdo VoD (vídeo sob demanda) usando as APIs REST dos serviços de mídia do Azure (AMS).

O tutorial apresenta o fluxo de trabalho básico dos Media Services, os objetos de programação mais comuns e as tarefas necessárias para o desenvolvimento dos Media Services. Na conclusão do tutorial, você poderá transmitir ou baixar progressivamente um arquivo de mídia de exemplo que você carregou, codificou e baixou.

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver aplicações VoD contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos a seguir são necessários para iniciar o desenvolvimento com os serviços de mídia com APIs REST.

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).
* Compreensão de como desenvolver com a API REST dos serviços de mídia. Para obter mais informações, consulte [visão geral da API REST dos serviços de mídia](media-services-rest-how-to-use.md).
* Um aplicativo de sua escolha que pode enviar solicitações e respostas HTTP. Este tutorial usa o [Fiddler](https://www.telerik.com/download/fiddler).

As tarefas a seguir são mostradas neste guia de início rápido.

1. Iniciar pontos finais de transmissão em fluxo (com o portal do Azure).
2. Conecte-se à conta dos serviços de mídia com a API REST.
3. Crie um novo ativo e carregue um arquivo de vídeo com a API REST.
4. Codifique o arquivo de origem em um conjunto de arquivos MP4 de taxa de bits adaptável com a API REST.
5. Publique o ativo e obtenha URLs de download progressivo e streaming com a API REST.
6. Reproduzir os conteúdos.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Use a mesma ID de política se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas para localizadores que se destinam a permanecer em vigor por um longo período (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Para obter detalhes sobre as entidades REST do AMS usadas neste artigo, consulte [API REST de serviços de mídia do Azure referência](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Além disso, consulte [conceitos dos serviços de mídia do Azure](media-services-concepts.md).

>[!NOTE]
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos finais de transmissão em fluxo com o portal do Azure

Ao trabalhar com os serviços de mídia do Azure, um dos cenários mais comuns é a entrega de vídeo por meio de streaming de taxa de bits adaptável. Os Serviços de Multimédia fornecem um empacotamento dinâmico, o que lhe permite entregar os seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Serviços de Multimédia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter de armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Para iniciar o ponto final de transmissão em fluxo, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Na janela Definições, clique em Pontos finais de transmissão em fluxo.
3. Clique no ponto final de transmissão em fluxo predefinido.

    A janela DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO é apresentada.

4. Clique no ícone Início.
5. Clique no botão Guardar para guardar as alterações.

## <a id="connect"></a>Conectar-se à conta dos serviços de mídia com a API REST

Para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Criar um novo ativo e carregar um arquivo de vídeo com a API REST

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados no ativo, seu conteúdo é armazenado com segurança na nuvem para processamento e streaming adicionais.

Um dos valores que você precisa fornecer ao criar um ativo são as opções de criação de ativos. A propriedade **Options** é um valor de enumeração que descreve as opções de criptografia com as quais um ativo pode ser criado. Um valor válido é um dos valores da lista abaixo, não uma combinação de valores desta lista:

* Nenhum = **0** -nenhuma criptografia é usada. Ao usar essa opção, seu conteúdo não será protegido em trânsito ou em repouso no armazenamento.
    Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção.
* StorageEncrypted = **1** -criptografa seu conteúdo limpo localmente usando a criptografia AES-256 bits e, em seguida, carrega-o no armazenamento do Azure, onde ele é armazenado criptografado em repouso. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
* CommonEncryptionProtected = **2** – Use essa opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou PlayReady DRM (por exemplo, Smooth streaming protegido com DRM do PlayReady).
* EnvelopeEncryptionProtected = **4** – Use essa opção se você estiver carregando o HLS criptografado com AES. Os arquivos devem ter sido codificados e criptografados pelo Gerenciador de transformação.

### <a name="create-an-asset"></a>Criar um ativo
Um ativo é um contêiner para vários tipos ou conjuntos de objetos nos serviços de mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada. Na API REST, a criação de um ativo requer o envio de solicitação POST para os serviços de mídia e a inserção de qualquer informação de propriedade sobre o ativo no corpo da solicitação.

O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

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


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

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

### <a name="create-an-assetfile"></a>Criar um Assetfile
A [](https://docs.microsoft.com/rest/api/media/operations/assetfile) entidade assetfile representa um arquivo de áudio ou vídeo que é armazenado em um contêiner de BLOB. Um arquivo de ativo sempre é associado a um ativo, e um ativo pode conter um ou vários AssetFiles. A tarefa de codificador de serviços de mídia falhará se um objeto de arquivo de ativo não estiver associado a um arquivo digital em um contêiner de BLOB.

Depois de carregar o arquivo de mídia digital em um contêiner de BLOB, use a solicitação HTTP **Merge** para atualizar o assetfile com informações sobre o arquivo de mídia (como mostrado posteriormente no tópico).

**Solicitação HTTP**

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


**Resposta HTTP**

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


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criando o AccessPolicy com permissão de gravação
Antes de carregar todos os arquivos no armazenamento de BLOBs, defina os direitos de política de acesso para gravação em um ativo. Para fazer isso, poste uma solicitação HTTP para o conjunto de entidades AccessPolicies. Defina um valor DurationInMinutes na criação ou receba uma mensagem de erro do servidor interno 500 novamente em resposta. Para obter mais informações sobre o AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

O exemplo a seguir mostra como criar um AccessPolicy:

**Solicitação HTTP**

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

**Resposta HTTP**

Se for bem-sucedida, a resposta a seguir será retornada:

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

### <a name="get-the-upload-url"></a>Obter a URL de carregamento

Para receber a URL de upload real, crie um localizador SAS. Os localizadores definem a hora de início e o tipo de ponto de extremidade de conexão para clientes que desejam acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de AccessPolicy e ativos para lidar com diferentes solicitações e necessidades de clientes. Cada um desses localizadores usa o valor StartTime mais o valor DurationInMinutes de AccessPolicy para determinar o período de tempo que uma URL pode ser usada. Para obter mais informações, [](https://docs.microsoft.com/rest/api/media/operations/locator)consulte localizador.

Uma URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

São aplicáveis algumas considerações:

* Você não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 
* Se você precisar carregar seus arquivos imediatamente, defina seu valor de StartTime como cinco minutos antes da hora atual. Isso ocorre porque pode haver uma distorção de relógio entre o computador cliente e os serviços de mídia. Além disso, o valor de StartTime deve estar no seguinte formato DateTime: AAAA-MM-DDTHH: mm: ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um atraso de 30-40 segundos após a criação de um localizador quando ele estiver disponível para uso. Esse problema se aplica à [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) e aos localizadores de origem.

O exemplo a seguir mostra como criar um localizador de URL SAS, conforme definido pela propriedade Type no corpo da solicitação ("1" para um localizador SAS e "2" para um localizador de origem sob demanda). A propriedade **path** retornada contém a URL que você deve usar para carregar o arquivo.

**Solicitação HTTP**

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


**Resposta HTTP**

Se for bem-sucedida, a resposta a seguir será retornada:

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

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregar um arquivo em um contêiner de armazenamento de BLOBs
Depois que o AccessPolicy e o localizador forem definidos, o arquivo real será carregado em um contêiner de armazenamento de BLOBs do Azure usando as APIs REST do armazenamento do Azure. Você deve carregar os arquivos como BLOBs de blocos. Não há suporte para BLOBs de páginas nos serviços de mídia do Azure.  

> [!NOTE]
> Você deve adicionar o nome de arquivo para o arquivo que deseja carregar no valor de **caminho** do localizador recebido na seção anterior. Por exemplo, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Atualizar o Assetfile
Agora que você carregou o arquivo, atualize as informações de tamanho do fileasset (e outras). Por exemplo:

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


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Excluir o localizador e o AccessPolicy
**Solicitação HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

**Solicitação HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Codificar o arquivo de origem em um conjunto de arquivos MP4 de taxa de bits adaptável

Depois de ingerir ativos nos serviços de mídia, a mídia pode ser codificada, transmultiplexada, marca d' água e assim por diante, antes de ser entregue aos clientes. Estas atividades são agendadas e executadas em várias instâncias de função de segundo plano para assegurar um elevado desempenho e disponibilidade. Essas atividades são chamadas de trabalhos e cada trabalho é composto por tarefas atômicas que fazem o trabalho real no arquivo de ativo (para obter mais informações, consulte descrições de [tarefas](https://docs.microsoft.com/rest/api/media/operations/task) , [trabalho](https://docs.microsoft.com/rest/api/media/operations/job)).

Como foi mencionado anteriormente, ao trabalhar com os serviços de mídia do Azure, um dos cenários mais comuns é fornecer streaming de taxa de bits adaptável para seus clientes. Os serviços de mídia podem empacotar dinamicamente um conjunto de arquivos MP4 de taxa de bits adaptável em um dos seguintes formatos: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A seção a seguir mostra como criar um trabalho que contém uma tarefa de codificação. A tarefa especifica a transcodificação do arquivo de mezanino em um conjunto de MP4s de taxa de bits adaptável usando **Media Encoder Standard**. A seção também mostra como monitorar o progresso do processamento do trabalho. Quando o trabalho for concluído, você poderá criar localizadores que são necessários para obter acesso aos seus ativos.

### <a name="get-a-media-processor"></a>Obter um processador de mídia
Nos serviços de mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Para a tarefa de codificação mostrada neste tutorial, vamos usar o Media Encoder Standard.

O código a seguir solicita a ID do codificador.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

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
Cada trabalho pode ter uma ou mais tarefas, dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar trabalhos e suas tarefas relacionadas de uma das duas maneiras: As tarefas podem ser definidas embutidas por meio da propriedade de navegação Tasks em entidades de trabalho ou por meio do processamento em lote OData. O SDK dos serviços de mídia usa o processamento em lotes. No entanto, para a legibilidade dos exemplos de código neste artigo, as tarefas são definidas em linha. Para obter informações sobre o processamento em lotes, consulte [protocolo Open Data (OData) processamento em lote](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

O exemplo a seguir mostra como criar e postar um trabalho com uma tarefa definida para codificar um vídeo em uma resolução e qualidade específicas. A seção de documentação a seguir contém a lista de todas as predefinições de [tarefa](https://msdn.microsoft.com/library/mt269960) com suporte pelo processador de Media Encoder Standard.  

**Solicitação HTTP**

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

**Resposta HTTP**

Se for bem-sucedida, a resposta a seguir será retornada:

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


Há algumas coisas importantes a serem observadas em qualquer solicitação de trabalho:

* As propriedades TaskBody devem usar XML literal para definir o número de ativos de entrada ou de saída que são usados pela tarefa. O artigo da tarefa contém a definição de esquema XML para o XML.
* Na definição de TaskBody, cada valor interno para `<inputAsset>` e `<outputAsset>` deve ser definido como JobInputAsset (valor) ou JobOutputAsset (valor).
* Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset (x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
* Você pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
* As tarefas não devem formar um ciclo.
* O parâmetro Value que você passa para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição da entidade do trabalho.

> [!NOTE]
> Como os serviços de mídia são criados no OData v3, os ativos individuais nas coleções de propriedades de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um par nome-valor "__metadata: URI".
>
>

* O InputMediaAssets é mapeado para um ou mais ativos que você criou nos serviços de mídia. OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
* OutputMediaAssets pode ser nomeado usando o atributo assetname. Se esse atributo não estiver presente, o nome do OutputMediaAsset será qualquer que seja o valor de texto interno do `<outputAsset>` elemento com um sufixo do valor do nome do trabalho ou do valor da ID do trabalho (no caso em que a propriedade Name não estiver definida). Por exemplo, se você definir um valor para assetname como "Sample", a propriedade de nome OutputMediaAsset será definida como "Sample". No entanto, se você não definiu um valor para assetname, mas definiu o nome do trabalho como "novotrabalho", o nome OutputMediaAsset seria "JobOutputAsset (value) _NewJob".

    O exemplo a seguir mostra como definir o atributo assetname:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Para habilitar o encadeamento de tarefas:

  * Um trabalho deve ter pelo menos duas tarefas
  * Deve haver pelo menos uma tarefa cuja entrada seja saída de outra tarefa no trabalho.

Para obter mais informações, consulte [criando um trabalho de codificação com a API REST dos serviços de mídia](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitorar o progresso do processamento
Você pode recuperar o status do trabalho usando a propriedade State, conforme mostrado no exemplo a seguir:

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta HTTP**

Se for bem-sucedida, a resposta a seguir será retornada:

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


### <a name="cancel-a-job"></a>Cancelar um trabalho
Os serviços de mídia permitem que você cancele trabalhos em execução por meio da função CancelJob. Essa chamada retornará um código de erro 400 se você tentar cancelar um trabalho quando seu estado for cancelado, cancelando, erro ou concluído.

O exemplo a seguir mostra como chamar CancelJob.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Se for bem-sucedido, um código de resposta 204 será retornado sem corpo de mensagem.

> [!NOTE]
> Você deve codificar a URL da ID do trabalho (normalmente NB: JID: UUID: someValue) ao passá-la como um parâmetro para CancelJob.
>
>

### <a name="get-the-output-asset"></a>Obter o ativo de saída
O código a seguir mostra como solicitar a ID do ativo de saída.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

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

## <a id="publish_get_urls"></a>Publicar o ativo e obter URLs de download progressivo e streaming com a API REST

Para transmitir ou transferir um elemento, primeiro tem de o "publicar" através da criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: Localizadores OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e localizadores de assinatura de acesso (SAS), usados para baixar arquivos de mídia. 

Depois de criar os localizadores, você pode criar as URLs que são usadas para transmitir ou baixar seus arquivos.

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

Esta seção mostra como executar as seguintes tarefas necessárias para "publicar" seus ativos.  

* Criando o AccessPolicy com permissão de leitura
* Criando uma URL SAS para baixar conteúdo
* Criando uma URL de origem para conteúdo de streaming

### <a name="creating-the-accesspolicy-with-read-permission"></a>Criando o AccessPolicy com permissão de leitura
Antes de baixar ou transmitir qualquer conteúdo de mídia, primeiro defina um AccessPolicy com permissões de leitura e crie a entidade do localizador apropriada que especifica o tipo de mecanismo de entrega que você deseja habilitar para seus clientes. Para obter mais informações sobre as propriedades disponíveis, consulte [Propriedades da entidade AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

O exemplo a seguir mostra como especificar o AccessPolicy para permissões de leitura para um determinado ativo.

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

Se for bem-sucedido, um código de êxito 201 será retornado descrevendo a entidade AccessPolicy que você criou. Em seguida, use a ID AccessPolicy junto com a ID do ativo do ativo que contém o arquivo que você deseja entregar (como um ativo de saída) para criar a entidade do localizador.

> [!NOTE]
> Esse fluxo de trabalho básico é o mesmo que carregar um arquivo ao ingerir um ativo (como foi discutido anteriormente neste tópico). Além disso, como o carregamento de arquivos, se você (ou seus clientes) precisarem acessar seus arquivos imediatamente, defina seu valor de StartTime como cinco minutos antes da hora atual. Essa ação é necessária porque pode haver uma distorção de relógio entre o cliente e os serviços de mídia. O valor de StartTime deve estar no seguinte formato DateTime: AAAA-MM-DDTHH: mm: ssZ (por exemplo, "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Criando uma URL SAS para baixar conteúdo
O código a seguir mostra como obter uma URL que pode ser usada para baixar um arquivo de mídia criado e carregado anteriormente. O AccessPolicy tem as permissões de leitura definidas e o caminho do localizador refere-se a uma URL de download de SAS.

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

Se for bem-sucedida, a resposta a seguir será retornada:

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

A propriedade **path** retornada contém a URL SAS.

> [!NOTE]
> Se você baixar conteúdo criptografado de armazenamento, deverá descriptografá-lo manualmente antes de renderizá-lo ou usar o MediaProcessor de descriptografia de armazenamento em uma tarefa de processamento para gerar arquivos processados em Clear para um OutputAsset e, em seguida, baixar desse ativo. Para obter mais informações sobre o processamento, consulte Criando um trabalho de codificação com a API REST dos serviços de mídia. Além disso, os localizadores de URL SAS não podem ser atualizados após terem sido criados. Por exemplo, você não pode reutilizar o mesmo localizador com um valor inicial atualizado. Isso ocorre devido à forma como as URLs SAS são criadas. Se você quiser acessar um ativo para download depois que um localizador tiver expirado, você deverá criar um novo com um novo StartTime.
>
>

### <a name="download-files"></a>Transferir ficheiros
Depois de definir o AccessPolicy e o localizador, você pode baixar arquivos usando as APIs REST do armazenamento do Azure.  

> [!NOTE]
> Você deve adicionar o nome do arquivo para o arquivo que deseja baixar para o valor do **caminho** do localizador recebido na seção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Como resultado do trabalho de codificação que você executou anteriormente (codificação no conjunto de MP4 adaptável), você tem vários arquivos MP4 que podem ser baixados progressivamente. Por exemplo:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Criando uma URL de streaming para conteúdo de streaming
O código a seguir mostra como criar um localizador de URL de streaming:

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

Se for bem-sucedida, a resposta a seguir será retornada:

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

Para transmitir uma URL de origem Smooth Streaming em um player de mídia de streaming, você deve acrescentar a propriedade Path com o nome do arquivo de manifesto Smooth Streaming, seguido por "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir HLS, acrescente (Format = M3U8-AAPL) após "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir MPEG DASH, acrescente (Format = MPD-time-CSF) após o "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproduzir seu conteúdo
Para transmitir o seu vídeo, utilize o [Leitor dos Media Services do Azure](https://aka.ms/azuremediaplayer).

Para testar o download progressivo, Cole uma URL em um navegador (por exemplo, IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Passos Seguintes: Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
