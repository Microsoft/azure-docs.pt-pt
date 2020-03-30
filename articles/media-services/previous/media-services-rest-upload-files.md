---
title: Faça upload de ficheiros numa conta azure media services usando REST [ Microsoft Docs
description: Aprenda a obter conteúdos de mídia nos Media Services criando e carregando ativos.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888590"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia com REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros.)  Uma vez que os ficheiros são enviados para o ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming. 

Neste tutorial, aprende-se a carregar um ficheiro e outra operação associada:

> [!div class="checklist"]
> * Configurar o Carteiro para todas as operações de upload
> * Ligar aos Media Services 
> * Criar uma política de acesso com permissão de escrita
> * Criar um ativo
> * Crie um localizador SAS e crie o URL de upload
> * Faça upload de um ficheiro para o armazenamento de blob usando o URL de upload
> * Crie metadados no ativo para o ficheiro de mídia que carregou

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta Azure Media Services utilizando o portal Azure.](media-services-portal-create-account.md)
- Reveja a [API accessing Azure Media Services com artigo de visão geral da autenticação AAD.](media-services-use-aad-auth-to-access-ams-api.md)
- Também para mais informações Consulte a [autenticação AD De Utilização Azure para aceder à API dos Serviços de Comunicação Social com](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad) artigo REST.
- Configure **Postman** como descrito em [Configure Postman for Media Services REST API chamadas](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerações

As seguintes considerações aplicam-se ao utilizar a API do Media Services REST:
 
* Ao aceder a entidades que utilizam a API do Media Services REST, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md). <br/>A coleção Postman utilizada neste tutorial cuida de definir todos os cabeçalhos necessários.
* Os Serviços de Media utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParâmetros.) Por esta razão, não é permitida a codificação por cento. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)por cento de codificação : !*'(:@&=+$,/%%#].". Além disso, só pode haver um '.' para a extensão do nome do ficheiro.
* O comprimento do nome não deve ser superior a 260 caracteres.
* Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.

## <a name="set-up-postman"></a>Configurar o Postman

Para obter passos sobre como configurar o Carteiro para este tutorial, consulte [Configure Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

1. Adicione valores de ligação ao seu ambiente. 

    Algumas variáveis que fazem parte do [ambiente](postman-environment.md) dos **MediaServices** precisam de ser definidas manualmente antes de poder começar a executar operações definidas na [recolha](postman-collection.md).

    Para obter valores para as cinco primeiras variáveis, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-env.png)
2. Especifique o valor da variável ambiente **MediaFileName.**

    Especifique o nome do ficheiro dos meios de comunicação que está a planear fazer o upload. Neste exemplo, vamos enviar o BigBuckBunny.mp4. 
3. Examine o ficheiro **AzureMediaServices.postman_environment.json.** Verá que quase todas as operações da coleção executam um roteiro de "teste". Os scripts tomam alguns valores devolvidos pela resposta e estabelecem variáveis ambientais apropriadas.

    Por exemplo, a primeira operação obtém um sinal de acesso e define-a na variável ambiente **AccessToken** que é usada em todas as outras operações.

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
4. À esquerda da janela do **Carteiro,** clique no **1. Obtenha a Ficha AAD Auth** -> **Get Azure AD Token para o Diretor de Serviço**.

    A porção de URL é preenchida com a variável ambiental **AzureADSTSEndpoint** (anteriormente no tutorial, você define os valores das variáveis ambientais que suportam a coleção).

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postment-get-token.png)

5. Prima **Enviar**.

    Pode ver a resposta que contém "access_token". O script "test" pega neste valor e define a variável ambiente **AccessToken** (como descrito acima). Se examinar as variáveis ambientais, verá que esta variável contém agora o valor do token de acesso (token do portador) que é usado no resto das operações. 

    Se o símbolo expirar, passe pelo passo "Get Azure AD Token for Service Principal". 

## <a name="create-an-access-policy-with-write-permission"></a>Criar uma política de acesso com permissão de escrita

### <a name="overview"></a>Descrição geral 

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Antes de enviar quaisquer ficheiros para o armazenamento de blob, delineie os direitos de política de acesso para escrever a um ativo. Para tal, PUBLIQUE um pedido http para o conjunto de entidades AccessPolicies. Defina um valor de DuraçãoInMinutes após a criação ou receba uma mensagem de erro do Servidor Interno 500 de volta em resposta. Para obter mais informações sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Criar uma política de acesso

1. Selecione **AccessPolicy** -> **CreatePolicy para upload**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-access-policy.png)

    O script "test" obtém o Id accessPolicy e define a variável ambiente apropriada.

## <a name="create-an-asset"></a>Criar um ativo

### <a name="overview"></a>Descrição geral

