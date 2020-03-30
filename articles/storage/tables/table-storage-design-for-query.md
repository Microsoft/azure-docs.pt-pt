---
title: Armazenamento de mesa de design azure para consultas Microsoft Docs
description: Mesas de design para consultas no armazenamento da mesa Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457636"
---
# <a name="design-for-querying"></a>Design das consultas
As soluções de serviço de mesa podem ser lidas intensivamente, escrever intensivamente, ou uma mistura dos dois. Este artigo centra-se nas coisas a ter em conta quando está a desenhar o seu serviço de Mesa para apoiar as operações de leitura de forma eficiente. Tipicamente, um design que suporta as operações de leitura de forma eficiente também é eficiente para as operações de escrita. No entanto, há considerações adicionais a ter em conta ao conceber para apoiar as operações de escrita, discutidas no artigo [Design para modificação](table-storage-design-for-modification.md)de dados .

Um bom ponto de partida para conceber a sua solução de serviço table para lhe permitir ler dados de forma eficiente é perguntar "Que consultas a minha aplicação terá de executar para recuperar os dados de que necessita do serviço Tabela?"  

> [!NOTE]
> Com o serviço de Mesa, é importante corrigir o design na frente porque é difícil e caro mudá-lo mais tarde. Por exemplo, numa base de dados relacional é muitas vezes possível abordar problemas de desempenho simplesmente adicionando índices a uma base de dados existente: esta não é uma opção com o serviço Tabela.  
> 
> 

Esta secção centra-se nas questões-chave que deve abordar quando desenha as suas tabelas para consulta. Os tópicos abordados nesta secção incluem:

