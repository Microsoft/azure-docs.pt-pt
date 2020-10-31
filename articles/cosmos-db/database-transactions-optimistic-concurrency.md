---
title: Transações de base de dados e controlo otimista da conuscção no Azure Cosmos DB
description: Este artigo descreve transações de bases de dados e controlo otimista de concordância em Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: c1ecd3a3d29d6876a59a2fa039802966f348a09d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089836"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transações e controlo de simultaneidade otimista
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As transações de base de dados fornecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. As bases de dados relacionais tradicionais, como o SQL Server, permitem-lhe escrever a lógica de negócio utilizando procedimentos armazenados e/ou gatilhos, enviando-o para o servidor para execução diretamente dentro do motor da base de dados. Com bases de dados relacionais tradicionais, é-lhe exigido que lide com duas linguagens de programação diferentes, a linguagem de programação de aplicações (não transacional), como JavaScript, Python, C#, Java, etc. e a linguagem de programação transacional (como o T-SQL) que é executada de forma nativa pela base de dados.

O motor de base de dados em Azure Cosmos DB suporta transações completas acid (Atomicity, Consistência, Isolamento, Durabilidade) com isolamento instantâneo. Todas as operações de base de dados no âmbito da [partição lógica](partitioning-overview.md) de um contentor são executadas transacionalmente dentro do motor de base de dados que é hospedado pela réplica da partição. Estas operações incluem tanto escrever (atualizar um ou mais itens dentro da partição lógica) e ler operações. O quadro a seguir ilustra diferentes operações e tipos de transações:

