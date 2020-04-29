---
title: Encriptar o seu conteúdo com encriptação de armazenamento usando ams REST API
description: Saiba como encriptar o seu conteúdo com encriptação de armazenamento utilizando APIs AMS REST.
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
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773591"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Encriptar o seu conteúdo com encriptação de armazenamento 

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não estão a ser adicionadas novas funcionalidades ou funcionalidades aos Serviços de Media v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)
>   

É altamente recomendado encriptar o seu conteúdo localmente usando encriptação de bit AES-256 e, em seguida, carregá-lo para o Armazenamento Azure onde é armazenado encriptado em repouso.

Este artigo dá uma visão geral da encriptação do armazenamento AMS e mostra-lhe como carregar o conteúdo encriptado de armazenamento:

* Crie uma chave de conteúdo.
* Criar um Ativo. Delineie a Opção de Criação de Ativos para storageEncryption ao criar o Ativo.
  
     Os ativos encriptados estão associados a chaves de conteúdo.
* Ligue a chave de conteúdo ao ativo.  
* Defina os parâmetros relacionados com a encriptação nas entidades Do AssetFile.

## <a name="considerations"></a>Considerações 

Se pretender entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Para mais informações, consulte [a Configuração de Políticas de Entrega](media-services-rest-configure-asset-delivery-policy.md)de Ativos .

Ao aceder a entidades em Serviços de Media, deve definir campos e valores específicos nos seus pedidos HTTP. Para mais informações, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento de serviços de mídia|Encriptação AES-256, chave gerida pela Media Services|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente|Suportado|Suportado|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault|Não suportado|Não suportado|

<sup>1</sup> Enquanto os Serviços de Media suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendado fazê-lo.