* [Como a sua escolha de PartitionKey e RowKey impacta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolher uma chave de partição apropriada](#choosing-an-appropriate-partitionkey)
* [Otimização de consultas para o serviço mesa](#optimizing-queries-for-the-table-service)
* [Classificação de dados no serviço Tabela](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como a sua escolha de PartitionKey e RowKey impacta o desempenho da consulta
Os seguintes exemplos assumem que o serviço de mesa está a armazenar entidades de empregados com a seguinte estrutura (a maioria dos exemplos omitem a propriedade **Timestamp** para clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **Chave de Partição** (Nome do Departamento) |Cadeia |
| **RowKey** (Id do empregado) |Cadeia |
| **Primeiro nome** |Cadeia |
| **Apelido** |Cadeia |
| **Idade** |Número inteiro |
| **Endereço de e-mail** |Cadeia |

O artigo Visão geral do armazenamento da [tabela Azure](table-storage-overview.md) descreve algumas das principais características do serviço De Mesa Azure que têm uma influência direta na conceção para consulta. Estes resultados resultam nas seguintes orientações gerais para a conceção de consultas de serviço de mesa. Note que a sintaxe de filtro utilizada nos exemplos abaixo é do serviço mesa REST API, para mais informações ver [Consultas](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***Point Query*** é a procura mais eficiente a utilizar e é recomendado para ser usado para procurar ou procurar em alto volume que requerem latência mais baixa. Tal consulta pode usar os índices para localizar uma entidade individual de forma muito eficiente, especificando tanto os valores **PartitionKey** como **RowKey.** Por exemplo: $filter=(PartitionKey eq 'Sales') e (RowKey eq '2')  
* O segundo melhor é uma Consulta de ***Gama*** que usa a **Chave de Partilha** e filtra uma gama de valores **RowKey** para devolver mais do que uma entidade. O valor **PartitionKey** identifica uma partição específica, e os valores **RowKey** identificam um subconjunto das entidades nessa partição. Por exemplo: $filter=PartitionKey eq 'Sales' e RowKey ge 'S' e RowKey lt 'T'  
* O terceiro melhor é um ***Exame de Partição*** que usa a **Chave de Partição** e filtra outra propriedade não chave e que pode devolver mais do que uma entidade. O valor **PartitionKey** identifica uma partição específica, e os valores de propriedade selecionam para um subconjunto das entidades nessa divisória. Por exemplo: $filter=PartitionKey eq 'Sales' e LastName eq 'Smith'  
* Uma ***digitalização*** de tabela não inclui a **Chave de Partição** e é muito ineficiente porque procura todas as divisórias que compõem a sua mesa por sua vez para quaisquer entidades correspondentes. Realizará uma digitalização de tabela independentemente de o seu filtro utilizar ou não o **RowKey**. Por exemplo: $filter=LastName eq 'Jones'  
* Consultas que devolvem várias entidades devolvem-nas ordenadas por **encomenda PartitionKey** e **RowKey.** Para evitar recorrer às entidades do cliente, escolha um **RowKey** que defina a ordem de classificação mais comum.  

Note que usar um "**ou**" para especificar um filtro baseado nos valores **RowKey** resulta numa varredura de divisória e não é tratado como uma consulta de intervalo. Por isso, deve evitar consultas que utilizem filtros como: $filter=PartitionKey eq 'Sales' e (RowKey eq '121' ou RowKey eq '322')  

Por exemplo, o código do lado do cliente que usa a Biblioteca do Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Execute uma consulta pontual utilizando a Biblioteca do Cliente de Armazenamento](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Recuperar várias entidades usando LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projeção do lado do servidor](table-storage-design-patterns.md#server-side-projection)  

Por exemplo, de código do lado do cliente que pode lidar com vários tipos de entidades armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidades heterogéneas](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Escolher uma chave de partição apropriada
A sua escolha de **PartitionKey** deve equilibrar a necessidade de permitir a utilização de EGTs (para garantir a consistência) contra a exigência de distribuir as suas entidades por várias divisórias (para garantir uma solução escalável).  

A certa altura, pode armazenar todas as suas entidades numa única divisória, mas isso pode limitar a escalabilidade da sua solução e evitar que o serviço de mesa seja capaz de carregar pedidos de equilíbrio. No outro extremo, poderia armazenar uma entidade por partição, o que seria altamente escalável e que permite ao serviço de mesa fazer pedidos de saldo de carga, mas o que o impediria de utilizar transações de grupos de entidades.  

Uma **Chave de Partição** ideal é aquela que lhe permite usar consultas eficientes e que tem divisórias suficientes para garantir que a sua solução é escalável. Normalmente, você vai descobrir que as suas entidades terão um imóvel adequado que distribui as suas entidades através de divisórias suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre utilizadores ou funcionários, o UserID pode ser uma boa Chave de Partição. Pode ter várias entidades que usam um dado UserID como chave de partição. Cada entidade que armazena dados sobre um utilizador é agruparada numa única partição, pelo que estas entidades são acessíveis através de transações de grupos de entidades, mantendo-se ainda altamente escaláveis.
> 
> 

Existem considerações adicionais na sua escolha de **PartitionKey** que se relacionam com a forma como irá inserir, atualizar e excluir entidades. Para mais informações, consulte [as tabelas de conceção para modificação](table-storage-design-for-modification.md)de dados .  

## <a name="optimizing-queries-for-the-table-service"></a>Otimização de consultas para o serviço mesa
O serviço de tabela indexa automaticamente as suas entidades utilizando os valores **PartitionKey** e **RowKey** num único índice agrupado, daí a razão pela qual as consultas de ponto são as mais eficientes de usar. No entanto, não existem outros índices que não os do índice agrupado na **PartitionKey** e **RowKey**.

Muitos desenhos devem satisfazer os requisitos para permitir a procura de entidades com base em múltiplos critérios. Por exemplo, localizar entidades de empregados com base em e-mail, identificação de empregadoou sobrenome. Os padrões descritos nos [padrões](table-storage-design-patterns.md) de design de tabela suprimem este tipo de requisitos e descrevem formas de trabalhar em torno do facto de o serviço tabela não fornecer índices secundários:  

* Padrão de [índice secundário intra-divisória](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Armazenar várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma divisória) para permitir procurações rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* Padrão de [índice secundário inter-divisória](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  
* [Padrão de Entidades Index](table-storage-design-patterns.md#index-entities-pattern) - Manter entidades indexadas para permitir pesquisas eficientes que devolvem listas de entidades.  

## <a name="sorting-data-in-the-table-service"></a>Classificação de dados no serviço Tabela
O serviço de mesa devolve entidades ordenadas por ordem ascendente com base na **PartitionKey** e, em seguida, por **RowKey**. Estas teclas são valores de cordas e para garantir que os valores numéricos se classificam corretamente, deve convertê-las num comprimento fixo e remar com zeros. Por exemplo, se o valor de identificação do empregado que usa como **O RowKey** for um valor inteiro, deve converter o id **123** a **00000123**do empregado .  

Muitas aplicações têm requisitos para utilizar dados classificados em diferentes encomendas: por exemplo, classificar os colaboradores pelo nome ou aderir à data. Os seguintes padrões abordam a forma de alternar as ordens de classificação para as suas entidades:  

* Padrão de [índice secundário intra-divisória](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Armazenar várias cópias de cada entidade utilizando diferentes valores RowKey (na mesma divisória) para permitir procurações rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores RowKey.  
* Padrão de [índice secundário inter-divisória](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Guarde várias cópias de cada entidade utilizando diferentes valores RowKey em divisórias separadas em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores RowKey.
* [Padrão de cauda de log](table-storage-design-patterns.md#log-tail-pattern) - Recupere as entidades *n* mais recentemente adicionadas a uma partição usando um valor **RowKey** que classifica em data e ordem de tempo inversa.  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Encriptar dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
