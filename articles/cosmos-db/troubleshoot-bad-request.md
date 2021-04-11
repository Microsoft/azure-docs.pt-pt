---
title: Resolução de problemas Azure Cosmos DB más exceções
description: Saiba como diagnosticar e corrigir exceções de mau pedido, como o conteúdo de entrada ou a chave de partição é inválida, a chave de partição não corresponde em Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556188"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnosticar e resolver problemas de más exceções no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O código de estado HTTP 400 representa o pedido contém dados inválidos ou faltam os parâmetros necessários.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Faltando a propriedade de ID
Neste cenário, é comum ver o erro:

*O conteúdo de entrada é inválido porque as propriedades requeridas - 'id; ' - estão desaparecidos*

Uma resposta com este erro significa que o documento JSON que está a ser enviado para o serviço carece da propriedade de ID necessária.

### <a name="solution"></a>Solução
Especifique uma `id` propriedade com um valor de cadeia de acordo com a [especificação REST](/rest/api/cosmos-db/documents) como parte do seu documento, os SDKs não autogeram valores para esta propriedade.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Tipo de chave de partição inválida
Neste cenário, é comum ver erros como:

*Chave de partição... é inválido.*

Uma resposta com este erro significa que o valor da chave de partição é de um tipo inválido.

### <a name="solution"></a>Solução
O valor da chave de partição deve ser uma corda ou um número, certifique-se de que o valor é dos tipos esperados.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Valor da chave da partição errada
Neste cenário, é comum ver o erro:

*PartitionKey extraído do documento não corresponde ao especificado no cabeçalho*

Uma resposta com este erro significa que está a executar uma operação e a passar um valor chave de partição que não corresponde ao valor corporal do documento para a propriedade esperada. Se o caminho chave de partição da coleção `/myPartitionKey` for, o documento tem uma propriedade chamada `myPartitionKey` com um valor que não corresponde ao que foi fornecido como valor chave de partição ao chamar o método SDK.

### <a name="solution"></a>Solução
Envie o parâmetro de valor da chave de partição que corresponda ao valor da propriedade do documento.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
