---
title: Design Azure Table armazenamento para modificação de dados / Microsoft Docs
description: Tabelas de conceção para modificação de dados no armazenamento da tabela Azure. Otimize as operações de inserção, atualização e exclusão. Garantir consistência nas suas entidades armazenadas.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236765"
---
# <a name="design-for-data-modification"></a>Design da modificação de dados
Este artigo centra-se nas considerações de design para otimizar inserções, atualizações e eliminações. Em alguns casos, você precisará avaliar o trade-off entre designs que otimizam para consulta contra designs que otimizam para modificação de dados, assim como você faz em designs para bases de dados relacionais (embora as técnicas de gestão das trade-offs de design são diferentes numa base de dados relacional). A secção Table Design Patterns descreve alguns padrões de design detalhados para o serviço de mesa e destaca alguns destes trade-offs. Na prática, verá que muitos projetos otimizados para consultas de entidades também funcionam bem para modificar entidades.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho das operações de inserção, atualização e exclusão
Para atualizar ou eliminar uma entidade, deve ser capaz de identificá-la utilizando os valores **PartitionKey** e **RowKey.** A este respeito, a sua escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhantes à sua escolha para apoiar consultas de pontos porque pretende identificar as entidades da forma mais eficiente possível. Não pretende utilizar uma partição ineficiente ou uma verificação de tabelas para localizar uma entidade para descobrir os valores **PartitionKey** e **RowKey** de que necessita para atualizar ou eliminar.  

Os seguintes padrões na secção Os padrões de design da tabela endereçam otimizar o desempenho ou a sua inserção, atualização e apagar operações:  

* [Padrão de eliminação](table-storage-design-patterns.md#high-volume-delete-pattern) de volume elevado - Permitir a supressão de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea em seu próprio quadro separado; elimina as entidades eliminando a tabela.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) - Guarde séries de dados completas numa única entidade para minimizar o número de pedidos que es faz.  
* [Padrão de entidades amplas](table-storage-design-patterns.md#wide-entities-pattern) - Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de grandes entidades](table-storage-design-patterns.md#large-entities-pattern) - Use o armazenamento de blob para armazenar grandes valores de propriedade.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Garantir consistência nas suas entidades armazenadas
O outro fator-chave que influencia a escolha das chaves para otimizar as modificações de dados é como garantir a consistência utilizando transações atómicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma divisão.  

Os seguintes padrões no artigo [Padrões de design](table-storage-design-patterns.md) de tabela abordam a consistência da gestão:  

* [Padrão de índice secundário intra-partição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma partição) para permitir análises rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Padrão de índice secundário inter-partição](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores RowKey em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Padrão de transações eventualmente consistente](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Ative um comportamento eventualmente consistente através dos limites de partição ou do sistema de armazenamento usando as filas Azure.
* [Padrão de entidades indexadas](table-storage-design-patterns.md#index-entities-pattern) - Manter entidades indexadas para permitir pesquisas eficientes que retornem listas de entidades.  
* [Padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) - Combine dados relacionados numa única entidade para que possa recuperar todos os dados de que necessita com uma única consulta de ponto.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) - Guarde séries de dados completas numa única entidade para minimizar o número de pedidos que es faz.  

Para obter informações sobre as transações de grupos de entidades, consulte a secção [Operações do grupo Entity](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Certifique-se de que o seu design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas deve sempre avaliar se este é o caso para o seu cenário específico. Alguns dos padrões do artigo [Padrões](table-storage-design-patterns.md) de Design de Tabela avaliam explicitamente as compensações entre consultas e entidades modificantes, e deve sempre ter em conta o número de cada tipo de operação.  

Os seguintes padrões no artigo Padrões de conceção de [tabelas](table-storage-design-patterns.md) abordam trocas entre a conceção de consultas eficientes e a conceção de uma modificação eficiente dos dados:  

* [Padrão de chave composto](table-storage-design-patterns.md#compound-key-pattern) - Use valores compostos **rowKey** para permitir que um cliente procure dados relacionados com uma única consulta de ponto.  
* [Registar o padrão da cauda](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* mais recentemente adicionadas a uma partição utilizando um valor **RowKey** que se classifica na ordem de data e hora inversa.  
