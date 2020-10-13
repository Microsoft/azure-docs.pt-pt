---
title: Encriptar o seu conteúdo com encriptação de armazenamento usando AMS REST API
description: Aprenda a encriptar o seu conteúdo com encriptação de armazenamento utilizando APIs AMS REST.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a3a51644f61d4a1e118798986f9c6fb6c52d0e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264169"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Encriptar o seu conteúdo com encriptação de armazenamento

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não estão a ser adicionadas novas funcionalidades ou funcionalidades aos Serviços de Comunicação Social v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)
>

Este artigo dá uma visão geral da encriptação de armazenamento AMS e mostra-lhe como carregar o conteúdo encriptado de armazenamento:

* Crie uma chave de conteúdo.
* Criar um Ativo. Desafie a Opção de Criação de Ativos para ArmazenamentoEncrição ao criar o Ativo.
  
     Os ativos encriptados estão associados a chaves de conteúdo.
* Ligue a chave de conteúdo ao ativo.  
* Desafine os parâmetros relacionados com a encriptação nas entidades DoeFile.

## <a name="considerations"></a>Considerações 

Se quiser entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Para obter mais informações, consulte [políticas de entrega de ativos configurantes.](media-services-rest-configure-asset-delivery-policy.md)

Ao aceder a entidades nos Serviços de Media, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Encriptação lateral do armazenamento

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento de serviços de mídia|Encriptação AES-256, chave gerida pelos Media Services|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](../../storage/common/storage-service-encryption.md)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente|Suportado|Suportado|
|[Encriptação Client-Side de armazenamento](../../storage/common/storage-client-side-encryption.md)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault|Não suportado|Não suportado|

<sup>1 Enquanto</sup> os Serviços de Mídia suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendável fazê-lo.

<sup>2</sup> Nos Serviços de Mídia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Serviços de Mídia v2. O que significa que a V3 funciona com os ativos encriptados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Visão geral da encriptação do armazenamento
A encriptação de armazenamento AMS aplica encriptação do modo **AES-CTR** a todo o ficheiro.  O modo AES-CTR é uma cifra de bloco que pode encriptar dados de comprimento arbitrário sem necessidade de enchimento. Opera encriptando um contra-bloco com o algoritmo AES e, em seguida, XOR-ing a saída de AES com os dados para encriptar ou desencriptar.  O contra-bloco utilizado é construído copiando o valor do InitializationVector para bytes 0 a 7 do valor de contador e os bytes 8 a 15 do valor de contador são definidos para zero. Do bloco de balcão de 16 bytes, os bytes 8 a 15 (ou seja, os bytes menos significativos) são usados como um simples número inteiro não assinado de 64 bits que é incrementado por um para cada bloco subsequente de dados processados e é mantido em ordem byte de rede. Se este número inteiro atingir o valor máximo (0xFFFFFFFFFFFFFFFFFFFF), em seguida, incrementando-o repõe o contador de blocos a zero (bytes 8 a 15) sem afetar os outros 64 bits do balcão (ou seja, bytes 0 a 7).   Para manter a segurança da encriptação do modo AES-CTR, o valor InitializationVector para um determinado identificador-chave para cada chave de conteúdo deve ser único para cada ficheiro e os ficheiros devem ter menos de 2^64 blocos de comprimento.  Este valor único é garantir que um valor de contra-valor nunca seja reutilizado com uma determinada chave. Para obter mais informações sobre o modo CTR, consulte [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (o artigo wiki utiliza o termo "Nonce" em vez de "InitializationVector").

Utilize **encriptação de armazenamento** para encriptar o seu conteúdo límpido localmente usando encriptação de bits AES-256 e, em seguida, carregá-lo para o Azure Storage, onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptados antes da codificação, e opcionalmente reen encriptados antes de serem carregados como um novo ativo de saída. O caso principal de utilização para encriptação de armazenamento é quando pretende proteger os seus ficheiros de meios de entrada de alta qualidade com uma encriptação forte em repouso no disco.

Para entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para que os Serviços de Comunicação saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, encriptação comum ou nenhuma encriptação).

## <a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys utilizados para encriptação
Os ativos encriptados estão associados às chaves de encriptação de armazenamento. Crie a chave de conteúdo a ser utilizada para encriptação antes de criar os ficheiros de ativos. Esta secção descreve como criar uma chave de conteúdo.

Seguem-se os passos gerais para gerar chaves de conteúdo que associa a ativos que pretende encriptar. 

1. Para encriptação de armazenamento, gere aleatoriamente uma chave AES de 32 byte. 
   
    A Chave AES de 32 bytes é a chave de conteúdo para o seu ativo, o que significa que todos os ficheiros associados a esse ativo precisam de usar a mesma chave de conteúdo durante a desencriptação. 
