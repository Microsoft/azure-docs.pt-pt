---
title: Projetar Azure Cosmos DB tabelas para dar suporte ao dimensionamento e ao desempenho
description: 'Guia de design da tabela de armazenamento do Azure: Criando tabelas escalonáveis e de alto desempenho no Azure Cosmos DB e na tabela de armazenamento do Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827780"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guia de design da tabela de armazenamento do Azure: Criando tabelas escalonáveis e de alto desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para criar tabelas escalonáveis e de alto desempenho, você deve considerar uma série de fatores, como performance, escalabilidade e custo. Se você tiver criado anteriormente esquemas para bancos de dados relacionais, essas considerações serão familiares para você, mas embora haja algumas semelhanças entre o modelo de armazenamento do serviço tabela do Azure e os modelos relacionais, também há muitas diferenças importantes. Essas diferenças normalmente levam a diferentes designs que podem parecer um contador intuitivo ou errado para alguém familiarizado com bancos de dados relacionais, mas isso faz sentido se você estiver criando um repositório de chave/valor NoSQL, como o serviço tabela do Azure. Muitas das suas diferenças de design refletirão o fato de que o serviço tabela foi projetado para oferecer suporte a aplicativos em escala de nuvem que podem conter bilhões de entidades (linhas na terminologia de banco de dados relacional) ou para conjuntos de dados que devem dar suporte a transações altas volumes: portanto, você precisa pensar de maneira diferente sobre como armazenar seus dados e entender como o serviço tabela funciona. Um armazenamento de dados NoSQL bem projetado pode permitir que sua solução seja dimensionada muito além (e com um custo menor) do que uma solução que usa um banco de dados relacional. Este guia ajuda você com esses tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço tabela do Azure
Esta seção destaca alguns dos principais recursos do serviço tabela que são especialmente relevantes para o design de desempenho e escalabilidade. Se você for novo no armazenamento do Azure e no serviço tabela, primeiro leia [introdução à armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e comece a [usar o armazenamento de tabelas do Azure usando o .net](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia seja no serviço tabela, ele incluirá algumas discussões sobre os serviços de BLOB e de fila do Azure e como você pode usá-los junto com o serviço tabela em uma solução.  

O que é o serviço tabela? Como você pode esperar do nome, o serviço tabela usa um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam as várias propriedades dessa entidade. Cada entidade tem um par de chaves para identificá-la exclusivamente e uma coluna de carimbo de data/hora que o serviço tabela usa para controlar quando a entidade foi atualizada pela última vez (o campo Timestamp é adicionado automaticamente e não é possível substituir manualmente o carimbo de data/hora por um valor arbitrário). O serviço tabela usa esse carimbo de data/hora modificado por último (LMT) para gerenciar a simultaneidade otimista.  

> [!NOTE]
> As operações da API REST do serviço tabela também retornam um valor de **ETag** que deriva do carimbo de data/hora da última modificação (LMT). Neste documento, você notará os termos ETag e LMT interalteradomente, pois eles se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo a seguir mostra um design de tabela simples para armazenar entidades de funcionário e departamento. Muitos dos exemplos mostrados posteriormente neste guia se baseiam nesse design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Gamba</td>
<td>Banheiros</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Junho</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Departamento</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Vendas</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, esse design é semelhante a uma tabela em um banco de dados relacional com as principais diferenças sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **age** , tem um tipo de dados, como inteiro ou cadeia de caracteres, assim como uma coluna em um banco de dado relacional. Embora diferentemente em um banco de dados relacional, a natureza sem esquema do serviço tabela significa que uma propriedade não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos em uma única propriedade, você deve usar um formato serializado, como JSON ou XML. Para obter mais informações sobre o serviço tabela, como tipos de dados com suporte, intervalos de datas com suporte, regras de nomenclatura e restrições de tamanho, consulte [noções básicas sobre o modelo de dados do serviço tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Como você verá, sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design de tabela. Cada entidade armazenada em uma tabela deve ter uma combinação exclusiva de **PartitionKey** e **RowKey**. Assim como acontece com as chaves em uma tabela de banco de dados relacional, os valores **PartitionKey** e **RowKey** são indexados para criar um índice clusterizado que permite buscas rápidas; no entanto, o serviço tabela não cria índices secundários para que sejam as únicas duas propriedades indexadas (alguns dos padrões descritos posteriormente mostram como você pode contornar essa limitação aparente).  

Uma tabela é composta de uma ou mais partições e, como você verá, muitas das decisões de design feitas estarão relacionadas à escolha de um **PartitionKey** e **RowKey** adequados para otimizar sua solução. Uma solução poderia consistir em apenas uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução terá várias tabelas. As tabelas ajudam você a organizar logicamente suas entidades, ajudá-lo a gerenciar o acesso aos dados usando listas de controle de acesso, e você pode descartar uma tabela inteira usando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Partições da tabela
O nome da conta, o nome da tabela e **PartitionKey** juntos identificam a partição no serviço de armazenamento em que o serviço tabela armazena a entidade. Além de ser parte do esquema de endereçamento para entidades, as partições definem um escopo para transações (consulte [Transações de grupo de entidades](#entity-group-transactions) abaixo) e formam a base de como o serviço tabela é dimensionado. Para obter mais informações sobre partições, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md).  

No serviço tabela, um nó individual conecta uma ou mais partições completas e o serviço é dimensionado por meio de balanceamento de carga dinâmico de partições entre nós. Se um nó estiver sob carga, o serviço tabela poderá *dividir* o intervalo de partições atendidas por esse nó em nós diferentes; Quando o tráfego é sublado, o serviço pode *mesclar* os intervalos de partição de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do serviço tabela e, em particular, como o serviço gerencia partições, consulte o artigo [armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transações de grupo de entidades
No serviço tabela, as transações de grupo de entidades (EGTs) são o único mecanismo interno para executar atualizações atômicas em várias entidades. EGTs também são chamados de *transações em lote* em algumas documentações. O EGTs só pode operar em entidades armazenadas na mesma partição (compartilhar a mesma chave de partição em uma determinada tabela), portanto, sempre que você precisar de um comportamento transacional atômico em várias entidades, será necessário garantir que essas entidades estejam na mesma partição. Isso geralmente é um motivo para manter vários tipos de entidade na mesma tabela (e partição) e não usar várias tabelas para tipos de entidade diferentes. Um único EGT pode operar em no máximo 100 entidades.  Se você enviar vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operem em entidades que são comuns em EGTs, pois o processamento pode ser atrasado.

O EGTs também introduz uma possível compensação para que você possa avaliar em seu design: usar mais partições aumentará a escalabilidade do seu aplicativo, pois o Azure tem mais oportunidades para solicitações de balanceamento de carga entre nós, mas isso pode limitar a capacidade de seu aplicativo para executar transações atômicas e manter uma consistência forte para seus dados. Além disso, há metas de escalabilidade específicas no nível de uma partição que pode limitar a taxa de transferência de transações que você pode esperar para um único nó: para obter mais informações sobre as metas de escalabilidade para contas de armazenamento do Azure e o serviço tabela, consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md). As seções posteriores deste guia discutem várias estratégias de design que ajudam você a gerenciar compensações como esta, e a discutir a melhor escolha de sua chave de partição com base nos requisitos específicos do seu aplicativo cliente.  

### <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela a seguir inclui alguns dos principais valores a serem considerados quando você está criando uma solução de serviço tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas em uma conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições em uma tabela |Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em uma partição |Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho de uma entidade individual |Até 1 MB com um máximo de 255 Propriedades (incluindo **PartitionKey**, **RowKey**e **timestamp**) |
| Tamanho do **PartitionKey** |Uma cadeia de caracteres de até 1 KB de tamanho |
| Tamanho do **RowKey** |Uma cadeia de caracteres de até 1 KB de tamanho |
| Tamanho de uma transação de grupo de entidades |Uma transação pode incluir no máximo 100 entidades e a carga deve ter menos de 4 MB de tamanho. Um EGT só pode atualizar uma entidade uma vez. |

Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custos
O armazenamento de tabela é relativamente barato, mas você deve incluir estimativas de custo para o uso de capacidade e a quantidade de transações como parte da sua avaliação de qualquer solução que usa o serviço tabela. No entanto, em muitos cenários, o armazenamento de dados desnormalizados ou duplicados para melhorar o desempenho ou a escalabilidade de sua solução é uma abordagem válida a ser tomada. Para obter mais informações sobre preços, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Orientações do design da tabela
Essas listas resumem algumas das principais diretrizes que você deve ter em mente ao criar suas tabelas, e este guia irá solucioná-las em mais detalhes posteriormente no. Essas diretrizes são diferentes das diretrizes que você normalmente seguiria para o design de banco de dados relacional.  

Projetando sua solução de serviço de tabela para ser eficiente de *leitura* :

* ***Design para consulta em aplicativos de leitura intensa.*** Quando você estiver projetando suas tabelas, pense nas consultas (especialmente as sensíveis à latência) que serão executadas antes de pensar em como você atualizará suas entidades. Isso normalmente resulta em uma solução eficiente e de alto desempenho.  
* ***Especifique PartitionKey e RowKey em suas consultas.*** *Consultas de ponto* como essas são as consultas de serviço de tabela mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.*** O armazenamento de tabela é barato, portanto, considere armazenar a mesma entidade várias vezes (com chaves diferentes) para permitir consultas mais eficientes.  
* ***Considere desnormalizar seus dados.*** O armazenamento de tabela é barato, portanto, considere desnormalizar seus dados. Por exemplo, armazene as entidades de resumo para que as consultas de dados agregados só precisem acessar uma única entidade.  
* ***Use valores de chave composta.*** As únicas chaves que você tem são **PartitionKey** e **RowKey**. Por exemplo, use valores de chave composta para habilitar caminhos alternativos de acesso com chave para entidades.  
* ***Use projeção de consulta.*** Você pode reduzir a quantidade de dados transferidos pela rede usando consultas que selecionam apenas os campos necessários.  

Criando sua solução de serviço de tabela para ser eficiente em termos de *gravação* :  

* ***Não crie partições ativas.*** Escolha as chaves que permitem que você distribua suas solicitações em várias partições a qualquer momento.  
* ***Evite picos no tráfego.*** Smooth o tráfego em um período de tempo razoável e evite picos de tráfego.
* ***Não crie necessariamente uma tabela separada para cada tipo de entidade.*** Quando você precisa de transações atômicas entre tipos de entidade, pode armazenar esses tipos de entidade múltiplas na mesma partição na mesma tabela.
* ***Considere a taxa de transferência máxima que você deve obter.*** Você deve estar ciente das metas de escalabilidade para o serviço tabela e garantir que seu design não fará com que você as exceda.  

Ao ler este guia, você verá exemplos que colocam todos esses princípios em prática.  

## <a name="design-for-querying"></a>Design das consultas
As soluções de serviço tabela podem ser de leitura intensiva, intensivas de gravação ou uma combinação dos dois. Esta seção se concentra nas coisas que você deve ter em mente ao criar o serviço tabela para dar suporte a operações de leitura com eficiência. Normalmente, um design que dá suporte a operações de leitura com eficiência também é eficiente para operações de gravação. No entanto, há considerações adicionais a serem lembradas ao projetar para dar suporte a operações de gravação, abordadas na próxima seção, [design para modificação de dados](#design-for-data-modification).

Um bom ponto de partida para projetar sua solução de serviço de tabela para permitir que você leia dados com eficiência é perguntar "quais consultas meu aplicativo precisará executar para recuperar os dados de que precisa do serviço tabela?"  

> [!NOTE]
> Com o serviço tabela, é importante que o design seja atualizado antecipadamente, pois é difícil e caro alterá-lo mais tarde. Por exemplo, em um banco de dados relacional, muitas vezes é possível resolver problemas de desempenho simplesmente adicionando índices a um banco de dados existente: isso não é uma opção com o serviço tabela.  
> 
> 

Esta seção aborda os principais problemas que você deve abordar ao projetar suas tabelas para consulta. Os tópicos abordados nesta seção incluem:

* [Como sua escolha de PartitionKey e RowKey impacta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolhendo um PartitionKey apropriado](#choosing-an-appropriate-partitionkey)
* [Otimizando consultas para o serviço tabela](#optimizing-queries-for-the-table-service)
* [Classificando dados no serviço tabela](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como sua escolha de PartitionKey e RowKey impacta o desempenho da consulta
Os exemplos a seguir pressupõem que o serviço tabela está armazenando entidades de funcionário com a seguinte estrutura (a maioria dos exemplos omite a propriedade **timestamp** para fins de clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (nome do departamento) |String |
| **RowKey** (ID do funcionário) |String |
| **FirstName** |String |
| **LastName** |String |
| **Idade** |Número inteiro |
| **EmailAddress** |String |

A seção anterior visão geral do serviço tabela do Azure descreve alguns dos principais recursos do serviço tabela do Azure que têm uma influência direta sobre como projetar para consulta. Isso resulta nas seguintes diretrizes gerais para a criação de consultas de serviço de tabela. A sintaxe de filtro usada nos exemplos abaixo é da API REST do serviço tabela, para obter mais informações, consulte [entidades de consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Uma ***consulta de ponto*** é a pesquisa mais eficiente para usar e é recomendável para ser usada para pesquisas de alto volume ou pesquisas que exigem menor latência. Essa consulta pode usar os índices para localizar uma entidade individual com eficiência, especificando os valores **PartitionKey** e **RowKey** . Por exemplo: $filter = (PartitionKey EQ ' Sales ') e (RowKey EQ ' 2 ')  
* O segundo melhor é uma ***consulta de intervalo*** que usa o **PartitionKey** e os filtros em um intervalo de valores de **RowKey** para retornar mais de uma entidade. O valor **PartitionKey** identifica uma partição específica e os valores de **RowKey** identificam um subconjunto das entidades nessa partição. Por exemplo: $filter = PartitionKey EQ ' Sales ' e RowKey ge ' s e RowKey lt ' s'  
* O terceiro melhor é uma ***verificação de partição*** que usa o **PartitionKey** e os filtros em outra propriedade que não seja de chave e que pode retornar mais de uma entidade. O valor **PartitionKey** identifica uma partição específica e os valores de propriedade selecionam um subconjunto das entidades nessa partição. Por exemplo: $filter = PartitionKey EQ ' Sales ' e LastName EQ ' Smith '  
* Uma ***verificação de tabela*** não inclui o **PartitionKey** e é ineficiente porque pesquisa todas as partições que compõem sua tabela por vez para qualquer entidade correspondente. Ele executará uma verificação de tabela, independentemente de o filtro usar ou não o **RowKey**. Por exemplo: $filter = LastName EQ ' Jones '  
* As consultas de armazenamento de tabelas do Azure que retornam várias entidades as retornam classificadas em ordem **PartitionKey** e **RowKey** . Para evitar a reclassificação das entidades no cliente, escolha um **RowKey** que defina a ordem de classificação mais comum. Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Usar um "**or**" para especificar um filtro com base nos valores de **RowKey** resulta em uma verificação de partição e não é tratado como uma consulta de intervalo. Portanto, você deve evitar consultas que usam filtros como: $filter = PartitionKey EQ ' Sales ' e (RowKey EQ ' 121 ' ou RowKey EQ ' 322 ')  

Para obter exemplos de código do lado do cliente que usam a biblioteca de cliente de armazenamento para executar consultas eficientes, consulte:  

* [Executando uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library)
* [Recuperando várias entidades usando o LINQ](#retrieving-multiple-entities-using-linq)
* [Projeção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que podem lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Escolhendo um PartitionKey apropriado
Sua escolha de **PartitionKey** deve balancear a necessidade de habilitar o uso de EGTS (para garantir a consistência) em relação à necessidade de distribuir suas entidades entre várias partições (para garantir uma solução escalonável).  

Em um extremo, você pode armazenar todas as suas entidades em uma única partição, mas isso pode limitar a escalabilidade de sua solução e impedir que o serviço tabela fosse capaz de balancear a carga de solicitações. No outro extremo, você pode armazenar uma entidade por partição, que seria altamente escalonável e que permite que o serviço tabela balanceie a carga de solicitações, mas isso impediria o uso de transações de grupo de entidades.  

Um **PartitionKey** ideal é aquele que permite que você use consultas eficientes e que tenha partições suficientes para garantir que sua solução seja escalonável. Normalmente, você descobrirá que suas entidades terão uma propriedade adequada que distribui suas entidades em partições suficientes.

> [!NOTE]
> Por exemplo, em um sistema que armazena informações sobre usuários ou funcionários, o UserID pode ser um bom PartitionKey. Você pode ter várias entidades que usam uma determinada UserID como a chave de partição. Cada entidade que armazena dados sobre um usuário é agrupada em uma única partição e, portanto, essas entidades podem ser acessadas por meio de transações de grupo de entidades, ainda sendo altamente escalonáveis.
> 
> 

Há considerações adicionais em sua escolha de **PartitionKey** relacionados a como você irá inserir, atualizar e excluir entidades: consulte a seção [design para modificação de dados](#design-for-data-modification) abaixo.  

### <a name="optimizing-queries-for-the-table-service"></a>Otimizando consultas para o serviço tabela
O serviço tabela indexa automaticamente suas entidades usando os valores **PartitionKey** e **RowKey** em um único índice clusterizado, portanto, o motivo pelo qual as consultas de ponto são as mais eficientes de usar. No entanto, não há nenhum índice diferente daquele no índice clusterizado em **PartitionKey** e **RowKey**.

Muitos designs devem atender aos requisitos para habilitar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionário com base em email, ID de funcionário ou sobrenome. Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam esses tipos de requisito e descrevem maneiras de solucionar o fato de que o serviço tabela não fornece índices secundários:  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para habilitar pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** .  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando diferentesValores de RowKey.  
* [Padrão de entidades de índice](#index-entities-pattern) – manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

### <a name="sorting-data-in-the-table-service"></a>Classificando dados no serviço tabela

Os resultados da consulta retornados pelo serviço tabela são classificados em ordem crescente com base em **PartitionKey** e, em seguida, por **RowKey**.

> [!NOTE]
> Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

As chaves na tabela de armazenamento do Azure são valores de cadeia de caracteres e para garantir que os valores numéricos sejam classificados corretamente, você deve convertê-los em um comprimento fixo e espercussão-los com zeros. Por exemplo, se o valor de ID de funcionário usado como **RowKey** for um valor inteiro, você deverá converter a ID de funcionário **123** em **00000123**. 

Muitos aplicativos têm requisitos para usar dados classificados em diferentes ordens: por exemplo, classificar funcionários por nome ou por data de ingresso. Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam como alternar ordens de classificação para suas entidades:  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de RowKey (na mesma partição) para habilitar pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de RowKey.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições separadas em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de RowKey.
* [Padrão de cauda do log](#log-tail-pattern) – recupere as entidades *n* adicionadas mais recentemente a uma partição usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

## <a name="design-for-data-modification"></a>Design da modificação de dados
Esta seção se concentra nas considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, você precisará avaliar a compensação entre os designs que otimizam a consulta em relação a designs que otimizam a modificação de dados, assim como acontece em designs de bancos de dados relacionais (embora as técnicas para gerenciar as compensações de design sejam diferente em um banco de dados relacional). A seção [padrões de design de tabela](#table-design-patterns) descreve alguns padrões de design detalhados para o serviço tabela e realça algumas dessas compensações. Na prática, você verá que muitos designs otimizados para consultar entidades também funcionam bem para modificar entidades.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Otimizando o desempenho de operações de inserção, atualização e exclusão
Para atualizar ou excluir uma entidade, você deve ser capaz de identificá-la usando os valores **PartitionKey** e **RowKey** . Nesse sentido, sua escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhantes à sua escolha para dar suporte a consultas de ponto, pois você deseja identificar entidades com a maior eficiência possível. Você não deseja usar uma verificação de partição ou de tabela ineficiente para localizar uma entidade a fim de descobrir os valores **PartitionKey** e **RowKey** necessários para atualizá-la ou excluí-la.  

Os seguintes padrões na seção [padrões de design de tabela](#table-design-patterns) abordam a otimização do desempenho ou as operações de inserção, atualização e exclusão:  

* [Padrão de exclusão de alto volume](#high-volume-delete-pattern) – habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada; Exclua as entidades excluindo a tabela.  
* [Padrão de série de dados](#data-series-pattern) – armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  
* [Padrão de entidades largas](#wide-entities-pattern) – use várias entidades físicas para armazenar entidades lógicas com mais de 252 Propriedades.  
* [Padrão de entidades grandes](#large-entities-pattern) – use o armazenamento de BLOBs para armazenar grandes valores de propriedade.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Garantindo a consistência em suas entidades armazenadas
O outro fator-chave que influencia sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência usando transações atômicas. Você só pode usar um EGT para operar em entidades armazenadas na mesma partição.  

Os seguintes padrões na seção [padrões de design de tabela](#table-design-patterns) abordam o gerenciamento de consistência:  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para habilitar pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** .  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando diferentes **RowKey** os.  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) – habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.
* [Padrão de entidades de índice](#index-entities-pattern) – manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern) – Combine dados relacionados juntos em uma única entidade para permitir que você recupere todos os dados necessários com uma única consulta de ponto.  
* [Padrão de série de dados](#data-series-pattern) – armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  

Para obter informações sobre transações de grupo de entidades, consulte a seção [transações do grupo de entidades](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir que seu design para modificações eficientes facilite consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas você deve sempre avaliar se esse é o caso para seu cenário específico. Alguns dos padrões na seção padrões de [design de tabela](#table-design-patterns) avaliam explicitamente as compensações entre consulta e modificação de entidades, e você deve sempre levar em conta o número de cada tipo de operação.  

Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam as compensações entre o design para consultas eficientes e o design para uma modificação eficiente de dados:  

* [Padrão de chave composta](#compound-key-pattern) – use valores de **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  
* [Padrão de cauda do log](#log-tail-pattern) – recupere as entidades *n* adicionadas mais recentemente a uma partição usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

## <a name="encrypting-table-data"></a>Criptografando dados da tabela
A biblioteca de cliente de armazenamento do .NET Azure dá suporte à criptografia de propriedades de entidade de cadeia de caracteres para operações de inserção e substituição. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias e são convertidas novamente em cadeias de caracteres após a descriptografia.    

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades a serem criptografadas. Isso pode ser feito especificando um atributo [Encryptproperty] (para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia em opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, uma chave de linha e um nome de propriedade e retorna um booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação na conexão. O delegado também fornece a possibilidade de lógica em relação a como as propriedades são criptografadas. (Por exemplo, se X, em seguida, criptografe A propriedade A; caso contrário, criptografe as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

Não há suporte para mesclagem no momento. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. A mesclagem requer fazer chamadas de serviço extras para ler a entidade pré-existente do serviço ou usar uma nova chave por propriedade, ambas não são adequadas por motivos de desempenho.     

Para obter informações sobre como criptografar dados de tabela, consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modelar relações
A criação de modelos de domínio é uma etapa fundamental no design de sistemas complexos. Normalmente, você usa o processo de modelagem para identificar entidades e as relações entre elas como uma maneira de entender o domínio de negócios e informar o design do seu sistema. Esta seção se concentra em como você pode converter alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para designs para o serviço tabela. O processo de mapeamento de um modelo de dados lógico para um modelo de dados baseado em NoSQL físico é diferente daquele usado durante a criação de um banco de dado relacional. O design de bancos de dados relacionais normalmente pressupõe que um processo de normalização é otimizado para minimizar a redundância – e um recurso de consulta declarativa que abstrai como a implementação de como o banco de dados funciona.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrem com frequência: por exemplo, um departamento tem muitos funcionários. Há várias maneiras de implementar relações um-para-muitos no serviço tabela, cada uma com prós e contras que podem ser relevantes para o cenário específico.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionários, em que cada departamento tem muitos funcionários e cada funcionário associado a um departamento específico. Uma abordagem é armazenar entidades de departamento e funcionários separadas, como estas:  

![Entidade departamento e funcionário][1]

Este exemplo mostra uma relação implícita de um-para-muitos entre os tipos com base no valor **PartitionKey** . Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e suas entidades de funcionário relacionadas na mesma partição. Você pode optar por usar partições diferentes, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidade.  

Uma abordagem alternativa é desnormalizar seus dados e armazenar somente entidades de funcionário com dados de departamento desnormalizados, conforme mostrado no exemplo a seguir. Nesse cenário específico, essa abordagem desnormalizada pode não ser a melhor se você tiver um requisito para poder alterar os detalhes de um gerente de departamento, pois isso é necessário para atualizar todos os funcionários do departamento.  

![Entidade de funcionário][2]

Para obter mais informações, consulte o [padrão de desnormalização](#denormalization-pattern) mais adiante neste guia.  

A tabela a seguir resume os prós e contras de cada uma das abordagens descritas acima para armazenar entidades de funcionário e departamento que têm uma relação um-para-muitos. Você também deve considerar a frequência com que espera executar várias operações: pode ser aceitável ter um design que inclua uma operação cara se essa operação ocorrer apenas com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Prós</th>
<th>Contras</th>
</tr>
<tr>
<td>Tipos de entidade separados, mesma partição, mesma tabela</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Você pode usar um EGT para manter a consistência se tiver um requisito para modificar uma entidade de departamento sempre que atualizar/inserir/excluir uma entidade de funcionário. Por exemplo, se você mantiver uma contagem de funcionários departamentais para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez seja necessário recuperar uma entidade de funcionário e de departamento para algumas atividades do cliente.</li>
<li>As operações de armazenamento acontecem na mesma partição. Em grandes volumes de transações, isso pode resultar em um HotSpot.</li>
<li>Não é possível mover um funcionário para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separados, partições diferentes ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento ou entidade de funcionário com uma única operação.</li>
<li>Em grandes volumes de transações, isso pode ajudar a distribuir a carga entre mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez seja necessário recuperar uma entidade de funcionário e de departamento para algumas atividades do cliente.</li>
<li>Você não pode usar o EGTs para manter a consistência ao atualizar/inserir/excluir um funcionário e atualizar um departamento. Por exemplo, atualizar uma contagem de funcionários em uma entidade de departamento.</li>
<li>Não é possível mover um funcionário para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar em um tipo de entidade única</td>
<td>
<ul>
<li>Você pode recuperar todas as informações necessárias com uma única solicitação.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser dispendioso manter a consistência se você precisar atualizar as informações do departamento (isso exigiria que você atualizasse todos os funcionários de um departamento).</li>
</ul>
</td>
</tr>
</table>

Como você escolhe entre essas opções, e quais dos prós e contras são mais significativos, depende de seus cenários de aplicativo específicos. Por exemplo, com que frequência você modifica entidades de departamento; todas as suas consultas de funcionários precisam de informações departamentais adicionais; quão perto você tem para os limites de escalabilidade em suas partições ou sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um-para-um
Os modelos de domínio podem incluir relações um-para-um entre entidades. Se precisar implementar uma relação um-para-um no serviço tabela, você também deverá escolher como vincular as duas entidades relacionadas quando precisar recuperá-las. Esse link pode ser implícito, com base em uma convenção nos valores de chave, ou explícito, armazenando um link na forma de valores **PartitionKey** e **RowKey** em cada entidade para sua entidade relacionada. Para obter uma discussão sobre se você deve armazenar as entidades relacionadas na mesma partição, consulte a seção [relações um-para-muitos](#one-to-many-relationships).  

Também há considerações de implementação que podem levar você a implementar relações um-para-um no serviço tabela:  

* Tratando grandes entidades (para obter mais informações, consulte [padrão de entidades grandes](#large-entities-pattern)).  
* Implementando controles de acesso (para obter mais informações, consulte [controlando o acesso com assinaturas de acesso compartilhado](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Ingressar no cliente
Embora haja maneiras de modelar relações no serviço tabela, você não deve se esquecer de que os dois principais motivos para usar o serviço tabela são escalabilidade e desempenho. Se você achar que está modelando muitas relações que comprometem o desempenho e a escalabilidade de sua solução, deverá se perguntar se é necessário criar todas as relações de dados em seu design de tabela. Você poderá simplificar o design e melhorar a escalabilidade e o desempenho da sua solução se permitir que o aplicativo cliente execute as junções necessárias.  

Por exemplo, se você tiver tabelas pequenas que contêm dados que não são alterados com frequência, você poderá recuperar esses dados uma vez e armazená-los no cliente. Isso pode evitar viagens de ida e volta repetidas para recuperar os mesmos dados. Nos exemplos que vimos neste guia, o conjunto de departamentos em uma pequena organização provavelmente será pequeno e mudará raramente, tornando-o um bom candidato a dados que o aplicativo cliente pode baixar uma vez e armazenar em cache como dados de pesquisa.  

### <a name="inheritance-relationships"></a>Relações de herança
Se o aplicativo cliente usar um conjunto de classes que fazem parte de uma relação de herança para representar entidades comerciais, você poderá persistir facilmente essas entidades no serviço tabela. Por exemplo, você pode ter o seguinte conjunto de classes definido em seu aplicativo cliente em que a **pessoa** é uma classe abstrata.

![Diagrama ER de relações de herança][3]

Você pode persistir instâncias das duas classes concretas no serviço tabela usando uma única tabela Person usando entidades que têm esta aparência:  

![Diagrama da entidade Customer e da entidade Employee][4]

Para obter mais informações sobre como trabalhar com vários tipos de entidade na mesma tabela no código do cliente, consulte a seção [trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types) , mais adiante neste guia. Isso fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design de tabela
Nas seções anteriores, você viu algumas discussões detalhadas sobre como otimizar o design da tabela para a recuperação de dados de entidade usando consultas e inserção, atualização e exclusão de dados de entidade. Esta seção descreve alguns padrões apropriados para uso com soluções de serviço de tabela. Além disso, você verá como é possível lidar praticamente com alguns dos problemas e compensações gerados anteriormente neste guia. O diagrama a seguir resume as relações entre os diferentes padrões:  

![Imagem de padrões de design de tabela][5]

O mapa de padrões acima realça algumas relações entre padrões (azuis) e antipadrões (laranja) documentados neste guia. Há, certamente, muitos outros padrões que valem a pena considerar. Por exemplo, um dos principais cenários do serviço de tabela é usar o [padrão de exibição materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) do padrão [CQRS (segregação de responsabilidade de consulta de comando)](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intra-Partition
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para habilitar pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** . As atualizações entre as cópias podem ser mantidas consistentes usando o EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores **PartitionKey** e **RowKey** ). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Entidade de funcionário][6]

Se você também quiser encontrar uma entidade de funcionário com base no valor de outra propriedade, como endereço de email, deverá usar uma verificação de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o serviço tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando um valor diferente de **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para **RowKey**, "empid_" e "email_" permitem consultar um único funcionário ou um intervalo de funcionários usando um intervalo de endereços de email ou de IDs de funcionários.  

![Entidade Employee com valores variados de RowKey][7]

Os dois critérios de filtro a seguir (um pesquisando por ID de funcionário e um pesquisando por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey EQ ' Sales ') e (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') e (RowKey EQ 'email_jonesj@contoso.com')  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado em ordem de endereço de email consultando as entidades com o prefixo apropriado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de 000100 a 000199, use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' empid_000100 ') e (RowKey Le ' empid_000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email começando com a letra ' a ' use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' email_a ') e (RowKey lt ' email_b ')  
  
  A sintaxe de filtro usada nos exemplos acima é da API REST do serviço tabela, para obter mais informações, consulte [entidades de consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo do seu design com base em seus requisitos de armazenamento previstos e adicionar somente entidades duplicadas para dar suporte às consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, você deve garantir que não exceda as metas de escalabilidade para uma partição individual.  
* Você pode manter suas entidades duplicadas consistentes entre si usando EGTs para atualizar as duas cópias da entidade atomicamente. Isso implica que você deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte a seção [usando transações de grupo de entidades](#entity-group-transactions).  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* Os valores numéricos de preenchimento no **RowKey** (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email no **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:

![Entidade de funcionário][8]

* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde você pode identificar cada entidade usando uma variedade de valores exclusivos. No entanto, você deve ter certeza de que não excederá os limites de escalabilidade de partição quando estiver executando pesquisas de entidade usando valores diferentes de **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações de grupo de entidades](#entity-group-transactions)
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partições
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** .  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores **PartitionKey** e **RowKey** ). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![Entidade de funcionário][9]

Se você também quiser encontrar uma entidade de funcionário com base no valor de outra propriedade, como endereço de email, deverá usar uma verificação de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o serviço tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

Você está prevendo um alto volume de transações em relação a essas entidades e deseja minimizar o risco de a taxa de serviço de tabela limitar o cliente.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando valores diferentes de **PartitionKey** e **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para **PartitionKey**, "empid_" e "email_" permitem identificar qual índice você deseja usar para uma consulta.  

![Entidade de funcionário com índice primário e entidade de funcionário com índice secundário][10]

Os dois critérios de filtro a seguir (um pesquisando por ID de funcionário e um pesquisando por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey EQ ' empid_Sales ') e (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') e (RowKey EQ 'jonesj@contoso.com')  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado em ordem de endereço de email consultando as entidades com o prefixo apropriado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de **000100** a **000199** classificados em uso de ordem de id de funcionário: $Filter = (PartitionKey EQ ' empid_Sales ') e (RowKey ge ' 000100 ') e (RowKey Le ' 000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que começa com ' a ' classificado em uso de ordem de endereço de email: $filter = (PartitionKey EQ ' email_Sales ') e (RowKey ge ' a ') e (RowKey lt ' b ')  

Observe que a sintaxe de filtro usada nos exemplos acima é da API REST do serviço tabela, para obter mais informações, consulte [entidades de consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você pode manter suas entidades duplicadas eventualmente consistentes entre si usando o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo do seu design com base em seus requisitos de armazenamento previstos e adicionar somente entidades duplicadas para dar suporte às consultas que seu aplicativo cliente executará.  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* Os valores numéricos de preenchimento no **RowKey** (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email no **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:
  
  ![Entidade Employee com índice secundário][11]
* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para pesquisar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde você pode identificar cada entidade usando uma variedade de valores exclusivos. Use esse padrão quando desejar evitar exceder os limites de escalabilidade de partição quando estiver executando pesquisas de entidade usando os diferentes valores de **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistentes
Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs habilitar transações atômicas em várias entidades que compartilham a mesma chave de partição. Por motivos de desempenho e escalabilidade, você pode decidir armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado: nesse cenário, não é possível usar o EGTs para manter a consistência. Por exemplo, você pode ter um requisito para manter a consistência eventual entre:  

* As entidades armazenadas em duas partições diferentes na mesma tabela, em tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada no serviço tabela e um blob armazenado no serviço BLOB.  
* Uma entidade armazenada no serviço tabela e um arquivo em um sistema de arquivos.  
* Um repositório de entidades no serviço tabela ainda indexado usando o serviço de Pesquisa Cognitiva do Azure.  

#### <a name="solution"></a>Solução
Usando as filas do Azure, você pode implementar uma solução que fornece consistência eventual em duas ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, suponha que você tenha um requisito para poder arquivar entidades de funcionário antigas. Entidades de funcionário antigas são raramente consultadas e devem ser excluídas de quaisquer atividades que lidam com os funcionários atuais. Para implementar esse requisito, você armazena os funcionários ativos na tabela **atual** e os funcionários antigos na tabela de **arquivamento** . O arquivamento de um funcionário exige que você exclua a entidade da tabela **atual** e adicione a entidade à tabela de **arquivo morto** , mas não pode usar um EGT para executar essas duas operações. Para evitar o risco de que uma falha faça com que uma entidade apareça em ambas ou nenhuma das tabelas, a operação de arquivamento deve ser eventualmente consistente. O diagrama de sequência a seguir descreve as etapas nesta operação. Mais detalhes são fornecidos para caminhos de exceção no texto a seguir.  

![Diagrama de solução para consistência eventual][12]

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure, neste exemplo, para arquivar #456 de funcionários. Uma função de trabalho pesquisa a fila em busca de novas mensagens; Quando encontra um, ele lê a mensagem e deixa uma cópia oculta na fila. A função de trabalho em seguida busca uma cópia da entidade da tabela **atual** , insere uma cópia na tabela de **arquivo morto** e, em seguida, exclui a original da tabela **atual** . Por fim, se não houvesse nenhum erro nas etapas anteriores, a função de trabalho excluirá a mensagem oculta da fila.  

Neste exemplo, a etapa 4 insere o funcionário na tabela de **arquivo morto** . Ele pode adicionar o funcionário a um blob no serviço BLOB ou a um arquivo em um sistema de arquivos.  

#### <a name="recovering-from-failures"></a>Recuperando de falhas
É importante que as operações nas etapas **4** e **5** devam ser *idempotentes* caso a função de trabalho precise reiniciar a operação de arquivamento. Se você estiver usando o serviço tabela, na etapa **4** , você deve usar uma operação "inserir ou substituir"; para a etapa **5** , você deve usar uma operação "excluir se houver" na biblioteca de cliente que você está usando. Se você estiver usando outro sistema de armazenamento, deverá usar uma operação idempotente apropriada.  

Se a função de trabalho nunca concluir a etapa **6**, depois de um tempo limite, a mensagem será exibida novamente na fila pronta para que a função de trabalho tente processá-la novamente. A função de trabalho pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "suspeita" para investigação, enviando-a para uma fila separada. Para obter mais informações sobre como ler mensagens da fila e verificar a contagem da remoção da fila, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços tabela e fila são erros transitórios e o aplicativo cliente deve incluir uma lógica de repetição adequada para tratá-los.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Essa solução não fornece isolamento de transação. Por exemplo, um cliente pode ler as tabelas **atual** e **arquivo morto** quando a função de trabalho estava entre as etapas **4** e **5**e ver uma exibição inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Você deve ter certeza de que as etapas 4 e 5 são idempotentes para garantir a consistência eventual.  
* Você pode dimensionar a solução usando várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando desejar garantir a consistência eventual entre entidades que existem em diferentes partições ou tabelas. Você pode estender esse padrão para garantir a consistência eventual para operações em todo o serviço de tabela e o serviço BLOB e outras fontes de dados de armazenamento não Azure, como o Database ou o sistema de arquivos.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)  
* [Mesclar ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação for importante para sua solução, considere reformular suas tabelas para permitir que você use o EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode recuperar com eficiência uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário ( **PartitionKey** e **RowKey**).  

![Entidade de funcionário][13]

Se você também deseja ser capaz de recuperar uma lista de entidades de funcionário com base no valor de outra propriedade não exclusiva, como seu sobrenome, você deve usar uma verificação de partição menos eficiente para localizar correspondências em vez de usar um índice para examiná-las diretamente. Isso ocorre porque o serviço tabela não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para habilitar a pesquisa por sobrenome com a estrutura de entidade mostrada acima, você deve manter listas de IDs de funcionários. Se você quiser recuperar as entidades de funcionário com um sobrenome específico, como Jones, deverá primeiro localizar a lista de IDs de funcionários para funcionários com Jones como seu sobrenome e, em seguida, recuperar essas entidades de funcionário. Há três opções principais para armazenar as listas de IDs de funcionários:  

* Use o armazenamento de BLOBs.  
* Crie entidades de índice na mesma partição que as entidades de funcionário.  
* Crie entidades de índice em uma partição ou tabela separada.  

<u>Opção #1: usar o armazenamento de BLOBs</u>  

Para a primeira opção, você cria um blob para cada sobrenome exclusivo e, em cada repositório de BLOB, uma lista dos valores de **PartitionKey** (departamento) e **RowKey** (ID de funcionário) para os funcionários que têm esse sobrenome. Ao adicionar ou excluir um funcionário, você deve garantir que o conteúdo do blob relevante seja eventualmente consistente com as entidades do funcionário.  

<u>Opção #2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, use as entidades de índice que armazenam os seguintes dados:  

![Entidade Employee com cadeia de caracteres contendo uma lista de IDs de funcionários com o mesmo sobrenome][14]

A propriedade **employeeids** contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no **RowKey**.  

As etapas a seguir descrevem o processo que você deve seguir ao adicionar um novo funcionário se estiver usando a segunda opção. Neste exemplo, estamos adicionando um funcionário com a ID 000152 e um sobrenome Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** "Sales" e o valor de **RowKey** "Jones". Salve a ETag desta entidade para usar na etapa 2.  
2. Crie uma transação de grupo de entidades (ou seja, uma operação em lote) que insira a nova entidade Employee (valor de**PartitionKey** "Sales" e o valor de **RowKey** "000152") e atualize a entidade de índice (o valor de**PartitionKey** "Sales" e **RowKey** valor "Jones") adicionando a nova ID de funcionário à lista no campo employeeids. Para obter mais informações sobre transações de grupo de entidades, consulte [Transações de grupo de entidades](#entity-group-transactions).  
3. Se a transação do grupo de entidades falhar devido a um erro de simultaneidade otimista (outra pessoa modificou a entidade de índice), você precisará começar novamente na etapa 1.  

Você pode usar uma abordagem semelhante para excluir um funcionário se estiver usando a segunda opção. Alterar o sobrenome de um funcionário é um pouco mais complexo, pois você precisará executar uma transação de grupo de entidades que atualiza três entidades: a entidade Employee, a entidade de índice para o sobrenome antigo e a entidade index para o novo sobrenome. Você deve recuperar cada entidade antes de fazer qualquer alteração a fim de recuperar os valores de ETag que você pode usar para executar as atualizações usando a simultaneidade otimista.  

As etapas a seguir descrevem o processo que você deve seguir quando precisar pesquisar todos os funcionários com um determinado sobrenome em um departamento se você estiver usando a segunda opção. Neste exemplo, Estamos pesquisando todos os funcionários com o sobrenome Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** "Sales" e o valor de **RowKey** "Jones".  
2. Analise a lista de IDs de funcionário no campo EmployeeIDs.  
3. Se você precisar de informações adicionais sobre cada um desses funcionários (como seus endereços de email), recupere cada uma das entidades de funcionário usando o valor de **PartitionKey** "vendas" e os valores de **RowKey** da lista de funcionários obtidos na etapa 2.  

<u>Opção #3:</u> Criar entidades de índice em uma partição ou tabela separada  

Para a terceira opção, use entidades de índice que armazenem os seguintes dados:  

![Entidade Employee com cadeia de caracteres contendo uma lista de IDs de funcionários com o mesmo sobrenome][15]

A propriedade **employeeids** contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no **RowKey**.  

Com a terceira opção, você não pode usar EGTs para manter a consistência porque as entidades de índice estão em uma partição separada das entidades de funcionário. Você deve garantir que as entidades de índice sejam eventualmente consistentes com as entidades de funcionário.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades de índice para obter a lista de valores de **RowKey** e, em seguida, consultas para recuperar cada entidade na lista.  
* Considerando que uma entidade individual tem um tamanho máximo de 1 MB, a opção #2 e a opção #3 na solução pressupõem que a lista de IDs de funcionários para qualquer sobrenome especificado nunca seja maior que 1 MB. Se a lista de IDs de funcionários for provavelmente maior que 1 MB de tamanho, use a opção #1 e armazene os dados de índice no armazenamento de BLOBs.  
* Se você usar a opção #2 (usando EGTs para lidar com a adição e exclusão de funcionários e alterar o sobrenome de um funcionário), será necessário avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se esse for o caso, você deve considerar uma solução eventualmente consistente (opção #1 ou #3 de opção) que usa filas para lidar com as solicitações de atualização e permite que você armazene suas entidades de índice em uma partição separada das entidades de funcionário.  
* A opção #2 nesta solução pressupõe que você deseja procurar por sobrenome dentro de um departamento: por exemplo, você deseja recuperar uma lista de funcionários com um sobrenome Jones no departamento de vendas. Se você quiser ser capaz de Pesquisar todos os funcionários com um sobrenome Jones em toda a organização, use a opção #1 ou Option #3.
* Você pode implementar uma solução baseada em fila que fornece consistência eventual (consulte o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando desejar pesquisar um conjunto de entidades que compartilham um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados juntos em uma única entidade para permitir que você recupere todos os dados necessários com uma única consulta de ponto.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, normalmente você normaliza dados para remover a duplicação, resultando em consultas que recuperam dados de várias tabelas. Se você normalizar seus dados nas tabelas do Azure, deverá fazer várias viagens de ida e volta do cliente para o servidor para recuperar os dados relacionados. Por exemplo, com a estrutura de tabela mostrada abaixo, você precisa de duas viagens de ida e volta para recuperar os detalhes de um departamento: um para buscar a entidade de departamento que inclui a ID do gerente e outra solicitação para buscar os detalhes do gerente em uma entidade de funcionário.  

![Entidade de departamento e entidade de funcionário][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade Department. Por exemplo:  

![Entidade de departamento desnormalizada e combinada][17]

Com as entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes necessários sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Há alguma sobrecarga de custo associada ao armazenamento de alguns dados duas vezes. O benefício de desempenho (resultante de menos solicitações para o serviço de armazenamento) normalmente supera o aumento marginal nos custos de armazenamento (e esse custo é parcialmente compensado por uma redução no número de transações que você precisa para buscar os detalhes de um departamento ).  
* Você deve manter a consistência das duas entidades que armazenam informações sobre os gerentes. Você pode lidar com o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica: nesse caso, a entidade Department e a entidade Employee para o gerente do departamento são armazenadas na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar pesquisar informações relacionadas com frequência. Esse padrão reduz o número de consultas que o cliente deve fazer para recuperar os dados necessários.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é natural usar junções em consultas para retornar partes relacionadas de dados para o cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contenham dados de desempenho e de revisão para esse funcionário.  

Suponha que você esteja armazenando entidades de funcionário no serviço tabela usando a seguinte estrutura:  

![Entidade de funcionário][18]

Você também precisa armazenar dados históricos relacionados a revisões e desempenho para cada ano que o funcionário trabalhou para sua organização e você precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Entidade de revisão do funcionário][19]

Observe que, com essa abordagem, você pode optar por duplicar algumas informações (como nome e sobrenome) na nova entidade para permitir que você recupere seus dados com uma única solicitação. No entanto, você não pode manter a consistência forte porque não pode usar um EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Entidade Employee com chave composta][20]

Observe como o **RowKey** agora é uma chave composta composta pela ID do funcionário e pelo ano dos dados de revisão que permitem recuperar o desempenho do funcionário e examinar os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como você pode recuperar todos os dados de revisão de um funcionário específico (como o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' empid_000123 ') e (RowKey lt ' empid_000124 ') & $select = RowKey, gerente de classificação, classificação de pares, comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve usar um caractere separador adequado que torna mais fácil analisar o valor de **RowKey** : por exemplo, **000123_2012**.  
* Você também está armazenando essa entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que você pode usar o EGTs para manter a consistência forte.
* Você deve considerar a frequência com que irá consultar os dados para determinar se esse padrão é apropriado.  Por exemplo, se você for acessar os dados de revisão com pouca frequência e os dados principais do funcionário, com frequência, você deverá mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar uma ou mais entidades relacionadas que você consulta com frequência.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de cauda de log
Recupere as entidades *n* adicionadas mais recentemente a uma partição usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

> [!NOTE]
> Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Portanto, esse padrão é adequado para o armazenamento de tabelas do Azure e não Azure Cosmos DB. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é poder recuperar as entidades criadas mais recentemente, por exemplo, as dez declarações de despesas mais recentes enviadas por um funcionário. As consultas de tabela dão suporte a uma **$Top** operação de consulta para retornar as primeiras *n* entidades de um conjunto: não há nenhuma operação de consulta equivalente para retornar as últimas n entidades em um conjunto.  

#### <a name="solution"></a>Solução
Armazene as entidades usando um **RowKey** que, naturalmente, classifica em ordem inversa de data/hora usando, portanto, a entrada mais recente é sempre a primeira na tabela.  

Por exemplo, para poder recuperar as dez solicitações de despesas mais recentes enviadas por um funcionário, você pode usar um valor de tique inverso derivado da data/hora atual. O exemplo C# de código a seguir mostra uma maneira de criar um valor adequado de "tiques invertidos" para um **RowKey** que é classificado do mais recente para o mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode voltar ao valor de data e hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem esta aparência:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve preencher o valor de tique inverso com zeros à esquerda para garantir que o valor da cadeia de caracteres seja classificado conforme o esperado.  
* Você deve estar ciente das metas de escalabilidade no nível de uma partição. Tenha cuidado para não criar partições de ponto de acesso.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar acessar entidades na ordem inversa de data/hora ou quando precisar acessar as entidades adicionadas mais recentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Antipadrão de preceder/acrescentar](#prepend-append-anti-pattern)  
* [Recuperando entidades](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de alto volume
Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada; Exclua as entidades excluindo a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos excluem dados antigos que não precisam mais estar disponíveis para um aplicativo cliente ou que o aplicativo tenha Arquivado em outro meio de armazenamento. Normalmente, você identifica esses dados por uma data: por exemplo, você tem um requisito para excluir registros de todas as solicitações de entrada com mais de 60 dias.  

Um design possível é usar a data e a hora da solicitação de entrada no **RowKey**:  

![Entidade de tentativa de logon][21]

Essa abordagem evita pontos de acesso de partição porque o aplicativo pode inserir e excluir entidades de entrada para cada usuário em uma partição separada. No entanto, essa abordagem pode ser dispendiosa e demorada se você tiver um grande número de entidades, pois primeiro você precisa executar uma verificação de tabela para identificar todas as entidades a serem excluídas e, em seguida, deve excluir cada entidade antiga. Você pode reduzir o número de viagens de ida e volta ao servidor necessário para excluir as entidades antigas enviando várias solicitações de exclusão para o EGTs.  

#### <a name="solution"></a>Solução
Use uma tabela separada para cada dia de tentativas de entrada. Você pode usar o design de entidade acima para evitar pontos de início quando estiver inserindo entidades, e a exclusão de entidades antigas agora é simplesmente uma questão de excluir uma tabela todos os dias (uma única operação de armazenamento) em vez de localizar e excluir centenas e milhares de pessoas entra em entidades todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O design dá suporte a outras maneiras em que seu aplicativo usará os dados, como Pesquisar entidades específicas, vincular a outros dados ou gerar informações agregadas?  
* O design evita pontos de acesso quando você está inserindo novas entidades?  
* Espere um atraso se você quiser reutilizar o mesmo nome de tabela depois de excluí-lo. É melhor usar sempre nomes de tabela exclusivos.  
* Espere uma limitação de taxa ao usar pela primeira vez uma nova tabela, enquanto o serviço tabela aprende os padrões de acesso e distribui as partições entre os nós. Você deve considerar a frequência com que precisa criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando tiver um alto volume de entidades que você deve excluir ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)
* [Modificando entidades](#modifying-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é que um aplicativo armazene uma série de dados que ele normalmente precisa recuperar de uma só vez. Por exemplo, seu aplicativo pode registrar quantas mensagens INSTANTÂNEAs cada funcionário envia a cada hora e, em seguida, usar essas informações para plotar quantas mensagens cada usuário enviou nas 24 horas anteriores. Um design pode ser armazenar 24 entidades para cada funcionário:  

![Entidade de estatísticas de mensagem][22]

Com esse design, você pode facilmente localizar e atualizar a entidade a ser atualizada para cada funcionário sempre que o aplicativo precisar atualizar o valor da contagem de mensagens. No entanto, para recuperar as informações para plotar um gráfico da atividade das 24 horas anteriores, você deve recuperar 24 entidades.  

#### <a name="solution"></a>Solução
Use o design a seguir com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagem com propriedades separadas][23]

Com esse design, você pode usar uma operação de mesclagem para atualizar a contagem de mensagens para um funcionário por uma hora específica. Agora, você pode recuperar todas as informações necessárias para plotar o gráfico usando uma solicitação para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se sua série de dados completa não couber em uma única entidade (uma entidade pode ter até 252 Propriedades), use um armazenamento de dados alternativo, como um blob.  
* Se você tiver vários clientes atualizando uma entidade simultaneamente, será necessário usar a **ETag** para implementar a simultaneidade otimista. Se você tiver muitos clientes, poderá enfrentar uma alta contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar atualizar e recuperar uma série de dados associada a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Mesclar ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em um blob)  

### <a name="wide-entities-pattern"></a>Padrão de entidades largas
Use várias entidades físicas para armazenar entidades lógicas com mais de 252 Propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter até 252 Propriedades (excluindo as propriedades obrigatórias do sistema) e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, normalmente você pode arredondar os limites do tamanho de uma linha adicionando uma nova tabela e impondo uma relação de 1 para 1 entre elas.  

#### <a name="solution"></a>Solução
Usando o serviço tabela, você pode armazenar várias entidades para representar um único objeto de negócios grande com mais de 252 Propriedades. Por exemplo, se você quiser armazenar uma contagem do número de mensagens INSTANTÂNEAs enviadas por cada funcionário pelos últimos 365 dias, poderá usar o design a seguir que usa duas entidades com esquemas diferentes:  

![Entidade de estatísticas de mensagem com RowKey 01 e entidade de estado de mensagem com RowKey 02][24]

Se você precisar fazer uma alteração que exija a atualização de ambas as entidades para mantê-las sincronizadas entre si, você poderá usar um EGT. Caso contrário, você pode usar uma única operação de mesclagem para atualizar a contagem de mensagens de um dia específico. Para recuperar todos os dados de um funcionário individual, você deve recuperar ambas as entidades, que podem ser feitas com duas solicitações eficientes que usam um valor de **PartitionKey** e **RowKey** .  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A recuperação de uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades exceda os limites de uma entidade individual no serviço tabela.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)
* [Mesclar ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use o armazenamento de BLOBs para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenarem valores que fazem com que o tamanho total da entidade exceda esse valor, você não poderá armazenar a entidade inteira no serviço tabela.  

#### <a name="solution"></a>Solução
Se sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você poderá armazenar dados no serviço BLOB e, em seguida, armazenar o endereço do blob em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento de BLOBs e armazenar um link para a foto na propriedade **Photo** da sua entidade Employee:  

![Entidade Employee com cadeia de caracteres para foto apontando para o armazenamento de BLOBs][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no serviço tabela e os dados no serviço BLOB, use o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho exceda os limites de uma entidade individual no serviço tabela.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades largas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão de preceder/acrescentar
Aumente a escalabilidade quando tiver um alto volume de inserções distribuindo as inserções entre várias partições.  

#### <a name="context-and-problem"></a>Contexto e problema
O prependência ou o acréscimo de entidades às entidades armazenadas normalmente resulta no aplicativo adicionando novas entidades à primeira ou à última partição de uma sequência de partições. Nesse caso, todas as inserções em um determinado momento estão ocorrendo na mesma partição, criando um ponto de acesso que impede que o serviço tabela faça o balanceamento de carga de inserções em vários nós e, possivelmente, fazendo com que seu aplicativo atinja as metas de escalabilidade para particion. Por exemplo, se você tiver um aplicativo que registra o acesso de rede e recursos por funcionários, uma estrutura de entidade, como mostrado abaixo, pode resultar na partição da hora atual se tornando um ponto de acesso se o volume de transações atingir o destino de escalabilidade para um partição individual:  

![Entidade de funcionário][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de HotSpot em qualquer partição específica, pois o aplicativo registra eventos:  

![Entidade Employee com RowKey compondo o ano, mês, dia, hora e ID do evento][27]

Observe que, com este exemplo, o **PartitionKey** e o **RowKey** são chaves compostas. O **PartitionKey** usa a ID do departamento e do funcionário para distribuir o registro em log entre várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa que evita a criação de partições ativas em inserções dá suporte eficiente às consultas que seu aplicativo cliente faz?  
* O volume previsto de transações significa que é provável que você atinja as metas de escalabilidade de uma partição individual e seja limitado pelo serviço de armazenamento?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o antipadrão preceder/acrescentar quando o volume de transações provavelmente resultará em limitação de taxa pelo serviço de armazenamento quando você acessar uma partição ativa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificando entidades](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Antipadrão de dados de log
Normalmente, você deve usar o serviço blob em vez do serviço tabela para armazenar dados de log.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum para dados de log é recuperar uma seleção de entradas de log para um intervalo de data/hora específico: por exemplo, você deseja localizar todas as mensagens de erro e críticas que o aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e a hora da mensagem de log para determinar a partição para a qual você salva as entidades de log: isso resulta em uma partição ativa porque, a qualquer momento, todas as entidades de log compartilharão o mesmo valor **PartitionKey** (consulte a seção [preceder/ acrescentar antipadrão](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de log resulta em uma partição ativa porque o aplicativo grava todas as mensagens de log na partição para a data e hora atuais:  

![Entidade de mensagem de log][28]

Neste exemplo, o **RowKey** inclui a data e a hora da mensagem de log para garantir que as mensagens de log sejam armazenadas classificadas em ordem de data/hora e inclua uma ID de mensagem caso várias mensagens de log compartilhem a mesma data e hora.  

Outra abordagem é usar um **PartitionKey** que garanta que o aplicativo grave mensagens em um intervalo de partições. Por exemplo, se a origem da mensagem de log fornecer uma maneira de distribuir mensagens em várias partições, você poderá usar o seguinte esquema de entidade:  

![Entidade de mensagem de log][29]

No entanto, o problema com esse esquema é que, para recuperar todas as mensagens de log de um período de tempo específico, você deve pesquisar todas as partições na tabela.

#### <a name="solution"></a>Solução
A seção anterior realçou o problema de tentar usar o serviço tabela para armazenar entradas de log e sugerir dois designs insatisfatórios. Uma solução levou a uma partição ativa com o risco de mau desempenho na gravação de mensagens de log; a outra solução resultou em um desempenho de consulta insatisfatório devido ao requisito de verificar cada partição na tabela para recuperar mensagens de log para um período de tempo específico. O armazenamento de blob oferece uma solução melhor para esse tipo de cenário e é assim que Análise de Armazenamento do Azure armazena os dados de log coletados.  

Esta seção descreve como Análise de Armazenamento armazena os dados de log no armazenamento de BLOBs como uma ilustração dessa abordagem para armazenar dados que você normalmente consulta por intervalo.  

O Análise de Armazenamento armazena mensagens de log em um formato delimitado em vários BLOBs. O formato delimitado torna mais fácil para um aplicativo cliente analisar os dados na mensagem de log.  

Análise de Armazenamento usa uma Convenção de nomenclatura para BLOBs que permite localizar o blob (ou BLOBs) que contém as mensagens de log para as quais você está pesquisando. Por exemplo, um blob chamado "Queue/2014/07/31/1800/000001. log" contém mensagens de log relacionadas ao serviço fila para a hora a partir de 18:00 em 31 de julho de 2014. O "000001" indica que este é o primeiro arquivo de log para esse período. O Análise de Armazenamento também registra os carimbos de data/hora das mensagens do primeiro e do último log armazenadas no arquivo como parte dos metadados do blob. A API para armazenamento de BLOBs permite localizar BLOBs em um contêiner com base em um prefixo de nome: para localizar todos os blobs que contêm dados de log de fila para a hora que começa em 18:00, você pode usar o prefixo "Queue/2014/07/31/1800."  

O Análise de Armazenamento armazena em buffer mensagens de log internamente e, em seguida, atualiza periodicamente o blob apropriado ou cria um novo com o lote mais recente de entradas de log. Isso reduz o número de gravações que ele deve executar para o serviço BLOB.  

Se você estiver implementando uma solução semelhante em seu próprio aplicativo, deverá considerar como gerenciar a compensação entre a confiabilidade (gravando cada entrada de log no armazenamento de BLOBs conforme ele acontece) e o custo e a escalabilidade (armazenando em buffer as atualizações em seu aplicativo e gravando eles para o armazenamento de BLOBs em lotes).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de log:  

* Se você criar um design de tabela que evite possíveis partições Hot, talvez você ache que não pode acessar seus dados de log com eficiência.  
* Para processar dados de log, um cliente geralmente precisa carregar muitos registros.  
* Embora os dados de log sejam geralmente estruturados, o armazenamento de BLOBs pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta seção aborda algumas das considerações que você deve ter em mente ao implementar os padrões descritos nas seções anteriores. A maior parte desta seção usa exemplos escritos C# em que usam a biblioteca de cliente de armazenamento (versão do 4.3.0 no momento da gravação).  

### <a name="retrieving-entities"></a>Recuperando entidades
Conforme discutido na seção [design para consulta](#design-for-querying), a consulta mais eficiente é uma consulta de ponto. No entanto, em alguns cenários, talvez seja necessário recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a biblioteca de cliente de armazenamento.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executando uma consulta de ponto usando a biblioteca de cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação de **recuperação** de tabela, conforme mostrado C# no trecho de código a seguir, que recupera uma entidade com um **PartitionKey** de valor "Sales" e um **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como este exemplo espera que a entidade que ele recupera seja do tipo **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Recuperando várias entidades usando o LINQ
Você pode recuperar várias entidades usando o LINQ com a biblioteca de cliente de armazenamento e especificando uma consulta com uma cláusula **Where** . Para evitar uma verificação de tabela, você deve sempre incluir o valor **PartitionKey** na cláusula WHERE e, se possível, o valor de **RowKey** para evitar verificações de tabela e de partição. O serviço tabela dá suporte a um conjunto limitado de operadores de comparação (maior que, maior ou igual, menor que, menor que ou igual a, igual e diferente) para usar na cláusula WHERE. O trecho C# de código a seguir localiza todos os funcionários cujo sobrenome começa com "B" (supondo que o **RowKey** armazena o sobrenome) no departamento de vendas (supondo que o **PartitionKey** armazene o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta especifica um **RowKey** e um **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código a seguir mostra a funcionalidade equivalente usando a API Fluent (para obter mais informações sobre APIs fluentes em geral, consulte [práticas recomendadas para criar uma API fluente](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> O exemplo Aninha vários métodos **CombineFilters** para incluir as três condições de filtro.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperando grandes números de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base em um valor de **PartitionKey** e um valor de **RowKey** . No entanto, em alguns cenários, você pode ter um requisito para retornar muitas entidades da mesma partição ou até mesmo de muitas partições.  

Você deve sempre testar completamente o desempenho do seu aplicativo nesses cenários.  

Uma consulta no serviço tabela pode retornar um máximo de 1.000 entidades ao mesmo tempo e pode ser executada por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não tiver sido concluída em cinco segundos ou se a consulta cruzar o limite da partição, o serviço tabela retornará um token de continuação para permitir que o aplicativo cliente solicite o próximo conjunto de entidades. Para obter mais informações sobre como os tokens de continuação funcionam, consulte [tempo limite de consulta e paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a biblioteca de cliente de armazenamento, ela poderá manipular automaticamente os tokens de continuação para você, pois ele retorna entidades do serviço tabela. O exemplo C# de código a seguir usando a biblioteca de cliente de armazenamento manipula automaticamente os tokens de continuação se o serviço de tabela os retornar em uma resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

O código C# a seguir trata os tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando tokens de continuação explicitamente, você pode controlar quando seu aplicativo recupera o próximo segmento de dados. Por exemplo, se o aplicativo cliente permitir que os usuários percorram as entidades armazenadas em uma tabela, um usuário poderá decidir não paginar todas as entidades recuperadas pela consulta para que seu aplicativo use apenas um token de continuação para recuperar o próximo segmento quando o usuário concluiu a paginação por todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Ele permite limitar a quantidade de dados a serem recuperados do serviço tabela e que você move pela rede.  
* Ele permite que você execute a e/s assíncrona no .NET.  
* Ele permite serializar o token de continuação para o armazenamento persistente para que você possa continuar no caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento que contém 1.000 entidades, embora possa ser menos. Esse também é o caso se você limitar o número de entradas que uma consulta retorna usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de pesquisa: o serviço tabela pode retornar um segmento contendo menos de n entidades junto com um token de continuação para permite que você recupere as entidades restantes.  
> 
> 

O código C# a seguir mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 Propriedades e ter até 1 MB de tamanho. Quando você consulta a tabela e recupera entidades, talvez não precise de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Você pode usar a projeção do lado do servidor para transferir apenas as propriedades necessárias. O exemplo a seguir recupera apenas a propriedade **email** (juntamente com **PartitionKey**, **RowKey**, **timestamp**e **ETag**) das entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observe como o valor **RowKey** está disponível, embora não tenha sido incluído na lista de propriedades a serem recuperados.  

### <a name="modifying-entities"></a>Modificando entidades
A biblioteca de cliente de armazenamento permite que você modifique suas entidades armazenadas no serviço tabela inserindo, excluindo e atualizando entidades. Você pode usar o EGTs para agrupar várias operações de inserção, atualização e exclusão para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da solução.  

As exceções geradas quando a biblioteca de cliente de armazenamento executa um EGT normalmente incluem o índice da entidade que causou a falha do lote. Isso é útil quando você está depurando código que usa EGTs.  

Você também deve considerar como seu design afeta como o aplicativo cliente lida com operações de atualização e simultaneidade.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por padrão, o serviço tabela implementa verificações de simultaneidade otimistas no nível de entidades individuais para operações de **inserção**, **mesclagem**e **exclusão** , embora seja possível que um cliente Force o serviço de tabela a ignorá-las procedimento. Para obter mais informações sobre como o serviço tabela gerencia a simultaneidade, consulte [Gerenciando a simultaneidade no armazenamento do Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Mesclar ou substituir
O método **replace** da classe **TableOperation** sempre substitui a entidade completa no serviço tabela. Se você não incluir uma propriedade na solicitação quando essa propriedade existir na entidade armazenada, a solicitação removerá essa propriedade da entidade armazenada. A menos que você queira remover uma propriedade explicitamente de uma entidade armazenada, você deve incluir todas as propriedades na solicitação.  

Você pode usar o método **Merge** da classe **TableOperation** para reduzir a quantidade de dados enviados para o serviço tabela quando desejar atualizar uma entidade. O método **Merge** substitui todas as propriedades na entidade armazenada por valores de propriedade da entidade incluída na solicitação, mas deixa intactas todas as propriedades na entidade armazenada que não estão incluídas na solicitação. Isso será útil se você tiver grandes entidades e precisar apenas atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os métodos **replace** e **Merge** falharão se a entidade não existir. Como alternativa, você pode usar os métodos **InsertOrReplace** e **InsertOrMerge** que criam uma nova entidade, caso ela não exista.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Trabalhando com tipos de entidade heterogêneas
O serviço tabela é um repositório *de tabela sem esquema* que significa que uma única tabela pode armazenar entidades de vários tipos, proporcionando grande flexibilidade ao seu design. O exemplo a seguir ilustra uma tabela que armazena as entidades Employee e Department:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Cada entidade ainda deve ter valores de **PartitionKey**, **RowKey**e **timestamp** , mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que você opte por armazenar essas informações em algum lugar. Há duas opções para identificar o tipo de entidade:  

* Preceda o tipo de entidade para **RowKey** (ou possivelmente o **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores de **RowKey** .  
* Use uma propriedade separada para registrar o tipo de entidade, conforme mostrado na tabela a seguir.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Departamento</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, pretendo o tipo de entidade para o **RowKey**, será útil se houver uma possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor de chave. Ele também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta seção são especialmente relevantes para as [relações de herança](#inheritance-relationships) de discussão anteriormente neste guia na seção relações de modelagem.  

> [!NOTE]
> Você deve considerar a inclusão de um número de versão no valor do tipo de entidade para permitir que aplicativos cliente evoluam objetos POCO e trabalhem com versões diferentes.  
> 
> 

O restante desta seção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Recuperando tipos de entidade heterogênea
Se você estiver usando a biblioteca de cliente de armazenamento, terá três opções para trabalhar com vários tipos de entidade.  

Se você souber o tipo da entidade armazenada com um **RowKey** e valores de **PartitionKey** específicos, poderá especificar o tipo de entidade ao recuperar a entidade, conforme mostrado nos dois exemplos anteriores que recuperam entidades do tipo **EmployeeEntity** : [Executando uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library) e [recuperando várias entidades usando LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é usar o tipo **DynamicTableEntity** (um recipiente de propriedades) em vez de um tipo de entidade poco concreto (essa opção também pode melhorar o desempenho porque não há necessidade de serializar e desserializar a entidade para tipos .net). O código C# a seguir recupera potencialmente várias entidades de tipos diferentes da tabela, mas retorna todas as entidades como instâncias de **DynamicTableEntity** . Em seguida, ele usa a propriedade **EntityType** para determinar o tipo de cada entidade:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Para recuperar outras propriedades, você deve usar o método **TryGetValue** na propriedade **Properties** da classe **DynamicTableEntity** .  

Uma terceira opção é combinar usando o tipo **DynamicTableEntity** e uma instância **EntityResolver** . Isso permite que você resolva para vários tipos de POCO na mesma consulta. Neste exemplo, o delegado **EntityResolver** está usando a propriedade **EntityType** para distinguir entre os dois tipos de entidade que a consulta retorna. O método **resolve** usa o delegado do **resolvedor** para resolver instâncias de **DynamicTableEntity** para instâncias **TableEntity** .  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Modificando tipos de entidade heterogênea
Você não precisa saber o tipo de uma entidade para excluí-la e sempre sabe o tipo de uma entidade ao inseri-la. No entanto, você pode usar o tipo **DynamicTableEntity** para atualizar uma entidade sem saber seu tipo e sem usar uma classe de entidade poco. O exemplo de código a seguir recupera uma única entidade e verifica se a propriedade **EmployeeCount** existe antes de atualizá-la.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Controlando o acesso com assinaturas de acesso compartilhado
Você pode usar tokens de SAS (assinatura de acesso compartilhado) para permitir que aplicativos cliente modifiquem (e consultem) as entidades de tabela diretamente sem a necessidade de autenticação direta com o serviço tabela. Normalmente, há três benefícios principais no uso de SAS em seu aplicativo:  

* Você não precisa distribuir sua chave de conta de armazenamento para uma plataforma não segura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no serviço tabela.  
* Você pode descarregar parte do trabalho que as funções da Web e de trabalho executam no gerenciamento de suas entidades para dispositivos cliente, como computadores de usuário final e dispositivos móveis.  
* Você pode atribuir um conjunto de permissões restritas e limitadas por tempo a um cliente (como permitir acesso somente leitura a recursos específicos).  

Para obter mais informações sobre como usar tokens SAS com o serviço tabela, consulte [usando assinaturas de acesso compartilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem a um aplicativo cliente as entidades no serviço tabela: faça isso em um ambiente que tenha acesso seguro às chaves da conta de armazenamento. Normalmente, você usa uma função Web ou de trabalho para gerar os tokens SAS e fornecê-los aos aplicativos cliente que precisam de acesso às suas entidades. Como ainda há uma sobrecarga envolvida na geração e entrega de tokens SAS aos clientes, você deve considerar a melhor maneira de reduzir essa sobrecarga, especialmente em cenários de alto volume.  

É possível gerar um token SAS que concede acesso a um subconjunto de entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceda acesso a um intervalo de valores **PartitionKey** ou a um intervalo de valores **PartitionKey** e **RowKey** . Você pode optar por gerar tokens SAS para usuários individuais do seu sistema, de modo que o token SAS de cada usuário só permita acesso às suas próprias entidades no serviço tabela.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que você esteja distribuindo suas solicitações entre várias partições, você pode melhorar a taxa de transferência e a capacidade de resposta do cliente usando consultas assíncronas ou paralelas.
Por exemplo, você pode ter duas ou mais instâncias de função de trabalho acessando suas tabelas em paralelo. Você pode ter funções de trabalho individuais responsáveis por determinados conjuntos de partições ou simplesmente ter várias instâncias de função de trabalho, cada uma capaz de acessar todas as partições em uma tabela.  

Em uma instância de cliente, você pode melhorar a taxa de transferência executando operações de armazenamento de forma assíncrona. A biblioteca de cliente de armazenamento facilita a gravação de consultas e modificações assíncronas. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, conforme mostrado no código C# a seguir:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Você pode facilmente modificar esse código para que a consulta seja executada de forma assíncrona da seguinte maneira:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **Async** e retorna uma instância de **tarefa** .  
* Em vez de chamar o método **ExecuteSegmented** para recuperar os resultados, o método agora chama o método **ExecuteSegmentedAsync** e usa o modificador **Await** para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar esse método várias vezes (com valores diferentes para o parâmetro **Department** ) e cada consulta será executada em um thread separado.  

Não há nenhuma versão assíncrona do método **Execute** na classe **TableQuery** porque a interface **IEnumerable** não oferece suporte à enumeração assíncrona.  

Você também pode inserir, atualizar e excluir entidades de forma assíncrona. O exemplo C# a seguir mostra um método síncrono simples para inserir ou substituir uma entidade Employee:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Você pode facilmente modificar esse código para que a atualização seja executada de forma assíncrona da seguinte maneira:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **Async** e retorna uma instância de **tarefa** .  
* Em vez de chamar o método **Execute** para atualizar a entidade, o método agora chama o método **ExecuteAsync** e usa o modificador **Await** para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar vários métodos assíncronos como esse, e cada invocação de método será executada em um thread separado.  

### <a name="credits"></a>Créditos
Gostaríamos de agradecer aos seguintes membros da equipe do Azure por suas contribuições: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah e Serdar Ozler, bem como Tom Hollander do Microsoft DX. 

Também gostaríamos de agradecer aos seguintes MVPs da Microsoft por seus valiosos comentários durante os ciclos de revisão: Igor Papirov e Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

