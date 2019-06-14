---
title: Criar tabelas de armazenamento do Azure para consultas | Documentos da Microsoft
description: Criar tabelas para consultas no armazenamento de tabelas do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 97373f6f0138d3ed8028ed4327b7e6cf90ad76a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325872"
---
# <a name="design-for-querying"></a>Design das consultas
Soluções de serviço de tabela podem ser lido com uso intensivo, com uso intensivo de escrita ou uma combinação dos dois. Este artigo se concentra em coisas que deve ter em mente quando está a conceber o seu serviço de tabela para suportar operações de leitura com eficiência. Normalmente, um design que suporta operações de leitura com eficiência é também eficiente para operações de escrita. No entanto, existem considerações adicionais a ter em mente ao projetar para oferecer suporte a operações de escrita, discutidas no artigo [Design para modificação de dados](table-storage-design-for-modification.md).

Um ponto de partida para conceber a sua solução de serviço de tabela para que possa ler os dados de forma eficiente é perguntar "que consultas meu aplicativo precisará executar para recuperar os dados necessários do serviço tabela?"  

> [!NOTE]
> Com o serviço de tabela, é importante obter o design correto com antecedência porque é difícil e dispendioso para alterá-la mais tarde. Por exemplo, numa base de dados relacional é, muitas vezes, possível para resolver problemas de desempenho pela simples adição de índices para uma base de dados: não se trata de uma opção com o serviço de tabela.  
> 
> 

Esta secção aborda os principais problemas que deve resolver ao projetar suas tabelas para consultar o. Os tópicos abordados nesta secção incluem:

* [Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolher um PartitionKey apropriado](#choosing-an-appropriate-partitionkey)
* [Otimizar as consultas para o serviço de tabela](#optimizing-queries-for-the-table-service)
* [Classificação de dados no serviço tabela](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta
Os exemplos seguintes partem do princípio do serviço de tabela é armazenar entidades de funcionários com a seguinte estrutura (a maioria dos exemplos omite a **Timestamp** propriedade por motivos de clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (nome de departamento) |Cadeia |
| **RowKey** (identificação do funcionário) |Cadeia |
| **FirstName** |Cadeia |
| **LastName** |Cadeia |
| **Idade** |Número inteiro |
| **EmailAddress** |String |

O artigo [descrição geral do armazenamento de tabelas do Azure](table-storage-overview.md) descreve alguns dos principais recursos do serviço de tabelas do Azure que tenham uma direta influência sobre a criação de uma consulta. Estes resultam nas seguintes diretrizes gerais para a criação de consultas do serviço de tabela. Tenha em atenção que a sintaxe de filtro utilizada nos exemplos abaixo é a partir da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* R ***consulta ponto*** é a pesquisa mais eficiente para utilizar e é recomendada a ser utilizado para pesquisas de grande volume ou exigir que a latência mais baixa de pesquisas. Uma consulta deste tipo pode utilizar os índices para localizar uma entidade individual de forma muito eficiente, especificando ambos os **PartitionKey** e **RowKey** valores. Por exemplo: $filter = (PartitionKey eq "Vendas") e (RowKey eq '2')  
* Em segundo lugar e o melhor é um ***intervalo de consulta*** que utiliza o **PartitionKey** e filtros numa ampla gama de **RowKey** valores a devolver mais do que uma entidade. O **PartitionKey** valor identifica uma partição específica e o **RowKey** valores identificam a um subconjunto de entidades nessa partição. Por exemplo: $filter = PartitionKey eq "De" vendas e da ge RowKey"e RowKey lt T'"  
* Melhor terceiro é um ***analisar de partição*** que utiliza o **PartitionKey** e filtros em outra propriedade sem chave e que podem devolver mais do que uma entidade. O **PartitionKey** valor identifica uma partição específica e a propriedade de valores select para um subconjunto de entidades nessa partição. Por exemplo: $filter = PartitionKey eq "Vendas" e LastName eq "Santos"  
* R ***Table Scan*** não inclui o **PartitionKey** e é muito ineficiente, pois ele pesquisa todas as partições que compõem a sua tabela, por sua vez, para qualquer entidades correspondentes. A operação executará uma análise de tabela, independentemente de estar ou não utiliza o seu filtro de **RowKey**. Por exemplo: $filter = LastName eq "Silva"  
* Consultas que devolvem várias entidades retorná-los a ordenados **PartitionKey** e **RowKey** ordem. Para evitar recorrer as entidades no cliente, escolha uma **RowKey** que define a ordem de classificação mais comuns.  

Observe que usar um "**ou**" para especificar um filtro com base nos **RowKey** valores resulta numa análise de partição e não é tratado como uma consulta de intervalo. Portanto, deve evitar consultas que usam filtros, tais como: $filter = PartitionKey eq "Vendas" e (RowKey eq '121' ou RowKey eq "322")  

Para obter exemplos de código do lado do cliente que utilizam a biblioteca de cliente de armazenamento para executar consultas eficientes, consulte:  

* [Executar uma consulta de ponto usando a biblioteca de cliente de armazenamento](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Obter várias entidades usando o LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projecção do lado do servidor](table-storage-design-patterns.md#server-side-projection)  

Para obter exemplos de código do lado do cliente que possa lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogênea](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Escolher um PartitionKey apropriado
Sua escolha de **PartitionKey** devem equilibrar a necessidade de ativar a utilização de EGTs (para garantir a consistência) contra o requisito para distribuir as suas entidades em várias partições (para assegurar uma solução escalável).  

De um lado, pode armazenar todas as suas entidades numa única partição, mas isso pode limitar a escalabilidade da sua solução e impediria que o serviço de tabela de conseguir a pedidos de balanceamento de carga. No outro extremo, pode armazenar uma entidade por partição, que seria altamente escalável e que permite que o serviço tabela pedidos de balanceamento de carga, mas que impediria de usar transações do grupo de entidades.  

Um ideal **PartitionKey** é um que permite-lhe utilizar consultas eficientes e que tem partições suficientes para garantir a sua solução é escalável. Normalmente, encontrará-se que as entidades terão uma propriedade adequada que distribui as entidades em várias partições suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre os utilizadores ou de funcionários, ID de utilizador pode ser uma boa PartitionKey. Pode ter várias entidades que utilizam um ID de utilizador especificado como a chave de partição. Cada entidade que armazena dados sobre um usuário é agrupada numa única partição e, portanto, essas entidades são acessíveis através de transações do grupo de entidades, ao mesmo tempo, altamente dimensionável.
> 
> 

Existem considerações adicionais na sua escolha de **PartitionKey** relacionadas com a forma como irá inserir, atualizar e eliminar entidades. Para obter mais informações, consulte [criando tabelas para modificação dos dados](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Otimizar as consultas para o serviço de tabela
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores num único índice em cluster, por conseguinte, a razão pela qual o ponto de consultas são mais eficiente a utilizar. No entanto, existem sem índices que não seja o que no índice em cluster no **PartitionKey** e **RowKey**.

Muitos designs têm de cumprir os requisitos para ativar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionários com base no e-mail, id de funcionário ou apelido. Os padrões descritos [padrões de Design de tabela](table-storage-design-patterns.md) esses tipos de requisito de endereços e descrevem formas de trabalhar para resolver o fato de que o serviço de tabela não fornece índices secundários:  

* [Padrão de índice secundário de intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores.  
* [Padrão de índice secundário de partição entre](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores em separar as partições ou separar em tabelas para permitir pesquisas rápidas e eficientes e de classificação alternativa pedidos utilizando diferentes **RowKey** valores.  
* [Padrão de entidades de índice](table-storage-design-patterns.md#index-entities-pattern) -manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

## <a name="sorting-data-in-the-table-service"></a>Classificação de dados no serviço tabela
O serviço de tabela devolve entidades ordenadas por ordem, com base em ascendente **PartitionKey** e, em seguida, por **RowKey**. Estas chaves são valores de cadeia de caracteres e para garantir que os valores numéricos são ordenados corretamente, deverá convertê-los num comprimento fixo e preenche-los com zeros. Por exemplo, se o valor de id de funcionário utilizar como a **RowKey** é um valor de número inteiro, deve converter a identificação do funcionário **123** para **00000123**.  

Muitos aplicativos têm requisitos para utilizar dados ordenados em ordens diferentes: por exemplo, classificação funcionários por nome, ou ao associar data. Os seguintes padrões abordam a forma de alternativo ordens de classificação para as entidades:  

* [Padrão de partição dentro de índice secundário](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes valores de RowKey (na mesma partição) para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena com diferentes valores de RowKey.  
* [Padrão de índice secundário de partição entre](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes valores de RowKey em partições separadas em tabelas separadas para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena com diferentes valores de RowKey .
* [Padrão de cauda do registo](table-storage-design-patterns.md#log-tail-pattern) -obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar uma **RowKey** valor ordena inversa de data e a ordem de tempo.  

## <a name="next-steps"></a>Passos Seguintes

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Modelando as relações](table-storage-design-modeling.md)
- [Encriptar dados da tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
