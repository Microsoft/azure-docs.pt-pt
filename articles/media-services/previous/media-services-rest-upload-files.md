---
title: Carregar ficheiros para uma conta de Media Services do Azure com REST | Documentos da Microsoft
description: Saiba como obter o conteúdo de mídia para os serviços multimédia criando e carregando ativos.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: dc9acae1e4d31e1456cd971fdab2745e7c58e910
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892681"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar ficheiros para uma conta de serviços de multimédia com REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. O [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) entidade pode conter vídeo, áudio, imagens, coleções de miniaturas, texto faixas e legendagem de áudio ficheiros (e os metadados relativos a esses ficheiros.)  Assim que os ficheiros são carregados para o elemento, o conteúdo estiver armazenado em segurança na cloud para processamento adicional e transmissão em fluxo. 

Neste tutorial, saiba como carregar um ficheiro e a outra operação associados a ele:

> [!div class="checklist"]
> * Configurar o Postman para todas as operações de carregamento
> * Ligar aos Media Services 
> * Criar uma política de acesso com permissão de escrita
> * Criar um elemento
> * Criar um localizador SAS e crie o URL de carregamento
> * Carregar um ficheiro para o armazenamento de Blobs com o URL de carregamento
> * Criar uma metadados no ativo para o ficheiro de suporte de dados que carregou

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).
- Reveja os [aceder ao suporte de dados de API dos serviços Azure com a descrição geral da autenticação do AAD](media-services-use-aad-auth-to-access-ams-api.md) artigo.
- Configurar **Postman** conforme descrito na [chama de configurar o Postman para a API de REST dos serviços de multimédia](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerações

As seguintes considerações aplicam-se ao utilizar a API de REST dos serviços de multimédia:
 
* Ao aceder a entidades com a API de REST de serviços de suporte de dados, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md). <br/>A coleção do Postman utilizada neste tutorial assume o controlo de configuração de todos os cabeçalhos necessários.
* Serviços de multimédia utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para o conteúdo de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, por cento de codificação não é permitida. O valor do **Name** propriedade não pode ter qualquer um dos seguintes [carateres por cento de codificação-reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro.
* O comprimento do nome não deve ser superior a 260 carateres.
* Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.

## <a name="set-up-postman"></a>Configurar o Postman

Para obter passos sobre como configurar o Postman para este tutorial, veja [configurar o Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

1. Adicione valores de ligação ao seu ambiente. 

    Algumas variáveis que fazem parte do **MediaServices** [ambiente](postman-environment.md) tem de ser definido manualmente antes de iniciar a execução de operações definidas no [coleção](postman-collection.md).

    Para obter os valores para as primeiras cinco variáveis, consulte o artigo [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-env.png)
2. Especifique o valor para o **MediaFileName** variável de ambiente.

    Especifique o nome de ficheiro do suporte de dados que estiver a planear para carregar. Neste exemplo, vamos carregar o BigBuckBunny.mp4. 
3. Examine os **AzureMediaServices.postman_environment.json** ficheiro. Verá que quase todas as operações na coleção de executar um script de "teste". Os scripts demorar alguns valores devolvidos pela resposta e definir variáveis de ambiente apropriadas.

    Por exemplo, a primeira operação obtém um token de acesso e defini-lo sobre a **AccessToken** variável de ambiente que é utilizado em todas as outras operações.

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
4. À esquerda dos **Postman** janela, clique na **1. Obtenha o token de autenticação do AAD** -> **obter do Azure AD Token para o Principal de serviço**.

    A parte do URL é preenchida com o **AzureADSTSEndpoint** variável de ambiente (anteriormente no tutorial, se definir os valores das variáveis de ambiente que suportam a coleção).

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postment-get-token.png)

5. Prima **Enviar**.

    Pode ver a resposta que contém "access_token". O script de "teste" usa esse valor e define a **AccessToken** variável de ambiente (conforme descrito acima). Se examinar as variáveis de ambiente, verá que essa variável contém agora o valor de token (token de portador) de acesso que é utilizado no resto das operações. 

    Se o token expirar percorra novamente o passo "Obter do Azure AD Token para o serviço Principal". 

## <a name="create-an-access-policy-with-write-permission"></a>Criar uma política de acesso com permissão de escrita

### <a name="overview"></a>Descrição geral 

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Antes de carregar todos os ficheiros no armazenamento de BLOBs, definiu o acesso a direitos de política para escrever para um elemento. Para fazê-lo, PUBLIQUE uma solicitação HTTP para o conjunto de entidades AccessPolicies. Definir um valor de DurationInMinutes após a criação ou receber uma mensagem de erro de servidor interno 500 postback em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Criar uma política de acesso

1. Selecione **AccessPolicy** -> **criar AccessPolicy para o Upload**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-access-policy.png)

    O script de "teste" obtém o Id de AccessPolicy e define a variável de ambiente apropriadas.

## <a name="create-an-asset"></a>Criar um elemento

### <a name="overview"></a>Descrição geral