2. Ligue para os métodos [GetProtectionKey e](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) para obter o Certificado X.509 correto que deve ser usado para encriptar a sua chave de conteúdo.
3. Criptografe a chave de conteúdo com a chave pública do Certificado X.509. 
   
   Media Services .NET SDK utiliza RSA com OAEP ao fazer a encriptação.  Pode ver um exemplo .NET na [função EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Crie um valor de dados calculado utilizando o identificador chave e a chave de conteúdo. O exemplo .NET a seguir calcula a parte de verificação utilizando a parte GUID do identificador de tecla e a chave de conteúdo clara.

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

5. Crie a tecla de conteúdo com o **EncryptedContentKey** (convertido em cadeia codificada de base64), **ProtectionKeyId,** **ProtectionKeyType,** **ContentKeyType**e **Valores de Checksum** que tenha recebido em etapas anteriores.

    Para encriptação de armazenamento, as seguintes propriedades devem ser incluídas no corpo de pedido.

    Solicitar propriedade do corpo    | Descrição
    ---|---
    Id | O ID ContentKey é gerado usando o seguinte formato, "nb:kid:UUID: \<NEW GUID> ".
    ContentKeyType | O tipo de chave de conteúdo é um número inteiro que define a chave. Para o formato de encriptação de armazenamento, o valor é 1.
    CriptografadoContentKey | Criamos um novo valor chave de conteúdo que é um valor de 256 bits (32 bytes). A chave é encriptada utilizando o certificado de encriptação de armazenamento X.509 que recuperamos do Microsoft Azure Media Services executando um pedido HTTP GET para os Métodos GetProtectionKeyId e GetProtectionKey. Como exemplo, consulte o seguinte código .NET: o método  **EncryptSymmetricKeyData** definido [aqui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProteçãoKeyId | Este é o ID chave de proteção para o certificado de encriptação de armazenamento X.509 que foi usado para encriptar a nossa chave de conteúdo.
    ProteçãoKeyType | Este é o tipo de encriptação para a chave de proteção que foi usada para encriptar a chave de conteúdo. Este valor é StorageEncrypation(1) para o nosso exemplo.
    Soma de verificação |O MD5 calculou a parte de verificação da chave de conteúdo. É calculado encriptando o ID de conteúdo com a chave de conteúdo. O código de exemplo demonstra como calcular a quantidade de cheques.


### <a name="retrieve-the-protectionkeyid"></a>Recuperar o ProtectionKeyId
O exemplo a seguir mostra como recuperar o ProtectionKeyId, uma impressão digital de certificado, para o certificado que deve utilizar ao encriptar a sua chave de conteúdo. Faça este passo para se certificar de que já tem o certificado apropriado na sua máquina.

Pedido:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Resposta:

```console
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
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recupere a Chave de Proteção para o ProtectionKeyId
O exemplo a seguir mostra como recuperar o certificado X.509 utilizando o ProtectionKeyId que recebeu no passo anterior.

Pedido:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Resposta:

```console
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
```

### <a name="create-the-content-key"></a>Criar a chave de conteúdo
Depois de ter recuperado o certificado X.509 e ter usado a sua chave pública para encriptar a sua chave de conteúdo, criar uma entidade **ContentKey** e definir os seus valores de propriedade em conformidade.

Um dos valores que deve definir ao criar a tecla de conteúdo é o tipo. Ao utilizar a encriptação de armazenamento, o valor deve ser definido para '1'. 

O exemplo a seguir mostra como criar uma **ContentKey** com um **ContentKeyType** definido para encriptação de armazenamento ("1") e o **ProtectionKeyType** definido para "0" para indicar que o ID da chave de proteção é a impressão digital do certificado X.509.  

Pedir

```console
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
```

Resposta:

```console
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
```

## <a name="create-an-asset"></a>Criar um recurso
O exemplo a seguir mostra como criar um ativo.

**Pedido de HTTP**

```console
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
```

**RESPOSTA HTTP**

Se for bem sucedida, a seguinte resposta é devolvida:

```console
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
```

## <a name="associate-the-contentkey-with-an-asset"></a>Associar o ContentKey a um Ativo
Depois de criar o ContentKey, associe-o ao seu Ativo utilizando a operação $links, como mostra o seguinte exemplo:

Pedido:

```console
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
```

Resposta:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Criar um Ficheiro de Ativos
A entidade [AssetFile](/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio que é armazenado num recipiente de bolhas. Um ficheiro de ativos está sempre associado a um ativo, e um ativo pode conter um ou muitos ficheiros de ativos. A tarefa Media Services Encoder falha se um objeto de ficheiro de ativo não estiver associado a um ficheiro digital num recipiente de bolhas.

A instância **do AssetFile** e o ficheiro de mídia real são dois objetos distintos. A instância Do Ficheiro Do Ativo contém metadados sobre o ficheiro de mídia, enquanto o ficheiro de mídia contém o conteúdo real dos meios de comunicação.

Depois de fazer o upload do seu ficheiro de mídia digital para um recipiente blob, utilizará o pedido **MERGE** HTTP para atualizar o AssetFile com informações sobre o seu ficheiro de media (não mostrado neste artigo). 

**Pedido de HTTP**

```console
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
```

**RESPOSTA HTTP**

```console
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
```
