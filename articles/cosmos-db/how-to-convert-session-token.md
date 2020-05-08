---
title: Como converter formatos simbólicos de sessão em .NET SDK - Azure Cosmos DB
description: Saiba como converter formatos token sessão para garantir compatibilidades entre diferentes versões .NET SDK
author: vinhms
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: vitrinh
ms.openlocfilehash: 377d8e3e923d6a8fa3b1722cee6451a696ce2490
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796905"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Converter formatos de token de sessão em .NET SDK

Este artigo explica como converter entre diferentes formatos de token de sessão para garantir a compatibilidade entre as versões SDK.

> [!NOTE]
> Por predefinição, o SDK acompanha automaticamente a sessão e utilizará o token de sessão mais recente.  Para mais informações, visite [tokens de sessão de utilização](how-to-manage-consistency.md#utilize-session-tokens). As instruções deste artigo aplicam-se apenas com as seguintes condições:
> * A sua conta Azure Cosmos DB utiliza a consistência da Sessão.
> * Está a gerir as fichas da sessão manualmente.
> * Está a utilizar várias versões do SDK ao mesmo tempo.

## <a name="session-token-formats"></a>Formatos simbólicos de sessão

Existem dois formatos simbólicos de sessão: **simples** e **vetor.**  Estes dois formatos não são permutáveis, pelo que o formato deve ser convertido ao passar para a aplicação do cliente com versões diferentes.
- O formato token de sessão **simples** é utilizado pelo .NET SDK V1 (Microsoft.Azure.DocumentDB -versão 1.x)
- O formato token da sessão **vetorial** é utilizado pelo .NET SDK V2 (Microsoft.Azure.DocumentDB -versão 2.x)

### <a name="simple-session-token"></a>Ficha de sessão simples

Um simples token de sessão tem este formato:`{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Símbolo da sessão de vetor

Um token de sessão vetorial tem o seguinte formato:`{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Converter para ficha de sessão simples

Para passar uma sessão simbólica ao cliente usando .NET SDK V1, use um formato token de sessão **simples.**  Por exemplo, utilize o seguinte código de amostra para o converter.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Converter em token de sessão de vetor

Para passar uma sessão simbólica ao cliente usando .NET SDK V2, use o formato token da sessão **vetorial.**  Por exemplo, utilize o seguinte código de amostra para o converter.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos:

* [Use tokens de sessão para gerir a consistência em Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Escolha o nível de consistência certo em Azure Cosmos DB](consistency-levels-choosing.md)
* [Consistência, disponibilidade e trocas de desempenho em Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)