---
title: Encriptar o seu conteúdo com encriptação de armazenamento através da API REST do AMS
description: Saiba como encriptar o seu conteúdo com encriptação de armazenamento com APIs de REST do AMS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ca7f749a04b569d183589fba8c788ce48f29358b
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295560"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Encriptar o seu conteúdo com encriptação de armazenamento 

É altamente recomendado para encriptar o conteúdo localmente utilizando a encriptação de AES-256 bits e, em seguida, carregue-o ao armazenamento do Azure onde estão armazenados encriptados em inatividade.

Este artigo fornece uma descrição geral da encriptação de armazenamento do AMS e mostra-lhe como carregar o conteúdo do armazenamento criptografado:

* Crie uma chave de conteúdo.
* Crie um elemento. Configurar o AssetCreationOption para StorageEncryption ao criar o elemento.
  
     Encriptados ativos estão associados com chaves de conteúdo.
* Ligar a chave de conteúdo para o elemento.  
* Defina os parâmetros relacionados com a encriptação nas entidades AssetFile.

## <a name="considerations"></a>Considerações 

Se quiser fornecer um recurso de criptografado de armazenamento, é necessário configurar a política de entrega de elementos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado. Para obter mais informações, consulte [configurar políticas de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia|Encriptação AES-256, chave gerida pelos serviços de multimédia|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chave de encriptação do lado do servidor oferecidas pelo armazenamento do Azure, gerida pelo Azure ou pelo cliente|Suportadas|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferecidas pelo armazenamento do Azure, chave gerida pelo cliente no Cofre de chaves de encriptação do lado do cliente|Não suportado|Não suportado|

<sup>1</sup> enquanto os serviços de multimédia oferece suporte à manipulação de conteúdo de forma/sem qualquer outra forma de encriptação, ao fazê-lo por isso, não é recomendado.

<sup>2</sup> em serviços de multimédia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para em versões anteriores compatibilidade quando os recursos foram criados com os serviços de multimédia v2. O que significa v3 funciona com o armazenamento existente encriptado ativos, mas não permitirá que a criação de novos itens.

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Descrição geral da encriptação de armazenamento
Aplica-se da encriptação de armazenamento do AMS **AES-CTR** encriptação do modo para o arquivo inteiro.  O modo de AES-CTR é uma codificação de bloco que pode criptografar os dados de comprimento arbitrário sem necessidade de preenchimento. Ele opera por meio da criptografia um bloco de contador com o algoritmo AES e, em seguida, fazer um XOR gerúndio a saída do AES com os dados para criptografar ou descriptografar.  O bloco de contador utilizado é construído ao copiar o valor da InitializationVector para bytes 0 a 7 do valor de contador e bytes 8 a 15 do valor do contador estão definidos como zero. O bloco de 16 bytes contador bytes 8 a 15 (ou seja, os bytes menos significativos) são utilizados como um inteiro não assinado de 64 bits simple que é incrementado em um para cada bloco subsequente de dados processados e é mantido na ordem de bytes de rede. Se este número inteiro atingir o valor máximo (0xFFFFFFFFFFFFFFFF), em seguida, incrementando-repõe o contador de bloco para zero (bytes 8 a 15) sem afetar as outras partes do contador (ou seja, a bytes 0 a 7) de 64.   Para manter a segurança de criptografia de modo AES CTR, o valor de InitializationVector para um identificador de chave de determinado para cada chave de conteúdo deve ser exclusivo para cada ficheiro e de ficheiros devem ser inferior a 2 ^ 64 blocos de comprimento.  Este valor exclusivo é garantir que um valor de contador jamais é reutilizado com uma determinada chave. Para obter mais informações sobre o modo CTR, consulte [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (o artigo wiki utiliza o termo "Valor de uso único", em vez de "InitializationVector").

Uso **encriptação do armazenamento** para encriptar o seu conteúdo transparente localmente utilizando AES-256 bit criptografia e, em seguida, carregue-o ao armazenamento do Azure onde estão armazenados encriptados em inatividade. Os elementos protegidos com encriptação de armazenamento são desencriptados automaticamente e colocados no sistema de ficheiros encriptados antes da codificação e opcionalmente encriptados novamente antes de carregar novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando deseja proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco.

Para fornecer um recurso de criptografado de armazenamento, é necessário configurar a política de entrega de elementos, para que os serviços de multimédia, saiba como deseja distribuir os seus conteúdos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado (por exemplo, AES, encriptação comum ou sem encriptação).

## <a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys utilizada para encriptação
Encriptados ativos estão associados com as chaves de encriptação de armazenamento. Crie a chave de conteúdo a ser utilizada para encriptação antes de criar os ficheiros de recursos. Esta secção descreve como criar uma chave de conteúdo.

Seguem-se os passos gerais para gerar chaves de conteúdo associado a recursos que pretende que sejam encriptados. 

1. Para a encriptação de armazenamento, gere aleatoriamente uma chave AES de 32 bytes. 
   
    A chave AES de 32 bytes é a chave de conteúdo para o seu elemento, o que significa que todos os ficheiros associados essa necessidade de recurso a utilizar a mesma chave de conteúdo durante a desencriptação. 
2. Chamar o [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) métodos para obter o certificado X.509 correto, que deve ser usado para criptografar a chave de conteúdo.
3. Encripte a chave de conteúdo com a chave pública do certificado X.509. 
   
   SDK .NET dos Media Services utiliza RSA com OAEP quando fazendo a criptografia.  Pode ver um exemplo de .NET no [EncryptSymmetricKeyData função](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Crie um valor de soma de verificação calculado com o identificador de chave e a chave de conteúdo. O exemplo seguinte do .NET calcula a soma de verificação usando a parte GUID de identificador de chave e a chave de conteúdo claro.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Criar a chave de conteúdo com o **EncryptedContentKey** (convertido na cadeia de caracteres codificada em base64), **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, e **soma de verificação** valores que recebeu nos passos anteriores.

    Para a encriptação de armazenamento, as seguintes propriedades devem ser incluídas no corpo do pedido.

    Propriedade do corpo do pedido    | Descrição
    ---|---
    Id | O ID de ContentKey é gerado com o seguinte formato, "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | O tipo de chave de conteúdo é um número inteiro que define a chave. Para o formato de encriptação de armazenamento, o valor é 1.
    EncryptedContentKey | Vamos criar um novo valor de chave conteúdo que é um valor de 256 bits (32 bytes). A chave for encriptada com o certificado X.509 encriptação de armazenamento que obtemos dos serviços de multimédia do Microsoft Azure ao executar um pedido HTTP GET para os métodos de GetProtectionKey e GetProtectionKeyId. Por exemplo, consulte o seguinte código do .NET: os **EncryptSymmetricKeyData** método definido [aqui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Esta é a proteção da chave ID para o certificado X.509 encriptação de armazenamento que foi utilizado para encriptar a nossa chave de conteúdo.
    ProtectionKeyType | Este é o tipo de encriptação da chave de proteção que foi usada para criptografar a chave de conteúdo. Este valor é StorageEncryption(1) para o nosso exemplo.
    Soma de verificação |A MD5 calculada soma de verificação para a chave de conteúdo. Ele é calculado ao encriptar o ID de conteúdo com a chave de conteúdo. O código de exemplo demonstra como calcular a soma de verificação.


### <a name="retrieve-the-protectionkeyid"></a>Obter o ProtectionKeyId
O exemplo seguinte mostra como recuperar o ProtectionKeyId, um thumbprint do certificado, para o certificado que tem de utilizar ao encriptar a chave de conteúdo. Efetue este passo para se certificar de que já tiver o certificado apropriado no seu computador.

Pedido:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Obter o ProtectionKey para o ProtectionKeyId
O exemplo seguinte mostra como obter o certificado X.509 com o ProtectionKeyId recebido no passo anterior.

Pedido:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Criar a chave de conteúdo
Depois de obter o certificado X.509 e utilizada a sua chave pública para criptografar a chave de conteúdo, criar um **ContentKey** entity e defina sua propriedade valores em conformidade.

Um dos valores que tem de definir quando criar o conteúdo chave é o tipo. Quando utilizar a encriptação de armazenamento, o valor deve ser definido como '1'. 

O exemplo seguinte mostra como criar uma **ContentKey** com um **ContentKeyType** definido para a encriptação de armazenamento ("1") e o **ProtectionKeyType** definido como "0" para indicar que a chave de proteção ID é o thumbprint do certificado X.509.  

Pedir

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Criar um elemento
O exemplo seguinte mostra como criar um elemento.

**Pedido de HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Resposta de HTTP**

Se tiver êxito, é devolvida a seguinte resposta:

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Associe o ContentKey com um elemento
Depois de criar o ContentKey, associá-la com o seu elemento usando a operação de $links, conforme mostrado no exemplo a seguir:

Pedido:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Resposta:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Criar um AssetFile
O [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entidade representa um ficheiro de vídeo ou áudio que é armazenado num contentor de Blobs. Um ficheiro de elemento é sempre associado a um elemento e um elemento pode conter um ou muitos arquivos de recurso. A tarefa de codificador de serviços de multimédia falha se um objeto de ficheiro de recurso não está associado um ficheiro digital num contentor de Blobs.

O **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de multimédia, ao passo que o ficheiro de suporte de dados contém o conteúdo de mídia real.

Depois de carregar o ficheiro de multimédia digital para um contentor de BLOBs, que irá utilizar o **intercalar** pedido HTTP para atualizar o AssetFile com informações sobre seu arquivo de mídia (não mostrado neste artigo). 

**Pedido de HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
