---
title: Armazenamento de tabela De design Azure para modificação de dados Microsoft Docs
description: Tabelas de design para modificação de dados no armazenamento da tabela Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75771551"
---
# <a name="design-for-data-modification"></a>Design da modificação de dados
Este artigo centra-se nas considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, terá de avaliar a compensação entre desenhos que otimizam a consulta contra designs que otimizam a modificação de dados tal como faz nos desenhos para bases de dados relacionais (embora as técnicas de gestão das compensações de design sejam diferentes numa base de dados relacional). A secção Table Design Patterns descreve alguns padrões de design detalhados para o serviço de mesa e destaca algumas destas trocas. Na prática, vai descobrir que muitos projetos otimizados para consultas de entidades também funcionam bem para modificar entidades.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de inserção, atualização e eliminação de operações
Para atualizar ou eliminar uma entidade, deve ser capaz de identificá-la utilizando os valores **PartitionKey** e **RowKey.** A este respeito, a sua escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhantes à sua escolha para apoiar consultas de pontos porque pretende identificar as entidades da forma mais eficiente possível. Não pretende utilizar uma partilha ou digitalização de mesa ineficiente para localizar uma entidade para descobrir os valores **PartitionKey** e **RowKey** que precisa para atualizá-lo ou eliminá-lo.  

Os seguintes padrões no endereço de padrões de design de tabela seletivas otimizam o desempenho ou a sua inserção, atualização e eliminação de operações:  

* Padrão de [eliminação](table-storage-design-patterns.md#high-volume-delete-pattern) de alto volume - Permitir a eliminação de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea na sua própria tabela separada; elimina as entidades eliminando a tabela.  
* [Padrão](table-storage-design-patterns.md#data-series-pattern) de série de dados - Armazenar séries completas de dados numa única entidade para minimizar o número de pedidos que efaz.  
* [Amplo padrão de entidades](table-storage-design-patterns.md#wide-entities-pattern) - Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* Padrão de [grandes entidades](table-storage-design-patterns.md#large-entities-pattern) - Use o armazenamento de blob para armazenar grandes valores de propriedade.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Garantir a consistência nas suas entidades armazenadas
O outro fator-chave que influencia a sua escolha de chaves para otimizar modificações de dados é como garantir a consistência utilizando transações atómicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma divisória.  

Os seguintes padrões no artigo Padrões de design de [tabelas](table-storage-design-patterns.md) abordam a consistência da gestão:  

* Padrão de [índice secundário intra-divisória](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Armazenar várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma divisória) para permitir procurações rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* Padrão de [índice secundário inter-divisória](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores RowKey em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Eventualmente consistente padrão de transações](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Ativar eventualmente comportamento consistente através dos limites da divisória ou do sistema de armazenamento usando as filas Azure.
* [Padrão de entidades indexadas](table-storage-design-patterns.md#index-entities-pattern) - Manter entidades indexadas para permitir pesquisas eficientes que devolvem listas de entidades.  
* Padrão de [desnormalização](table-storage-design-patterns.md#denormalization-pattern) - Combine dados relacionados numa única entidade para que possa recuperar todos os dados necessários com uma consulta de ponto único.  
* [Padrão](table-storage-design-patterns.md#data-series-pattern) de série de dados - Armazenar séries completas de dados numa única entidade para minimizar o número de pedidos que efaz.  

Para obter informações sobre transações de grupos de entidades, consulte a secção [De operações do grupo Entidade.](table-storage-design.md#entity-group-transactions)  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir o seu design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consulta eficiente resulta em modificações eficientes, mas você deve sempre avaliar se este é o caso para o seu cenário específico. Alguns dos padrões do artigo [Padrões](table-storage-design-patterns.md) de Design de Tabela avaliam explicitamente as compensações entre consultas e entidades modificadas, e deve sempre ter em conta o número de cada tipo de operação.  

Os seguintes padrões no artigo Padrões de design de [tabelas](table-storage-design-patterns.md) abordam as compensações entre a conceção de consultas eficientes e a conceção de uma modificação eficiente dos dados:  

* [Padrão de chave composto](table-storage-design-patterns.md#compound-key-pattern) - Utilize valores compostos **RowKey** para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  
* [Padrão de cauda de log](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* mais recentemente adicionadas a uma partição usando um valor **RowKey** que classifica em data e ordem de tempo inversa.  