Uma [asset](https://docs.microsoft.com/rest/api/media/operations/asset) é um contentor para vários tipos ou conjuntos de objetos nos serviços de multimédia, incluindo o vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e ficheiros de legendagem de áudio. Na API do REST, a criação de um recurso requer enviar o pedido POST para serviços de multimédia e colocar todas as informações sobre o recurso de propriedade no corpo do pedido.

Uma das propriedades que podem ser adicionados ao criar um recurso está **opções**. Pode especificar uma das seguintes opções de encriptação: **NONE** (o padrão, sem encriptação é utilizado), **StorageEncrypted** (para o conteúdo que foi previamente encriptado com encriptação de armazenamento do lado do cliente), **CommonEncryptionProtected**, ou  **EnvelopeEncryptionProtected**. Quando tiver um recurso de encriptados, tem de configurar uma política de entrega. Para obter mais informações, consulte [configurar políticas de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

Se o seu elemento é criptografado, tem de criar uma **ContentKey** e ligá-lo ao seu elemento, conforme descrito no seguinte artigo: [Como criar um ContentKey](media-services-rest-create-contentkey.md). Depois de carregar os ficheiros no elemento, tem de atualizar as propriedades de encriptação no **AssetFile** entidade com os valores obtidos durante a **Asset** encriptação. Fazê-lo através da **intercalar** pedido HTTP. 

Neste exemplo, estamos a criar um recurso não encriptado. 

### <a name="create-an-asset"></a>Criar um elemento

1. Selecione **ativos** -> **criar elemento**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-asset.png)

    O script de "teste" obtém o Id de recurso e define a variável de ambiente apropriadas.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Criar um localizador SAS e crie o URL de carregar

### <a name="overview"></a>Descrição geral

Assim que tiver o AccessPolicy e localizador definido, o arquivo real é carregado para um contentor de armazenamento de Blobs do Azure com as APIs de REST de armazenamento do Azure. Tem de carregar os ficheiros como blobs de blocos. Blobs de páginas não são suportados pelos serviços de multimédia do Azure.  

Para obter mais informações sobre como trabalhar com blobs de armazenamento do Azure, consulte [API de REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Para receber o URL de carregamento real, crie um localizador SAS (mostrado abaixo). Os localizadores definem a hora de início e o tipo de ponto final de ligação para clientes que desejam acessar arquivos num elemento. Pode criar várias entidades de localizador para um determinado par AccessPolicy e ativo processar pedidos de cliente diferentes e necessidades. Cada um desses localizadores usa o valor de StartTime e o valor de DurationInMinutes do AccessPolicy para determinar o período de tempo que pode ser utilizado um URL. Para obter mais informações, consulte [localizador](https://docs.microsoft.com/rest/api/media/operations/locator).

Um URL de SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Considerações

São aplicáveis algumas considerações:

* Não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. Para obter mais informações, consulte o localizador.
* Se precisar de carregar os ficheiros imediatamente, deverá definir o valor de StartTime para cinco minutos antes da hora atual. Isso é porque pode haver relógio distorção entre o computador cliente e os serviços de multimédia. Além disso, o valor de StartTime tem de estar no seguinte formato de DateTime: AAAA-MM-: ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um segundo 30-40 atrasar depois de um localizador é criado para quando estiver disponível para utilização.

### <a name="create-a-sas-locator"></a>Criar um localizador SAS

1. Selecione **localizador** -> **criar localizador SAS**.
2. Prima **Enviar**.

    O script de "teste" cria o "URL de carregar" com base no nome de ficheiro do suporte de dados que especificou e informações de localizador SAS e define a variável de ambiente apropriadas.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um ficheiro para o armazenamento de Blobs com o URL de carregamento

### <a name="overview"></a>Descrição geral

Agora que tem o URL de carregamento, terá de escrever alguns códigos utilizando as APIs de Blobs do Azure diretamente para carregar o ficheiro para o contentor SAS. Para obter mais informações, veja os artigos seguintes:

- [Utilizar o REST API de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [COLOCAR o Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Carregar blobs no armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Carregar um ficheiro com o Postman

Por exemplo, vamos utilizar o Postman para carregar um ficheiro. mp4 pequeno. Pode haver um limite de tamanho de ficheiro sobre o carregamento do binário através do Postman.

O pedido de carregamento não é parte dos **AzureMedia** coleção. 

Criar e configurar um novo pedido:
1. Prima **+**, para criar um novo separador do pedido.
2. Selecione **colocar** operação e colar **{{UploadURL}}** no URL.
2. Deixe **autorização** separador como está (não defini-lo o **Token de portador**).
3. Na **cabeçalhos** separador, especifique: **Chave**: "x-ms-BLOBs-type" e **valor**: "BlockBlob".
2. Na **corpo** separador, clique em **binário**.
4. Escolha o ficheiro com o nome que especificou na **MediaFileName** variável de ambiente.
5. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Criar uma metadados no elemento

Depois do ficheiro foi carregado, terá de criar uma metadados no ativo para o ficheiro de suporte de dados que carregou para o armazenamento de blob associado com o seu elemento.

1. Selecione **AssetFiles** -> **CreateFileInfos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-file-info.png)

O ficheiro deve ser carregado e definir seus metadados.

## <a name="validate"></a>Validação

Para validar o ficheiro foi carregado com êxito, convém consultar o [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) e comparar o **ContentFileSize** (ou outros detalhes) para o que esperar ver no recurso de novo. 

Por exemplo, o seguinte procedimento **obter** operação leva dados de ficheiro para o ficheiro de elemento (no ou caso, o ficheiro de BigBuckBunny.mp4). A consulta está a utilizar o [variáveis de ambiente](postman-environment.md) que definiu anteriormente.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Resposta irá conter o tamanho, o nome e outras informações.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Passos Seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

