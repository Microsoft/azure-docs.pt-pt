---
title: Carregar arquivos em uma conta dos serviços de mídia do Azure usando REST | Microsoft Docs
description: Saiba como obter conteúdo de mídia nos serviços de mídia criando e carregando ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888590"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar arquivos em uma conta dos serviços de mídia usando REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados no ativo, seu conteúdo é armazenado com segurança na nuvem para processamento e streaming adicionais. 

Neste tutorial, você aprenderá a carregar um arquivo e outra operação associada a ele:

> [!div class="checklist"]
> * Configurar o postmaster para todas as operações de upload
> * Ligar aos Media Services 
> * Criar uma política de acesso com permissão de gravação
> * Criar um ativo
> * Criar um localizador SAS e criar a URL de carregamento
> * Carregar um arquivo no armazenamento de BLOBs usando a URL de carregamento
> * Criar metadados no ativo para o arquivo de mídia carregado

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta dos serviços de mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
- Examine o artigo [visão geral da autenticação de serviços de mídia do Azure com o AAD Authentication](media-services-use-aad-auth-to-access-ams-api.md) .
- Além disso, para obter mais informações, consulte o artigo [usar a autenticação do Azure ad para acessar a API dos serviços de mídia com REST](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad) .
- Configure o **postmaster** conforme descrito em [Configurar o postmaster para chamadas à API REST dos serviços de mídia](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerações

As seguintes considerações se aplicam ao usar a API REST dos serviços de mídia:
 
* Ao acessar entidades usando a API REST dos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md). <br/>A coleção de postmaster usada neste tutorial cuida da definição de todos os cabeçalhos necessários.
* Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao compilar URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}. Origin. mediaservices. Windows. net/{GUID}/{IAssetFile. Name}/streamingparameters.) Por esse motivo, a codificação por porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres reservados para codificação de porcentagem](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ & = + $,/?% # [] ". Além disso, só pode haver um '. ' para a extensão de nome de arquivo.
* O comprimento do nome não deve ser maior que 260 caracteres.
* Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.

## <a name="set-up-postman"></a>Configurar o Postman

Para obter as etapas sobre como configurar o postmaster para este tutorial, consulte [Configurar o postmaster](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

1. Adicione valores de conexão ao seu ambiente. 

    Algumas variáveis que fazem parte do [ambiente](postman-environment.md) do **mediaservices** precisam ser definidas manualmente antes que você possa iniciar a execução de operações definidas na [coleção](postman-collection.md).

    Para obter valores para as primeiras cinco variáveis, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-env.png)
2. Especifique o valor para a variável de ambiente **nomedoarquivodemídia** .

    Especifique o nome do arquivo da mídia que você está planejando carregar. Neste exemplo, vamos carregar o BigBuckBunny. mp4. 
3. Examine o arquivo **AzureMediaServices. postman_environment. JSON** . Você verá que quase todas as operações na coleção executam um script de "teste". Os scripts usam alguns valores retornados pela resposta e definem as variáveis de ambiente apropriadas.

    Por exemplo, a primeira operação obtém um token de acesso e o define na variável de ambiente **AccessToken** que é usada em todas as outras operações.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. À esquerda da janela do **postmaster** , clique em **1. Obtenha o token de autenticação do AAD** -> **obter o token do Azure ad para a entidade de serviço**.

    A parte da URL é preenchida com a variável de ambiente **AzureADSTSEndpoint** (anteriormente no tutorial, você define os valores das variáveis de ambiente que oferecem suporte à coleção).

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postment-get-token.png)

5. Prima **Enviar**.

    Você pode ver a resposta que contém "access_token". O script de "teste" usa esse valor e define a variável de ambiente **AccessToken** (conforme descrito acima). Se você examinar suas variáveis de ambiente, verá que essa variável agora contém o valor de token de acesso (token de portador) que é usado no restante das operações. 

    Se o token expirar, passe a etapa "obter token do Azure AD para entidade de serviço" novamente. 

## <a name="create-an-access-policy-with-write-permission"></a>Criar uma política de acesso com permissão de gravação

### <a name="overview"></a>Descrição geral 

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Antes de carregar todos os arquivos no armazenamento de BLOBs, defina os direitos de política de acesso para gravação em um ativo. Para fazer isso, poste uma solicitação HTTP para o conjunto de entidades AccessPolicies. Defina um valor DurationInMinutes na criação ou receba uma mensagem de erro do servidor interno 500 novamente em resposta. Para obter mais informações sobre o AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Criar uma política de acesso

1. Selecione **AccessPolicy** -> **criar AccessPolicy para upload**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-access-policy.png)

    O script de "teste" Obtém a ID de AccessPolicy e define a variável de ambiente apropriada.

## <a name="create-an-asset"></a>Criar um ativo

### <a name="overview"></a>Descrição geral

