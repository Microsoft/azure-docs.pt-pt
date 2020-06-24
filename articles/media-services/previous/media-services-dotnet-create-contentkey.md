---
title: Criar ContentKeys com .NET
description: Este artigo demonstra como criar chaves de conteúdo que fornecem acesso seguro aos ativos.
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
ms.openlocfilehash: aebd6dee9314d6e5641988767c024790b6b721f4
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84712365"
---
# <a name="create-contentkeys-with-net"></a>Criar ContentKeys com .NET 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Os Serviços de Comunicação Social permitem-lhe criar e entregar ativos encriptados. Um **ContentKey** fornece acesso seguro aos seus **Ativos.** 

Quando cria um novo ativo (por exemplo, antes [de carregar ficheiros),](media-services-dotnet-upload-files.md)pode especificar as seguintes opções de encriptação: **StorageEncrypted**, **CommonEncrypationProtected**, ou **EnvelopeEncryptionProtected**. 

Quando entrega ativos aos seus clientes, pode configurar para que [os ativos sejam encriptados dinamicamente](media-services-dotnet-configure-asset-delivery-policy.md) com uma das duas encriptações seguintes: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Os ativos encriptados têm de ser associados ao **ContentKey.** Este artigo descreve como criar uma chave de conteúdo.

> [!NOTE]
> Ao criar um novo ativo **StorageEncrypted** utilizando os Media Services .NET SDK, o **ContentKey** é automaticamente criado e ligado ao ativo.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Um dos valores que deve definir ao criar uma chave de conteúdo é o tipo de chave de conteúdo. Escolha entre um dos seguintes valores. 

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

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Criar o tipo de envelope ContentKey
O seguinte código snippet cria uma chave de conteúdo do tipo de encriptação do envelope. Em seguida, associa a chave ao ativo especificado.

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


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Criar tipo comum ContentKey
O seguinte corte de código cria uma chave de conteúdo do tipo de encriptação comum. Em seguida, associa a chave ao ativo especificado.

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

