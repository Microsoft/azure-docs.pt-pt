---
title: Transações de banco de dados e controle de simultaneidade otimista no Azure Cosmos DB
description: Este artigo descreve as transações de banco de dados e o controle de simultaneidade otimista no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b58255aa471fe78c84b5f6a7432c0f3d402f0875
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467906"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transações e controlo de simultaneidade otimista

As transações de banco de dados fornecem um modelo de programação seguro e previsível para lidar com alterações simultâneas nos dados. Os bancos de dados relacionais tradicionais, como SQL Server, permitem que você grave a lógica de negócios usando procedimentos armazenados e/ou gatilhos, envie-o para o servidor para execução diretamente no mecanismo de banco de dados. Com os bancos de dados relacionais tradicionais, você precisa lidar com duas linguagens de programação diferentes da linguagem de programação de aplicativo (não transacional), como JavaScript, C#Python, Java, etc., e a linguagem de programação transacional ( como T-SQL) que é executado nativamente pelo banco de dados.

O mecanismo de banco de dados no Azure Cosmos DB dá suporte a transações inteiras em conformidade com ACID (atomicidade, consistência, isolamento, durabilidade) com isolamento de instantâneo. Todas as operações de banco de dados no escopo da [partição lógica](partition-data.md) de um contêiner são executadas de forma transacional no mecanismo de banco de dados hospedado pela réplica da partição. Essas operações incluem gravação (Atualizando um ou mais itens dentro da partição lógica) e operações de leitura. A tabela a seguir ilustra diferentes operações e tipos de transação:

| **Operação**  | **Tipo de operação** | **Transação de um ou vários itens** |
|---------|---------|---------|
| Inserir (sem um gatilho anterior/pós) | Escrita | Transação de item único |
| Inserir (com um gatilho anterior/pós) | Gravar e ler | Transação de vários itens |
| Substituir (sem um gatilho anterior/pós) | Escrita | Transação de item único |
| Substituir (por um gatilho pre/post) | Gravar e ler | Transação de vários itens |
| Upsert (sem um gatilho anterior/pós) | Escrita | Transação de item único |
| Upsert (com um gatilho anterior/pós) | Gravar e ler | Transação de vários itens |
| Excluir (sem um gatilho anterior/pós) | Escrita | Transação de item único |
| Excluir (com um gatilho anterior/pós) | Gravar e ler | Transação de vários itens |
| Executar procedimento armazenado | Gravar e ler | Transação de vários itens |
| Execução iniciada pelo sistema de um procedimento de mesclagem | Escrita | Transação de vários itens |
| O sistema iniciou a execução da exclusão de itens com base na expiração (TTL) de um item | Escrita | Transação de vários itens |
| Leitura | Leitura | Transação de item único |
| Feed de Alterações | Leitura | Transação de vários itens |
| Leitura paginada | Leitura | Transação de vários itens |
| Consulta paginada | Leitura | Transação de vários itens |
| Executar UDF como parte da consulta paginada | Leitura | Transação de vários itens |

## <a name="multi-item-transactions"></a>Transações de vários itens

Azure Cosmos DB permite que você escreva [procedimentos armazenados, gatilhos pre/post, UDFs (funções definidas pelo usuário)](stored-procedures-triggers-udfs.md) e procedimentos de mesclagem em JavaScript. Azure Cosmos DB nativamente dá suporte à execução de JavaScript dentro de seu mecanismo de banco de dados. Você pode registrar procedimentos armazenados, gatilhos pre/post, UDFs (funções definidas pelo usuário) e procedimentos de mesclagem em um contêiner e, posteriormente, executá-los de forma transacional no mecanismo de banco de dados Cosmos do Azure. Gravar a lógica do aplicativo em JavaScript permite a expressão natural do fluxo de controle, o escopo da variável, a atribuição e a integração de primitivos de manipulação de exceção nas transações do banco de dados diretamente na linguagem JavaScript.

Os procedimentos armazenados, gatilhos, UDFs e procedimentos de mesclagem baseados em JavaScript são encapsulados em uma transação ACID ambiente com isolamento de instantâneo em todos os itens dentro da partição lógica. Durante a sua execução, se o programa JavaScript lançar uma exceção, toda a transação será anulada e revertida. O modelo de programação resultante é simples, mas poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação durável e, ao mesmo tempo, usam suas estruturas de linguagem familiares e primitivos de biblioteca.

