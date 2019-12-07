---
title: Criar chaves de conteúdo com REST | Microsoft Docs
description: Este artigo demonstra como criar chaves de conteúdo que fornecem acesso seguro aos ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 9927de3ab3fb68cea17095d7255fd8a68f66c9a8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901480"
---
# <a name="create-content-keys-with-rest"></a>Criar chaves de conteúdo com REST
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Os serviços de mídia permitem que você entregue ativos criptografados. Um **ContentKey** fornece acesso seguro ao seu **ativo**s. 

Ao criar um novo ativo (por exemplo, antes de [carregar arquivos](media-services-rest-upload-files.md)), você pode especificar as seguintes opções de criptografia: **StorageEncrypted**, **CommonEncryptionProtected**ou **EnvelopeEncryptionProtected**. 

Ao entregar ativos aos seus clientes, você pode configurar o para que os [ativos sejam criptografados dinamicamente](media-services-rest-configure-asset-delivery-policy.md) com uma das duas seguintes criptografias: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Os ativos criptografados devem ser associados a **ContentKey**s. Este artigo descreve como criar uma chave de conteúdo.

A seguir estão as etapas gerais para gerar chaves de conteúdo que você associa aos ativos que você deseja criptografar. 

1. Gere aleatoriamente uma chave AES de 16 bytes (para criptografia comum e de envelope) ou uma chave AES de 32 bytes (para criptografia de armazenamento). 
   
    Essa é a chave de conteúdo para seu ativo, o que significa que todos os arquivos associados a esse ativo precisam usar a mesma chave de conteúdo durante a descriptografia. 
2. Chame os métodos [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) para obter o certificado X. 509 correto que deve ser usado para criptografar a chave de conteúdo.
3. Criptografe a chave de conteúdo com a chave pública do certificado X. 509. 
   
   O SDK do .NET dos serviços de mídia usa RSA com OAEP ao fazer a criptografia.  Você pode ver um exemplo na [função EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Crie um valor de soma de verificação (com base no algoritmo de soma de verificação de chave AES do PlayReady) calculado usando o identificador de chave e a chave de conteúdo. Para obter mais informações, consulte a seção "algoritmo de soma de verificação de chave AES do PlayReady" do documento de objeto de cabeçalho do PlayReady localizado [aqui](https://www.microsoft.com/playready/documents/).
   
   O exemplo de .NET a seguir calcula a soma de verificação usando a parte de GUID do identificador de chave e a chave de conteúdo de limpeza.
   
        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
         {
 
             byte[] array = null;
             using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
             {
                 aesCryptoServiceProvider.Mode = CipherMode.ECB;
                 aesCryptoServiceProvider.Key = contentKey;
                 aesCryptoServiceProvider.Padding = PaddingMode.None;
                 ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
                 array = new byte[16];
                 cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
             }
             byte[] array2 = new byte[8];
             Array.Copy(array, array2, 8);
             return Convert.ToBase64String(array2);
         }
5. Crie a chave de conteúdo com o **EncryptedContentKey** (convertido em cadeia de caracteres codificada em Base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**e valores de **soma de verificação** que você recebeu nas etapas anteriores.
6. Associe a entidade **ContentKey** à sua entidade de **ativo** por meio da operação $links.

Este artigo não mostra como gerar uma chave AES, criptografar a chave e calcular a soma de verificação. 

> [!NOTE]
> 
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligue-se aos Serviços Multimédia

Para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="retrieve-the-protectionkeyid"></a>Recuperar o ProtectionKeyId
O exemplo a seguir mostra como recuperar o ProtectionKeyId, uma impressão digital do certificado, para o certificado que você deve usar ao criptografar sua chave de conteúdo. Execute esta etapa para certificar-se de que você já tem o certificado apropriado em seu computador.

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

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperar o ProtectionKey para o ProtectionKeyId
O exemplo a seguir mostra como recuperar o certificado X. 509 usando o ProtectionKeyId recebido na etapa anterior.

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

## <a name="create-the-contentkey"></a>Criar o ContentKey
Depois de recuperar o certificado X. 509 e usado sua chave pública para criptografar a chave de conteúdo, crie uma entidade **ContentKey** e defina seus valores de propriedade adequadamente.

Um dos valores que você deve definir ao criar a chave de conteúdo é o tipo. Escolha um dos seguintes valores:

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }


O exemplo a seguir mostra como criar um **ContentKey** com um **ContentKeyType** definido para criptografia de armazenamento ("1") e o **ProtectionKeyType** definido como "0" para indicar que a ID da chave de proteção é a impressão digital do certificado X. 509.  

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

## <a name="associate-the-contentkey-with-an-asset"></a>Associar o ContentKey a um ativo
Depois de criar o ContentKey, associe-o ao seu ativo usando a operação $links, conforme mostrado no exemplo a seguir:

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


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

