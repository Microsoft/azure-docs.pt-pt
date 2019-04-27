---
title: Transações de base de dados e o controlo de simultaneidade otimista no Azure Cosmos DB
description: Este artigo descreve as transações de base de dados e o controlo de simultaneidade otimista no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 568f47aacf39793d4c2da46798682abc002ca33b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889360"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transações e controlo de simultaneidade otimista

Transações da base de dados fornecem um modelo de programação seguro e previsível para lidar com alterações simultâneas nos dados. O bases de dados de relacionais tradicionais, como o SQL Server, permitem-lhe escrever a lógica de negócios usando procedimentos armazenados e/ou acionadores, enviá-lo para o servidor para execução diretamente no motor de base de dados. Com bases de dados relacionais tradicionais, é necessário para lidar com duas linguagens de programação diferentes, o idioma, como JavaScript, Python, de programação de aplicações (não transacional) C#, Java, etc. e o transacional (linguagem de programação Por exemplo, o T-SQL) que é executada nativamente pela base de dados.

O motor de base de dados do Azure Cosmos DB suporta completas de transações em conformidade de ACID (atomicidade, consistência, isolamento, durabilidade) com o isolamento do instantâneo. Todas as bases de dados operações dentro do escopo de um contentor [partição lógica](partition-data.md) ao nível das transações são executados dentro do motor de base de dados que é alojado pela réplica da partição. Estas operações incluem ambos escrever (atualizar um ou mais itens dentro da partição lógica) e operações de leitura. A tabela seguinte ilustra as operações diferentes e tipos de relatada:

| **Operação**  | **Tipo de operação** | **Único ou a transação de Item de múltiplas** |
|---------|---------|---------|
| Inserir (sem um acionador de pré/pós) | Escrita | Transação de item único |
| Inserir (com um acionador de pré/pós) | Escrever e ler | Transação de item multi |
| Substituir (sem um acionador de pré/pós) | Escrita | Transação de item único |
| Substituir (com um acionador de pré/pós) | Escrever e ler | Transação de item multi |
| Upsert (sem um acionador de pré/pós) | Escrita | Transação de item único |
| Upsert (com um acionador de pré/pós) | Escrever e ler | Transação de item multi |
| Eliminar (sem um acionador de pré/pós) | Escrita | Transação de item único |
| Eliminar (com um acionador de pré/pós) | Escrever e ler | Transação de item multi |
| Executar procedimento armazenado | Escrever e ler | Transação de item multi |
| Sistema iniciou a execução de um procedimento de intercalação | Escrita | Transação de item multi |
| Sistema iniciou a execução da eliminação de itens com base em expiração (TTL) de um item | Escrita | Transação de item multi |
| Leitura | Leitura | Transação de item único |
| Feed de Alterações | Leitura | Transação de item multi |
| Leitura paginada | Leitura | Transação de item multi |
| Consulta paginada | Leitura | Transação de item multi |
| Executar UDF como parte da consulta paginada | Leitura | Transação de item multi |

## <a name="multi-item-transactions"></a>Transações do item multi

O Azure Cosmos DB permite-lhe escrever [procedimentos armazenados, acionadores de pré/pós,-funções definidas pelo utilizador-(UDFs)](stored-procedures-triggers-udfs.md) e intercalar procedimentos em JavaScript. O Azure Cosmos DB suporta nativamente a execução de JavaScript dentro de seu motor de base de dados. Pode registrar procedimentos armazenados, pré/pós acionadores, -funções definidas pelo utilizador-(UDFs) e procedimentos de intercalação no contentor e versões posteriores execução-los ao nível das transações dentro do motor de base de dados do Cosmos do Azure. Escrever a lógica do aplicativo em JavaScript permite a natural expressão de fluxo de controle, definir o âmbito das variáveis, atribuição e integração de primitivos dentro as transações de base de dados diretamente na linguagem JavaScript de manipulação de exceção.

O JavaScript com base em procedimentos armazenados, acionadores, UDFs e procedimentos de mesclagem são encapsulados dentro de uma transação do ambiente ACID com isolamento de instantâneo em todos os itens dentro da partição lógica. Durante a sua execução, se o programa de JavaScript emitir uma exceção, toda a transação foi abortada e revertidas. O modelo de programação resultante é simples mas poderosa. Os desenvolvedores de JavaScript obtém um modelo de programação durável, enquanto ainda usando sua linguagem familiar constrói e primitivos de biblioteca.