A capacidade de executar o JavaScript diretamente no mecanismo de banco de dados fornece desempenho e execução transacional de operações de banco de dados em relação aos itens de um contêiner. Além disso, como o mecanismo de banco de dados Cosmos do Azure dá suporte nativo a JSON e JavaScript, não há nenhuma incompatibilidade de impedância entre os sistemas de tipos de um aplicativo e o banco de dados.

## <a name="optimistic-concurrency-control"></a>Controle de simultaneidade otimista 

O controle de simultaneidade otimista permite que você impeça atualizações e exclusões perdidas. Operações simultâneas e conflitantes estão sujeitas ao bloqueio pessimista regular do mecanismo de banco de dados hospedado pela partição lógica que possui o item. Quando duas operações simultâneas tentam atualizar a versão mais recente de um item em uma partição lógica, uma delas vencerá e a outra falhará. No entanto, se uma ou duas operações tentarem atualizar simultaneamente o mesmo item tiver lido anteriormente um valor mais antigo do item, o banco de dados não saberá se o valor lido anteriormente por uma ou ambas as operações conflitantes era realmente o valor mais recente do item. Felizmente, essa situação pode ser detectada com o **controle de simultaneidade otimista (OCC)** antes de permitir que as duas operações insiram o limite da transação dentro do mecanismo de banco de dados. O OCC protege seus dados contra a substituição acidental de alterações feitas por outras pessoas. Ele também impede que outras pessoas substituam acidentalmente suas próprias alterações.

As atualizações simultâneas de um item estão sujeitas ao OCC pela camada de protocolo de comunicação do Azure Cosmos DB. O banco de dados Cosmos do Azure garante que a versão do lado do cliente do item que você está atualizando (ou excluindo) seja a mesma que a versão do item no contêiner Cosmos do Azure. Isso garante que suas gravações sejam protegidas acidentalmente por gravações de outros e vice-versa. Em um ambiente de vários usuários, o controle de simultaneidade otimista protege você contra acidentalmente exclusão ou atualização de versão incorreta de um item. Dessa forma, os itens são protegidos contra os problemas infamosos de "atualização perdida" ou "exclusão perdida".

Cada item armazenado em um contêiner Cosmos do Azure tem uma propriedade `_etag` definida pelo sistema. O valor de `_etag` é automaticamente gerado e atualizado pelo servidor sempre que o item é atualizado. `_etag`pode ser usado com o cabeçalho de `if-match` solicitação fornecido pelo cliente para permitir que o servidor decida se um item pode ser atualizado condicionalmente. O valor do `if-match` cabeçalho corresponde ao valor `_etag` de no servidor, o item é atualizado. Se o valor do cabeçalho `if-match` de solicitação não for mais atual, o servidor rejeitará a operação com uma mensagem de resposta "falha na pré-condição http 412". Em seguida, o cliente pode buscar novamente o item para adquirir a versão atual do item no servidor ou substituir a versão do item no servidor pelo seu próprio `_etag` valor para o item. Além disso, `_etag` o pode ser usado com `if-none-match` o cabeçalho para determinar se uma rebusca de um recurso é necessária. 

O valor do `_etag` item é alterado toda vez que o item é atualizado. Para operações de substituição de `if-match` item, deve ser expresso explicitamente como parte das opções de solicitação. Para obter um exemplo, consulte o código de exemplo no [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag`os valores são verificados implicitamente para todos os itens gravados tocadas pelo procedimento armazenado. Se qualquer conflito for detectado, o procedimento armazenado reverterá a transação e lançará uma exceção. Com esse método, todas ou nenhuma gravação no procedimento armazenado é aplicada atomicamente. Esse é um sinal para o aplicativo reaplicar atualizações e tentar novamente a solicitação original do cliente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre transações de banco de dados e controle de simultaneidade otimista nos seguintes artigos:

- [Trabalhando com bancos de dados, contêineres e itens do Azure Cosmos](databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos de conflito e políticas de resolução](conflict-resolution-policies.md)
- [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md)
