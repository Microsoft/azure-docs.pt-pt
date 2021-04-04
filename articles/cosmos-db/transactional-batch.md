---
title: Operações de lote transacional em Azure Cosmos DB utilizando o .NET SDK
description: Saiba como utilizar o TransactionalBatch no Azure Cosmos DB .NET SDK para realizar um grupo de operações pontuais que tenham sucesso ou falhem.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347562"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Operações de lote transacional em Azure Cosmos DB utilizando o .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O lote transacional descreve um grupo de operações pontuais que precisam de ter sucesso ou falhar juntamente com a mesma chave de partição num recipiente. No .NET SDK, a `TransactionalBatch` classe é utilizada para definir este lote de operações. Se todas as operações forem bem sucedidas na ordem que são descritas no âmbito da operação do lote transacional, a transação será comprometida. No entanto, se alguma operação falhar, toda a transação será revertida.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>O que é uma transação em Azure Cosmos DB

Uma transação numa base de dados típica pode ser definida como uma sequência de operações realizadas como uma única unidade lógica de trabalho. Cada transação fornece garantias de propriedade ACID (Atomicity, Consistência, Isolamento, Durabilidade).

* **A atomicidade** garante que todas as operações realizadas dentro de uma transação são tratadas como uma única unidade, e ou todas são comprometidas ou nenhuma delas são.
* **A consistência** garante que os dados estão sempre num estado válido em transações.
* **O isolamento** garante que não há duas transações que interfiram entre si – muitos sistemas comerciais fornecem múltiplos níveis de isolamento que podem ser utilizados com base nas necessidades da aplicação.
* **A durabilidade** garante que qualquer alteração que seja cometida numa base de dados estará sempre presente.
A Azure Cosmos DB suporta [transações completas em conformidade com o ACID com isolamento instantâneo](database-transactions-optimistic-concurrency.md) para operações dentro da mesma chave de [partição lógica](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Operações de lote transacional Vs procedimentos armazenados

A Azure Cosmos DB suporta atualmente procedimentos armazenados, que também fornecem o âmbito transacional das operações. No entanto, as operações de lote transacional oferecem os seguintes benefícios:

* **Opção linguística** – O lote transacional é suportado no SDK e o idioma com que já trabalha, enquanto os procedimentos armazenados precisam de ser escritos no JavaScript.
* **Versão de código** – O código de aplicação de versão e a sua inscrição no seu pipeline CI/CD é muito mais natural do que orquestrar a atualização de um procedimento armazenado e certificar-se de que a capotagem acontece no momento certo. Também facilita as mudanças de retrocesso.
* **Desempenho** – Redução da latência em operações equivalentes até 30% quando comparada com a execução do procedimento armazenado.
* **Serialização de conteúdo –** Cada operação dentro de um lote transacional pode aproveitar as opções de serialização personalizadas para a sua carga útil.

## <a name="how-to-create-a-transactional-batch-operation"></a>Como criar uma operação de lote transacional

Ao criar uma operação de lote transacional, você começa a partir de uma instância de recipiente e `CreateTransactionalBatch` ligue:

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Em seguida, você precisa ligar para `ExecuteAsync` o lote:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Uma vez recebida a resposta, examine se é bem sucedida ou não e extrai os resultados:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Se houver uma falha, a operação falhada terá um código de estado do seu erro correspondente. Todas as outras operações terão um código de estado 424 (dependência falhada). No exemplo abaixo, a operação falha porque tenta criar um item que já existe (409 HttpStatusCode.Conflict). O código de estado permite identificar a causa da falha de transação.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Como são executadas as operações de lote transacional

Quando o `ExecuteAsync` método é chamado, todas as operações no `TransactionalBatch` objeto são agrupadas, serializadas numa única carga útil, e enviadas como um único pedido para o serviço DB Azure Cosmos.

O serviço recebe o pedido e executa todas as operações dentro de um âmbito transacional, e devolve uma resposta usando o mesmo protocolo de serialização. Esta resposta é um sucesso, ou uma falha, e fornece respostas individuais de operação por operação.

O SDK expõe a resposta para verificar o resultado e, opcionalmente, extrair cada um dos resultados internos da operação.

## <a name="limitations"></a>Limitações

Atualmente, existem dois limites conhecidos:

* O limite de tamanho de pedido de Azure Cosmos limita o tamanho da `TransactionalBatch` carga útil para não exceder 2 MB, e o tempo máximo de execução é de 5 segundos.
* Existe um limite atual de 100 operações por `TransactionalBatch` para garantir que o desempenho é como esperado e dentro das AEA.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o que pode fazer com o TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Visite [as nossas amostras](sql-api-dotnet-v3sdk-samples.md) para mais formas de utilizar o nosso Cosmos DB .NET SDK
