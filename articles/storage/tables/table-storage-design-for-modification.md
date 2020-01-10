---
title: Projetar o armazenamento de tabelas do Azure para modificação de dados | Microsoft Docs
description: Projetar tabelas para modificação de dados no armazenamento de tabelas do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771551"
---
# <a name="design-for-data-modification"></a>Design da modificação de dados
Este artigo se concentra nas considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, terá de avaliar os designs que otimizar para a consulta em relação a designs que otimizar para modificação de dados, tal como acontece no designs para bases de dados relacionais (embora as técnicas para gerenciar as compensações de design são diferentes aspetos diferentes na base de dados relacional). A seção padrões de design de tabela descreve alguns padrões de design detalhados para o serviço tabela e realça algumas dessas compensações. Na prática, verá que muitos designs otimizados para consultar entidades também funcionam bem para modificar entidades.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de operações de inserção, atualização e exclusão
Para atualizar ou eliminar uma entidade, deve conseguir identificá-lo utilizando o **PartitionKey** e **RowKey** valores. Nesse sentido, a sua escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhante à sua escolha para oferecer suporte a consultas de ponto de como deseja identificar entidades como forma mais eficiente possível. Não pretende utilizar uma análise de tabela ou da partição ineficiente localizar uma entidade para detetar os **PartitionKey** e **RowKey** valores tem de atualizar ou eliminá-lo.  

Os seguintes padrões na seção padrões de design de tabela abordam a otimização do desempenho ou as operações de inserção, atualização e exclusão:  

* [Eliminar o volume alto padrão de](table-storage-design-patterns.md#high-volume-delete-pattern) -ativar a eliminação de um grande volume de entidades ao armazenar todas as entidades para eliminação simultânea em sua própria tabela separada; para eliminar as entidades a eliminar a tabela.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) -série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  
* [Padrão de entidades ampla](table-storage-design-patterns.md#wide-entities-pattern) -utilizar várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de entidades grandes](table-storage-design-patterns.md#large-entities-pattern) -utilizar o armazenamento de BLOBs para armazenar os valores de propriedade grandes.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Garanta a consistência em suas entidades armazenadas
O fator principal que influencia à sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência com transações atômicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma partição.  

Os seguintes padrões no artigo [padrões de design de tabela](table-storage-design-patterns.md) abordam o gerenciamento de consistência:  

* [Padrão de índice secundário de intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores.  
* [Padrão de índice secundário de partição entre](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes RowKey valores em partições separadas ou em tabelas separadas para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena usando diferentes **RowKey** valores.  
* [Padrão de transações eventualmente consistente](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -ativar o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.
* [Padrão de entidades de índice](table-storage-design-patterns.md#index-entities-pattern) – manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  
* [Padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) -combinar dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  
* [Padrão de série de dados](table-storage-design-patterns.md#data-series-pattern) -série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

Para obter informações sobre transações de grupo de entidades, consulte a seção [transações do grupo de entidades](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garanta que seu design para modificações eficientes facilite consultas eficientes
Em muitos casos, uma estrutura para os resultados da consultas eficientes modificações eficientes, mas sempre deve avaliar se for este o caso para o seu cenário específico. Alguns dos padrões no artigo padrões de [design de tabela](table-storage-design-patterns.md) avaliam explicitamente as compensações entre consulta e modificação de entidades, e você deve sempre levar em conta o número de cada tipo de operação.  

Os padrões a seguir no artigo [padrões de design de tabela](table-storage-design-patterns.md) abordam as compensações entre o design para consultas eficientes e o design para uma modificação eficiente de dados:  

* [Padrão de chave composta](table-storage-design-patterns.md#compound-key-pattern) – use valores de **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  
* [Padrão de cauda do registo](table-storage-design-patterns.md#log-tail-pattern) -obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar uma **RowKey** valor ordena inversa de data e a ordem de tempo.  
