---
title: Transações de bases de dados e controlo de condivisas otimista saca do Azure Cosmos DB
description: Este artigo descreve transações de bases de dados e controlo de condivisas otimista saca do Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806529"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transações e controlo de simultaneidade otimista

As transações de bases de dados fornecem um modelo de programação seguro e previsível para lidar com alterações simultâneas dos dados. As bases de dados relacionais tradicionais, como o SQL Server, permitem escrever a lógica do negócio utilizando procedimentos armazenados e/ou gatilhos, enviá-la para o servidor para ser executada diretamente dentro do motor de base de dados. Com bases de dados relacionais tradicionais, é-lhe exigido lidar com duas línguas de programação diferentes, a linguagem de programação de aplicações (não transacional), como JavaScript, Python, C#, Java, etc. e a linguagem de programação transacional (como t-SQL) que é executada de forma nativa pela base de dados.

O motor de base de dados em Azure Cosmos DB suporta transações completas de acid (Atomicity, Consistência, Isolamento, Durabilidade) com isolamento instantâneo. Todas as operações de base de dados no âmbito da [partição lógica](partition-data.md) de um contentor são executadas transacionadamente dentro do motor de base de dados que é alojado pela réplica da divisória. Estas operações incluem tanto a escrita (atualização de um ou mais itens dentro da partição lógica) como as operações de leitura. O quadro seguinte ilustra diferentes operações e tipos de transações:

| **Operação**  | **Tipo de Operação** | **Transação de itens únicos ou multi** |
|---------|---------|---------|
| Inserir (sem um gatilho pré/post) | Escrita | Transação de item único |
| Inserir (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Substitua (sem um gatilho pré/post) | Escrita | Transação de item único |
| Substitua (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Upsert (sem um gatilho pré/post) | Escrita | Transação de item único |
| Upsert (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Eliminar (sem um gatilho pré/post) | Escrita | Transação de item único |
| Eliminar (com um gatilho pré/post) | Escrever e Ler | Transação multi-item |
| Executar procedimento armazenado | Escrever e Ler | Transação multi-item |
| Sistema iniciado execução de um procedimento de fusão | Escrita | Transação multi-item |
| Sistema iniciado execução de itens de aleting com base na expiração (TTL) de um item | Escrita | Transação multi-item |
| Leitura | Leitura | Transação de item único |
| Feed de Alterações | Leitura | Transação multi-item |
| Leitura paginada | Leitura | Transação multi-item |
| Consulta Paginada | Leitura | Transação multi-item |
| Executar a UDF como parte da consulta paginada | Leitura | Transação multi-item |

## <a name="multi-item-transactions"></a>Transações multi-item

O Azure Cosmos DB permite-lhe escrever [procedimentos armazenados, gatilhos pré/post, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md) e procedimentos de fusão no JavaScript. A Azure Cosmos DB suporta de forma nativa a execução do JavaScript dentro do seu motor de base de dados. Pode registar procedimentos armazenados, gatilhos pré/post, funções definidas pelo utilizador (UDFs) e fundir procedimentos num recipiente e executá-los posteriormente de forma transacional dentro do motor de base de dados Azure Cosmos. A lógica da aplicação de escrita no JavaScript permite a expressão natural do fluxo de controlo, da deteção variável, da atribuição e integração de primitivos de manipulação de exceções dentro das transações de base de dados diretamente na linguagem JavaScript.

Os procedimentos, gatilhos, UDFs e procedimentos de fusão baseados no JavaScript estão envolvidos numa transação de ACID ambiente com isolamento instantâneo em todos os itens dentro da divisória lógica. Durante a sua execução, se o programa JavaScript lançar uma exceção, toda a transação é abortada e enrolada. O modelo de programação resultante é simples, mas poderoso. Os desenvolvedores javaScript obtêm um modelo de programação durável enquanto ainda usam as suas construções linguísticas familiares e primitivos de biblioteca.

A capacidade de executar o JavaScript diretamente dentro do motor de base de dados fornece desempenho e execução transacional de operações de base de dados contra os itens de um recipiente. Além disso, uma vez que o motor de base de dados Azure Cosmos suporta nativamente o JSON e o JavaScript, não existe um desfasamento de impedância entre os sistemas de tipo de uma aplicação e a base de dados.

## <a name="optimistic-concurrency-control"></a>Controlo de moeda otimista

O controlo de moeda sinuosa permite evitar atualizações e eliminações perdidas. Operações simultâneas e conflituosas estão sujeitas ao bloqueio pessimista regular do motor de base de dados alojado pela partição lógica que detém o item. Quando duas operações simultâneas tentarem atualizar a versão mais recente de um item dentro de uma divisória lógica, uma delas ganhará e a outra falhará. No entanto, se uma ou duas operações que tentam atualizar simultaneamente o mesmo item tinham lido previamente um valor mais antigo do item, a base de dados não sabe se o valor previamente lido por qualquer uma das operações contraditórias foi, de facto, o valor mais recente do item. Felizmente, esta situação pode ser detetada com o **Optimistic Concurrency Control (OCC)** antes de deixar as duas operações entrarem no limite de transação dentro do motor de base de dados. A OCC protege os seus dados de alterações de sobreposição acidental que foram feitas por outros. Também impede que outros sobreponham acidentalmente as suas próprias alterações.

As atualizações simultâneas de um item são submetidas ao OCC pela camada de protocolo de comunicação da Azure Cosmos DB. A base de dados Azure Cosmos garante que a versão do lado do cliente do item que está a atualizar (ou apagando) é a mesma que a versão do item no contentor Azure Cosmos. Isto garante que as suas escritas estão protegidas de serem substituídas acidentalmente pelos escritos de outros e vice-versa. Num ambiente multiutilizador, o controlo de moeda otimista protege-o de apagar ou atualizar acidentalmente a versão errada de um item. Como tal, os itens estão protegidos contra os infames problemas de "atualização perdida" ou "eliminação perdida".

Cada item armazenado num contentor Azure Cosmos `_etag` tem um sistema de propriedade definida. O valor `_etag` do é gerado e atualizado automaticamente pelo servidor sempre que o item é atualizado. `_etag`pode ser usado com `if-match` o cabeçalho de pedido fornecido pelo cliente para permitir que o servidor decida se um item pode ser atualizado condicionalmente. O valor `if-match` do cabeçalho corresponde `_etag` ao valor do servidor, o item é então atualizado. Se o valor `if-match` do cabeçalho de pedido já não estiver atual, o servidor rejeita a operação com uma mensagem de resposta "HTTP 412 Precondition failure". O cliente pode então re-obter o item para adquirir a versão atual do item no servidor `_etag` ou anular a versão do item no servidor com o seu próprio valor para o item. Além `_etag` disso, pode `if-none-match` ser usado com o cabeçalho para determinar se é necessária uma rebusca de um recurso.

O valor do `_etag` artigo muda cada vez que o item é atualizado. Para operações de `if-match` substituição do artigo, deve ser explicitamente expressa como parte das opções de pedido. Por exemplo, consulte o código da amostra no [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`os valores são verificados implicitamente para todos os itens escritos tocados pelo procedimento armazenado. Se for detetado algum conflito, o procedimento armazenado reverterá a transação e lançará uma exceção. Com este método, todas ou nenhumas escritas dentro do procedimento armazenado são aplicadas atomicamente. Este é um sinal para a aplicação para reaplicar atualizações e rejulgar o pedido original do cliente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre transações de bases de dados e controlo de moedas otimistas nos seguintes artigos:

- [Trabalhar com bases de dados da Azure Cosmos, contentores e itens](databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos de conflito e políticas de resolução](conflict-resolution-policies.md)
- [Procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md)
