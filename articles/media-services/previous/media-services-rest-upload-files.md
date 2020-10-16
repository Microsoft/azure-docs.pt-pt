---
title: Faça upload de ficheiros numa conta da Azure Media Services usando REST Microsoft Docs
description: Saiba como obter conteúdo sonoro nos Media Services criando e carregando ativos usando REST.
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
ms.openlocfilehash: bc0369e99552859393da206e791477040681ccc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281070"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia com REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. A entidade [Do Ativo](/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros.)  Uma vez que os ficheiros são enviados para o ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming. 

Neste tutorial, aprende-se a fazer o upload de um ficheiro e de outras operações associadas:

> [!div class="checklist"]
> * Configurar o Carteiro para todas as operações de upload
> * Ligar aos Media Services 
> * Criar uma política de acesso com permissão de escrita
> * Criar um recurso
> * Crie um localizador SAS e crie o URL de upload
> * Faça o upload de um ficheiro para o armazenamento de blob usando o URL de upload
> * Crie um metadados no ativo para o ficheiro de mídia que fez o upload

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
- Reveja a API dos Serviços de Media de Acesso com o artigo [Visão Geral de autenticação da AAD.](media-services-use-aad-auth-to-access-ams-api.md)
- Para mais informações Reveja a [autenticação Ad Use Azure para aceder à API dos Serviços de Mídia com](./media-services-rest-connect-with-aad.md) artigo REST.
- Configure **o Carteiro,** conforme descrito no [Configure Postman for Media Services REST API calls](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerações

Aplicam-se as seguintes considerações aquando da utilização da API dos Serviços de Comunicação Social:
 
* Ao aceder a entidades que utilizam a API dos Serviços de Mídia, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md). <br/>A coleção do Carteiro utilizada neste tutorial cuida de definir todos os cabeçalhos necessários.
* Os Serviços de Comunicação Social utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esta razão, não é permitido codificar por cento. O valor da propriedade **Name** não pode ter nenhum dos [seguintes caracteres reservados por cento:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;:@&=+$,/?%#]". Além disso, só pode haver um ''' para a extensão do nome do ficheiro.
* O comprimento do nome não deve ser superior a 260 caracteres.
* Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.

## <a name="set-up-postman"></a>Configurar o Postman

Para obter etapas sobre como configurar o Carteiro para este tutorial, consulte [o Configure Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

1. Adicione valores de ligação ao seu ambiente. 

    Algumas variáveis que fazem parte do [ambiente](postman-environment.md) **mediaServices** precisam de ser definidas manualmente antes de poder começar a executar operações definidas na [coleção](postman-collection.md).

    Para obter valores para as primeiras cinco variáveis, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-env.png)
2. Especifique o valor para a variável **ambiente MediaFileName.**

    Especifique o nome do ficheiro dos meios de comunicação que está a planear enviar. Neste exemplo, vamos carregar o BigBuckBunny.mp4. 
3. Examine a **AzureMediaServices.postman_environment.jsarquivada.** Verá que quase todas as operações da coleção executam um script de "teste". Os scripts tomam alguns valores devolvidos pela resposta e definem variáveis ambientais apropriadas.

    Por exemplo, a primeira operação obtém um token de acesso e coloca-o na variável ambiente **AccessToken** que é usada em todas as outras operações.

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
4. À esquerda da janela do **Carteiro,** clique no **1. Obtenha a AAD Auth token**  ->  **Get Azure AD Token para diretor de serviço.**

    A porção de URL é preenchida com a variável ambiente **AzureADSTSEndpoint** (anteriormente no tutorial, você define os valores das variáveis ambientais que suportam a coleção).

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postment-get-token.png)

5. Prima **Enviar**.

    Pode ver a resposta que contém "access_token". O script "test" toma este valor e define a variável ambiente **AccessToken** (como descrito acima). Se examinar as variáveis ambientais, verá que esta variável contém agora o valor de token de acesso (token ao portador) que é usado no resto das operações. 

    Se o token expirar, passe pelo passo "Get Azure AD Token for Service Principal" novamente. 

## <a name="create-an-access-policy-with-write-permission"></a>Criar uma política de acesso com permissão de escrita

### <a name="overview"></a>Descrição geral 

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Antes de enviar quaisquer ficheiros para o armazenamento de bolhas, descreva os direitos de política de acesso para escrever a um ativo. Para isso, publique um pedido HTTP à entidade AccessPolicies. Defina um valor DurationInMinutes após a criação ou receberá uma mensagem de erro do Servidor Interno de 500 em resposta. Para mais informações sobre AccessPolicies, consulte [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Criar uma política de acesso

1. Selecione **AccessPolicy**  ->  **Create AccessPolicy for Upload**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-access-policy.png)

    O script "test" obtém o Id AccessPolicy e define a variável ambiente apropriada.

## <a name="create-an-asset"></a>Criar um recurso

### <a name="overview"></a>Descrição geral

Um [ativo](/rest/api/media/operations/asset) é um recipiente para vários tipos ou conjuntos de objetos em Serviços de Mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados. Na API REST, a criação de um Ativo requer o envio de um pedido de CORREIO aos Serviços de Comunicação Social e a colocação de qualquer informação de propriedade sobre o seu ativo no organismo de pedido.

Uma das propriedades que pode adicionar ao criar um ativo são **opções.** Pode especificar uma das seguintes opções de encriptação: **Nenhuma** (padrão, nenhuma encriptação é utilizada), **StorageEncrypted** (para conteúdos pré-encriptados com encriptação de armazenamento do lado do cliente), **CommonEncrypationProtegado,** ou **EnvelopeEncrypationProtetado**. Quando se tem um ativo encriptado, é necessário configurar uma política de entrega. Para obter mais informações, consulte [as políticas de entrega de ativos configuradas.](media-services-rest-configure-asset-delivery-policy.md)

