---
title: Design Azure Cosmos DB tabelas para escala e performance
description: 'Guia de design de armazenamento de mesa azure: mesas escaláveis e performantes no armazenamento Azure Cosmos DB e Azure Table'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395653"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guia de design de mesa de armazenamento de mesa azure: tabelas escaláveis e performantes

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para criar tabelas dimensionáveis e de alto desempenho, tem de considerar uma variedade de fatores, incluindo o custo. Se tiver criado anteriormente esquemas para bases de dados relacionais, estará familiarizado com estas considerações. No entanto, embora existam algumas semelhanças entre o Armazenamento de tabelas do Azure e os modelos relacionais, também há muitas diferenças importantes. Estas diferenças levam normalmente a diferentes designs que podem parecer incorretos ou não intuitivos para alguém familiarizado com bases de dados relacionais, mas isso faz sentido se estiver a criar um arquivo de chaves/valores NoSQL, como o Armazenamento de tabelas.

O armazenamento de mesas destina-se a apoiar aplicações em escala em nuvem que possam conter milhares de milhões de entidades ("linhas" na terminologia relacional da base de dados) de dados, ou para conjuntos de dados que devem suportar volumes de transações elevados. Por isso, é preciso pensar de forma diferente sobre como armazena os seus dados e compreender como funciona o armazenamento de mesa. Uma loja de dados NoSQL bem concebida pode permitir que a sua solução escale muito mais (e a um custo mais baixo) do que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-azure-table-storage"></a>Sobre o armazenamento da Mesa Azure
Esta secção destaca algumas das principais características do armazenamento de mesa seletivas que são especialmente relevantes para o design para o desempenho e escalabilidade. Se for novo no armazenamento de Armazenamento e Mesa do Azure, consulte introdução [ao Armazenamento Microsoft Azure](../storage/common/storage-introduction.md) e [inicie-se com o armazenamento da Mesa Azure utilizando .NET](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia esteja no armazenamento de mesa, inclui alguma discussão sobre o armazenamento de fila Azure e armazenamento Azure Blob, e como você pode usá-los juntamente com armazenamento de mesa em uma solução.  

O armazenamento de mesa utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades de entidade. Cada entidade tem um par de chaves para identificá-lo de forma única, e uma coluna de carimbo de tempo que o armazenamento de mesa usa para rastrear quando a entidade foi atualizada pela última vez. O campo de carimbo saciado automaticamente e não pode substituir manualmente a marca de tempo com um valor arbitrário. O armazenamento de mesa utiliza este carimbo temporal (LMT) modificado para gerir a conmoeda otimista.  

> [!NOTE]
> Armazenamento de mesa AS operações da API também devolvem um valor `ETag` que obtém do LMT. Neste documento, os termos ETag e LMT são utilizados intercambiavelmente, porque se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo seguinte mostra um design de tabela simples para armazenar as entidades employee e department. Muitos dos exemplos mostrados mais tarde neste guia são baseiam-se este design simples.  

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
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
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
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>CaO</td>
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
<th>FirstName</th>
<th>LastName</th>
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


Até agora, este design parece semelhante a uma tabela numa base de dados relacional. As principais diferenças são as colunas obrigatórias e a capacidade de armazenar vários tipos de entidades na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, como **o FirstName** ou **a Age,** tem um tipo de dados, como o número inteiro ou o string, tal como uma coluna numa base de dados relacional. No entanto, ao contrário de uma base de dados relacional, a natureza sem esquemas de armazenamento de mesas significa que um imóvel não precisa de ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para mais informações, consulte [Understanding Table storage data model](https://msdn.microsoft.com/library/azure/dd179338.aspx).

A sua escolha de `PartitionKey` e `RowKey` é fundamental para um bom design de mesa. Todas as entidades armazenadas numa mesa devem ter uma combinação única de `PartitionKey` e `RowKey`. Tal como acontece com as teclas numa tabela de bases de dados relacional, os valores `PartitionKey` e `RowKey` estão indexados para criar um índice agrupado que permita uma rápida pesquisa. O armazenamento de mesas, no entanto, não cria quaisquer índices secundários, por isso estas são as duas únicas propriedades indexadas (alguns dos padrões descritos mais tarde mostram como você pode trabalhar em torno desta limitação aparente).  

Uma tabela é composta por uma ou mais divisórias, e muitas das decisões de design que tomar será em torno de escolher um `PartitionKey` adequado e `RowKey` para otimizar a sua solução. Uma solução pode consistir apenas numa única tabela que contém todas as suas entidades organizadas em divisórias, mas tipicamente uma solução tem várias tabelas. As tabelas ajudam-no a organizar logicamente as suas entidades e a ajudá-lo a gerir o acesso aos dados utilizando listas de controlo de acesso. Pode largar uma mesa inteira utilizando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Partições da tabela
O nome da conta, o nome da tabela e `PartitionKey` juntos identificam a partição dentro do serviço de armazenamento onde o armazenamento de mesa armazena a entidade. Além de fazerem parte do regime de endereçamento das entidades, as divisórias definem uma margem de manobra para transações (ver a secção posterior neste artigo, [entidade seletiva de operações),](#entity-group-transactions)e formam a base de como as tabelas de armazenamento de tabelas. Para obter mais informações sobre divisórias de mesa, consulte a lista de [verificação de desempenho e escalabilidade para armazenamento](../storage/tables/storage-performance-checklist.md)de mesa .  

No armazenamento de mesa, um nó individual presta serviços uma ou mais divisórias completas, e as escalas de serviço através de divisórias dinamicamente equilibradas em nós. Se um nó estiver sob carga, o armazenamento de mesa pode dividir a gama de divisórias servidas por esse nó em diferentes nós. Quando o tráfego diminui, o armazenamento de mesa pode fundir as divisórias desde nós silenciosos de volta a um único nó.  

Para obter mais informações sobre os detalhes internos do armazenamento de mesa, e em particular como gere as divisórias, consulte o [Microsoft Azure Storage: Um serviço de armazenamento em nuvem altamente disponível com forte consistência.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

### <a name="entity-group-transactions"></a>Transações de grupos de entidades
No armazenamento em tabela, as transações de grupos de entidades (EGTs) são o único mecanismo incorporado para a realização de atualizações atómicas em várias entidades. Os EGTs também são referidos como transações de *lote.* Os EGTs só podem operar em entidades armazenadas na mesma divisória (partilhando a mesma chave de partição numa determinada tabela), pelo que sempre que necessitar de comportamento transacional atómico em várias entidades, certifique-se de que essas entidades estão na mesma divisão. Esta é muitas vezes uma razão para manter vários tipos de entidades na mesma tabela (e partição), e não usar várias tabelas para diferentes tipos de entidades. Um único EGT pode operar no máximo 100 entidades.  Se submeter vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operam em entidades que são comuns em todos os EGTs. Caso contrário, arrisca-se a atrasar o processamento.

Os EGTs também introduzem uma potencial compensação para você avaliar no seu design. A utilização de mais divisórias aumenta a escalabilidade da sua aplicação, porque o Azure tem mais oportunidades para pedidos de equilíbrio de carga em nós. Mas isto pode limitar a capacidade da sua aplicação para realizar transações atómicas e manter uma forte consistência para os seus dados. Além disso, existem alvos específicos de escalabilidade ao nível de uma partição que podem limitar a entrada de transações que você pode esperar para um único nó.

Para obter mais informações sobre os alvos de escalabilidade das contas de armazenamento do Azure, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre os objetivos de escalabilidade para o armazenamento de mesas, consulte [a escalabilidade e os objetivos de desempenho para](../storage/tables/scalability-targets.md)o armazenamento de mesas . As seções posteriores deste guia discutem várias estratégias de que o ajudam a gerir as vantagens e desvantagens, como este e descrevem a melhor maneira de escolher a sua chave de partição com base nos requisitos específicos da sua aplicação de cliente de design.  

### <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela a seguir inclui alguns dos valores-chave a ter em conta quando está a desenhar uma solução de armazenamento de mesa:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas numa conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de partições numa tabela |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de entidades numa partição |Limitado apenas pela capacidade da conta de armazenamento. |
| Tamanho de uma entidade individual |Até 1 MB, com um máximo de 255 propriedades (incluindo o `PartitionKey`, `RowKey`e `Timestamp`). |
| Tamanho da `PartitionKey` |Uma corda até 1 KB de tamanho. |
| Tamanho da `RowKey` |Uma corda até 1 KB de tamanho. |
| Tamanho de uma transação de grupo de entidades |Uma transação pode incluir no máximo 100 entidades, e a carga útil deve ter menos de 4 MB de tamanho. Uma vez, um EGT só pode atualizar uma entidade. |

Para mais informações, consulte Compreender o modelo de [dados do serviço tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custos
O armazenamento de mesa é relativamente barato, mas deve incluir estimativas de custos tanto para o uso da capacidade como para a quantidade de transações como parte da sua avaliação de qualquer solução que utilize o armazenamento de mesa. Em muitos cenários, no entanto, armazenar dados desnormalizados ou duplicados de forma a melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida a tomar. Para obter mais informações sobre preços, consulte o preço do [Armazenamento De Azure.](https://azure.microsoft.com/pricing/details/storage/)  

## <a name="guidelines-for-table-design"></a>Orientações do design da tabela
Estas listas resumem algumas das principais diretrizes que deve ter em mente quando está a desenhar as suas tabelas. Este guia aborda-os com mais detalhes mais tarde. Estas diretrizes são diferentes das diretrizes que normalmente seguiria para o design relacional da base de dados.  

Projetando o seu armazenamento de mesa para ser *lido* eficientemente:

* **Design para consulta em aplicações pesadas de leitura.** Quando estiver a desenhar as suas mesas, pense nas consultas (especialmente as sensíveis à latência) que irá executar antes de pensar em como irá atualizar as suas entidades. Isso normalmente resulta numa solução eficiente e de elevado desempenho.  
* **Especifique tanto `PartitionKey` como `RowKey` nas suas consultas.** Consultas de *pontos* como estas são as consultas de armazenamento de mesa mais eficientes.  
* **Considere armazenar cópias duplicadas de entidades.** O armazenamento de mesa é barato, por isso considere armazenar a mesma entidade várias vezes (com chaves diferentes), para permitir consultas mais eficientes.  
* **Considere desnormalizar os seus dados.** O armazenamento de mesa é barato, por isso considere desnormalizar os seus dados. Por exemplo, armazene entidades de resumidas para que as consultas para agregar dados apenas precisam de aceder a uma única entidade.  
* **Utilize valores-chave compostos.** As únicas chaves que tens são `PartitionKey` e `RowKey`. Por exemplo, utilize os valores de chave compostos para ativar caminhos de com chave de acesso alternativo para entidades.  
* **Use a projeção de consulta.** Pode reduzir a quantidade de dados que transferir através da rede através de consultas que selecionar apenas os campos que precisa.  

Projetando o seu armazenamento de mesa para ser *eficiente:*  

* **Não crie divisórias quentes.** Escolha as chaves que permitem que distribuir os pedidos por várias partições em qualquer ponto no tempo.  
* **Evite picos no trânsito.** Distribua o tráfego por um período de tempo razoável e evite picos de tráfego.
* **Não crie necessariamente uma tabela separada para cada tipo de entidade.** Quando necessitar de transações atómicas em todos os tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição na mesma tabela.
* **Considere o máximo de entrada que deve conseguir.** Deve estar ciente dos alvos de escalabilidade para o armazenamento de mesa, e certifique-se de que o seu design não fará com que exceda-os.  

Mais tarde neste guia, você verá exemplos que colocam todos estes princípios em prática.  

## <a name="design-for-querying"></a>Design das consultas
O armazenamento de mesa pode ser lido intensivamente, escrever intensivo, ou uma mistura dos dois. Esta secção considera a conceção para apoiar as operações de leitura de forma eficiente. Normalmente, um design que suporta operações de leitura com eficiência é também eficiente para operações de escrita. No entanto, existem considerações adicionais na conceção de apoio às operações de escrita. Estes são discutidos na secção seguinte, [Design para modificação de dados](#design-for-data-modification).

Um bom ponto de partida para lhe permitir ler dados de forma eficiente é perguntar "Que consultas a minha aplicação terá de executar para recuperar os dados de que necessita?"  

> [!NOTE]
> Com o armazenamento de mesa, é importante ter o design correto na frente, porque é difícil e caro mudá-lo mais tarde. Por exemplo, numa base de dados relacional, é frequentemente possível abordar problemas de desempenho simplesmente adicionando índices a uma base de dados existente. Esta não é uma opção com armazenamento de mesa.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Como a sua escolha de `PartitionKey` e `RowKey` afeta o desempenho da consulta
Os seguintes exemplos assumem que o armazenamento de mesa está a armazenar entidades de empregados com a seguinte estrutura (a maioria dos exemplos omitem a `Timestamp` propriedade para clareza):  

| Nome da coluna | Tipo de dados |
| --- | --- |
| `PartitionKey` (nome do departamento) |String |
| `RowKey` (Identificação do Empregado) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Número inteiro |
| `EmailAddress` |String |

Aqui estão algumas diretrizes gerais para a conceção de consultas de armazenamento de mesa. A sintaxe do filtro utilizada nos seguintes exemplos é da API de armazenamento de mesa REST API. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

* Uma *consulta* pontual é a procura mais eficiente a ser usada, e é recomendada para procuras de alto volume ou para procurações que requerem a latência mais baixa. Tal consulta pode usar os índices para localizar uma entidade individual de forma eficiente, especificando tanto os valores `PartitionKey` como `RowKey`. Por exemplo: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* O segundo melhor é uma consulta de *alcance.* Utiliza o `PartitionKey`e filtra uma gama de valores `RowKey` para devolver mais do que uma entidade. O valor `PartitionKey` identifica uma partição específica, e os valores `RowKey` identificam um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* O terceiro melhor é uma *varredura de partição.* Utiliza o `PartitionKey`e filtra outra propriedade não chave e pode devolver mais do que uma entidade. O valor `PartitionKey` identifica uma partição específica, e os valores de propriedade selecionam para um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Uma *digitalização* de mesa não inclui o `PartitionKey`, e é ineficiente porque procura todas as divisórias que compõem a sua mesa para quaisquer entidades correspondentes. Executa uma digitalização de mesa independentemente de o seu filtro utilizar ou não o `RowKey`. Por exemplo: `$filter=LastName eq 'Jones'`.  
* Consultas de armazenamento de mesa azure que devolvem várias entidades ordenam-nas em `PartitionKey` e `RowKey` encomenda. Para evitar recorrer às entidades do cliente, escolha um `RowKey` que defina a ordem de classificação mais comum. Os resultados da consulta devolvidos pela API da Tabela Azure em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de características, consulte [diferenças entre a Tabela API no armazenamento de Azure Cosmos DB e Azure Table.](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)

A utilização de um "**ou**" para especificar um filtro com base em valores `RowKey` resulta numa varredura de divisória, e não é tratada como uma consulta de intervalo. Por isso, evite consultas que utilizem filtros como: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Por exemplo, o código do lado do cliente que usa a Biblioteca do Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Executar uma consulta pontual usando a Biblioteca do Cliente de Armazenamento](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperar várias entidades utilizando o LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projeção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que possa lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Escolha um `PartitionKey` apropriado
A sua escolha de `PartitionKey` deve equilibrar a necessidade de permitir a utilização de EGTs (para garantir a consistência) contra a exigência de distribuir as suas entidades por várias divisórias (para garantir uma solução escalável).  

Num extremo, pode armazenar todas as suas entidades numa única divisória. Mas isto pode limitar a escalabilidade da sua solução, e impediria que o armazenamento de mesa saisse capaz de carregar pedidos de equilíbrio. No outro extremo, pode armazenar uma entidade por partição. Isto é altamente escalável e permite que o armazenamento de tabelas faça pedidos de equilíbrio de carga, mas impede-o de utilizar transações de grupos de entidades.  

Um ideal `PartitionKey` permite-lhe utilizar consultas eficientes e tem divisórias suficientes para garantir que a sua solução é escalável. Normalmente, você vai descobrir que as suas entidades terão um imóvel adequado que distribui as suas entidades através de divisórias suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre utilizadores ou funcionários, `UserID` pode ser uma boa `PartitionKey`. Pode ter várias entidades que usam um determinado `UserID` como chave de partição. Cada entidade que armazena dados sobre um utilizador é agrunada numa única partição. Estas entidades são acessíveis através de EGTs, mantendo-se ainda altamente escaláveis.
> 
> 

Existem considerações adicionais na sua escolha de `PartitionKey` que se relacionam com a forma como insere, atualiza e elimina as entidades. Para mais informações, consulte [design para modificação](#design-for-data-modification) de dados mais tarde neste artigo.  

### <a name="optimize-queries-for-table-storage"></a>Otimizar consultas para armazenamento de mesa
O armazenamento de mesaindexa automaticamente as suas entidades utilizando os valores `PartitionKey` e `RowKey` num único índice agrupado. Esta é a razão pela qual as consultas de pontos são as mais eficientes de usar. No entanto, não existem outros índices que não os do índice agrupado sobre o `PartitionKey` e o `RowKey`.

Muitos designs têm de cumprir os requisitos para ativar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de empregados com base em e-mail, identificação do empregado ou apelido. Os seguintes padrões na secção Padrões de design de [tabelas](#table-design-patterns) abordam este tipo de requisitos. Os padrões também descrevem formas de trabalhar em torno do facto de que o armazenamento de mesa não fornece índices secundários.  

* [Padrão de índice secundário intra-partição](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores `RowKey` (na mesma divisória). Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  
* Padrão de [índice secundário inter-divisória](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores de `RowKey` em divisórias separadas ou em tabelas separadas. Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  
* [Padrão de entidades](#index-entities-pattern)indexadas : Manter entidades indexadas para permitir pesquisas eficientes que devolução de listas de entidades.  

### <a name="sort-data-in-table-storage"></a>Ordenar dados no armazenamento de tabelas

O armazenamento de tabelas devolve os resultados da consulta ordenadas em ordem ascendente, com base em `PartitionKey` e depois por `RowKey`.

> [!NOTE]
> Os resultados da consulta devolvidos pela API da Tabela Azure em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de características, consulte [diferenças entre a Tabela API no armazenamento de Azure Cosmos DB e Azure Table.](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)

As chaves no armazenamento da mesa são valores de cordas. Para garantir que os valores numéricos se classificam corretamente, deve convertê-los num comprimento fixo e remar com zeros. Por exemplo, se o valor de identificação do empregado que usa como `RowKey` é um valor inteiro, deve converter o ID **123** do empregado para **00000123**. 

Muitos aplicativos têm requisitos para utilizar dados ordenados em ordens diferentes: por exemplo, classificação funcionários por nome, ou ao associar data. Os seguintes padrões na secção Padrões de design de [tabelas](#table-design-patterns) abordam como alternar ordens de classificação para as suas entidades:  

* [Padrão de índice secundário intra-partição](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores `RowKey` (na mesma divisória). Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  
* Padrão de [índice secundário inter-divisória](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores de `RowKey` em divisórias separadas em tabelas separadas. Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.
* [Padrão de cauda](#log-tail-pattern)de log : Recuperar as entidades *n* mais recentemente adicionadas a uma partição, utilizando um valor `RowKey` que se classifica na data e ordem de tempo inversa.  

## <a name="design-for-data-modification"></a>Design da modificação de dados
Esta secção concentra-se em considerações de design para otimizar as inserções, atualizações e eliminações. Em alguns casos, terá de avaliar a compensação entre desenhos que otimizam a consulta contra designs que otimizam a modificação de dados. Esta avaliação é semelhante à que se faz nos desenhos para bases de dados relacionais (embora as técnicas de gestão das compensações de design sejam diferentes numa base de dados relacional). Os [padrões](#table-design-patterns) de design de tabela da secção descrevem alguns padrões de design detalhados para armazenamento de mesa, e destaca algumas destas trocas. Na prática, você vai descobrir que muitos projetos otimizados para consultas entidades também funcionam bem para modificar entidades.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de inserção, atualização e eliminação de operações
Para atualizar ou eliminar uma entidade, deve ser capaz de identificá-la utilizando os valores `PartitionKey` e `RowKey`. A este respeito, a sua escolha de `PartitionKey` e `RowKey` para modificar entidades deve seguir critérios semelhantes à sua escolha para apoiar consultas de pontos. Quer identificar as entidades da forma mais eficiente possível. Não pretende utilizar uma partilha ou digitalização de mesa ineficiente para localizar uma entidade de forma a descobrir os valores `PartitionKey` e `RowKey` que precisa para atualizá-lo ou apagá-lo.  

Os seguintes padrões no endereço de design de [tabela](#table-design-patterns) seletiva otimizam o desempenho da sua inserção, atualização e eliminação de operações:  

* Padrão de [eliminação](#high-volume-delete-pattern)de alto volume : Permitir a eliminação de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea na sua própria tabela separada. Elimina as entidades eliminando a tabela.  
* [Padrão](#data-series-pattern)de série de dados : Armazenar séries completas de dados numa única entidade para minimizar o número de pedidos que efaz.  
* [Padrão de entidades amplas](#wide-entities-pattern): Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de grandes entidades:](#large-entities-pattern)Use o armazenamento de blob para armazenar grandes valores de propriedade.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Garantir a consistência nas suas entidades armazenadas
O fator principal que influencia à sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência com transações atômicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma partição.  

Os seguintes padrões na secção Padrões de design de [tabelas](#table-design-patterns) abordam a consistência da gestão:  

* [Padrão de índice secundário intra-partição](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores `RowKey` (na mesma divisória). Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  
* Padrão de [índice secundário inter-divisória](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando diferentes valores de `RowKey` em divisórias separadas ou em tabelas separadas. Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern): Ativar eventualmente um comportamento consistente através dos limites da divisória ou dos limites do sistema de armazenamento usando as filas Azure.
* [Padrão de entidades](#index-entities-pattern)indexadas : Manter entidades indexadas para permitir pesquisas eficientes que devolução de listas de entidades.  
* Padrão de [desnormalização](#denormalization-pattern): Combine dados relacionados numa única entidade, para que possa recuperar todos os dados necessários com uma consulta de ponto único.  
* [Padrão](#data-series-pattern)de série de dados : Armazenar séries completas de dados numa única entidade, para minimizar o número de pedidos que efaz.  

Para mais informações, consulte [as transações](#entity-group-transactions) do grupo Entity mais tarde neste artigo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir o seu design para modificações eficientes facilita consultas eficientes
Em muitos casos, uma estrutura para os resultados da consultas eficientes modificações eficientes, mas sempre deve avaliar se for este o caso para o seu cenário específico. Alguns dos padrões da secção Padrões de design de [tabela avaliam](#table-design-patterns) explicitamente as trocas entre consultas e entidades modificantes, e deve sempre ter em conta o número de cada tipo de operação.  

Os seguintes padrões na secção Padrões de design de [tabelas](#table-design-patterns) abordam as compensações entre a conceção de consultas eficientes e a conceção de uma modificação eficiente dos dados:  

* [Padrão de chave composta:](#compound-key-pattern)Utilize valores compostos `RowKey` para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  
* [Padrão de cauda](#log-tail-pattern)de log : Recuperar as entidades *n* mais recentemente adicionadas a uma partição, utilizando um valor `RowKey` que se classifica na data e ordem de tempo inversa.  

## <a name="encrypt-table-data"></a>Encriptar dados de tabela
A biblioteca de clientes .NET Azure Storage suporta encriptação de propriedades de entidades de cadeia para inserir e substituir operações. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta às cordas após a desencriptação.    

Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Ou especifica um atributo `EncryptProperty` (para entidades POCO que derivam de `TableEntity`), ou especifica um resolver de encriptação nas opções de pedido. Um resolver de encriptação é um delegado que pega uma chave de partição, chave de linha e nome de propriedade, e devolve um Boolean que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usa esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve ao fio. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar propriedade A; de outra forma encriptar propriedades A e B.) Não é necessário fornecer esta informação enquanto lê ou consulta entidades.

A fusão não é apoiada atualmente. Como um subconjunto de propriedades poderia ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resultará na perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente a partir do serviço, ou usar uma nova chave por imóvel. Nenhum destes são adequados por razões de desempenho.     

Para obter informações sobre encriptar dados de tabelas, consulte [encriptação do lado do cliente e cofre de chaves Azure para o Armazenamento Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relacionamentos modelo
A criação de modelos de domínio é uma etapa importante no design de sistemas complexos. Normalmente, utiliza-se o processo de modelação para identificar entidades e as relações entre elas, como forma de compreender o domínio do negócio e informar o design do seu sistema. Esta secção centra-se na forma como pode traduzir alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para desenhos para armazenamento de mesa. O processo de mapeamento de um modelo de dados lógico para um modelo de dados físico baseado no NoSQL é diferente do utilizado na conceção de uma base de dados relacional. O design de bases de dados relacionais normalmente assume um processo de normalização de dados otimizado para minimizar o despedimento. Tal design também assume uma capacidade de consulta declarativa que abstrae a implementação de como a base de dados funciona.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Existem várias formas de implementar relações de um a muitas em armazenamento de mesa, cada uma com prós e contras que podem ser relevantes para o cenário particular.  

Considere o exemplo de uma grande multinacional com dezenas de milhares de departamentos e entidades de funcionários. Cada departamento tem muitos empregados e cada empregado está associado a um departamento específico. Uma abordagem é armazenar entidades separadas de departamento e empregados, tais como:  

![Gráfico mostrando uma entidade de departamento e uma entidade colaboradora][1]

Este exemplo mostra uma relação implícita entre os tipos, com base no valor `PartitionKey`. Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e respetivas entidades de funcionários relacionados na mesma partição. Pode optar por utilizar diferentes divisórias, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidades.  

Uma abordagem alternativa é desnormalizar os seus dados e armazenar apenas entidades de empregados com dados dedepartamentodesdes, como mostra o exemplo seguinte. Neste cenário em particular, esta abordagem desnormalizada pode não ser a melhor se tiver a obrigação de poder alterar os detalhes de um gestor de departamento. Para isso, terias de atualizar todos os empregados do departamento.  

![Gráfico da entidade colaboradora][2]

Para mais informações, consulte o [padrão de Desnormalização](#denormalization-pattern) mais tarde neste guia.  

A tabela que se segue resume os prós e os contras de cada uma das abordagens para armazenar entidades de funcionários e departamentos que têm uma relação de um a muitos. Deve também considerar a frequência com que espera realizar várias operações. Pode ser aceitável ter um desenho que inclua uma operação dispendiosa se essa operação acontecer apenas com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Profissionais de TI</th>
<th>Contras</th>
</tr>
<tr>
<td>Tipos de entidade separada, a mesma partição, a mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Pode usar um EGT para manter a consistência, se tiver um requisito para modificar uma entidade de departamento sempre que atualizar/inserir/eliminar uma entidade employee. Por exemplo, se mantém uma contagem de funcionários departamentais para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez precise de recuperar um empregado e uma entidade do departamento para algumas atividades de clientes.</li>
<li>Operações de armazenamento acontecem na mesma partição. Em volumes de transações elevados, isto pode resultar num hotspot.</li>
<li>Não se pode transferir um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separada, partições diferentes, ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em volumes de transações elevados, isto pode ajudar a espalhar a carga por mais divisórias.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez precise de recuperar um empregado e uma entidade do departamento para algumas atividades de clientes.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualiza/insere/elimina um funcionário e atualize um departamento. Por exemplo, a atualizar um número de funcionários numa entidade de departamento.</li>
<li>Não se pode transferir um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar para o único tipo de entidade</td>
<td>
<ul>
<li>Pode recuperar todas as informações de que precisa com uma única solicitação.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser dispendioso manter a consistência se precisar de atualizar informações do departamento (isto exigiria que atualizasse todos os colaboradores de um departamento).</li>
</ul>
</td>
</tr>
</table>

A forma como escolhe entre estas opções, e qual dos prós e contras são mais significativos, depende dos seus cenários específicos de candidatura. Por exemplo, com que frequência modifica as entidades do departamento? Todas as suas consultas de empregados precisam de informações adicionais do departamento? Qual a sua proximidade com os limites de escalabilidade das suas divisórias ou da sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um para um
Os modelos de domínio podem incluir relações um-a-um entre entidades. Se precisa implementar uma relação um-para-um no armazenamento de mesa, também deve escolher como ligar as duas entidades relacionadas quando precisa de recuperar as duas. Este link pode ser implícito, com base numa convenção nos valores-chave, ou explícito, armazenando um link sob a forma de `PartitionKey` e `RowKey` valores em cada entidade à sua entidade coligada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma partilha, consulte a secção [Relações One-a-many](#one-to-many-relationships).  

Existem também considerações de implementação que podem levá-lo a implementar relações um-para-um no armazenamento de tabelas:  

* Manuseamento de grandes entidades (para mais informações, consulte [o padrão de grandes entidades).](#large-entities-pattern)  
* Implementação de controlos de acesso (para mais informações, consulte [o acesso ao Controlo com assinaturas de acesso partilhado).](#control-access-with-shared-access-signatures)  

### <a name="join-in-the-client"></a>Junte-se no cliente
Embora existam formas de modelar relações no armazenamento de mesa, não se esqueça que as duas principais razões para usar o armazenamento de mesa são a escalabilidade e desempenho. Se descobrir que está a modelar muitas relações que comprometem o desempenho e a escalabilidade da sua solução, deve perguntar-se se é necessário construir todas as relações de dados no design da sua tabela. Poderá simplificar o design e melhorar a escalabilidade e desempenho da sua solução, caso deixe a sua aplicação de cliente realizar as juntas necessárias.  

Por exemplo, se tiver pequenas tabelas que contenham dados que não mudam frequentemente, pode recuperar estes dados uma vez, e cache-lo no cliente. Isto pode evitar idas e voltas repetidas para obter os mesmos dados. Nos exemplos que temos olhado neste guia, o conjunto de departamentos de uma pequena organização é provável que seja pequeno e mude pouco frequentemente. Isto faz com que seja um bom candidato para dados que uma aplicação de cliente pode descarregar uma vez e cache como dados de procura.  

### <a name="inheritance-relationships"></a>Relações de herança
Se a sua aplicação de cliente utilizar um conjunto de classes que fazem parte de uma relação de herança para representar entidades empresariais, pode facilmente persistir essas entidades no armazenamento de Mesa. Por exemplo, pode ter o seguinte conjunto de aulas definidas na sua aplicação de cliente, onde `Person` é uma classe abstrata.

![Diagrama de relações de herança][3]

Pode persistir casos das duas classes concretas no armazenamento de mesa utilizando uma única mesa `Person`. Utilize entidades que se pareçam com as seguintes:  

![Gráfico mostrando entidade cliente e entidade colaboradora][4]

Para obter mais informações sobre o trabalho com vários tipos de entidades na mesma tabela no código do cliente, consulte Trabalhar com tipos de [entidades heterogéneas](#work-with-heterogeneous-entity-types) mais tarde neste guia. Esta opção fornece exemplos de como a reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design da tabela
Em secções anteriores, aprendeu a otimizar o design da sua tabela tanto para recuperar dados da entidade, utilizando consultas, como para inserir, atualizar e apagar dados da entidade. Esta secção descreve alguns padrões adequados para uso com armazenamento de mesa. Além disso, você verá como você pode praticamente abordar algumas das questões e compensações levantadas anteriormente neste guia. O diagrama seguinte resume as relações entre os diferentes padrões:  

![Diagrama de padrões de design de mesa][5]

O mapa de padrões destaca algumas relações entre padrões (azul) e anti-padrões (laranja) que são documentados neste guia. Claro que existem muitos outros padrões que vale a pena examinar. Por exemplo, um dos cenários-chave para o armazenamento de mesa é usar o padrão de [visão materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) a partir do padrão de segregação de responsabilidade de consulta de [comando.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de partição dentro de índice secundário
Armazenar várias cópias de cada entidade utilizando diferentes valores de `RowKey` (na mesma divisória). Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`. As atualizações entre cópias podem ser mantidas consistentes utilizando EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os valores `PartitionKey` e `RowKey`. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação de cliente pode usar uma consulta pontual para recuperar uma entidade de empregado individual utilizando o nome do departamento e o ID do empregado (os valores `PartitionKey` e `RowKey`). Um cliente também pode recuperar entidades ordenadas por identificação de empregado dentro de cada departamento.

![Gráfico da entidade colaboradora][6]

Se também quiser encontrar uma entidade colaboradora com base no valor de outra propriedade, como o endereço de e-mail, deve utilizar uma verificação de divisórias menos eficiente para encontrar uma correspondência. Isto porque o armazenamento de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de colaboradores classificados numa ordem diferente da ordem `RowKey`.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade, com cada cópia utilizando um valor `RowKey` diferente. Se armazenar uma entidade com as seguintes estruturas, pode recuperar eficientemente as entidades dos colaboradores com base no endereço de e-mail ou na identificação do empregado. Os valores prefixos para `RowKey`, `empid_`, e `email_` permitem-lhe consultar um único colaborador, ou um leque de colaboradores, utilizando uma série de endereços de e-mail ou iDs de empregados.  

![Gráfico mostrando entidade colaboradora com valores rowKey variados][7]

Os dois critérios de filtro seguintes (um olhando para cima por id do empregado, e um olhando para cima por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter = (PartitionKey eq "Vendas") e (RowKey eq "empid_000223")  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se consultar uma série de entidades de empregados, pode especificar uma gama ordenada na ordem de identificação do empregado, ou um intervalo classificado na ordem de endereço de e-mail. Consulta para entidades com prefixo adequado no `RowKey`.  

* Para encontrar todos os colaboradores do departamento de Vendas com identificação de empregado na gama 000100 a 000199, use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail a começar pela letra "a", use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
A sintaxe do filtro utilizada nos exemplos anteriores é da API de armazenamento de mesa REST API. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de mesas é relativamente barato de usar, pelo que o custo de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, deverá sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento antecipados, e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação de cliente irá executar.  
* Como as entidades do índice secundário são armazenadas na mesma divisória que as entidades originais, certifique-se de que não excede os objetivos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si utilizando EGTs para atualizar as duas cópias da entidade de atomicamente. Isso implica que deve armazenar todas as cópias de uma entidade na mesma partição. Para mais informações, consulte as transações do [grupo Use.](#entity-group-transactions)  
* O valor utilizado para o `RowKey` deve ser único para cada entidade. Considere a utilização de valores da chave compostas.  
* Acolchoamento de valores numéricos na `RowKey` (por exemplo, o ID do empregado 000223) permite a triagem e filtragem corretas com base nos limites superiores e inferiores.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades utilizando o endereço de e-mail na `RowKey` nunca precisarem da idade do trabalhador, estas entidades podem ter a seguinte estrutura:

  ![Gráfico da entidade colaboradora][8]

* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para procurar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- A sua aplicação de cliente precisa de recuperar entidades utilizando uma variedade de chaves diferentes.
- O seu cliente precisa de recuperar entidades em ordens de tipo diferentes.
- Pode identificar cada entidade utilizando uma variedade de valores únicos.

No entanto, certifique-se de que não excede os limites de escalabilidade da divisória quando está a realizar procuras de entidades utilizando os diferentes valores `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de índice secundário inter-divisória](#inter-partition-secondary-index-pattern)
* [Padrão de chave composto](#compound-key-pattern)
* [Transações de grupos de entidades](#entity-group-transactions)
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice de partição entre secundário
Armazenar várias cópias de cada entidade utilizando diferentes valores de `RowKey` em divisórias separadas ou em tabelas separadas. Isto permite procurações rápidas e eficientes, e alternaordens de classificação utilizando diferentes valores `RowKey`.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os valores `PartitionKey` e `RowKey`. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação de cliente pode usar uma consulta pontual para recuperar uma entidade de empregado individual utilizando o nome do departamento e o ID do empregado (os valores `PartitionKey` e `RowKey`). Um cliente também pode recuperar entidades ordenadas por identificação de empregado dentro de cada departamento.  

![Gráfico da entidade colaboradora][9]

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto porque o armazenamento de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de colaboradores classificados numa ordem diferente da ordem `RowKey`.  

Está a antecipar um grande volume de transações contra estas entidades e quer minimizar o risco de a taxa de armazenamento da Tabela limitar o seu cliente.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade, com cada cópia utilizando diferentes `PartitionKey` e `RowKey` valores. Se armazenar uma entidade com as seguintes estruturas, pode recuperar eficientemente as entidades dos colaboradores com base no endereço de e-mail ou na identificação do empregado. Os valores prefixos para `PartitionKey`, `empid_`, e `email_` permitem identificar que índice pretende utilizar para uma consulta.  

![Entidade de colaboradores com índice primário e entidade de empregados com índice secundário][10]

Os dois critérios de filtro seguintes (um olhando para cima por id do empregado, e um olhando para cima por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter = (PartitionKey eq ' empid_Sales") e (RowKey eq"000223")
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se consultar uma série de entidades de empregados, pode especificar uma gama ordenada na ordem de identificação do empregado, ou um intervalo classificado na ordem de endereço de e-mail. Consulta para entidades com prefixo adequado no `RowKey`.  

* Para encontrar todos os colaboradores do departamento de Vendas com identificação de empregado na gama **000100** a **000199**, ordenado por ordem de identificação dos funcionários, use: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail que comece com "a", classificado na ordem de endereço de e-mail, use: $filter=(PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

Note que a sintaxe do filtro utilizada nos exemplos anteriores é da Mesa de armazenamento REST API. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistentes entre si, utilizando o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de mesas é relativamente barato de utilizar, pelo que o custo de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, avalie sempre o custo do seu design com base nos seus requisitos de armazenamento antecipados, e adicione apenas entidades duplicadas para suportar as consultas que a sua aplicação de cliente irá executar.  
* O valor utilizado para o `RowKey` deve ser único para cada entidade. Considere a utilização de valores da chave compostas.  
* Acolchoamento de valores numéricos na `RowKey` (por exemplo, o ID do empregado 000223) permite a triagem e filtragem corretas com base nos limites superiores e inferiores.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades utilizando o endereço de e-mail na `RowKey` nunca precisarem da idade do trabalhador, estas entidades podem ter a seguinte estrutura:
  
  ![Gráfico mostrando entidade colaboradora com índice secundário][11]
* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para procurar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- A sua aplicação de cliente precisa de recuperar entidades utilizando uma variedade de chaves diferentes.
- O seu cliente precisa de recuperar entidades em ordens de tipo diferentes.
- Pode identificar cada entidade utilizando uma variedade de valores únicos.

Utilize este padrão quando pretender evitar exceder os limites de escalabilidade da divisória quando estiver a realizar as procurações de entidades utilizando os diferentes valores `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intra-partição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composto](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ative o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs ativar transações atómicas em múltiplas entidades que partilham a mesma chave de partição. Por razões de desempenho e escalabilidade, poderá decidir armazenar entidades que tenham requisitos de consistência em divisórias separadas ou num sistema de armazenamento separado. Neste cenário, não se pode usar EGTs para manter a consistência. Por exemplo, pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, nas tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada em armazenamento de mesa e uma bolha armazenada no armazenamento Blob.  
* Uma entidade armazenada no armazenamento de mesa e um ficheiro num sistema de ficheiros.  
* Uma entidade armazenada no armazenamento de mesa, ainda indexada pela utilização de Pesquisa Cognitiva Azure.  

#### <a name="solution"></a>Solução
Ao utilizar as filas do Azure, pode implementar uma solução que fornece a consistência eventual entre dois ou mais partições ou sistemas de armazenamento.

Para ilustrar esta abordagem, assuma que tem a obrigação de poder arquivar antigas entidades de funcionários. As entidades antigas colaboradoras raramente são questionadas, devendo ser excluídas de quaisquer atividades que tratem com os atuais colaboradores. Para implementar este requisito, armazena colaboradores ativos na tabela **Current** e antigos colaboradores na tabela **Arquivo.** Arquivar um empregado requer que você elimine a entidade da tabela **Current,** e adicione a entidade à tabela **Arquivo.**

Mas não pode usar um EGT para realizar estas duas operações. Para evitar o risco de uma falha faz com que uma entidade a aparecer nas tabelas de ambos ou nenhum, a operação de arquivo tem de ser eventualmente consistente. O diagrama de sequência seguinte descreve os passos nesta operação.  

![Diagrama da solução para manter a consistência eventual][12]

Um cliente inicia a operação de arquivo colocando uma mensagem numa fila Azure (neste exemplo, para arquivar #456 dos funcionários). Uma função de trabalho consulta a fila para novas mensagens; Quando encontrar uma, ele lê a mensagem e mantém uma cópia oculta na fila. O papel do trabalhador em seguida requer uma cópia da entidade da tabela **Atual,** insere uma cópia na tabela **Arquivo** e, em seguida, elimina o original da tabela **Atual.** Por fim, se não houver nenhum erro dos passos anteriores, a função de trabalho elimina a mensagem oculta da fila.  

Neste exemplo, o passo 4 do diagrama insere o empregado na tabela **Archive.** Pode adicionar o empregado a uma bolha no armazenamento blob ou a um ficheiro num sistema de ficheiros.  

#### <a name="recover-from-failures"></a>Recuperar de falhas
É importante que as operações nos passos 4-5 no diagrama sejam *idempotentes* no caso de o papel do trabalhador precisar de reiniciar a operação de arquivo. Se estiver a utilizar o armazenamento de mesa, para o passo 4 deve utilizar uma operação de "inserir ou substituir"; para o passo 5, deve utilizar uma operação de "apagar se existir" na biblioteca de clientes que está a usar. Se estiver a utilizar outro sistema de armazenamento, deve utilizar uma operação idempotente adequada.  

Se a função do trabalhador nunca completar o passo 6 no diagrama, então, após um intervalo, a mensagem reaparece na fila pronta para o papel do trabalhador tentar reprocessá-la. O papel do trabalhador pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "venenosa" para investigação enviando-a para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificação da contagem de filas, consulte [Obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros do armazenamento de mesa e armazenamento de fila são erros transitórios, e a sua aplicação do cliente deve incluir uma lógica de retry adequada para lidar com eles.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não prevê o isolamento de transações. Por exemplo, um cliente poderia ler as tabelas **Current** and **Archive** quando o papel do trabalhador estava entre os passos 4-5 no diagrama, e ver uma visão inconsistente dos dados. Eventualmente, os dados serão consistentes.  
* Deve ter a certeza de que os passos 4-5 são idempotentes para garantir uma eventual consistência.  
* Pode dimensionar a solução ao utilizar várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja garantir a consistência eventual entre entidades que existem em partições diferentes ou tabelas. Pode estender este padrão para garantir uma eventual consistência para operações através do armazenamento de mesa e armazenamento blob, e outras fontes de dados de armazenamento não-Azure, como uma base de dados ou o sistema de ficheiros.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Fundir ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transações for importante para a sua solução, considere redesenhar as suas tabelas para permitir que utilize EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades indexadas
Manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os valores `PartitionKey` e `RowKey`. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente, utilizando uma consulta pontual. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação cliente pode recuperar eficientemente uma entidade de empregados individuais utilizando o nome do departamento e o ID do empregado (o `PartitionKey` e o `RowKey`).  

![Gráfico da entidade colaboradora][13]

Se também pretende recuperar uma lista de entidades colaboradoras com base no valor de outra propriedade não única, como o apelido, deve utilizar uma verificação de partição menos eficiente. Esta varredura encontra fósforos, em vez de usar um índice para os procurar diretamente. Isto porque o armazenamento de mesa não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para permitir a procura por último nome com a estrutura da entidade anterior, deve manter listas de IDs dos empregados. Se quiser recuperar as entidades colaboradoras com um nome em particular, como o Jones, tem primeiro de localizar a lista de identificações dos empregados para funcionários com o Jones como apelido e, em seguida, recuperar essas entidades de empregados. Existem três opções principais para armazenar as listas de IDs dos empregados:  

* Use o armazenamento blob.  
* Crie entidades de índice na mesma partição que as entidades de funcionários.  
* Crie entidades de índice numa partição separada ou a tabela.  

Opção 1: Utilizar o armazenamento blob  

Crie uma bolha para cada apelido único, e em cada blob armazenar uma lista dos valores `PartitionKey` (departamento) e `RowKey` (ID dos empregados) para os colaboradores que têm esse apelido. Ao adicionar ou excluir um empregado, certifique-se de que o conteúdo da bolha relevante é eventualmente consistente com as entidades colaboradoras.  

Opção 2: Criar entidades indexadas na mesma partilha  

Utilize entidades indexadas que armazenem os seguintes dados:  

![Entidade de funcionário sintetista gráfico, com cadeia contendo uma lista de IDs de empregados com o mesmo apelido][14]

A propriedade `EmployeeIDs` contém uma lista de identificações de empregados para empregados com o apelido armazenado no `RowKey`.  

Os seguintes passos delineiam o processo que deve seguir quando estiver a adicionar um novo empregado. Neste exemplo, estamos adicionando um empregado com ID 000152 e sobrenome Jones no departamento de vendas:  

1. Recupere a entidade do índice com um valor `PartitionKey` "Vendas", e o valor `RowKey` "Jones". Guarde a ETag dessa entidade para utilizar no passo 2.  
2. Criar uma operação de grupo de entidades (isto é, uma operação de lote) que insere a nova entidade colaboradora (`PartitionKey` valor "Vendas" e `RowKey` valor "000152"), e atualiza a entidade indexada (`PartitionKey` valor "Vendas" e `RowKey` valor "Jones"). O EGT fá-lo adicionando o novo ID do empregado à lista no campo dos Funcionários. Para mais informações sobre EGTs, consulte [as transações do grupo Entidade.](#entity-group-transactions)  
3. Se o EGT falhar devido a um erro de condivisa otimista (isto é, outra pessoa modificou a entidade indexada), então precisa recomeçar no passo 1.  

Pode utilizar uma abordagem semelhante para apagar um empregado se estiver a usar a segunda opção. Mudar o apelido de um colaborador é um pouco mais complexo, porque é preciso executar um EGT que atualiza três entidades: a entidade colaboradora, a entidade indexada ao antigo apelido, e a entidade indexada para o novo apelido. Deve recuperar cada entidade antes de efetuar quaisquer alterações, de forma a recuperar os valores Do ETag que pode utilizar para realizar as atualizações utilizando uma moeda otimista.  

Os seguintes passos delineiam o processo que deve seguir quando precisa de procurar todos os colaboradores com um nome especial num departamento. Neste exemplo, estamos procurando todos os empregados com o sobrenome Jones no departamento de vendas:  

1. Recupere a entidade do índice com um valor `PartitionKey` "Vendas", e o valor `RowKey` "Jones".  
2. Analise a lista de identificações de empregados no campo `EmployeeIDs`.  
3. Se necessitar de informações adicionais sobre cada um destes colaboradores (como os seus endereços de e-mail), recupere cada uma das entidades colaboradoras utilizando `PartitionKey` valor "Vendas", e `RowKey` valores da lista de colaboradores que obteve no passo 2.  

Opção 3: Criar entidades indexadas numa divisória ou tabela separada  

Para esta opção, utilize entidades indexadas que armazenem os seguintes dados:  

![Entidade de funcionário sintetista gráfico, com cadeia contendo uma lista de IDs de empregados com o mesmo apelido][15]

A propriedade `EmployeeIDs` contém uma lista de identificações de empregados para empregados com o apelido armazenado no `RowKey`.  

Não é possível utilizar EGTs para manter a consistência, porque as entidades indexadas estão numa divisão separada das entidades colaboradoras. Certifique-se de que as entidades indexadas são eventualmente consistentes com as entidades colaboradoras.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades indexadas para obter a lista de valores `RowKey`, e depois consultas para recuperar cada entidade na lista.  
* Como uma entidade individual tem um tamanho máximo de 1 MB, a opção 2 e a opção 3 na solução assumem que a lista de IDs de empregados para qualquer apelido em particular nunca é superior a 1 MB. Se a lista de IDs dos empregados for suscetível de ter mais de 1 MB de tamanho, utilize a opção 1 e guarde os dados do índice no armazenamento blob.  
* Se utilizar a opção 2 (utilizando EGTs para lidar com a adição e a pagando colaboradores e alterando o apelido de um empregado), deve avaliar se o volume de transações se aproximará dos limites de escalabilidade numa determinada partição. Se for esse o caso, deverá considerar uma solução eventualmente consistente (opção 1 ou opção 3). Estes utilizam filas para lidar com os pedidos de atualização e permitem-lhe armazenar as suas entidades indexadas numa partilha separada das entidades colaboradoras.  
* A opção 2 nesta solução pressupõe que pretende procurar pelo último nome dentro de um departamento. Por exemplo, quer recuperar uma lista de empregados com um sobrenome Jones no departamento de vendas. Se quiser procurar todos os empregados com um apelido Jones em toda a organização, use a opção 1 ou a opção 3.
* Pode implementar uma solução baseada na fila que proporciona uma eventual consistência. Para mais detalhes, consulte o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando quiser procurar um conjunto de entidades que todos partilham um valor de propriedade comum, como todos os colaboradores com o apelido Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente normalmente normaliza-se os dados para remover a duplicação que ocorre quando as consultas recuperam dados de várias tabelas. Se normalizar os dados nas tabelas do Azure, tem de certificar vários ida e volta do cliente para o servidor para obter os dados relacionados. Por exemplo, com a seguinte estrutura de mesa, você precisa de duas viagens de ida e volta para recuperar os detalhes para um departamento. Uma viagem vai buscar a entidade do departamento que inclui o ID do gestor, e a segunda viagem traz os detalhes do gestor numa entidade de empregados.  

![Gráfico da entidade do departamento e entidade colaboradora][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade do departamento. Por exemplo:  

![Gráfico de entidade dedepartamento desnormalizada e combinada][17]

Com entidades de departamento armazenadas com estas propriedades, agora pode recuperar todos os detalhes que precisa sobre um departamento usando uma consulta pontual.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Existe algum custo de sobrecarga associado ao armazenamento alguns dados duas vezes. O benefício de desempenho resultante de menos pedidos de armazenamento de mesa normalmente supera o aumento marginal dos custos de armazenamento. Além disso, este custo é parcialmente compensado por uma redução do número de transações que você precisa para obter os detalhes de um departamento.  
* Tem de manter a consistência das duas entidades que armazenam informações sobre os gestores. Pode lidar com a questão da consistência utilizando EGTs para atualizar várias entidades numa única transação atómica. Neste caso, a entidade do departamento e a entidade colaboradora do gestor do departamento são armazenadas na mesma divisão.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa com frequência procurar informações relacionadas. Este padrão reduz o número de consultas de que seu cliente tem de efetuar para recuperar os dados que ela requer.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Utilize valores compostos `RowKey` para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é natural que se utilizem juntas em consultas para devolver peças de dados relacionadas ao cliente numa única consulta. Por exemplo, pode utilizar o ID do empregado para procurar uma lista de entidades relacionadas que contenham dados de desempenho e revisão para esse funcionário.  

Assuma que está a armazenar entidades de empregados no armazenamento de mesa utilizando a seguinte estrutura:  

![Gráfico da entidade colaboradora][18]

Também precisa de armazenar dados históricos relativos a avaliações e desempenho para cada ano que o colaborador tem trabalhado para a sua organização, e precisa de ter acesso a essa informação por ano. Uma opção é criar outra tabela que armazena as entidades com a seguinte estrutura:  

![Gráfico da entidade de revisão de funcionários][19]

Com esta abordagem, poderá decidir duplicar algumas informações (como o primeiro nome e apelido) na nova entidade, para lhe permitir recuperar os seus dados com um único pedido. No entanto, não pode manter uma forte consistência porque não pode usar um EGT para atualizar atomicamente as duas entidades.  

#### <a name="solution"></a>Solução
Guarde um novo tipo de entidade na sua tabela original utilizando entidades com a seguinte estrutura:  

![Gráfico da entidade do empregado com chave composta][20]

Repare como o `RowKey` é agora uma chave composta, composta pela identificação do empregado e pelo ano dos dados de revisão. Isto permite-lhe recuperar o desempenho do colaborador e rever os dados com um único pedido para uma única entidade.  

O exemplo a seguir descreve como recuperar todos os dados de revisão de um funcionário específico (por exemplo, o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000123") e (RowKey lt 'empid_000124') & $select = RowKey, Gestor de classificação, a classificação de ponto a ponto, comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um carácter separador adequado que facilite a análise do valor `RowKey`: por exemplo, **000123_2012**.  
* Também está a armazenar esta entidade na mesma partilha que outras entidades que contêm dados relacionados para o mesmo colaborador. Isto significa que pode usar EGTs para manter uma forte consistência.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é apropriado. Por exemplo, se aceder frequentemente aos dados de revisão, e os principais dados dos colaboradores, deve mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver de armazenar um ou mais entidades relacionadas que consulta frequentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de cauda do registo
Recupere as entidades *n* mais recentemente adicionadas a uma partição usando um valor `RowKey` que classifica na data e ordem de tempo inversa.  

> [!NOTE]
> Os resultados da consulta devolvidos pela API da Tabela Azure em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Assim, embora este padrão seja adequado para armazenamento de mesa, não é adequado para Azure Cosmos DB. Para obter uma lista detalhada das diferenças de funcionalidades, consulte [diferenças entre a Tabela API em Azure Cosmos DB e Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é conseguir obter as entidades recentemente criadas, por exemplo o mais recente dez afirmações enviadas por um funcionário de despesas. As consultas de mesa suportam uma operação de consulta `$top` para devolver as primeiras entidades *n* de um conjunto. Não há nenhuma operação de consulta equivalente para devolver as últimas entidades *num* conjunto.  

#### <a name="solution"></a>Solução
Armazenar as entidades utilizando uma `RowKey` que naturalmente separa em data/hora inversa, pelo que a entrada mais recente é sempre a primeira da tabela.  

Por exemplo, para poder recuperar as dez declarações de despesas mais recentes enviadas por um funcionário, pode utilizar um valor de escala inversa derivado de data/hora atual. A C# amostra de código que se segue mostra uma forma de criar um valor adequado de "carrapatos invertidos" para uma `RowKey` que se classifica do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar ao valor da data/hora utilizando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem o seguinte aspeto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve remar o valor do tique-taque inverso com os zeros principais, para garantir que o valor da cadeia classifica como esperado.  
* Deve estar ciente dos destinos de escalabilidade no nível de uma partição. Tenha cuidado para não criar divisórias de ponto sinuoso.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de aceder a entidades em data/hora inversa, ou quando necessitar de aceder às entidades mais recentes.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Prepend / apêndice anti-padrão](#prepend-append-anti-pattern)  
* [Recuperar entidades](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de volume elevado
Permitir a eliminação de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea na sua própria tabela separada. Elimina as entidades eliminando a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos eliminar dados antigos que já não tem de estar disponíveis para uma aplicação cliente ou que o aplicativo tem arquivados para outro meio de armazenamento. Normalmente identifica esses dados por uma data. Por exemplo, tem a obrigação de apagar registos de todos os pedidos de inscrição com mais de 60 dias de idade.  

Um dos desenhos possíveis é utilizar a data e a hora do pedido de inscrição no `RowKey`:  

![Gráfico da entidade de tentativa de login][21]

Esta abordagem evita os hotspots de partição, uma vez que a aplicação pode inserir e eliminar entidades de inscrição para cada utilizador numa divisória separada. No entanto, esta abordagem pode ser dispendiosa e morosa se tiver um grande número de entidades. Em primeiro lugar, é necessário efetuar uma digitalização de tabela para identificar todas as entidades a eliminar, e depois tem de apagar cada entidade antiga. Pode reduzir o número de ida e volta para o servidor necessário para eliminar as entidades antigas através da criação de batches vários pedidos delete para EGTs.  

#### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de início de sessão. Pode utilizar o design da entidade anterior para evitar hotspots quando estiver a inserir entidades. A apagar entidades antigas é agora simplesmente uma questão de apagar uma mesa todos os dias (uma única operação de armazenamento), em vez de encontrar e apagar centenas e milhares de entidades individuais de inscrição todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas de utilizar os dados, tais como procurar entidades específicas, ligar-se a outros dados ou gerar informação agregada?  
* O design evitar pontos ativos ao são inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de tabela depois excluí-lo. É melhor sempre usar nomes de tabela exclusivo.  
* Espere um pouco de limitação de tarifas quando utilizar pela primeira vez uma nova tabela, enquanto o armazenamento de mesa aprende os padrões de acesso e distribui as divisórias através dos nós. Deve considerar a frequência tem de criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tem um grande volume de entidades que tem de eliminar ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Modificar entidades](#modify-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é para uma aplicação armazenar uma série de dados que, normalmente, precisar de obter ao mesmo tempo. Por exemplo, seu aplicativo pode gravar o número de mensagens Instantâneas de mensagens, cada funcionário envia a cada hora e, em seguida, utilize estas informações para desenhar quantas mensagens cada usuário enviado através das 24 horas anteriores. Um design poderá estar a armazenar 24 entidades de cada funcionário:  

![Entidade gráfica de estatísticas de mensagens][22]

Com esta estrutura, pode facilmente localizar e atualizar a entidade a atualizar para cada funcionário, sempre que a aplicação tem de atualizar o valor de contagem de mensagens. No entanto, para obter as informações para desenhar um gráfico da atividade para as anteriores 24 horas, tem de obter 24 entidades.  

#### <a name="solution"></a>Solução
Utilize o seguinte design, com uma propriedade separada para armazenar a contagem de mensagens por cada hora:  

![Gráfico mostrando mensagem stats entidade com propriedades separadas][23]

Com esta estrutura, pode usar uma operação de intercalação para atualizar a contagem de mensagens para um funcionário durante uma hora específica. Agora, você pode recuperar toda a informação que precisa para traçar o gráfico usando um pedido para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se a sua série completa de dados não encaixar numa única entidade (uma entidade pode ter até 252 propriedades), utilize uma loja de dados alternativa, como uma bolha.  
* Se tiver vários clientes a atualizar uma entidade simultaneamente, utilize o **ETag** para implementar uma conmoeda otimista. Se tiver muitos clientes, poderá ter muita controvérsia.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa atualizar e obter uma série de dados associada a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Fundir ou substituir](#merge-or-replace)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern) (se estiver armazenando a série de dados em uma bolha)  

### <a name="wide-entities-pattern"></a>Padrão de entidades grande
Utilize várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode ter mais de 252 propriedades (excluindo as propriedades do sistema obrigatório), e não pode armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente, você trabalharia em torno de quaisquer limites no tamanho de uma linha adicionando uma nova tabela, e aplicando uma relação 1-a-1 entre eles.  

#### <a name="solution"></a>Solução
Ao utilizar o armazenamento de mesa, pode armazenar várias entidades para representar um único objeto de negócio com mais de 252 propriedades. Por exemplo, se pretender armazenar uma contagem do número de mensagens IM enviadas por cada colaborador nos últimos 365 dias, pode utilizar o seguinte design que utiliza duas entidades com diferentes esquemas:  

![Gráfico mostrando mensagem stats entidade com Rowkey 01 e entidade estatísticas de mensagens com Rowkey 02][24]

Se precisar de fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma operação de intercalação único para atualizar a contagem de mensagens para um dia específico. Para recuperar todos os dados de um funcionário individual, deve recuperar ambas as entidades. Pode fazê-lo com dois pedidos eficientes que utilizam um `PartitionKey` e um valor `RowKey`.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere o seguinte ponto ao decidir como implementar este padrão:  

* Obter uma entidade lógica concluída envolve a, pelo menos, duas transações de armazenamento: um para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho ou número de propriedades exceda os limites para uma entidade individual no armazenamento de mesa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Fundir ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Utilize o armazenamento Blob para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenarem valores que fazem com que o tamanho total da sua entidade exceda este valor, não pode armazenar toda a entidade no armazenamento de Mesa.  

#### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no armazenamento Blob e, em seguida, armazenar o endereço da bolha numa propriedade da entidade. Por exemplo, pode armazenar a foto de um empregado no armazenamento blob, e armazenar um link para a foto na propriedade `Photo` da sua entidade colaboradora:  

![Gráfico mostrando entidade colaboradora com corda para foto apontando para armazenamento Blob][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter uma eventual consistência entre a entidade no armazenamento de mesa e os dados no armazenamento blob, utilize o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as suas entidades.
* Obter uma entidade completa envolve a, pelo menos, duas transações de armazenamento: um para recuperar a entidade e outra para recuperar os dados de Blobs.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho exceda os limites para uma entidade individual no armazenamento de mesa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades amplas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão preceder/acrescentar
Quando tiver um grande volume de inserções, aumente a escalabilidade espalhando as inserções em várias divisórias.  

#### <a name="context-and-problem"></a>Contexto e problema
Prefixação ou acrescentando entidades para as suas entidades armazenadas normalmente resulta na aplicação a adicionar novas entidades na partição do primeiro ou último de uma sequência de partições. Neste caso, todas as inserções em qualquer momento em particular estão a ocorrer na mesma divisória, criando um hotspot. Isto impede que o armazenamento de tabelas se insira em vários nós e, possivelmente, faz com que a sua aplicação atinja os alvos de escalabilidade para a partilha. Por exemplo, considere o caso de uma aplicação que regista a rede e o acesso de recursos por parte dos colaboradores. Uma estrutura de entidade como a seguinte pode levar a que a partição da hora atual se torne um ponto de interesse, se o volume de transações atingir o objetivo de escalabilidade para uma partição individual:  

![Gráfico da entidade colaboradora][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição em particular, uma vez que a aplicação regista eventos:  

![Gráfico mostrando entidade colaboradora com RowKey composto o ano, mês, dia, hora e id do evento][27]

Note com este exemplo como tanto o `PartitionKey` como o `RowKey` são chaves compostas. O `PartitionKey` usa o departamento e a identificação do empregado para distribuir o registo através de várias divisórias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa evita a criação de partições muito ativas em inserções com eficiência suporta as consultas que a aplicação de cliente faz?  
* O seu volume antecipado de transações significa que é provável que atinja os alvos de escalabilidade para uma partição individual e seja estrangulado pelo armazenamento de mesa?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o anti-padrão de preparação/apêndice quando o seu volume de transações é suscetível de resultar na limitação da taxa pelo armazenamento de mesa quando aceder a uma divisória quente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificar entidades](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Padrão de anti de dados de registo
Normalmente, deve utilizar o armazenamento Blob em vez do armazenamento de mesa para armazenar dados de registo.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de utilização comum para dados de registo é recuperar uma seleção de entradas de registo para um intervalo de data/hora específico. Por exemplo, pretende encontrar todos os erros e mensagens críticas que a sua aplicação registou entre as 15:04 e as 15:06 numa data específica. Não pretende utilizar a data e a hora da mensagem de registo para determinar a partição a que guarda entidades de registo. Isto resulta numa partição quente porque, em qualquer momento, todas as entidades de registo partilharão o mesmo valor `PartitionKey` (ver [o anti-padrão Prepend/apêndice).](#prepend-append-anti-pattern) Por exemplo, o esquema da entidade seguinte para uma mensagem de log resulta numa partição quente, porque a aplicação escreve todas as mensagens de registo na partição para a data e hora atuais:  

![Gráfico da entidade de mensagens de log][28]

Neste exemplo, o `RowKey` inclui a data e a hora da mensagem de registo para garantir que as mensagens de registo são classificadas em data/hora. O `RowKey` também inclui um ID de mensagem, caso várias mensagens de registo partilhem a mesma data e hora.  

Outra abordagem é utilizar um `PartitionKey` que garanta que a aplicação escreva mensagens através de uma série de divisórias. Por exemplo, se a fonte da mensagem de registo fornecer uma forma de distribuir mensagens em muitas divisórias, pode utilizar o seguinte esquema de entidade:  

![Gráfico da entidade de mensagens de log][29]

No entanto, o problema com este esquema é que para recuperar todas as mensagens de registo por um período de tempo específico, você deve pesquisar todas as divisórias na tabela.

#### <a name="solution"></a>Solução
A secção anterior destacou o problema de tentar utilizar o armazenamento de mesa para armazenar entradas de registo, e sugeriu dois desenhos insatisfatórios. Uma solução levou a uma partição quente com o risco de escrever mensagens de registo de mau desempenho. A outra solução resultou num fraco desempenho de consulta, devido à exigência de digitalizar todas as divisórias da tabela para recuperar mensagens de registo durante um período de tempo específico. O armazenamento blob oferece uma solução melhor para este tipo de cenário, e é assim que a análise do Armazenamento Azure armazena os dados de registo que recolhe.  

Esta secção descreve como a análise de armazenamento armazena dados de log no armazenamento blob, como uma ilustração desta abordagem para armazenar dados que você normalmente consulta por alcance.  

A análise de armazenamento armazena mensagens de log num formato delimitado em várias bolhas. O formato delimitado torna mais fácil para um aplicativo de cliente analisar os dados na mensagem de registo.  

A análise de armazenamento utiliza uma convenção de nomeação para bolhas que lhe permite localizar a bolha (ou bolhas) que contêm as mensagens de registo para as quais está a pesquisar. Por exemplo, uma bolha chamada "queue/2014/07/31/1800/000001.log" contém mensagens de registo que se relacionam com o serviço de fila para a hora a partir das 18:00 do dia 31 de julho de 2014. "000001" indica que este é o primeiro ficheiro de registo para este período. A análise de armazenamento também regista os carimbos de tempo das primeiras e últimas mensagens de registo armazenadas no ficheiro, como parte dos metadados da bolha. A API para armazenamento Blob permite localizar bolhas num recipiente com base num prefixo de nome. Para localizar todas as bolhas que contenham dados de registo de fila para a hora a partir das 18:00, pode utilizar o prefixo "fila/2014/07/31/1800".  

Os tampões de análise de armazenamento registam mensagens internamente e, em seguida, atualiza periodicamente a bolha apropriada ou cria uma nova com o mais recente lote de entradas de registo. Isto reduz o número de escritos que deve executar para o armazenamento blob.  

Se estiver a implementar uma solução semelhante na sua própria aplicação, considere como gerir a compensação entre fiabilidade e custo e escalabilidade. Por outras palavras, avalie o efeito de escrever cada entrada de registo no armazenamento blob à medida que acontece, em comparação com atualizações de tampão na sua aplicação e escrevendo-as para o armazenamento blob em lotes.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de mesa que evite potenciais divisórias quentes, poderá descobrir que não consegue aceder aos seus dados de registo de forma eficiente.  
* Para processar dados de registo, um cliente, muitas vezes, precisa de carregar o número de registos.  
* Embora os dados de registo sejam muitas vezes estruturados, o armazenamento blob pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta secção descreve algumas das considerações a ter em mente ao implementar os padrões descritos nas secções anteriores. Grande parte dessa seção utiliza exemplos escritos em c# que utilizam a biblioteca de cliente de armazenamento (versão versão 4.3.0 no momento da escrita).  

### <a name="retrieve-entities"></a>Recuperar entidades
Como discutido na secção [Design para consulta,](#design-for-querying)a consulta mais eficiente é uma consulta pontual. No entanto, em alguns cenários poderá ser necessário recuperar várias entidades. Esta secção descreve algumas abordagens comuns para recuperar entidades utilizando a Biblioteca do Cliente de Armazenamento.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Executar uma consulta pontual usando a Biblioteca do Cliente de Armazenamento
A maneira mais fácil de fazer uma consulta pontual é usar a operação da tabela **Recuperar.** Como mostra o C# seguinte código, esta operação recupera uma entidade com um `PartitionKey` de valor "Vendas", e uma `RowKey` de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Note como este exemplo espera que a entidade que recupera seja de tipo `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperar várias entidades utilizando o LINQ
Pode recuperar várias entidades utilizando o LINQ com biblioteca de clientes de armazenamento e especificando uma consulta com uma cláusula **de local.** Para evitar uma digitalização de tabela, deve sempre incluir o valor `PartitionKey` na cláusula de sempre e, se possível, o valor `RowKey` para evitar digitalizações de tabela e partição. A armazenagem de mesa suporta um conjunto limitado de operadores de comparação (superior estoiro ou igual, inferior a, menos ou iguais, iguais e não iguais) para utilizar na cláusula em que. O C# seguinte código de snippet encontra todos os empregados cujo apelido começa com "B" (assumindo que o `RowKey` armazena o último nome) no departamento de Vendas (assumindo que o `PartitionKey` armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Note como a consulta especifica um `RowKey` e um `PartitionKey` para garantir um melhor desempenho.  

A amostra de código seguinte mostra uma funcionalidade equivalente utilizando a API fluente (para mais informações sobre APIs fluentes em geral, consulte [as melhores práticas para conceber uma API fluente):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

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
> A amostra nidifica vários métodos `CombineFilters` para incluir as três condições do filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperar um grande número de entidades a partir de uma consulta
Uma consulta ideal devolve uma entidade individual com base num valor `PartitionKey` e um valor `RowKey`. No entanto, em alguns cenários poderá ter a obrigação de devolver muitas entidades da mesma divisão, ou mesmo de muitas divisórias. Sempre totalmente, deve testar o desempenho da sua aplicação em tais cenários.  

Uma consulta contra o armazenamento de mesa pode devolver um máximo de 1.000 entidades de uma só vez, e correr por um máximo de cinco segundos. O armazenamento em mesa devolve um sinal de continuação para permitir ao pedido do cliente o próximo conjunto de entidades, se alguma das seguintes entidades for verdadeira:

- O conjunto de resultados contém mais de 1.000 entidades.
- A consulta não terminou em cinco segundos.
- A consulta cruza o limite da divisória. 

Para obter mais informações sobre como funcionam os tokens de continuação, consulte [o tempo de paragem e a paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx)da Consulta.  

Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, pode lidar automaticamente com fichas de continuação para si, uma vez que devolve entidades do armazenamento de Mesa. Por exemplo, C# a amostra de código seguinte lida automaticamente com tokens de continuação se o armazenamento de tabelaos os devolver numa resposta:  

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

O seguinte código c# lida com tokens de continuação explicitamente:  

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

Ao utilizar tokens de continuação explicitamente, pode controlar quando a aplicação obtém o segmento seguinte de dados. Por exemplo, se a sua aplicação cliente permitir que os utilizadores páginam através das entidades armazenadas numa tabela, um utilizador pode decidir não páginar através de todas as entidades recuperadas pela consulta. A sua aplicação utilizaria apenas um sinal de continuação para recuperar o próximo segmento quando o utilizador tivesse terminado de prestar apagões através de todas as entidades do segmento atual. Essa abordagem tem vários benefícios:  

* Pode limitar a quantidade de dados a obter do armazenamento da Tabela e que se move sobre a rede.  
* Pode realizar I/O assíncrono em .NET.  
* Pode serializar o símbolo de continuação para armazenamento persistente, para que possa continuar em caso de falha de aplicação.  

> [!NOTE]
> Um símbolo de continuação normalmente devolve um segmento contendo 1.000 entidades, embora possa conter menos. É também o caso se limitar o número de entradas que uma consulta devolve usando **o Take** para devolver as primeiras entidades n que correspondem aos seus critérios de procuração. O armazenamento de mesapode devolver um segmento com menos do que n entidades, juntamente com um símbolo de continuação que lhe permita recuperar as restantes entidades.  
> 
> 

O código do c# seguinte mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projecção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ser até 1 MB de tamanho. Quando consulta a tabela e recupera entidades, pode não precisar de todas as propriedades, e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo seguinte recupera apenas a propriedade `Email` (juntamente com `PartitionKey`, `RowKey`, `Timestamp`e `ETag`) das entidades selecionadas pela consulta.  

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

Note como o valor `RowKey` está disponível, mesmo que não esteja incluído na lista de propriedades para recuperar.  

### <a name="modify-entities"></a>Modificar entidades
A Biblioteca do Cliente de Armazenamento permite modificar as suas entidades armazenadas no armazenamento de mesa, inserindo, apagando e atualizando entidades. Pode utilizar EGTs para emlotar múltiplas inserções, atualizar e eliminar as operações em conjunto, para reduzir o número de viagens redondas necessárias e melhorar o desempenho da sua solução.  

As exceções lançadas quando a Biblioteca de Clientes de Armazenamento executa um EGT normalmente incluem o índice da entidade que fez com que o lote falhasse. Isso é útil quando está a depurar o código que usa EGTs.  

Também deve considerar como seu design afeta como a aplicação cliente lida com operações de atualização e de simultaneidade.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o armazenamento de mesa implementa controlos de moeda optimistaao nível de entidades individuais para inserir, fundir e eliminar operações, embora seja possível que um cliente force o armazenamento de mesa a contornar estes controlos. Para mais informações, consulte [Gerir a moeda no Armazenamento Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Intercalação ou substituição
O método `Replace` da classe `TableOperation` substitui sempre a entidade completa no armazenamento de mesa. Se não incluir um imóvel no pedido quando esse imóvel existe na entidade armazenada, o pedido retira esse imóvel da entidade armazenada. A menos que queira remover uma propriedade explicitamente de uma entidade armazenada, tem de incluir todas as propriedades no pedido.  

Pode utilizar o método `Merge` da classe `TableOperation` para reduzir a quantidade de dados que envia para o armazenamento de Mesa quando pretende atualizar uma entidade. O método `Merge` substitui quaisquer imóveis na entidade armazenada por valores patrimoniais da entidade incluída no pedido. Este método deixa intactas quaisquer propriedades na entidade armazenada que não estejam incluídas no pedido. Isto é útil se tiver grandes entidades, e apenas precisa atualizar um pequeno número de propriedades num pedido.  

> [!NOTE]
> Os métodos `*Replace` e `Merge` falham se a entidade não existir. Como alternativa, pode utilizar os métodos `InsertOrReplace` e `InsertOrMerge` que criam uma nova entidade se não existir.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidades heterogéneas
O armazenamento de mesa é uma loja de mesa *sem esquemas.* Isto significa que uma única mesa pode armazenar entidades de vários tipos, proporcionando uma grande flexibilidade no seu design. O exemplo seguinte ilustra uma tabela de armazenamento dos funcionários e entidades de departamento:  

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
<th>FirstName</th>
<th>LastName</th>
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
<th>FirstName</th>
<th>LastName</th>
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
<th>FirstName</th>
<th>LastName</th>
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

Cada entidade deve ainda ter `PartitionKey`, `RowKey`e valores `Timestamp`, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada que indique o tipo de entidade, a menos que decida armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Prepare o tipo de entidade para o `RowKey` (ou possivelmente o `PartitionKey`). Por exemplo, `EMPLOYEE_000123` ou `DEPARTMENT_SALES` como valores `RowKey`.  
* Utilize uma propriedade separada para registar o tipo de entidade, como mostra a tabela seguinte.  

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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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
<th>entityType</th>
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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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

A primeira opção, pré-passar o tipo de entidade para o `RowKey`, é útil se houver a possibilidade de duas entidades de diferentes tipos poderem ter o mesmo valor-chave. Ele também agrupa as entidades do mesmo tipo em conjunto na partição.  

As técnicas discutidas nesta secção são especialmente relevantes para a discussão sobre[as relações de Herança.](#inheritance-relationships)  

> [!NOTE]
> Considere incluir um número de versão no valor do tipo de entidade, para permitir que as aplicações do cliente evoluam objetos POCO e trabalhem com diferentes versões.  
> 
> 

O resto desta secção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperar tipos de entidades heterogéneas
Se estiver a utilizar a Biblioteca de Clientes de Armazenamento, tem três opções para trabalhar com vários tipos de entidades.  

Se conhecer o tipo de entidade armazenada com `RowKey` e valores `PartitionKey` específicos, então pode especificar o tipo de entidade quando recuperar a entidade. Viu isto nos dois exemplos anteriores que recuperam entidades de tipo `EmployeeEntity`: Faça uma consulta pontual [utilizando a Biblioteca de Clientes](#run-a-point-query-by-using-the-storage-client-library) de Armazenamento e [recupere várias entidades utilizando o LINQ.](#retrieve-multiple-entities-by-using-linq)  

A segunda opção é utilizar o tipo `DynamicTableEntity` (um saco de propriedade), em vez de um tipo de entidade POCO de betão. Esta opção também pode melhorar o desempenho, porque não há necessidade de serializar e desserializar a entidade para os tipos .NET. O C# código que se segue potencialmente recupera várias entidades de diferentes tipos da tabela, mas devolve todas as entidades como `DynamicTableEntity` instâncias. Em seguida, utiliza a propriedade `EntityType` para determinar o tipo de cada entidade:  

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

Para recuperar outras propriedades, deve utilizar o método `TryGetValue` sobre a propriedade `Properties` da classe `DynamicTableEntity`.  

Uma terceira opção é combinar usando o tipo `DynamicTableEntity` e uma `EntityResolver` instância. Isto permite-lhe resolver para vários tipos POCO da mesma consulta. Neste exemplo, o delegado `EntityResolver` está a usar a propriedade `EntityType` para distinguir entre os dois tipos de entidade que a consulta devolve. O método `Resolve` utiliza o delegado `resolver` para resolver `DynamicTableEntity` instâncias para `TableEntity` casos.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Modificar tipos de entidades heterogéneas
Não precisa de saber o tipo de entidade para a apagar, e conhece sempre o tipo de entidade quando a insere. No entanto, pode utilizar o tipo `DynamicTableEntity` para atualizar uma entidade sem conhecer o seu tipo, e sem utilizar uma classe de entidade POCO. A amostra de código seguinte recupera uma única entidade, e verifica se a propriedade `EmployeeCount` existe antes de atualizá-la.  

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

### <a name="control-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso partilhado
Pode utilizar fichas de assinatura de acesso partilhado (SAS) para permitir que as aplicações do cliente modifiquem diretamente (e consulta) entidades de tabela, sem necessidade de autenticar diretamente com armazenamento de mesa. Normalmente, há três benefícios principais para através da SAS em seu aplicativo:  

* Não é necessário distribuir a chave da sua conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) de forma a permitir que esse dispositivo aceda e modifique entidades no armazenamento de Mesa.  
* Pode descarregar parte do trabalho que as funções web e dos trabalhadores desempenham na gestão das suas entidades. Pode descarregar para dispositivos clientes, como computadores de utilizador final e dispositivos móveis.  
* Pode atribuir um conjunto limitado de permissões a um cliente (como permitir o acesso apenas a recursos específicos).  

Para obter mais informações sobre a utilização de tokens SAS com armazenamento de mesa, consulte Utilizar assinaturas de [acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, ainda deve gerar as fichas SAS que concedem uma aplicação de cliente às entidades em armazenamento de mesa. Faça-o num ambiente que tenha acesso seguro às chaves da sua conta de armazenamento. Normalmente, utiliza uma função web ou de trabalho para gerar os tokens de SAS e entregue-o para as aplicações de cliente que precisam de acesso para as suas entidades. Uma vez que ainda há uma sobrecarga envolvida na geração e fornecer SAS tokens para os clientes, deve considerar a melhor maneira, para reduzir esta sobrecarga, especialmente em cenários de volume elevado.  

É possível gerar um símbolo SAS que dá acesso a um subconjunto das entidades numa tabela. Por padrão, cria-se um símbolo SAS para uma mesa inteira. Mas também é possível especificar que o token SAS concede acesso a uma gama de valores `PartitionKey`, ou a uma gama de valores `PartitionKey` e `RowKey`. Pode optar por gerar tokens SAS para utilizadores individuais do seu sistema, de modo a que o token SAS de cada utilizador só lhes permita aceder às suas próprias entidades no armazenamento de Mesa.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que são propagar os pedidos entre várias partições, pode melhorar a capacidade de resposta de débito e o cliente utilizando consultas assíncronas ou paralelas.
Por exemplo, pode ter dois ou mais trabalho instâncias de função acessar suas tabelas em paralelo. Pode ter funções individuais de trabalhador responsável por determinados conjuntos de divisórias, ou simplesmente ter múltiplas instâncias de função de trabalhador, cada uma capaz de aceder a todas as divisórias numa tabela.  

Dentro de uma instância de cliente, você pode melhorar a entrada executando operações de armazenamento assincronicamente. A biblioteca de cliente de armazenamento torna mais fácil escrever consultas assíncronas e modificações. Por exemplo, pode começar com o método sincronizado que recupera todas as entidades numa C# partição, como mostra o seguinte código:  

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

Pode modificar facilmente este código de modo a que a consulta seja executado assincronicamente, da seguinte forma:  

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

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método inclui agora o modificador `async` e devolve uma `Task` instância.  
* Em vez de chamar o método `ExecuteSegmented` para recuperar resultados, o método chama agora o método `ExecuteSegmentedAsync`. O método utiliza o modificador `await` para obter resultados assincronicamente.  

A aplicação do cliente pode chamar este método várias vezes, com valores diferentes para o parâmetro `department`. Cada consulta corre num fio separado.  

Não existe uma versão assíncrona do método `Execute` na classe `TableQuery`, porque a interface `IEnumerable` não suporta uma enumeração assíncrona.  

Também pode inserir, atualizar e eliminar entidades de forma assíncrona. O exemplo do c# seguinte mostra um método simples, síncrono, para inserir ou substituir uma entidade employee:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode modificar facilmente este código de modo a que a atualização seja recorrida assincronicamente, da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método inclui agora o modificador `async` e devolve uma `Task` instância.  
* Em vez de chamar o método `Execute` para atualizar a entidade, o método chama agora o método `ExecuteAsync`. O método utiliza o modificador `await` para obter resultados assincronicamente.  

A aplicação do cliente pode chamar vários métodos assíncronos como este, e cada invocação de método corre em um fio separado.  


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

