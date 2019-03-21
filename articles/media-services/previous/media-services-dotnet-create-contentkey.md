---
title: Criar ContentKeys com .NET
description: Saiba como criar chaves de conteúdo que fornecem acesso seguro aos recursos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab26be3b9ac5d209cfe8117bdf9e87e0c7e74188
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183089"
---
# <a name="create-contentkeys-with-net"></a>Criar ContentKeys com .NET 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Serviços de multimédia permite-lhe criar e fornecer recursos encriptados. R **ContentKey** proporciona acesso seguro ao seu **Asset**s. 

Quando cria um novo recurso (por exemplo, antes de [carregar ficheiros](media-services-dotnet-upload-files.md)), pode especificar as seguintes opções de encriptação: **StorageEncrypted**, **CommonEncryptionProtected**, ou **EnvelopeEncryptionProtected**. 

Quando fornecer recursos aos seus clientes, pode [configurar para ativos seja encriptado dinamicamente](media-services-dotnet-configure-asset-delivery-policy.md) com um das duas encriptações seguintes: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Ativos encriptados têm de ser associados **ContentKey**s. Este artigo descreve como criar uma chave de conteúdo.

> [!NOTE]
> Ao criar um novo **StorageEncrypted** asset utilizando o SDK de .NET de serviços de suporte de dados, o **ContentKey** é automaticamente criado e está associado ao recurso.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Um dos valores que tem de definir quando criar um conteúdo chave é o tipo de chave de conteúdo. Escolha um dos seguintes valores. 

```csharp
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
```

## <a id="envelope_contentkey"></a>Criar tipo de envelope ContentKey
O fragmento de código seguinte cria uma chave de conteúdo do tipo de encriptação de envelope. Em seguida, associa a chave com o elemento especificado.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>Criar o tipo comum ContentKey
O fragmento de código seguinte cria uma chave de conteúdo do tipo de encriptação comum. Em seguida, associa a chave com o elemento especificado.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