| **Operação**  | **Tipo de operação** | **Transação de item único ou multi-itens** |
|---------|---------|---------|
| Inserir (sem um gatilho pré/post) | Escrita | Transação de item único |
| Inserir (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Substituir (sem um gatilho pré/post) | Escrita | Transação de item único |
| Substitua (por um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Upsert (sem um gatilho pré/post) | Escrita | Transação de item único |
| Upsert (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Excluir (sem um gatilho pré/post) | Escrita | Transação de item único |
| Excluir (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Executar procedimento armazenado | Escrever e Ler | Transação multi-item |
| Sistema iniciou execução de um procedimento de fusão | Escrita | Transação multi-item |
| Sistema iniciou execução de itens de supressão com base na expiração (TTL) de um item | Escrita | Transação multi-item |
| Leitura | Leitura | Transação de um único item |
| Feed de Alterações | Ler | Transação multi-item |
| Leitura Paginada | Ler | Transação multi-item |
| Consulta paginada | Ler | Transação multi-item |
| Executar uDF como parte da consulta paginada | Ler | Transação multi-item |

## <a name="multi-item-transactions"></a>Transações multi-itens

O Azure Cosmos DB permite-lhe escrever [procedimentos armazenados, detonadores pré/post, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md) e procedimentos de fusão em JavaScript. A Azure Cosmos DB suporta de forma nativa a execução do JavaScript dentro do seu motor de base de dados. Pode registar procedimentos armazenados, detonadores pré/post, funções definidas pelo utilizador (UDFs) e fundir procedimentos num recipiente e executá-los transacionalmente dentro do motor de base de dados Azure Cosmos. A lógica de aplicação de escrita no JavaScript permite a expressão natural do fluxo de controlo, deteção variável, atribuição e integração de primitivos de tratamento de exceção dentro das transações de base de dados diretamente na linguagem JavaScript.

Os procedimentos armazenados, gatilhos, UDFs e fusões baseados em JavaScript são embrulhados numa transação de ACID ambiente com isolamento instantâneo em todos os itens dentro da partição lógica. Durante a sua execução, se o programa JavaScript lançar uma exceção, toda a transação é abortada e revertida. O modelo de programação resultante é simples, mas poderoso. Os desenvolvedores javaScript obtêm um modelo de programação durável enquanto ainda usam as suas construções linguísticas familiares e primitivos de biblioteca.

A capacidade de executar o JavaScript diretamente dentro do motor de base de dados fornece desempenho e execução transacional de operações de base de dados contra os itens de um contentor. Além disso, uma vez que o motor de base de dados Azure Cosmos suporta de forma nativa json e JavaScript, não existe um desfasamento de impedância entre os sistemas tipo de uma aplicação e a base de dados.

## <a name="optimistic-concurrency-control"></a>Controlo da simultaneidade otimista

O controlo de concordância otimista permite evitar atualizações perdidas e eliminações. As operações contraditórias e simultâneas são submetidas ao bloqueio pessimista regular do motor de base de dados acolhido pela partição lógica que detém o item. Quando duas operações simultâneas tentam atualizar a versão mais recente de um item dentro de uma partição lógica, uma delas ganhará e a outra falhará. No entanto, se uma ou duas operações que tentam atualizar simultaneamente o mesmo item tinham lido previamente um valor mais antigo do item, a base de dados não sabe se o valor previamente lido por ambas ou ambas as operações conflituosas foi, de facto, o valor mais recente do item. Felizmente, esta situação pode ser detetada com o Controlo otimista da **Concurrency (OCC)** antes de permitir que as duas operações entrem no limite da transação dentro do motor de base de dados. O OCC protege os seus dados contra alterações de sobreposição acidental que foram feitas por outros. Também impede que outros exerçam acidentalmente as suas próprias alterações.

As atualizações simultâneas de um item são submetidas ao OCC pela camada de protocolo de comunicação da Azure Cosmos DB. A base de dados Azure Cosmos garante que a versão do lado do cliente do item que está a atualizar (ou a eliminar) é a mesma que a versão do item no recipiente Azure Cosmos. Isto garante que as suas escritas estão protegidas de serem substituídas acidentalmente pelas escritas de outros e vice-versa. Num ambiente multiutilizador, o controlo otimista de concordância protege-o de eliminar ou atualizar acidentalmente uma versão errada de um item. Como tal, os itens estão protegidos contra os infames problemas de "atualização perdida" ou "eliminação perdida".

Cada item armazenado num contentor Azure Cosmos tem uma propriedade definida pelo `_etag` sistema. O valor do `_etag` produto é gerado e atualizado automaticamente pelo servidor sempre que o item é atualizado. `_etag` pode ser usado com o cabeçalho de pedido fornecido pelo cliente `if-match` para permitir que o servidor decida se um item pode ser atualizado condicionalmente. O valor do `if-match` cabeçalho corresponde ao valor do `_etag` servidor, o item é então atualizado. Se o valor do cabeçalho de `if-match` pedido deixar de estar atual, o servidor rejeita a operação com uma mensagem de resposta "FALHA DE Pré-condição HTTP 412". O cliente pode então re-buscar o item para adquirir a versão atual do item no servidor ou sobrepor-se à versão do item no servidor com o seu próprio `_etag` valor para o item. Além disso, `_etag` pode ser usado com o `if-none-match` cabeçalho para determinar se é necessário um refetch de um recurso.

O valor do item `_etag` muda sempre que o item é atualizado. Para substituir as operações de artigo, `if-match` deve ser expressa explicitamente como parte das opções de pedido. Por exemplo, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). `_etag` os valores são verificados implicitamente para todos os itens escritos tocados pelo procedimento armazenado. Se for detetado algum conflito, o procedimento armazenado reverterá a transação e lançará uma exceção. Com este método, qualquer escrita ou nenhuma escrita dentro do procedimento armazenado são aplicadas atomicamente. Este é um sinal para a aplicação para reaplicar atualizações e re-tentar o pedido original do cliente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as transações de bases de dados e o controlo otimista da conuscção nos seguintes artigos:

- [Trabalhar com bases de dados, contentores e itens da Azure Cosmos](account-databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos de conflito e políticas de resolução](conflict-resolution-policies.md)
- [Procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md)