Um [ativo](https://docs.microsoft.com/rest/api/media/operations/asset) é um contêiner para vários tipos ou conjuntos de objetos nos serviços de mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada. Na API REST, a criação de um ativo requer o envio de solicitação POST para os serviços de mídia e a inserção de qualquer informação de propriedade sobre o ativo no corpo da solicitação.

Uma das propriedades que você pode adicionar ao criar um ativo é **Opções**. Você pode especificar uma das seguintes opções de criptografia: **nenhuma** (padrão, nenhuma criptografia é usada), **StorageEncrypted** (para o conteúdo que foi previamente criptografado com criptografia de armazenamento do lado do cliente), **CommonEncryptionProtected**ou  **EnvelopeEncryptionProtected**. Quando você tem um ativo criptografado, precisa configurar uma política de entrega. Para obter mais informações, consulte [Configurando políticas de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

Se o ativo for criptografado, você deverá criar um **ContentKey** e vinculá-lo ao seu ativo, conforme descrito no seguinte artigo: [como criar um ContentKey](media-services-rest-create-contentkey.md). Depois de carregar os arquivos no ativo, você precisa atualizar as propriedades de criptografia na entidade **assetfile** com os valores obtidos durante a criptografia do **ativo** . Faça isso usando a solicitação HTTP **Merge** . 

Neste exemplo, estamos criando um ativo não criptografado. 

### <a name="create-an-asset"></a>Criar um ativo

1. Selecione **ativos** -> **criar ativo**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-asset.png)

    O script de "teste" Obtém a ID do ativo e define a variável de ambiente apropriada.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Criar um localizador SAS e criar a URL de carregamento

### <a name="overview"></a>Descrição geral

Depois que o AccessPolicy e o localizador forem definidos, o arquivo real será carregado em um contêiner de armazenamento de BLOBs do Azure usando as APIs REST do armazenamento do Azure. Você deve carregar os arquivos como BLOBs de blocos. Não há suporte para BLOBs de páginas nos serviços de mídia do Azure.  

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Para receber a URL de upload real, crie um localizador SAS (mostrado abaixo). Os localizadores definem a hora de início e o tipo de ponto de extremidade de conexão para clientes que desejam acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de AccessPolicy e ativos para lidar com diferentes solicitações e necessidades de clientes. Cada um desses localizadores usa o valor StartTime mais o valor DurationInMinutes de AccessPolicy para determinar o período de tempo que uma URL pode ser usada. Para obter mais informações, consulte [localizador](https://docs.microsoft.com/rest/api/media/operations/locator).

Uma URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Considerações

São aplicáveis algumas considerações:

* Você não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. Para obter mais informações, consulte localizador.
* Se você precisar carregar seus arquivos imediatamente, defina seu valor de StartTime como cinco minutos antes da hora atual. Isso ocorre porque pode haver uma distorção de relógio entre o computador cliente e os serviços de mídia. Além disso, o valor de StartTime deve estar no seguinte formato DateTime: YYYY-MM-DDTHH: mm: ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um atraso de 30-40 segundos após a criação de um localizador quando ele estiver disponível para uso.

### <a name="create-a-sas-locator"></a>Criar um localizador SAS

1. Selecione o **localizador** -> **criar localizador SAS**.
2. Prima **Enviar**.

    O script de "teste" cria a "URL de carregamento" com base no nome do arquivo de mídia especificado e nas informações do localizador SAS e define a variável de ambiente apropriada.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um arquivo no armazenamento de BLOBs usando a URL de carregamento

### <a name="overview"></a>Descrição geral

Agora que você tem a URL de carregamento, precisa escrever um código usando as APIs de blob do Azure diretamente para carregar o arquivo no contêiner SAS. Para obter mais informações, veja os artigos seguintes:

- [Usando a API REST do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [COLOCAR blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Carregar BLOBs no armazenamento de BLOBs](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Carregar um arquivo com o postmaster

Por exemplo, usamos o postmaster para carregar um arquivo. mp4 pequeno. Pode haver um limite de tamanho de arquivo ao carregar o binário por meio do postmaster.

A solicitação de carregamento não faz parte da coleção **AzureMedia** . 

Criar e configurar uma nova solicitação:
1. Pressione **+** para criar uma nova guia de solicitação.
2. Selecione **colocar** operação e cole **{{UploadURL}}** na URL.
2. Deixe a guia **autorização** como está (não a defina como o **token de portador**).
3. Na guia **cabeçalhos** , especifique: **chave**: "x-ms-blob-Type" e **valor**: "BlockBlob".
2. Na guia **corpo** , clique em **binário**.
4. Escolha o arquivo com o nome que você especificou na variável de ambiente **nomedoarquivodemídia** .
5. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Criar metadados no ativo

Depois que o arquivo tiver sido carregado, você precisará criar um metadados no ativo para o arquivo de mídia que você carregou no armazenamento de blob associado ao seu ativo.

1. Selecione **AssetFiles** -> **createfileinfos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-file-info.png)

O arquivo deve ser carregado e seu conjunto de metadados.

## <a name="validate"></a>Validação

Para validar que o arquivo foi carregado com êxito, talvez você queira consultar o [assetfile](https://docs.microsoft.com/rest/api/media/operations/assetfile) e comparar o **ContentFileSize** (ou outros detalhes) com o que você espera ver no novo ativo. 

Por exemplo, a operação **Get** a seguir traz dados de arquivo para seu arquivo de ativo (no caso, o arquivo BigBuckBunny. mp4). A consulta está usando as [variáveis de ambiente](postman-environment.md) que você definiu anteriormente.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

A resposta conterá o tamanho, o nome e outras informações.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