<sup>2</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Media Services v2. O que significa que a V3 trabalha com os ativos encriptados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como se conectar à AMS API, consulte [Aceda à API dos Serviços de Mídia Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Visão geral da encriptação do armazenamento
A encriptação de armazenamento AMS aplica encriptação do modo **AES-CTR** a todo o ficheiro.  O modo AES-CTR é uma cifra de bloco que pode encriptar dados de comprimento arbitrário sem necessidade de enchimento. Opera encriptando um bloco de contra-bloqueio com o algoritmo AES e, em seguida, XOR-ing a saída de AES com os dados para encriptar ou desencriptar.  O bloco de contra-bloqueio utilizado é construído copiando o valor do Vetor de Inicialização para bytes 0 a 7 do valor de contra-contador e os bytes 8 a 15 do valor de contador estão definidos para zero. Do bloco de balcão de 16 bytes, os bytes 8 a 15 (isto é, os bytes menos significativos) são usados como um simples suporte não assinado de 64 bits que é incrementado por um para cada bloco subsequente de dados processados e é mantido em ordem byte de rede. Se este inteiro atingir o valor máximo (0xFFFFFFFFFFFFFFFFFFFfFF), então incrementá-lo repõe o contador de blocos para zero (bytes 8 a 15) sem afetar os outros 64 bits do contador (isto é, bytes 0 a 7).   A fim de manter a segurança da encriptação do modo AES-CTR, o valor do Vetor de Inicialização para um determinado identificador chave para cada chave de conteúdo deve ser único para cada ficheiro e os ficheiros devem ter menos de 2^64 blocos de comprimento.  Este valor único é garantir que um contra-valor nunca é reutilizado com uma determinada chave. Para obter mais informações sobre o modo CTR, consulte [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (o artigo wiki usa o termo "Nonce" em vez de "Vetor de Inicialização").

Utilize **encriptação** de armazenamento para encriptar o seu conteúdo limpo localmente usando encriptação de bitS AES-256 e, em seguida, carregá-lo para o Armazenamento Azure onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptado antes da codificação, e opcionalmente reencriptados antes de serem reencriptados como um novo ativo de saída. O principal caso de utilização para encriptação de armazenamento é quando pretende proteger os seus ficheiros de mídia de entrada de alta qualidade com encriptação forte em repouso no disco.

Para entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para que os Serviços de Media saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação do armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, encriptação comum ou nenhuma encriptação).

## <a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys usados para encriptação
Os ativos encriptados estão associados a chaves de encriptação de armazenamento. Crie a chave de conteúdo a ser usada para encriptação antes de criar os ficheiros de ativos. Esta secção descreve como criar uma chave de conteúdo.

Seguem-se passos gerais para gerar chaves de conteúdo que associa com ativos que pretende ser encriptados. 

1. Para encriptação de armazenamento, gera aleatoriamente uma chave AES de 32 bytes. 
   
    A Chave AES de 32 bytes é a chave de conteúdo para o seu ativo, o que significa que todos os ficheiros associados a esse ativo precisam de usar a mesma chave de conteúdo durante a desencriptação. 
2. Ligue para os métodos [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) para obter o certificado X.509 correto que deve ser usado para encriptar a sua chave de conteúdo.
3. Criptografe a sua chave de conteúdo com a chave pública do Certificado X.509. 
   
   Media Services .NET SDK usa RSA com OAEP ao fazer a encriptação.  Pode ver um exemplo .NET na [função EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Criar um valor de verificação calculado utilizando a chave de identificação e chave de conteúdo. O exemplo .NET que se segue calcula o controlo utilizando a parte GUID do identificador chave e a chave de conteúdo clara.

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

5. Crie a chave de conteúdo com a Chave de **Conteúdo Encriptado** (convertida em cadeia codificada por base64), **ProtectionKeyId,** **ProtectionKeyType**, **ContentKeyType**e **Checksum** valores que recebeu em etapas anteriores.

    Para encriptação de armazenamento, as seguintes propriedades devem ser incluídas no organismo de pedido.

    Solicitar propriedade do corpo    | Descrição
    ---|---
    Id | O ID ContentKey é gerado utilizando o seguinte formato,\<"nb:kid:UUID: NEW GUID>".
    Tipo de chave de conteúdo | O tipo de chave de conteúdo é um inteiro que define a chave. Para o formato de encriptação de armazenamento, o valor é 1.
    Chave de Conteúdo Encriptado | Criamos um novo valor-chave de conteúdo que é um valor de 256 bits (32 bytes). A chave é encriptada utilizando o certificado de encriptação de armazenamento X.509 que recuperamos dos Serviços de Media Microsoft Azure executando um pedido HTTP GET para os Métodos GetProtectionKeyId e GetProtectionKey. Como exemplo, consulte o seguinte código .NET: o método **EncryptSymmetricKeyData** definido [aqui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtecçãoKeyid | Este é o ID da chave de proteção para o certificado de encriptação de armazenamento X.509 que foi usado para encriptar a nossa chave de conteúdo.
    Protecção-ChaveTipo | Este é o tipo de encriptação para a chave de proteção que foi usada para encriptar a chave de conteúdo. Este valor é StorageEncryption(1) para o nosso exemplo.
    Soma de verificação |O MD5 calculou a verificação da chave de conteúdo. É calculado encriptando o ID do conteúdo com a chave de conteúdo. O código de exemplo demonstra como calcular a verificação.


### <a name="retrieve-the-protectionkeyid"></a>Recuperar o ProtectionKeyId
O exemplo que se segue mostra como recuperar o ProtectionKeyId, uma impressão digital do certificado, para o certificado que deve utilizar ao encriptar a sua chave de conteúdo. Faça este passo para se certificar de que já tem o certificado apropriado na sua máquina.

Pedido:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperar a Chave de Proteção para o ProtectionKeyId
O exemplo que se segue mostra como recuperar o certificado X.509 utilizando o ProtectionKeyId que recebeu no passo anterior.

Pedido:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
Depois de ter recuperado o certificado X.509 e ter usado a sua chave pública para encriptar a chave de conteúdo, criar uma entidade **ContentKey** e definir os seus valores de propriedade em conformidade.

Um dos valores que deve definir quando criar a chave de conteúdo é o tipo. Ao utilizar encriptação de armazenamento, o valor deve ser definido para '1'. 

O exemplo seguinte mostra como criar um **ContentKey** com um conjunto **ContentKeyType** para encriptação de armazenamento ("1") e o **conjunto ProtectionKeyType** para "0" para indicar que o ID da chave de proteção é a impressão digital do certificado X.509.  

Pedir

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

## <a name="create-an-asset"></a>Criar um ativo
O exemplo que se segue mostra como criar um ativo.

**Pedido de HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Resposta HTTP**

Se for bem sucedido, devolve-se a seguinte resposta:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Associar o ContentKey a um Ativo
Depois de criar o ContentKey, associe-o ao seu Ativo utilizando a operação $links, como mostra o seguinte exemplo:

Pedido:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Resposta:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Criar um AssetFile
A entidade [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio que é armazenado num recipiente de bolha. Um ficheiro de ativo está sempre associado a um ativo, e um ativo pode conter um ou muitos ficheiros de ativos. A tarefa Do Codificador de Serviços de Media falha se um objeto de ficheiro de ativo não estiver associado a um ficheiro digital num recipiente de blob.

A instância **AssetFile** e o ficheiro de mídia real são dois objetos distintos. A instância AssetFile contém metadados sobre o ficheiro de mídia, enquanto o ficheiro de mídia contém o conteúdo real dos meios de comunicação.

Depois de enviar o seu ficheiro de mídia digital para um recipiente blob, utilizará o pedido **MERGE** HTTP para atualizar o AssetFile com informações sobre o seu ficheiro de mídia (não mostrados neste artigo). 

**Pedido de HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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