A capacidade de execução do JavaScript diretamente dentro do motor de base de dados proporciona desempenho e a execução transacional de operações de base de dados dos itens de um contentor. Além disso, uma vez que o motor de base de dados do Cosmos do Azure suporte nativo para JSON e JavaScript, não há nenhum incompatibilidade de impedância entre os sistemas de tipo de um aplicativo e a base de dados.

## <a name="optimistic-concurrency-control"></a>Controlo de simultaneidade otimista 

Controlo de simultaneidade otimista, pode impedir que as atualizações perdidas e eliminações. Operações simultâneas em conflito estão sujeitos ao bloqueio pessimista regulares do mecanismo de banco de dados hospedado pela partição lógica que é proprietário do item. Quando duas operações simultâneas tentam atualizar a versão mais recente de um item dentro de uma partição lógica, uma delas vencem e o outro falhará. No entanto, se uma ou duas operações de tentar atualizar simultaneamente no mesmo item anteriormente tinham lido um valor mais antigo do item, a base de dados não sabe se o valor de leitura anteriormente por uma ou ambas as operações em conflito foi, de fato, o valor mais recente do item. Felizmente, esta situação pode ser detectada com o **controlo de simultaneidade otimista (OCC)** antes permitindo que as duas operações introduza o limite de transação dentro do motor de base de dados. OCC protege os dados a substituição acidental as alterações efetuadas por outras pessoas. Ele também impede que outras pessoas a substituição acidental as suas próprias alterações.

As atualizações em simultâneo de um item estão sujeitos ao OCC pela camada de protocolo de comunicação do Azure Cosmos DB. Base de dados do Cosmos do Azure garante que a versão do lado do cliente do item que está a atualizar (ou a eliminar) é o mesmo que a versão do item no contentor do Cosmos do Azure. Isso garante que as escritas são protegidas contra a ser substituídas acidentalmente pelas gravações de outras pessoas e vice-versa. Num ambiente de vários utilizador, o controle de simultaneidade otimista protege contra o acidentalmente a eliminar ou atualizar a versão incorreta de um item. Como tal, os itens estão protegidos contra a infame "atualização perdida" ou "eliminar perdido" problemas.

Cada item armazenado num contentor do Cosmos do Azure com o sistema definido `_etag` propriedade. O valor da `_etag` é gerado automaticamente e atualizado pelo servidor toda vez que o item é atualizado. `_etag` pode ser utilizado com fornecido pelo cliente `if-match` cabeçalho do pedido para permitir que o servidor decidir se um item pode ser atualizado de forma condicional. O valor do `if-match` cabeçalho corresponde ao valor da `_etag` no servidor, o item, em seguida, é atualizado. Se o valor da `if-match` cabeçalho do pedido já não está disponível, o servidor rejeita a operação com um "HTTP 412 Falha de pré-condição" mensagem de resposta. O cliente, em seguida, pode obter novamente o item de adquirir a versão atual do item no servidor ou substituir a versão do item no servidor com o seu próprio `_etag` valor para o item. Além disso, `_etag` pode ser utilizado com o `if-none-match` cabeçalho para determinar se um refetch de um recurso é necessário. 

O item `_etag` valor alterações sempre que o item for atualizado. Para operações de item de substituição, `if-match` tem de ser explicitamente expresso como parte das opções de pedido. Por exemplo, veja o código de exemplo [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` valores implicitamente são verificados para todos os itens escritos tocados pelo procedimento armazenado. Se for detectado qualquer conflito, o procedimento armazenado irá reverter a transação e lançar uma exceção. Com esse método, gravações de todos ou nenhum dentro do procedimento armazenado sejam aplicadas automaticamente. Este é um sinal para a aplicação para voltar a aplicar atualizações e repita o pedido do cliente original.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre transações de base de dados e o controlo de simultaneidade otimista nos seguintes artigos:

- [Trabalhar com bases de dados do Cosmos do Azure, contentores e itens](databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos de conflito e diretivas de resolução](conflict-resolution-policies.md)
- [Procedimentos armazenados, acionadores e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md)
