---
title: Design Azure Table armazenamento para consultas | Microsoft Docs
description: Tabelas de design para consultas no armazenamento da mesa Azure. Escolha uma chave de partição apropriada, otimize as consultas e sortifique os dados para o serviço Tabela.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93316159"
---
# <a name="design-for-querying"></a>Design das consultas
As soluções de serviço de mesa podem ser lidas intensivamente, escrever intensivamente ou uma mistura das duas. Este artigo centra-se nas coisas a ter em conta quando está a desenhar o seu serviço de Mesa para suportar as operações de leitura de forma eficiente. Tipicamente, um design que suporta operações de leitura eficientemente também é eficiente para operações de escrita. No entanto, existem considerações adicionais a ter em conta ao conceber operações de escrita, discutidas no artigo [Design para modificação de dados](table-storage-design-for-modification.md).

Um bom ponto de partida para conceber a sua solução de serviço de mesa para permitir que leia os dados de forma eficiente é perguntar "Que consultas a minha aplicação precisará para executar para recuperar os dados de que precisa do serviço table?"  

> [!NOTE]
> Com o serviço Table, é importante corrigir o design na frente porque é difícil e caro mudá-lo mais tarde. Por exemplo, numa base de dados relacional é muitas vezes possível abordar problemas de desempenho simplesmente adicionando índices a uma base de dados existente: esta não é uma opção com o serviço de Tabela.  
> 
> 

Esta secção centra-se nas questões-chave que deve abordar quando desenha as suas tabelas para consulta. Os tópicos abordados nesta secção incluem:

* [Como a sua escolha de PartitionKey e RowKey impacta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolher uma PartitionKey apropriada](#choosing-an-appropriate-partitionkey)
* [Otimização de consultas para o serviço Mesa](#optimizing-queries-for-the-table-service)
* [Triagem de dados no serviço tabela](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como a sua escolha de PartitionKey e RowKey impacta o desempenho da consulta
Os exemplos a seguir assumem que o serviço de mesa está a armazenar entidades de empregados com a seguinte estrutura (a maioria dos exemplos omitem a propriedade **Timestamp** para maior clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (Nome do Departamento) |String |
| **RowKey** (ID do empregado) |String |
| **FirstName** |String |
| **LastName** |String |
| **Age** |Número inteiro |
| **EmailAddress** |String |

A [visão geral de armazenamento da tabela Azure](table-storage-overview.md) descreve algumas das principais características do serviço Azure Table que têm uma influência direta na conceção para consulta. Estes resultados resultam nas seguintes diretrizes gerais para a conceção de consultas de serviço de tabela. Note que a sintaxe do filtro utilizada nos exemplos abaixo é do serviço de tabela REST API, para mais informações ver [Entidades De Consulta](/rest/api/storageservices/Query-Entities).  

* A ***Point Query** _ é o lookup mais eficiente para usar e é recomendado para ser usado para consultas ou procuras de alto volume que requerem a latência mais baixa. Tal consulta pode usar os índices para localizar uma entidade individual de forma muito eficiente, especificando tanto os valores _ *PartitionKey** como **o RowKey.** Por exemplo: $filter=(PartitionKey eq 'Sales') e (RowKey eq '2')  
* O segundo melhor é uma **_ Range Query** _ que usa o _ *PartitionKey** e filtra uma gama de valores **RowKey** para devolver mais do que uma entidade. O valor **PartitionKey** identifica uma divisória específica, e os valores **RowKey** identificam um subconjunto das entidades nessa partição. Por exemplo: $filter=PartitionKey eq 'Sales' e RowKey ge 'S' e RowKey lt 'T'  
* O terceiro melhor é um ***Partition Scan** _ que usa o _ *PartitionKey** e filtra em outra propriedade não chave e que pode devolver mais do que uma entidade. O valor **PartitionKey** identifica uma divisória específica, e os valores de propriedade selecionam para um subconjunto das entidades nessa partição. Por exemplo: $filter=PartitionKey eq 'Sales' e LastName eq 'Smith'  
* A ***Table Scan** _ não inclui o _ *PartitionKey** e é muito ineficiente porque procura todas as divisórias que compõem a sua mesa por sua vez para quaisquer entidades correspondentes. Realizará uma verificação de mesa independentemente de o seu filtro utilizar ou não o **RowKey**. Por exemplo: $filter=LastName eq 'Jones'  
* Consultas que devolvem várias entidades retorná-las ordenadas na ordem **PartitionKey** e **RowKey.** Para evitar recorrer às entidades do cliente, escolha um **RowKey** que defina a ordem de classificação mais comum.  

Note que usar um "**ou**" para especificar um filtro baseado nos valores **rowKey** resulta numa digitalização de partição e não é tratado como uma consulta de alcance. Por isso, deve evitar consultas que utilizem filtros como: $filter=PartitionKey eq 'Sales' e (RowKey eq '121' ou RowKey eq '322')  

Por exemplo, o código do lado do cliente que utiliza a Biblioteca do Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Execute uma consulta de pontos utilizando a Biblioteca do Cliente de Armazenamento](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Recuperar várias entidades usando LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projeção do lado do servidor](table-storage-design-patterns.md#server-side-projection)  

Por exemplo, código do lado do cliente que pode lidar com vários tipos de entidades armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidades heterogéneas](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Escolher uma PartitionKey apropriada
A sua escolha de **PartitionKey** deve equilibrar a necessidade de permitir a utilização de EGTs (para garantir a consistência) contra a exigência de distribuir as suas entidades por múltiplas divisórias (para garantir uma solução escalável).  

Num extremo, poderia armazenar todas as suas entidades numa única partição, mas isso pode limitar a escalabilidade da sua solução e impedir que o serviço de mesa seja capaz de carregar pedidos de equilíbrio. No outro extremo, poderia armazenar uma entidade por partição, o que seria altamente escalável e que permite ao serviço de mesa fazer pedidos de equilíbrio de carga, mas que o impediria de utilizar transações de grupos de entidades.  

Um **PartitionKey** ideal é aquele que lhe permite usar consultas eficientes e que tem divisórias suficientes para garantir que a sua solução é escalável. Normalmente, você vai descobrir que as suas entidades terão um imóvel adequado que distribui suas entidades através de divisórias suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre utilizadores ou funcionários, o UserID pode ser uma boa PartitionKey. Pode ter várias entidades que usam um dado UserID como chave de partição. Cada entidade que armazena dados sobre um utilizador é agrupada numa única partição, pelo que estas entidades são acessíveis através de transações de grupos de entidades, sendo ainda altamente escalável.
> 
> 

Existem considerações adicionais na sua escolha de **PartitionKey** que se relacionam com a forma como vai inserir, atualizar e eliminar entidades. Para obter mais informações, consulte [tabelas de conceção para modificação de dados.](table-storage-design-for-modification.md)  

## <a name="optimizing-queries-for-the-table-service"></a>Otimização de consultas para o serviço Mesa
O serviço Tabela indexa automaticamente as suas entidades utilizando os valores **PartitionKey** e **RowKey** num único índice agrupado, daí a razão pela qual as consultas de pontos são as mais eficientes de usar. No entanto, não existem outros índices que não os do índice agrupado no **PartitionKey** e **no RowKey**.

Muitos projetos devem satisfazer requisitos para permitir a procura de entidades com base em múltiplos critérios. Por exemplo, localizar entidades de empregados com base em e-mail, ID do empregado ou apelido. Os padrões descritos nos [Padrões de Design](table-storage-design-patterns.md) de Tabela abordam este tipo de requisitos e descrevem formas de trabalhar em torno do facto de que o serviço de tabela não fornece índices secundários:  

* [Padrão de índice secundário intra-partição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma partição) para permitir análises rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Padrão de índice secundário inter-partição](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Índice Entidades Padrononhou](table-storage-design-patterns.md#index-entities-pattern) - Manter entidades indexadas para permitir pesquisas eficientes que retornem listas de entidades.  

## <a name="sorting-data-in-the-table-service"></a>Triagem de dados no serviço tabela
O serviço table devolve entidades classificadas por ordem ascendente com base na **PartitionKey** e depois no **RowKey**. Estas teclas são valores de cordas e para garantir que os valores numéricos se classificam corretamente, deve convertê-los num comprimento fixo e remá-los com zeros. Por exemplo, se o valor de identificação do empregado que usa como **RowKey** for um valor inteiro, deverá converter o ID do empregado **123** a **00000123**.  

Muitas aplicações têm requisitos para usar dados classificados em diferentes ordens: por exemplo, classificar os colaboradores pelo nome, ou aderir à data. Os seguintes padrões abordam como alternar ordens de classificação para as suas entidades:  

* [Padrão de índice secundário intra-partição](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores RowKey (na mesma partição) para permitir análises rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores RowKey.  
* [Padrão de índice secundário inter-partição](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores RowKey em divisórias separadas em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores RowKey.
* [Registar o padrão da cauda](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* mais recentemente adicionadas a uma partição utilizando um valor **RowKey** que se classifica na ordem de data e hora inversa.  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Criptografe dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)