Se o seu ativo estiver encriptado, tem de criar um **ContentKey** e ligá-lo ao seu ativo, conforme descrito no seguinte artigo: [Como criar um ContentKey](media-services-rest-create-contentkey.md). Depois de fazer o upload dos ficheiros para o ativo, tem de atualizar as propriedades de encriptação na entidade **AssetFile** com os valores que obteve durante a encriptação **do Ativo.** Faça-o utilizando o pedido **MERGE** HTTP. 

Neste exemplo, estamos a criar um ativo não encriptado. 

### <a name="create-an-asset"></a>Criar um recurso

1. Selecione **Ativos**  ->  **Criar Ativo**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-asset.png)

    O script "test" recebe o ID do Ativo e define a variável ambiente apropriada.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Crie um localizador SAS e crie o URL de upload

### <a name="overview"></a>Descrição geral

Uma vez definido o conjunto AccessPolicy e Localizador, o ficheiro real é enviado para um recipiente de armazenamento Azure Blob usando as APIs de ARMAZENAMENTO Azure Storage REST. Tens de carregar os ficheiros como blocos. As bolhas de página não são suportadas pela Azure Media Services.  

Para obter mais informações sobre o trabalho com as bolhas de armazenamento Azure, consulte [a Blob Service REST API](/rest/api/storageservices/blob-service-rest-api).

Para receber o URL de upload real, crie um localizador SAS (mostrado abaixo). Os localizadores definem a hora de início e o tipo de ponto final de ligação para os clientes que pretendem aceder aos Ficheiros num Ativo. Pode criar várias entidades de Localizador para um dado par accessPolicy e Ativo para lidar com diferentes pedidos e necessidades do cliente. Cada um destes Localizadores utiliza o valor StartTime mais o valor DurationInMinutes da AccessPolicy para determinar o tempo de utilização de um URL. Para mais informações, consulte [o Localizador.](/rest/api/media/operations/locator)

Um URL SAS tem o seguinte formato:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Considerações

São aplicáveis algumas considerações:

* Não é possível ter mais de cinco localizadores únicos associados a um dado Ativo ao mesmo tempo. Para mais informações, consulte o Localizador.
* Se precisar de fazer o upload dos seus ficheiros imediatamente, deverá definir o valor startTime para cinco minutos antes da hora atual. Isto porque pode haver um desvio no relógio entre a máquina do seu cliente e os Serviços de Comunicação Social. Além disso, o seu valor StartTime deve estar no seguinte formato DateTime: YYYYY-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").    
* Pode haver um atraso de 30-40 segundos após a criação de um Localizador para quando estiver disponível para utilização.

### <a name="create-a-sas-locator"></a>Criar um localizador SAS

1. **Selecione localizador**  ->  **criar localizador SAS.**
2. Prima **Enviar**.

    O script "test" cria o "UPLOAD URL" com base no nome de ficheiro de mídia especificado e informações de localizador SAS e define a variável ambiente apropriada.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Faça o upload de um ficheiro para o armazenamento de blob usando o URL de upload

### <a name="overview"></a>Descrição geral

Agora que tem o URL de upload, precisa de escrever algum código utilizando as APIs Azure Blob diretamente para fazer o upload do seu ficheiro para o contentor SAS. Para obter mais informações, veja os artigos seguintes:

- [Utilizar a API REST do Armazenamento do Azure](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PUT Blob](/rest/api/storageservices/put-blob)
- [Carregar bolhas para o armazenamento blob](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Faça upload de um ficheiro com o Carteiro

Como exemplo, usamos o Carteiro para carregar um pequeno ficheiro .mp4. Pode haver um limite de tamanho de ficheiro no carregamento binário através do Carteiro.

O pedido de upload não faz parte da coleção **AzureMedia.** 

Criar e configurar um novo pedido:
1. Pressione **+** , para criar um novo separador de pedidos.
2. Selecione a operação **PUT** e cole **{{UploadURL}}** no URL.
2. Deixe o separador **de autorização** como está (não o coloque no **Token do Portador).**
3. No **separador Cabeçalhos,** especifique: **Tecla**: "x-ms-blob-type" e **Valor**: "BlockBlob".
2. No **separador Corpo,** clique **em binário.**
4. Escolha o ficheiro com o nome especificado na variável **ambiente MediaFileName.**
5. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Criar metadados no ativo

Uma vez que o ficheiro tenha sido carregado, precisa de criar um metadados no ativo para o ficheiro de mídia que carregou no armazenamento de bolhas associado ao seu ativo.

1. Selecione **ActivosFiles**  ->  **CreateFileInfos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-file-info.png)

O ficheiro deve ser carregado e o conjunto de metadados.

## <a name="validate"></a>Validação

Para validar que o ficheiro foi carregado com sucesso, é melhor consultar o [AssetFile](/rest/api/media/operations/assetfile) e comparar o **ContentFileSize** (ou outros detalhes) com o que espera ver no novo ativo. 

Por exemplo, a seguinte operação **GET** traz dados de ficheiro para o seu ficheiro de ativos (em caso ou caso, o ficheiro BigBuckBunny.mp4). A consulta está a usar as [variáveis ambientais](postman-environment.md) que definiu anteriormente.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

A resposta conterá tamanho, nome e outras informações.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
