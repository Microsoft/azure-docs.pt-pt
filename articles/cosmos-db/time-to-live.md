---
title: Expirar dados no Azure Cosmos DB com TTL
description: Com o valor de TTL, o Microsoft Azure Cosmos DB fornece a capacidade de ter documentos automaticamente removidos do sistema após um período de tempo.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d09abfbec7ace3112de35869ccc95c56e113e6fe
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991840"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>TTL no Azure Cosmos DB 

Com "Time to Live" ou o valor de TTL, o Azure Cosmos DB oferece a capacidade de excluir itens automaticamente a partir de um contentor após um determinado período de tempo. Por predefinição, pode definir a hora em direto ao nível do contentor e substituir o valor numa base por item. Depois de definir o valor de TTL um contentor ou num nível de item, Azure Cosmos DB irá automaticamente remover estes itens após o período de tempo, desde a hora da que última foram modificados. Valor de TTL é configurado em segundos. Quando configura o valor de TTL, o sistema automaticamente irá eliminar os itens expirados com base no valor TTL, ao contrário de uma operação de eliminação explicitamente emitido pela aplicação de cliente.

## <a name="time-to-live-for-containers-and-items"></a>Tempo de duração para contentores e itens

O tempo para o valor de TTL é definido em segundos, e ela é interpretada como um delta a partir do momento em que um item foi modificado pela última vez. Pode definir o TTL num contentor ou um item no contentor:

1. **TTL de num contêiner** (definido com o `DefaultTimeToLive`):

   - Se estiver em falta (ou definida como nula), itens não são automaticamente expirados.

   - Se o momento presente e o valor for definido como "-1", é igual a infinito – itens não expiram por predefinição.

   - Se o momento presente e o valor está definido como um número ("n") – itens expirarem "n" segundos após a sua última hora de modificação.

2. **TTL de num item** (definido com o `ttl`):

   - Esta propriedade é aplicável apenas se `DefaultTimeToLive` está presente e não está definido como nulo para o contentor principal.

   - Se estiver presente, substitui o `DefaultTimeToLive` valor do contentor principal.

## <a name="time-to-live-configurations"></a>Tempo para configurações em direto

* Se o valor de TTL é definido como "n" num contentor, em seguida, os itens nesse contentor irão expirar após n segundos.  Se não existem itens no mesmo contentor que tem seu próprio tempo para live, definido como -1 (que indica que isso não ocorra) ou se alguns itens tenham substituído o tempo de duração definição com um número diferente, estes itens expirarem com base no valor TTL configurado. 

* Se o valor de TTL não está definido num contentor, em seguida, o tempo de duração num item neste contentor não tem qualquer efeito. 

* Se o valor de TTL num contêiner é definido como -1, um item neste contentor que tem tempo para o conjunto em direto para n, irá expirar após n segundos e itens restantes não irão expirar. 

A eliminar itens com base no valor de TTL é gratuita. Não existe nenhum custo adicional (ou seja, sem RUs adicionais são consumidos) quando o item é eliminado como resultado de expiração do TTL.

## <a name="next-steps"></a>Passos Seguintes

Saiba como configurar o mosaico para Live nos seguintes artigos:

* [Como configurar o TTL](how-to-time-to-live.md)