Um [ativo](https://docs.microsoft.com/rest/api/media/operations/asset) é um recipiente para vários tipos ou conjuntos de objetos em Media Services, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados. Na Rest API, a criação de um Ativo requer o envio de pedido de CORREIO para os Serviços de Media e a colocação de qualquer informação de propriedade sobre o seu ativo no organismo de pedido.

Uma das propriedades que pode adicionar ao criar um ativo é **opções.** Pode especificar uma das seguintes opções de encriptação: **Nenhuma (padrão, nenhuma** encriptação é usada), **ArmazenamentoEncriptado** (para conteúdo que foi pré-encriptado com encriptação de armazenamento do lado do cliente), **CommonEncryptionProtected**, ou **EnvelopeEncryptionProtected**. Quando se tem um ativo encriptado, é necessário configurar uma política de entrega. Para mais informações, consulte [a Configuração de políticas](media-services-rest-configure-asset-delivery-policy.md)de entrega de ativos.

Se o seu ativo estiver encriptado, deve criar um **ContentKey** e ligá-lo ao seu ativo, conforme descrito no seguinte artigo: [Como criar uma Chave](media-services-rest-create-contentkey.md)de Conteúdo . Depois de fazer o upload dos ficheiros para o ativo, precisa de atualizar as propriedades de encriptação da entidade **AssetFile** com os valores que obteve durante a encriptação **do Ativo.** Faça-o utilizando o pedido **MERGE** HTTP. 

Neste exemplo, estamos a criar um ativo não encriptado. 

### <a name="create-an-asset"></a>Criar um ativo

1. Selecione **Ativos** -> **Criar Ativos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-asset.png)

    O script "test" obtém o Id do ativo e define a variável ambiente apropriada.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Crie um localizador SAS e crie o URL de upload

### <a name="overview"></a>Descrição geral

Uma vez que tenha o conjunto AccessPolicy e Locator, o ficheiro real é enviado para um recipiente de armazenamento Azure Blob utilizando as APIs DE REPOUSO DE ARMAZENAMENTO Azure. Tem de carregar os ficheiros como blocos. As bolhas de página não são suportadas pela Azure Media Services.  

Para obter mais informações sobre o trabalho com as bolhas de armazenamento Azure, consulte [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Para receber o URL de upload real, crie um Localizador SAS (mostrado abaixo). Os localizadores definem o tempo de início e o tipo de ponto final de ligação para clientes que desejam aceder a Ficheiros num Ativo. Pode criar várias entidades localizadoras para um dado par accessPolicy e Asset para lidar com diferentes pedidos e necessidades do cliente. Cada um destes Localizadores utiliza o valor StartTime mais o valor DurationInMinutes da AccessPolicy para determinar o tempo que um URL pode ser usado. Para mais informações, consulte [O Localizador.](https://docs.microsoft.com/rest/api/media/operations/locator)

Um URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Considerações

São aplicáveis algumas considerações:

* Não pode ter mais de cinco localizadores únicos associados a um dado Ativo de uma só vez. Para mais informações, consulte o Locator.
* Se necessitar de fazer o upload dos seus ficheiros imediatamente, deverá definir o seu valor Inicial para cinco minutos antes da hora atual. Isto porque pode haver um desvio de relógio entre a sua máquina cliente e os Serviços de Media. Além disso, o seu valor startTime deve estar no seguinte formato DateTime: YYYY-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um atraso de 30-40 segundos após a criação de um Localizador para quando estiver disponível para utilização.

### <a name="create-a-sas-locator"></a>Criar um localizador SAS

1. Selecione **Locator** -> **Criar localizador SAS**.
2. Prima **Enviar**.

    O script "test" cria o "Upload URL" com base no nome do ficheiro de mídia especificado e na informação do localizador SAS e define a variável ambiental apropriada.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Faça upload de um ficheiro para o armazenamento de blob usando o URL de upload

### <a name="overview"></a>Descrição geral

Agora que tem o URL de upload, precisa de escrever algum código utilizando diretamente as APIs do Azure Blob para fazer o upload do seu ficheiro para o recipiente SAS. Para obter mais informações, veja os artigos seguintes:

- [Utilizar a API REST do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PUT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Faça upload de bolhas para o armazenamento blob](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Faça upload de um ficheiro com o Carteiro

Como exemplo, usamos o Carteiro para carregar um pequeno ficheiro .mp4. Pode haver um limite de tamanho de ficheiro no upload binário através do Carteiro.

O pedido de upload não faz parte da coleção **AzureMedia.** 

Criar e criar um novo pedido:
1. Prima, **+** para criar um novo separador de pedidos.
2. Selecione **operação PUT** e cola **{{UploadURL}}** no URL.
2. Deixe o separador **autorização** tal como está (não o coloque no **Token**bearer ).
3. No separador **Cabeçalhos,** especifique: **Tecla**: "x-ms-blob-type" e **Valor**: "BlockBlob".
2. No separador **Corpo,** clique **em binário**.
4. Escolha o ficheiro com o nome especificado na variável ambiente **MediaFileName.**
5. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Criar metadados no ativo

Uma vez que o ficheiro tenha sido carregado, é necessário criar metadados no ativo para o ficheiro de mídia que fez upload para o armazenamento blob associado ao seu ativo.

1. Selecione **Ficheiros** -> de Ativos**CreateFileInfos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-file-info.png)

O ficheiro deve ser carregado e o seu conjunto de metadados.

## <a name="validate"></a>Validação

Para validar que o ficheiro foi carregado com sucesso, poderá querer consultar o [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) e comparar o **ContentFileSize** (ou outros detalhes) com o que espera ver no novo ativo. 

Por exemplo, a seguinte operação **GET** traz dados de ficheiros para o seu ficheiro de ativos (no caso ou no caso, o ficheiro BigBuckBunny.mp4). A consulta está a usar as [variáveis ambientais](postman-environment.md) que definiu anteriormente.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

A resposta conterá tamanho, nome e outras informações.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

