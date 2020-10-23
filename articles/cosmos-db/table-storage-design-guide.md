---
title: Design Azure Cosmos DB tabelas para escalar e performance
description: 'Guia de design de armazenamento de mesa azure: mesas escaláveis e performantes no armazenamento da Azure Cosmos DB e da Azure Table'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: dc140553cbca2347678c376cc9420cfddef22b07
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428053"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guia de conceção de tabela do armazenamento de Tabelas do Azure: Tabelas dimensionáveis e com bom desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para criar tabelas dimensionáveis e de alto desempenho, tem de considerar uma variedade de fatores, incluindo o custo. Se tiver criado anteriormente esquemas para bases de dados relacionais, estará familiarizado com estas considerações. No entanto, embora existam algumas semelhanças entre o Armazenamento de tabelas do Azure e os modelos relacionais, também há muitas diferenças importantes. Estas diferenças levam normalmente a diferentes designs que podem parecer incorretos ou não intuitivos para alguém familiarizado com bases de dados relacionais, mas isso faz sentido se estiver a criar um arquivo de chaves/valores NoSQL, como o Armazenamento de tabelas.

O armazenamento de mesas destina-se a apoiar aplicações em escala em nuvem que podem conter milhares de milhões de entidades ("linhas" na terminologia de bases de dados relacionais) de dados ou para conjuntos de dados que devem suportar volumes de transações elevados. Por isso, tem de pensar de forma diferente sobre como armazena os seus dados e compreender como funciona o armazenamento da Tabela. Uma loja de dados NoSQL bem concebida pode permitir que a sua solução se dimensione muito mais (e a um custo mais baixo) do que uma solução que utilize uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-azure-table-storage"></a>Sobre o armazenamento da mesa Azure
Esta secção destaca algumas das principais características do armazenamento da mesa que são especialmente relevantes para a conceção de desempenho e escalabilidade. Se é novo no armazenamento de armazenamento e mesa do Azure, consulte [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e [inicie-se com o armazenamento da tabela Azure utilizando .NET](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia esteja no armazenamento de mesa, ele inclui alguma discussão sobre o armazenamento da Azure Queue e o armazenamento Azure Blob, e como você pode usá-los juntamente com o armazenamento de mesa em uma solução.  

O armazenamento de mesa utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada fileira da tabela representa uma entidade, e as colunas armazenam as várias propriedades dessa entidade. Cada entidade tem um par de chaves para identificá-lo de forma única, e uma coluna de timetamp que o armazenamento da mesa usa para rastrear quando a entidade foi atualizada pela última vez. O campo de tempos é adicionado automaticamente e não é possível substituir manualmente a placa de tempo com um valor arbitrário. O armazenamento de mesa utiliza esta última etiqueta de tempo modificada (LMT) para gerir a concordância otimista.  

> [!NOTE]
> Armazenamento de mesaS AS operações da API também devolvem um `ETag` valor que obtém do LMT. Neste documento, os termos ETag e LMT são utilizados intercambiavelmente, porque se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo a seguir mostra um design de mesa simples para armazenar funcionários e entidades de departamento. Muitos dos exemplos mostrados mais tarde neste guia são baseados neste design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Salão</td>
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
<th>E-mail</th>
</tr>
<tr>
<td>Jun</td>
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
<th>Contagem de Funcionários</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Rio Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, este design parece semelhante a uma tabela numa base de dados relacional. As principais diferenças são as colunas obrigatórias e a capacidade de armazenar vários tipos de entidades na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, como **o FirstName** ou **o Age,** tem um tipo de dados, como o número inteiro ou o fio, tal como uma coluna numa base de dados relacional. No entanto, ao contrário de uma base de dados relacional, a natureza sem esquemas de armazenamento de mesa significa que uma propriedade não precisa de ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos numa única propriedade, deve utilizar um formato serializado como JSON ou XML. Para obter mais informações, consulte [o modelo de dados de armazenamento de tabelas de compreensão.](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Sua escolha e é fundamental para um bom design de `PartitionKey` `RowKey` mesa. Todas as entidades armazenadas numa mesa devem ter uma combinação única de `PartitionKey` e `RowKey` . Tal como acontece com as chaves numa tabela de bases de dados relacional, os `PartitionKey` valores e `RowKey` valores são indexados para criar um índice agrupado que permite visualizações rápidas. O armazenamento de mesa, no entanto, não cria quaisquer índices secundários, por isso estas são as duas únicas propriedades indexadas (alguns dos padrões descritos mais tarde mostram como você pode trabalhar em torno desta limitação aparente).  

Uma tabela é composta por uma ou mais divisórias, e muitas das decisões de design que tomarão estarão por perto escolhendo um adequado `PartitionKey` e `RowKey` otimizando a sua solução. Uma solução pode consistir em apenas uma tabela que contém todas as suas entidades organizadas em divisórias, mas tipicamente uma solução tem várias tabelas. As tabelas ajudam-no a organizar logicamente as suas entidades e a ajudá-lo a gerir o acesso aos dados utilizando listas de controlo de acesso. Pode deixar cair uma mesa inteira utilizando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Divisórias de mesa
O nome da conta, o nome da tabela e, `PartitionKey` em conjunto, identificam a partição dentro do serviço de armazenamento onde o armazenamento de mesa armazena a entidade. Além de fazerem parte do regime de endereçamento das entidades, as divisórias definem um âmbito de transação (ver secção mais tarde neste artigo, [operações de grupo da Entidade),](#entity-group-transactions)e formam a base de como as escalas de armazenamento do quadro. Para obter mais informações sobre divisórias de mesa, consulte [a lista de verificação de desempenho e escalabilidade para armazenamento de mesas.](../storage/tables/storage-performance-checklist.md)  

No armazenamento de mesa, um nó individual presta uma ou mais divisórias completas, e as balanças de serviço por divisórias de equilíbrio dinâmico de carga em nós. Se um nó estiver carregado, o armazenamento da mesa pode dividir a gama de divisórias servidas por esse nó em diferentes nós. Quando o tráfego diminui, o armazenamento de mesa pode fundir as gamas de divisórias de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do armazenamento da mesa, e em particular a forma como gere as divisórias, consulte [o Microsoft Azure Storage: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Transações de grupos de entidades
No armazenamento de mesas, as transações de grupos de entidades (EGTs) são o único mecanismo incorporado para a realização de atualizações atómicas em várias entidades. Os EGTs são também referidos como *transações de lote*. As EGTs só podem operar em entidades armazenadas na mesma partição (partilhando a mesma chave de partição numa tabela específica), por isso, sempre que precisar de comportamento transacional atómico entre várias entidades, certifique-se de que essas entidades estão na mesma divisão. Esta é frequentemente uma razão para manter vários tipos de entidades na mesma tabela (e partição), e não usar várias tabelas para diferentes tipos de entidades. Um único EGT pode operar no máximo 100 entidades.  Se submeter vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operam em entidades que são comuns em todos os EGTs. Caso contrário, arrisca-se a atrasar o processamento.

Os EGTs também introduzem uma potencial compensação para que possa avaliar no seu design. A utilização de mais divisórias aumenta a escalabilidade da sua aplicação, porque o Azure tem mais oportunidades para pedidos de equilíbrio de carga em nós. Mas isso pode limitar a capacidade da sua aplicação de realizar transações atómicas e manter uma forte consistência para os seus dados. Além disso, existem alvos específicos de escalabilidade ao nível de uma partição que podem limitar o rendimento das transações que se pode esperar de um único nó.

Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento Azure, consulte [os objetivos de Escalaability para contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre os objetivos de escalabilidade para armazenamento de mesa, consulte [metas de escalabilidade e desempenho para armazenamento de mesa.](../storage/tables/scalability-targets.md) Secções posteriores deste guia discutem várias estratégias de design que o ajudam a gerir trade-offs como este, e discutem a melhor forma de escolher a sua chave de partição com base nos requisitos específicos da sua aplicação ao cliente.  

### <a name="capacity-considerations"></a>Considerações de capacidade
A tabela a seguir inclui alguns dos valores-chave a ter em conta quando está a desenhar uma solução de armazenamento de mesa:  

| Capacidade total de uma conta de armazenamento Azure | 500 TB |
| --- | --- |
| Número de tabelas numa conta de armazenamento Azure |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de divisórias numa tabela |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de entidades em partição |Limitado apenas pela capacidade da conta de armazenamento. |
| Tamanho de uma entidade individual |Até 1 MB, com um máximo de 255 propriedades (incluindo o `PartitionKey` `RowKey` , e `Timestamp` . |
| Tamanho do `PartitionKey` |Uma corda até 1 KB de tamanho. |
| Tamanho do `RowKey` |Uma corda até 1 KB de tamanho. |
| Dimensão de uma transação de grupo de entidades |Uma transação pode incluir no máximo 100 entidades, e a carga útil deve ter um tamanho inferior a 4 MB. Um EGT só pode atualizar uma entidade uma vez. |

Para obter mais informações, consulte [o modelo de dados de serviço de tabela.](https://msdn.microsoft.com/library/azure/dd179338.aspx)  

### <a name="cost-considerations"></a>Considerações de custos
O armazenamento de mesa é relativamente barato, mas deve incluir estimativas de custos tanto para o uso da capacidade como para a quantidade de transações como parte da sua avaliação de qualquer solução que utilize o armazenamento da mesa. Em muitos cenários, no entanto, armazenar dados denormalizados ou duplicados de forma a melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida a tomar. Para obter mais informações sobre preços, consulte [os preços de Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Orientações do design da tabela
Estas listas resumem algumas das principais diretrizes que deve ter em mente quando estiver a desenhar as suas tabelas. Este guia aborda-os a todos mais detalhadamente mais tarde. Estas diretrizes são diferentes das diretrizes que normalmente seguiria para o design de bases de dados relacionais.  

Projetar o seu armazenamento de mesa para ser *lido* eficientemente:

* **Design para consulta em aplicações pesadas de leitura.** Quando estiver a desenhar as suas tabelas, pense nas consultas (especialmente nas sensíveis à latência) que irá correr antes de pensar em como atualizar as suas entidades. Isto normalmente resulta numa solução eficiente e performante.  
* **Especifique tanto `PartitionKey` como `RowKey` nas suas consultas.** *Consultas pontuais* como estas são as consultas de armazenamento de mesa mais eficientes.  
* **Considere armazenar cópias duplicadas de entidades.** O armazenamento de mesa é barato, por isso considere armazenar a mesma entidade várias vezes (com chaves diferentes), para permitir consultas mais eficientes.  
* **Considere desnormalizar os seus dados.** O armazenamento de mesa é barato, por isso considere desnormalizar os seus dados. Por exemplo, armazenar entidades sumárias para que as consultas por dados agregados apenas precisem de aceder a uma única entidade.  
* **Utilize valores-chave compostos.** As únicas chaves que tem são `PartitionKey` `RowKey` e. Por exemplo, utilize valores-chave compostos para permitir caminhos de acesso com chave suplente às entidades.  
* **Use a projeção de consulta.** Pode reduzir a quantidade de dados que transfere através da rede utilizando consultas que selecionam apenas os campos de que necessita.  

Projetar o seu armazenamento de mesa para ser *eficiente:*  

* **Não crie divisórias quentes.** Escolha as teclas que lhe permitem espalhar os seus pedidos através de várias divisórias a qualquer momento.  
* **Evite picos no trânsito.** Distribua o tráfego durante um período de tempo razoável e evite picos de tráfego.
* **Não crie necessariamente uma tabela separada para cada tipo de entidade.** Quando necessitar de transações atómicas entre tipos de entidades, pode armazenar estes múltiplos tipos de entidades na mesma divisória na mesma tabela.
* **Considere a produção máxima que deve alcançar.** Deve estar ciente dos alvos de escalabilidade para armazenamento de mesa, e certifique-se de que o seu design não fará com que os exceda.  

Mais tarde neste guia, você verá exemplos que colocam todos estes princípios em prática.  

## <a name="design-for-querying"></a>Design das consultas
O armazenamento de mesa pode ser lido intensivamente, escrever intensivo, ou uma mistura dos dois. Esta secção considera conceber para apoiar as operações de leitura de forma eficiente. Tipicamente, um design que suporta operações de leitura eficientemente também é eficiente para operações de escrita. No entanto, existem considerações adicionais na conceção para apoiar operações de escrita. Estes são discutidos na secção seguinte, [Design para modificação de dados.](#design-for-data-modification)

Um bom ponto de partida para permitir que leia os dados de forma eficiente é perguntar "Que consultas a minha aplicação precisará de correr para recuperar os dados de que precisa?"  

> [!NOTE]
> Com o armazenamento de mesa, é importante corrigir o design na frente, porque é difícil e caro mudá-lo mais tarde. Por exemplo, numa base de dados relacional, é frequentemente possível abordar problemas de desempenho simplesmente adicionando índices a uma base de dados existente. Isto não é uma opção com armazenamento de mesa.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Como a sua escolha e afeta o desempenho da `PartitionKey` `RowKey` consulta
Os exemplos a seguir assumem que o armazenamento de mesa está a armazenar entidades de empregados com a seguinte estrutura (a maioria dos exemplos omitem o `Timestamp` imóvel para maior clareza):  

| Nome da coluna | Tipo de dados |
| --- | --- |
| `PartitionKey` (Nome do departamento) |String |
| `RowKey` (ID do empregado) |Cadeia |
| `FirstName` |Cadeia |
| `LastName` |Cadeia |
| `Age` |Número inteiro |
| `EmailAddress` |String |

Aqui estão algumas diretrizes gerais para a conceção de consultas de armazenamento de mesa. A sintaxe do filtro utilizada nos seguintes exemplos é da API DE ARMAZENAMENTO DE MESA. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

* Uma *consulta de pontos* é a procura mais eficiente para usar, e é recomendado para consultas ou procuras de alto volume que requerem a latência mais baixa. Tal consulta pode usar os índices para localizar uma entidade individual de forma eficiente, especificando tanto os valores como os `PartitionKey` `RowKey` valores. Por exemplo: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* O segundo melhor é uma *consulta de alcance.* Utiliza o `PartitionKey` , e filtra numa gama de `RowKey` valores para devolver mais do que uma entidade. O `PartitionKey` valor identifica uma divisória específica, e os `RowKey` valores identificam um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* O terceiro melhor é uma *varredura de partição.* Usa o `PartitionKey` , e filtros em outra propriedade não chave e pode devolver mais do que uma entidade. O `PartitionKey` valor identifica uma divisória específica, e os valores de propriedade selecionam para um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Uma tomografia de *mesa* não inclui o , e `PartitionKey` é ineficiente porque procura todas as divisórias que compõem a sua mesa para quaisquer entidades correspondentes. Executa uma verificação de mesa independentemente de o filtro utilizar ou não o `RowKey` . Por exemplo: `$filter=LastName eq 'Jones'`.  
* Consultas de armazenamento de mesa Azure que devolvem várias entidades classificam-nas `PartitionKey` e `RowKey` encomendam. Para evitar recorrer às entidades do cliente, escolha uma `RowKey` que defina a ordem de classificação mais comum. Os resultados de consulta devolvidos pela Azure Table API em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de características, consulte [as diferenças entre a Tabela API no Azure Cosmos DB e o armazenamento da tabela Azure](table-api-faq.md#table-api-vs-table-storage).

Usar um "**ou**" para especificar um filtro com base `RowKey` em valores resulta numa varredura de partição, e não é tratado como uma consulta de alcance. Por isso, evite consultas que utilizem filtros como: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')` .  

Por exemplo, o código do lado do cliente que utiliza a Biblioteca do Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Executar uma consulta de pontos utilizando a Biblioteca do Cliente de Armazenamento](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperar várias entidades utilizando o LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projeção do lado do servidor](#server-side-projection)  

Por exemplo, código do lado do cliente que pode lidar com vários tipos de entidades armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Escolha um apropriado `PartitionKey`
A sua escolha `PartitionKey` deve equilibrar a necessidade de permitir a utilização de EGTs (para garantir a consistência) contra a exigência de distribuir as suas entidades por múltiplas divisórias (para garantir uma solução escalável).  

Num extremo, pode armazenar todas as suas entidades numa única divisória. Mas isso pode limitar a escalabilidade da sua solução, e impedir que o armazenamento da Mesa seja capaz de carregar pedidos de equilíbrio. No outro extremo, pode armazenar uma entidade por partição. Isto é altamente escalável e permite o armazenamento de mesa para pedidos de equilíbrio de carga, mas impede-o de usar transações de grupos de entidades.  

Um ideal `PartitionKey` permite-lhe usar consultas eficientes, e tem divisórias suficientes para garantir que a sua solução é escalável. Normalmente, você vai descobrir que as suas entidades terão um imóvel adequado que distribui suas entidades através de divisórias suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre utilizadores ou funcionários, `UserID` pode ser um bom `PartitionKey` . Pode ter várias entidades que usam um particular `UserID` como chave de partição. Cada entidade que armazena dados sobre um utilizador é agrupada numa única divisória. Estas entidades são acessíveis através de EGTs, sendo ainda altamente escaláveis.
> 
> 

Existem considerações adicionais na sua escolha `PartitionKey` que se relacionam com a forma como insere, atualiza e apaga entidades. Para mais informações, consulte [Design para modificação de dados](#design-for-data-modification) mais tarde neste artigo.  

### <a name="optimize-queries-for-table-storage"></a>Otimizar consultas para armazenamento de mesa
O armazenamento de mesa indexa automaticamente as suas entidades utilizando os `PartitionKey` valores e `RowKey` valores num único índice agrupado. Esta é a razão pela qual as consultas pontuais são as mais eficientes de usar. No entanto, não existem outros índices que não os do índice agrupado no `PartitionKey` e `RowKey` .

Muitos projetos devem satisfazer requisitos para permitir a procura de entidades com base em múltiplos critérios. Por exemplo, localizar entidades de empregados com base em e-mail, ID do empregado ou apelido. Os seguintes padrões na secção [Os padrões de design](#table-design-patterns) da tabela abordam este tipo de requisitos. Os padrões também descrevem formas de trabalhar em torno do facto de que o armazenamento de mesa não fornece índices secundários.  

* [Padrão de índice secundário intraparteção](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes (na mesma partição). Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  
* [Padrão de índice secundário inter-partição](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes em divisórias separadas ou em tabelas separadas. Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  
* [Padrão de entidades indexadas](#index-entities-pattern): Manter entidades indexadas para permitir pesquisas eficientes que devolvam listas de entidades.  

### <a name="sort-data-in-table-storage"></a>Ordenar dados no armazenamento de mesa

Os resultados da consulta de armazenamento de mesas ordenados em ordem ascendente, com base em `PartitionKey` e depois por `RowKey` .

> [!NOTE]
> Os resultados de consulta devolvidos pela Azure Table API em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de características, consulte [as diferenças entre a Tabela API no Azure Cosmos DB e o armazenamento da tabela Azure](table-api-faq.md#table-api-vs-table-storage).

As teclas no armazenamento da mesa são valores de corda. Para garantir que os valores numéricos se classificam corretamente, deve convertê-los num comprimento fixo e remá-los com zeros. Por exemplo, se o valor de identificação do empregado que usa como valor inteiro, deve converter o `RowKey` ID do empregado **123** a **00000123**. 

Muitas aplicações têm requisitos para usar dados classificados em diferentes ordens: por exemplo, classificar os colaboradores pelo nome, ou aderir à data. Os seguintes padrões na secção [Os padrões de design](#table-design-patterns) da tabela abordam como alternar ordens de classificação para as suas entidades:  

* [Padrão de índice secundário intraparteção](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes (na mesma partição). Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  
* [Padrão de índice secundário inter-partição](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` diferentes valores em divisórias separadas em tabelas separadas. Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.
* [Padrão de cauda](#log-tail-pattern)de log : Recupere as entidades *n* mais recentemente adicionadas a uma partição, utilizando um valor que se classifica em ordem de data e `RowKey` hora inversa.  

## <a name="design-for-data-modification"></a>Design da modificação de dados
Esta secção centra-se nas considerações de design para otimizar inserções, atualizações e eliminações. Em alguns casos, você precisará avaliar o trade-off entre designs que otimizam para consulta contra designs que otimizam para modificação de dados. Esta avaliação é semelhante à que se faz nos desenhos para bases de dados relacionais (embora as técnicas de gestão das trocas de design sejam diferentes numa base de dados relacional). Os [padrões](#table-design-patterns) de design de tabela de secção descreve alguns padrões de design detalhados para armazenamento de mesa, e destaca alguns destes trade-offs. Na prática, você vai descobrir que muitos projetos otimizados para consultas entidades também funcionam bem para modificar entidades.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho das operações de inserção, atualização e exclusão
Para atualizar ou eliminar uma entidade, deve ser capaz de identificá-la utilizando os `PartitionKey` valores e `RowKey` valores. A este respeito, a sua escolha e modificação de `PartitionKey` `RowKey` entidades deve seguir critérios semelhantes à sua escolha para apoiar consultas de pontos. Quer identificar as entidades da forma mais eficiente possível. Não pretende utilizar uma partição ineficiente ou uma verificação de tabela para localizar uma entidade para descobrir os `PartitionKey` `RowKey` valores necessários para a atualizar ou eliminar.  

Os seguintes padrões na secção [Os padrões de design](#table-design-patterns) da tabela endereçam otimizar o desempenho da sua inserção, atualização e apagar operações:  

* [Padrão de eliminação](#high-volume-delete-pattern)de volume elevado : Permitir a supressão de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea na sua própria tabela separada. Apaga-se as entidades apagando a mesa.  
* [Padrão da série de](#data-series-pattern)dados : Guarde séries de dados completas numa única entidade para minimizar o número de pedidos que es faz.  
* [Padrão de entidades amplas](#wide-entities-pattern): Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de grandes entidades](#large-entities-pattern): Use o armazenamento de bolhas para armazenar grandes valores de propriedade.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Garantir consistência nas suas entidades armazenadas
O outro fator-chave que influencia a escolha das chaves para otimizar as modificações de dados é como garantir a consistência utilizando transações atómicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma divisão.  

Os seguintes padrões na secção [Os padrões de design](#table-design-patterns) da tabela abordam a consistência da gestão:  

* [Padrão de índice secundário intraparteção](#intra-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes (na mesma partição). Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  
* [Padrão de índice secundário inter-partição](#inter-partition-secondary-index-pattern): Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes em divisórias separadas ou em tabelas separadas. Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern): Permitir um comportamento eventualmente consistente através dos limites de partição ou do sistema de armazenamento utilizando as filas Azure.
* [Padrão de entidades indexadas](#index-entities-pattern): Manter entidades indexadas para permitir pesquisas eficientes que devolvam listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern): Combine dados relacionados numa única entidade, para que possa recuperar todos os dados de que necessita com uma consulta de ponto único.  
* [Padrão da série de](#data-series-pattern)dados : Armazenar séries de dados completas numa única entidade, para minimizar o número de pedidos que es faz.  

Para mais informações, consulte [as transações do grupo Entity](#entity-group-transactions) mais tarde neste artigo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Certifique-se de que o seu design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas deve sempre avaliar se este é o caso para o seu cenário específico. Alguns dos padrões na secção [Os padrões](#table-design-patterns) de design de mesa avaliam explicitamente as compensações entre as entidades que questionam e modificam, e deve sempre ter em conta o número de cada tipo de operação.  

Os seguintes padrões na secção [Os padrões](#table-design-patterns) de conceção da tabela abordam as compensações entre a conceção de consultas eficientes e a conceção de uma modificação eficiente dos dados:  

* [Padrão de chave composto](#compound-key-pattern): Utilize `RowKey` valores compostos para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  
* [Padrão de cauda](#log-tail-pattern)de log : Recupere as entidades *n* mais recentemente adicionadas a uma partição, utilizando um valor que se classifica em ordem de data e `RowKey` hora inversa.  

## <a name="encrypt-table-data"></a>Criptografe dados de tabela
A biblioteca de clientes .NET Azure Storage suporta a encriptação das propriedades da entidade de cordas para inserir e substituir operações. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta em cordas após a desencriptação.    

Para as tabelas, além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Ou especificar um `EncryptProperty` atributo (para entidades POCO que derivam `TableEntity` de), ou especificar uma encriptação resolver nas opções de pedido. Um resolver de encriptação é um delegado que pega numa chave de partição, chave de linha e nome de propriedade, e devolve um Boolean que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente utiliza esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve para o fio. O delegado também prevê a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar a propriedade A; caso contrário, encriptar propriedades A e B.) Não é necessário fornecer esta informação enquanto lê ou consulta entidades.

A fusão não é suportada atualmente. Porque um subconjunto de propriedades poderia ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resultará na perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente a partir do serviço, ou usar uma nova chave por propriedade. Nenhum destes é adequado por razões de desempenho.     

Para obter informações sobre a encriptação de dados da tabela, consulte [a encriptação do lado do cliente e o cofre da chave Azure para o Armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relacionamentos de modelo
Os modelos de domínio de construção são um passo fundamental na conceção de sistemas complexos. Normalmente, utiliza-se o processo de modelação para identificar entidades e as relações entre elas, como forma de compreender o domínio do negócio e informar o design do seu sistema. Esta secção centra-se em como você pode traduzir alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para designs para armazenamento de mesa. O processo de mapeamento de um modelo de dados lógico para um modelo de dados físico baseado no NoSQL é diferente do utilizado na conceção de uma base de dados relacional. O design de bases de dados relacionais normalmente pressupõe um processo de normalização de dados otimizado para minimizar a redundância. Tal design também assume uma capacidade de consulta declarativa que abstrata a implementação de como a base de dados funciona.  

### <a name="one-to-many-relationships"></a>Relacionamentos de um a muitos
Relacionamentos entre objetos de domínio de negócios ocorrem frequentemente: por exemplo, um departamento tem muitos funcionários. Existem várias formas de implementar relações one-to-many no armazenamento de mesa, cada uma com prós e contras que podem ser relevantes para o cenário particular.  

Considere o exemplo de uma grande multinacional com dezenas de milhares de departamentos e entidades de funcionários. Todos os departamentos têm muitos empregados e cada empregado está associado a um departamento específico. Uma abordagem é armazenar departamentos separados e entidades de empregados, tais como:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Este exemplo mostra uma relação implícita entre os tipos, com base no `PartitionKey` valor. Cada departamento pode ter muitos empregados.  

Este exemplo mostra também uma entidade de departamento e as suas entidades associadas na mesma divisão. Pode optar por utilizar diferentes contas de divisórias, tabelas ou até mesmo armazenamento para os diferentes tipos de entidades.  

Uma abordagem alternativa é desnormalizar os seus dados e armazenar apenas entidades de empregados com dados desnormalizados do departamento, como mostra o exemplo seguinte. Neste cenário específico, esta abordagem desnormalizada pode não ser a melhor se tiver a obrigação de poder alterar os detalhes de um gerente de departamento. Para isso, precisaria de atualizar todos os funcionários do departamento.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Para mais informações, consulte o [padrão de desnormalização](#denormalization-pattern) mais tarde neste guia.  

A tabela seguinte resume os prós e os contras de cada uma das abordagens para armazenar entidades de empregados e departamentos que têm uma relação de um para muitos. Também deve considerar a frequência com que espera realizar várias operações. Pode ser aceitável ter um desenho que inclua uma operação dispendiosa se essa operação só acontecer com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Vantagens</th>
<th>Desvantagens</th>
</tr>
<tr>
<td>Tipos de entidades separadas, mesma divisória, mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento com uma única operação.</li>
<li>Pode utilizar um EGT para manter a consistência se tiver a obrigação de modificar uma entidade de departamento sempre que atualizar/inserir/eliminar uma entidade do colaborador. Por exemplo, se mantiver uma contagem de funcionários do departamento para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar de recuperar um funcionário e uma entidade do departamento para algumas atividades do cliente.</li>
<li>As operações de armazenamento acontecem na mesma divisória. Em volumes de transações elevados, isto pode resultar num hotspot.</li>
<li>Não se pode mudar um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidades separadas, diferentes divisórias, ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento ou entidade de empregados com uma única operação.</li>
<li>Em volumes de transações elevados, isto pode ajudar a espalhar a carga por mais divisórias.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar de recuperar um funcionário e uma entidade do departamento para algumas atividades do cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualiza/insere/apaga um funcionário e atualiza um departamento. Por exemplo, atualizar a contagem de funcionários numa entidade do departamento.</li>
<li>Não se pode mudar um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar em tipo de entidade única</td>
<td>
<ul>
<li>Pode recuperar toda a informação que precisar com um único pedido.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser caro manter a consistência se precisar de atualizar as informações do departamento (isto exigiria que atualizasse todos os funcionários de um departamento).</li>
</ul>
</td>
</tr>
</table>

A forma como escolhes entre estas opções, e qual dos prós e contras são mais significativos, depende dos teus cenários específicos de aplicação. Por exemplo, com que frequência modifica entidades do departamento? Todas as suas consultas de empregados precisam de informações adicionais do departamento? Quão perto está dos limites de escalabilidade das suas divisórias ou da sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um-para-um
Os modelos de domínio podem incluir relações um-para-um entre entidades. Se precisa implementar uma relação um-para-um no armazenamento de mesa, também deve escolher como ligar as duas entidades relacionadas quando precisa de recuperar as duas. Este link pode ser implícito, com base numa convenção nos valores-chave, ou explícito, armazenando um link sob a forma `PartitionKey` e `RowKey` valores de cada entidade à sua entidade relacionada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma divisão, consulte a secção [Um a muitos relacionamentos](#one-to-many-relationships).  

Existem também considerações de implementação que podem levá-lo a implementar relações um-para-um no armazenamento de mesa:  

* Manusear grandes entidades (para mais informações, consulte [o padrão das grandes entidades).](#large-entities-pattern)  
* Implementando controlos de acesso (para obter mais informações, consulte [o acesso ao Controlo com assinaturas de acesso partilhado).](#control-access-with-shared-access-signatures)  

### <a name="join-in-the-client"></a>Junte-se ao cliente
Embora existam formas de modelar relações no armazenamento de mesa, não se esqueça que as duas principais razões para usar o armazenamento de mesa são a escalabilidade e o desempenho. Se você acha que você está modelando muitas relações que comprometem o desempenho e escalabilidade da sua solução, você deve perguntar a si mesmo se é necessário construir todas as relações de dados no seu design de mesa. Poderá simplificar o design e melhorar a escalabilidade e desempenho da sua solução, se deixar que a sua aplicação ao cliente execute as junções necessárias.  

Por exemplo, se tiver pequenas tabelas que contenham dados que não mudam frequentemente, pode recuperar estes dados uma vez, e cache-os no cliente. Isto pode evitar repetidas velhagens para recuperar os mesmos dados. Nos exemplos que vimos neste guia, o conjunto de departamentos de uma pequena organização é provável que seja pequeno e mude de forma rara. Isto faz com que seja um bom candidato para dados que uma aplicação de cliente pode descarregar uma vez e cache como dados de procura.  

### <a name="inheritance-relationships"></a>Relações de herança
Se a sua aplicação ao cliente utilizar um conjunto de classes que fazem parte de uma relação de herança para representar entidades empresariais, pode facilmente persistir essas entidades no armazenamento de mesa. Por exemplo, pode ter o seguinte conjunto de aulas definidas na sua aplicação de cliente, onde `Person` é uma classe abstrata.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Pode persistir casos das duas classes de betão no armazenamento de mesa utilizando uma única `Person` tabela. Utilize entidades que se pareçam com o seguinte:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Para obter mais informações sobre o trabalho com vários tipos de entidades na mesma tabela no código do cliente, consulte [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types) mais tarde neste guia. Isto fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design da tabela
Em secções anteriores, aprendeu sobre como otimizar o design da sua tabela tanto para recuperar dados da entidade, utilizando consultas, como para inserir, atualizar e eliminar dados da entidade. Esta secção descreve alguns padrões adequados para utilização com armazenamento de mesa. Além disso, você verá como você pode praticamente abordar algumas das questões e trade-offs levantadas anteriormente neste guia. O diagrama seguinte resume as relações entre os diferentes padrões:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

O mapa de padrões destaca algumas relações entre padrões (azul) e anti-padrões (laranja) que são documentados neste guia. Há, naturalmente, muitos outros padrões que vale a pena considerar. Por exemplo, um dos cenários-chave para o armazenamento da mesa é usar o padrão de [vista materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) a partir do padrão de segregação de [responsabilidade de comando.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão do índice secundário intra-partição
Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes (na mesma partição). Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes. As atualizações entre cópias podem ser mantidas consistentes utilizando EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os `PartitionKey` valores e `RowKey` valores. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação de cliente pode usar uma consulta de ponto para recuperar uma entidade de colaborador individual, utilizando o nome do departamento e o ID do empregado (os `PartitionKey` `RowKey` valores). Um cliente também pode recuperar entidades classificadas por identificação de funcionários dentro de cada departamento.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Se também quiser encontrar uma entidade de colaboradores com base no valor de outra propriedade, como endereço de e-mail, deve utilizar uma verificação de partição menos eficiente para encontrar uma correspondência. Isto porque o armazenamento de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados numa ordem diferente da `RowKey` encomenda.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade, com cada cópia usando um `RowKey` valor diferente. Se armazenar uma entidade com as seguintes estruturas, poderá recuperar eficientemente entidades de colaboradores com base em endereço de e-mail ou ID do funcionário. Os valores do prefixo para `RowKey` `empid_` , e `email_` permitem-lhe consultar um único empregado, ou uma série de colaboradores, utilizando uma série de endereços de e-mail ou IDs de funcionários.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Os dois critérios de filtro a seguir (um olhando para cima por ID do empregado, e um procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq' email_jonesj@contoso.com ')  

Se consultar uma série de entidades de colaboradores, pode especificar um intervalo classificado na ordem de identificação do empregado ou um intervalo ordenado por encomenda de endereço de e-mail. Consulta para entidades com o prefixo apropriado no `RowKey` .  

* Para encontrar todos os colaboradores do departamento de Vendas com um ID de empregado na gama 000100 a 000199, utilize: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail a começar pela letra "a", utilize: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
A sintaxe do filtro utilizada nos exemplos anteriores é da API DE ARMAZENAMENTO DE MESA. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de mesas é relativamente barato de usar, por isso o custo de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, deve sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento previstos, e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação do cliente irá executar.  
* Como as entidades de índice secundário são armazenadas na mesma divisão que as entidades originais, certifique-se de que não excede os objetivos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si, utilizando EGTs para atualizar as duas cópias da entidade atomicamente. Isto implica que deve armazenar todas as cópias de uma entidade na mesma divisão. Para obter mais informações, consulte [as transações do grupo de entidades.](#entity-group-transactions)  
* O valor utilizado para o `RowKey` deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Os valores numéricos de enchimento no `RowKey` (por exemplo, o ID 000223 do empregado) permitem a triagem e filtragem corretas com base nos limites superior e inferior.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades utilizando o endereço de e-mail `RowKey` nunca precisar da idade do trabalhador, estas entidades podem ter a seguinte estrutura:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados de que necessita com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para procurar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- A sua aplicação ao cliente precisa de recuperar entidades utilizando uma variedade de chaves diferentes.
- O seu cliente precisa de recuperar entidades em ordens de tipo diferente.
- Pode identificar cada entidade utilizando uma variedade de valores únicos.

No entanto, certifique-se de que não excede os limites de escalabilidade da partição quando estiver a realizar sondagens de entidades utilizando os diferentes `RowKey` valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão do índice secundário inter-partição](#inter-partition-secondary-index-pattern)
* [Padrão de chave composto](#compound-key-pattern)
* [Transações de grupos de entidades](#entity-group-transactions)
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão do índice secundário inter-partição
Armazenar várias cópias de cada entidade utilizando `RowKey` valores diferentes em divisórias separadas ou em tabelas separadas. Isto permite sondagens rápidas e eficientes e ordens de classificação alternativas utilizando `RowKey` valores diferentes.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os `PartitionKey` valores e `RowKey` valores. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação de cliente pode usar uma consulta de ponto para recuperar uma entidade de colaborador individual, utilizando o nome do departamento e o ID do empregado (os `PartitionKey` `RowKey` valores). Um cliente também pode recuperar entidades classificadas por identificação de funcionários dentro de cada departamento.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados"::: [9]

Se também quiser encontrar uma entidade de colaboradores com base no valor de outra propriedade, como endereço de e-mail, deve utilizar uma verificação de partição menos eficiente para encontrar uma correspondência. Isto porque o armazenamento de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados numa ordem diferente da `RowKey` encomenda.  

Está a antecipar um grande volume de transações contra estas entidades, e quer minimizar o risco da taxa de armazenamento da Tabela que limita o seu cliente.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade, com cada cópia usando `PartitionKey` valores e `RowKey` valores diferentes. Se armazenar uma entidade com as seguintes estruturas, poderá recuperar eficientemente entidades de colaboradores com base em endereço de e-mail ou ID do funcionário. Os valores do prefixo para `PartitionKey` `empid_` , e `email_` permitem-lhe identificar qual o índice que pretende utilizar para uma consulta.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Os dois critérios de filtro a seguir (um olhando para cima por ID do empregado, e um procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq ' jonesj@contoso.com ')  

Se consultar uma série de entidades de colaboradores, pode especificar um intervalo classificado na ordem de identificação do empregado ou um intervalo ordenado por encomenda de endereço de e-mail. Consulta para entidades com o prefixo apropriado no `RowKey` .  

* Para encontrar todos os colaboradores do departamento de Vendas com um ID de empregado na gama **000100** a **000199**, classificados em ordem de ID dos funcionários, utilize: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail que comece com "a", classificado em ordem de endereço de e-mail, use: $filter=(PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

Note que a sintaxe do filtro utilizada nos exemplos anteriores é da API DE ARMAZENAMENTO DE MESA. Para mais informações, consulte [as entidades de Consulta.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistentes entre si, utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de mesas é relativamente barato de utilizar, pelo que a despesa com o armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, avalie sempre o custo do seu design com base nos seus requisitos de armazenamento previstos e apenas adicione entidades duplicadas para suportar as consultas que a sua aplicação do cliente irá executar.  
* O valor utilizado para o `RowKey` deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Os valores numéricos de enchimento no `RowKey` (por exemplo, o ID 000223 do empregado) permitem a triagem e filtragem corretas com base nos limites superior e inferior.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades utilizando o endereço de e-mail `RowKey` nunca precisar da idade do trabalhador, estas entidades podem ter a seguinte estrutura:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados de que necessita com uma única consulta, do que usar uma consulta para localizar uma entidade utilizando o índice secundário e outra para procurar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- A sua aplicação ao cliente precisa de recuperar entidades utilizando uma variedade de chaves diferentes.
- O seu cliente precisa de recuperar entidades em ordens de tipo diferente.
- Pode identificar cada entidade utilizando uma variedade de valores únicos.

Utilize este padrão quando pretender evitar exceder os limites de escalabilidade da partição quando estiver a realizar sondagens de entidades utilizando os diferentes `RowKey` valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão do índice secundário intra-partição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composto](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ativar um comportamento eventualmente consistente através dos limites da partição ou dos limites do sistema de armazenamento utilizando as filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
As EGTs permitem transações atómicas em várias entidades que partilham a mesma chave de partição. Por razões de desempenho e escalabilidade, poderá decidir armazenar entidades que tenham requisitos de consistência em divisórias separadas ou num sistema de armazenamento separado. Em tal cenário, não se pode usar EGTs para manter a consistência. Por exemplo, pode ter a obrigação de manter uma eventual consistência entre:  

* Entidades armazenadas em duas divisórias diferentes na mesma tabela, em tabelas diferentes, ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no armazenamento de mesa e uma bolha armazenada no armazenamento blob.  
* Uma entidade armazenada no armazenamento de mesa e um ficheiro num sistema de ficheiros.  
* Uma entidade armazenada no armazenamento de mesa, mas indexada através da pesquisa cognitiva Azure.  

#### <a name="solution"></a>Solução
Ao utilizar as filas Azure, pode implementar uma solução que proporciona uma eventual consistência em duas ou mais divisórias ou sistemas de armazenamento.

Para ilustrar esta abordagem, assuma que tem a obrigação de arquivar ex-entidades de funcionários. As antigas entidades de trabalhadores raramente são questionadas e devem ser excluídas de quaisquer atividades que lidem com os atuais colaboradores. Para implementar este requisito, armazena funcionários ativos na tabela **Current** e antigos funcionários na tabela **Archive.** Arquivar um funcionário requer que elimine a entidade da tabela **Current** e adicione a entidade à tabela **Archive.**

Mas não pode usar um EGT para realizar estas duas operações. Para evitar o risco de uma falha fazer com que uma entidade apareça em ambas ou em nenhuma das tabelas, a operação de arquivo deve ser eventualmente consistente. O diagrama de sequência a seguir descreve os passos desta operação.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Um cliente inicia a operação de arquivo colocando uma mensagem numa fila Azure (neste exemplo, para arquivar #456 dos colaboradores). Um papel de trabalhador sonda a fila para novas mensagens; quando encontra um, lê a mensagem e deixa uma cópia escondida na fila. A função do trabalhador em seguida recolhe uma cópia da entidade da tabela **Corrente,** insere uma cópia na tabela **Archive** e, em seguida, elimina o original da tabela **Current.** Finalmente, se não houve erros dos passos anteriores, o papel do trabalhador elimina a mensagem escondida da fila.  

Neste exemplo, o passo 4 no diagrama insere o funcionário na tabela **Archive.** Pode adicionar o empregado a uma bolha no armazenamento blob ou a um ficheiro num sistema de ficheiros.  

#### <a name="recover-from-failures"></a>Recuperar de falhas
É importante que as operações nos passos 4-5 no diagrama sejam *idempotentes* no caso de a função do trabalhador necessitar de reiniciar a operação de arquivo. Se estiver a utilizar o armazenamento da mesa, para o passo 4 deve utilizar uma operação de "inserir ou substituir"; para o passo 5, deve utilizar uma operação de "excluir se existir" na biblioteca do cliente que está a utilizar. Se estiver a utilizar outro sistema de armazenamento, deve utilizar uma operação idempotente apropriada.  

Se o papel do trabalhador nunca completar o passo 6 no diagrama, então, após um intervalo, a mensagem reaparece na fila pronta para o papel do trabalhador tentar reprocessá-lo. O papel do trabalhador pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "venenosa" para investigação enviando-a para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificação da contagem de deques, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros do armazenamento de mesa e do armazenamento da fila são erros transitórios, e a aplicação do seu cliente deve incluir lógica de repetição adequada para lidar com eles.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não prevê o isolamento de transações. Por exemplo, um cliente pode ler as tabelas **Corrente** e **Arquivo** quando o papel do trabalhador estava entre os passos 4-5 no diagrama, e ver uma visão inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Deve certificar-se de que os passos 4-5 são idempotentes para garantir uma eventual consistência.  
* Pode escalar a solução utilizando várias filas e instâncias de função do trabalhador.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando pretender garantir uma eventual consistência entre entidades que existem em diferentes divisórias ou tabelas. Pode estender este padrão para garantir uma eventual consistência para operações através do armazenamento de mesa e armazenamento blob, e outras fontes de dados de armazenamento não-Azure, como uma base de dados ou o sistema de ficheiros.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Fundir ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transações for importante para a sua solução, considere redesenhar as suas tabelas para permitir a utilização de EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades indexadas para permitir pesquisas eficientes que devolvam listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de mesa indexa automaticamente as entidades utilizando os `PartitionKey` valores e `RowKey` valores. Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando uma consulta de ponto. Por exemplo, utilizando a seguinte estrutura de tabela, uma aplicação do cliente pode recuperar eficientemente uma entidade de colaborador individual utilizando o nome do departamento e o ID do funcionário (o `PartitionKey` e `RowKey` ).  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Se também quiser ser capaz de recuperar uma lista de entidades de colaboradores com base no valor de outra propriedade não única, como o apelido, deve utilizar uma verificação de partição menos eficiente. Esta digitalização encontra fósforos, em vez de usar um índice para os procurar diretamente. Isto porque o armazenamento de mesa não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para ativar o apelido com a estrutura da entidade anterior, deve manter as listas de IDs dos colaboradores. Se quiser recuperar as entidades de empregados com um apelido particular, como o Jones, tem primeiro de localizar a lista de identificações dos empregados para funcionários com o Jones como apelido e, em seguida, recuperar essas entidades de empregados. Existem três opções principais para armazenar as listas de IDs dos funcionários:  

* Use o armazenamento Blob.  
* Criar entidades indexárias na mesma divisão que as entidades de trabalho.  
* Criar entidades indexárias numa divisória ou tabela separada.  

Opção 1: Utilizar o armazenamento blob  

Crie uma bolha para cada apelido único, e em cada blob guarde uma lista dos `PartitionKey` valores (departamento) e `RowKey` (ID do empregado) para os funcionários que tenham esse apelido. Quando adicionar ou eliminar um empregado, certifique-se de que o conteúdo da bolha relevante é eventualmente consistente com as entidades do colaborador.  

Opção 2: Criar entidades indexárias na mesma partição  

Utilize entidades indexárias que armazenam os seguintes dados:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados" e `RowKey` valor "Jones"). O EGT faz isso adicionando o novo ID do funcionário à lista no campo Dos Colaboradores. Para obter mais informações sobre EGTs, consulte [as transações do grupo Entity](#entity-group-transactions).  
3. Se o EGT falhar devido a um erro de concordância otimista (ou seja, alguém modificou a entidade de índice), então tem de começar de novo no passo 1.  

Pode usar uma abordagem semelhante para eliminar um empregado se estiver a usar a segunda opção. Mudar o apelido de um colaborador é um pouco mais complexo, pois é necessário executar um EGT que atualiza três entidades: a entidade de colaboradores, a entidade indexa para o apelido antigo e a entidade indexa para o novo apelido. Tem de recuperar cada entidade antes de efetuar quaisquer alterações, de modo a recuperar os valores ETag que pode utilizar para executar as atualizações utilizando a concordância otimista.  

Os seguintes passos descrevem o processo que deve seguir quando precisa de procurar todos os colaboradores com um sobrenome particular num departamento. Neste exemplo, estamos a procurar todos os empregados com apelido Jones no departamento de vendas:  

1. Recupere a entidade indexa com um `PartitionKey` valor "Vendas", e o `RowKey` valor "Jones".  
2. Analise a lista de identificações dos empregados no `EmployeeIDs` terreno.  
3. Se precisar de informações adicionais sobre cada um destes colaboradores (como os seus endereços de e-mail), recupere cada uma das entidades colaboradoras utilizando `PartitionKey` o valor "Vendas", e `RowKey` valores da lista de colaboradores que obteve na 2.º etapa.  

Opção 3: Criar entidades indexárias numa divisória ou tabela separada  

Para esta opção, utilize entidades indexárias que armazenem os seguintes dados:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

A `EmployeeIDs` propriedade contém uma lista de IDs de empregados para funcionários com o último nome armazenado no e `RowKey` `PartitionKey` .  

Não pode usar EGTs para manter a consistência, porque as entidades indexam-se numa divisão separada das entidades dos colaboradores. Certifique-se de que as entidades indexam eventualmente são consistentes com as entidades dos colaboradores.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades indexam para obter a lista de `RowKey` valores e, em seguida, consultas para recuperar cada entidade na lista.  
* Como uma entidade individual tem um tamanho máximo de 1 MB, a opção 2 e a opção 3 na solução assumem que a lista de IDs dos empregados para qualquer apelido em particular nunca é superior a 1 MB. Se a lista de IDs dos empregados for provavelmente superior a 1 MB de tamanho, use a opção 1 e guarde os dados do índice no armazenamento blob.  
* Se utilizar a opção 2 (utilizando EGTs para lidar com a adição e eliminação de funcionários e alterando o apelido de um empregado), deve avaliar se o volume de transações se aproximará dos limites de escalabilidade numa determinada divisão. Se for esse o caso, deve considerar uma solução eventualmente consistente (opção 1 ou opção 3). Estas utilizam filas para lidar com os pedidos de atualização e permitem armazenar as suas entidades indexárias numa divisão separada das entidades dos colaboradores.  
* A opção 2 nesta solução pressupõe que pretende procurar pelo apelido dentro de um departamento. Por exemplo, quer recuperar uma lista de empregados com apelido Jones no departamento de vendas. Se quiser ser capaz de procurar todos os empregados com um sobrenome Jones em toda a organização, use a opção 1 ou a opção 3.
* Pode implementar uma solução baseada em filas que proporciona uma eventual consistência. Para obter mais detalhes, consulte o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando quiser procurar um conjunto de entidades que todos partilham um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados numa única entidade para que possa recuperar todos os dados de que necessita com uma única consulta de ponto.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente normaliza os dados para remover a duplicação que ocorre quando as consultas recuperam dados de várias tabelas. Se normalizar os seus dados nas tabelas Azure, deve efetuar várias viagens de ida e volta do cliente ao servidor para recuperar os seus dados relacionados. Por exemplo, com a seguinte estrutura de mesa, você precisa de duas viagens de ida e volta para recuperar os detalhes para um departamento. Uma viagem vai buscar a entidade do departamento que inclui o ID do gerente, e a segunda viagem recolhe os detalhes do gestor numa entidade de empregados.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades distintas, desnormalizar os dados e guardar uma cópia dos detalhes do gestor na entidade do departamento. Por exemplo:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Com entidades de departamento armazenadas com estas propriedades, você pode agora recuperar todos os detalhes que você precisa sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Há algumas despesas associadas ao armazenamento de alguns dados duas vezes. O benefício de desempenho resultante de menos pedidos de armazenamento de mesa normalmente supera o aumento marginal dos custos de armazenamento. Além disso, este custo é parcialmente compensado por uma redução do número de transações que você precisa para obter os detalhes de um departamento.  
* Deve manter a consistência das duas entidades que armazenam informação sobre os gestores. Pode lidar com a questão da consistência utilizando EGTs para atualizar várias entidades numa única transação atómica. Neste caso, a entidade do departamento e a entidade de empregados do gerente do departamento estão armazenadas na mesma divisão.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisar de procurar informações relacionadas. Este padrão reduz o número de consultas que o seu cliente deve fazer para recuperar os dados que necessita.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composto
Utilize `RowKey` valores compostos para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é natural usar juntas em consultas para devolver peças de dados relacionadas ao cliente numa única consulta. Por exemplo, pode usar o ID do empregado para procurar uma lista de entidades relacionadas que contêm dados de desempenho e revisão para esse funcionário.  

Assuma que está a armazenar entidades de empregados no armazenamento de mesa utilizando a seguinte estrutura:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Também precisa de armazenar dados históricos relativos a avaliações e desempenho para cada ano que o colaborador tenha trabalhado para a sua organização, e precisa de ter acesso a esta informação por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Com esta abordagem, poderá decidir duplicar algumas informações (como o primeiro nome e apelido) na nova entidade, para que possa recuperar os seus dados com um único pedido. No entanto, não é possível manter uma forte consistência porque não pode usar um EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Armazenar um novo tipo de entidade na sua tabela original utilizando entidades com a seguinte estrutura:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Note como `RowKey` a é agora uma chave composta, composta pela identificação do empregado e pelo ano dos dados de revisão. Isto permite-lhe recuperar o desempenho do colaborador e rever os dados com um único pedido para uma única entidade.  

O exemplo a seguir descreve como pode recuperar todos os dados de revisão para um determinado empregado (como o empregado 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000123') e (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um carácter separador adequado que facilite a análise do `RowKey` valor: por exemplo, **000123_2012**.  
* Também está a armazenar esta entidade na mesma divisão que outras entidades que contêm dados relacionados para o mesmo funcionário. Isto significa que pode usar EGTs para manter uma forte consistência.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é apropriado. Por exemplo, se aceder aos dados de revisão com pouca frequência, e os principais dados dos colaboradores frequentemente, deverá mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisar de armazenar uma ou mais entidades relacionadas que consulta com frequência.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidades heterogéneas](#work-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de cauda de log
Recupere as entidades *n* mais recentemente adicionadas a uma partição utilizando um `RowKey` valor que se classifica em ordem de data e hora inversa.  

> [!NOTE]
> Os resultados de consulta devolvidos pela Azure Table API em Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Assim, embora este padrão seja adequado para armazenamento de mesa, não é adequado para Azure Cosmos DB. Para obter uma lista detalhada das diferenças de características, consulte [as diferenças entre a Tabela API em Azure Cosmos DB e O Armazenamento de MesaS Azure](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é poder recuperar as entidades mais recentemente criadas, por exemplo, os dez pedidos de despesas mais recentes apresentados por um trabalhador. As consultas de tabela suportam uma `$top` operação de consulta para devolver as primeiras *entidades n* de um conjunto. Não há nenhuma operação de consulta equivalente para devolver as últimas entidades *n* num conjunto.  

#### <a name="solution"></a>Solução
Armazenar as entidades utilizando uma `RowKey` que naturalmente classifica em ordem de data/hora inversa, pelo que a entrada mais recente é sempre a primeira na tabela.  

Por exemplo, para poder recuperar os dez pedidos de despesas mais recentes apresentados por um empregado, pode utilizar um valor de tique reverso derivado da data/hora corrente. A amostra de código C# que se segue mostra uma forma de criar um valor adequado de "tiques invertidos" para um `RowKey` que se separa do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar ao valor da data/hora utilizando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta da mesa é assim:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve remar o valor do tique-taque invertido com os principais zeros, para garantir que o valor da corda se classifica como esperado.  
* Deve estar ciente dos alvos de escalabilidade ao nível de uma partição. Tenha cuidado para não criar divisórias de ponto quente.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisar de aceder a entidades em ordem de data/hora inversa, ou quando precisar de aceder às entidades mais recentemente adicionadas.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Prepend / apêndio anti-padrão](#prepend-append-anti-pattern)  
* [Recuperar entidades](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de eliminação de volume elevado
Permitir a supressão de um elevado volume de entidades armazenando todas as entidades para a supressão simultânea na sua própria tabela separada. Apaga-se as entidades apagando a mesa.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitas aplicações eliminam dados antigos que já não precisam de estar disponíveis para uma aplicação de cliente, ou que a aplicação tenha arquivado para outro suporte de armazenamento. Normalmente identifica esses dados por data. Por exemplo, tem a obrigação de apagar registos de todos os pedidos de inscrição com mais de 60 dias.  

Um dos projetos possíveis é utilizar a data e a hora do pedido de inscrição no `RowKey` :  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Esta abordagem evita hotspots de partição, porque a aplicação pode inserir e eliminar entidades de inscrição para cada utilizador numa divisória separada. No entanto, esta abordagem pode ser dispendiosa e morosa se tiver um grande número de entidades. Primeiro, é necessário efetuar uma verificação de tabelas para identificar todas as entidades para eliminar, e depois deve eliminar cada entidade antiga. Pode reduzir o número de viagens de ida e volta ao servidor necessárias para eliminar as entidades antigas, desemargando vários pedidos de eliminação em EGTs.  

#### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de inscrição. Pode utilizar o design da entidade anterior para evitar hotspots quando estiver a inserir entidades. A supressão de entidades idosas é agora apenas uma questão de apagar uma mesa todos os dias (uma única operação de armazenamento), em vez de encontrar e apagar centenas e milhares de entidades individuais de inscrição todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas de a sua aplicação utilizar os dados, tais como procurar entidades específicas, ligar-se a outros dados ou gerar informação agregada?  
* O seu design evita pontos quentes quando está a inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de mesa depois de o apagar. É melhor usar sempre nomes de mesa únicos.  
* Espere alguma limitação de tarifas quando usar uma nova tabela, enquanto o armazenamento de mesa aprende os padrões de acesso e distribui as divisórias através dos nós. Deve considerar a frequência com que necessita para criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando tiver um grande volume de entidades que deve apagar ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Modificar entidades](#modify-entities)  

### <a name="data-series-pattern"></a>Padrão da série de dados
Armazenar séries de dados completas numa única entidade para minimizar o número de pedidos que es faz.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é uma aplicação para armazenar uma série de dados que normalmente precisa para recuperar tudo de uma vez. Por exemplo, a sua aplicação pode registar quantas mensagens IM cada funcionário envia a cada hora e, em seguida, usar esta informação para traçar quantas mensagens cada utilizador enviou nas 24 horas anteriores. Um dos projetos poderá ser armazenar 24 entidades para cada colaborador:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Com este design, pode localizar e atualizar facilmente a entidade para atualizar para cada colaborador sempre que a aplicação precisar de atualizar o valor da contagem de mensagens. No entanto, para recuperar a informação para traçar um gráfico da atividade nas 24 horas anteriores, deve recuperar 24 entidades.  

#### <a name="solution"></a>Solução
Utilize o seguinte design, com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Com este design, pode utilizar uma operação de fusão para atualizar a contagem de mensagens para um empregado durante uma hora específica. Agora, pode recuperar toda a informação que precisa para traçar o gráfico usando um pedido para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se a sua série completa de dados não se encaixar numa única entidade (uma entidade pode ter até 252 propriedades), utilize uma loja de dados alternativa, como uma bolha.  
* Se tiver vários clientes a atualizar uma entidade simultaneamente, utilize o **ETag** para implementar uma concordância otimista. Se tiver muitos clientes, pode sentir grande contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de atualizar e recuperar uma série de dados associada a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Fundir ou substituir](#merge-or-replace)  
* [Eventualmente padrão de transações consistentes](#eventually-consistent-transactions-pattern) (se estiver a armazenar a série de dados numa bolha)  

### <a name="wide-entities-pattern"></a>Padrão de entidades amplas
Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode ter mais de 252 propriedades (excluindo as propriedades obrigatórias do sistema), e não pode armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente, trabalharia em torno de quaisquer limites do tamanho de uma linha adicionando uma nova tabela, e aplicando uma relação de 1 a 1 entre eles.  

#### <a name="solution"></a>Solução
Ao utilizar o armazenamento de mesa, pode armazenar várias entidades para representar um único objeto de negócio grande com mais de 252 propriedades. Por exemplo, se pretender armazenar uma contagem do número de mensagens IM enviadas por cada colaborador nos últimos 365 dias, pode utilizar o seguinte design que utiliza duas entidades com esquemas diferentes:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Se precisar de fazer uma alteração que exija atualizar ambas as entidades para mantê-las sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma única operação de fusão para atualizar a contagem de mensagens para um dia específico. Para recuperar todos os dados para um funcionário individual, deve recuperar ambas as entidades. Pode fazê-lo com dois pedidos eficientes que usam um `PartitionKey` valor e um `RowKey` valor.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere o seguinte ponto ao decidir como implementar este padrão:  

* Recuperar uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho ou número de propriedades exceda os limites para uma entidade individual no armazenamento de mesa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Fundir ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de grandes entidades
Use o armazenamento Blob para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se um ou vários dos valores da loja das suas propriedades que fazem com que o tamanho total da sua entidade exceda este valor, não pode armazenar toda a entidade no armazenamento de Mesa.  

#### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no armazenamento Blob e, em seguida, armazenar o endereço da bolha numa propriedade da entidade. Por exemplo, pode armazenar a foto de um empregado no armazém da Blob e armazenar um link para a foto na `Photo` propriedade da sua entidade colaboradora:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter uma eventual consistência entre a entidade no armazenamento de mesa e os dados no armazenamento blob, utilize o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho exceda os limites para uma entidade individual no armazenamento de mesa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades amplas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Prepend/apêndice anti-padrão
Quando tiver um grande volume de inserções, aumente a escalabilidade espalhando as inserções através de múltiplas divisórias.  

#### <a name="context-and-problem"></a>Contexto e problema
A premissão ou a anexação de entidades às suas entidades armazenadas normalmente resulta na adição de novas entidades à primeira ou última partição de uma sequência de divisórias. Neste caso, todas as inserções em qualquer momento estão a decorrer na mesma partição, criando um hotspot. Isto impede o armazenamento da mesa de inserções de equilíbrio de carga em vários nós, e possivelmente faz com que a sua aplicação atinja os alvos de escalabilidade para a partição. Por exemplo, considere o caso de uma aplicação que regista o acesso à rede e ao acesso de recursos por parte dos colaboradores. Uma estrutura de entidades como a seguinte pode resultar em que a partição da hora atual se torne um hotspot, se o volume de transações atingir o objetivo de escalabilidade para uma partição individual:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição específica, uma vez que a aplicação regista eventos:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Note com este exemplo como ambas as `PartitionKey` `RowKey` teclas e são compostas. Usa `PartitionKey` o departamento e a identificação dos funcionários para distribuir o registo através de várias divisórias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa que evita a criação de divisórias quentes em inserções suporta de forma eficiente as consultas que a sua aplicação do cliente faz?  
* O seu volume previsto de transações significa que é provável que atinja os objetivos de escalabilidade para uma divisória individual e seja estrangulado pelo armazenamento da mesa?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o anti-padrão pré-preento/apêndice quando o seu volume de transações é suscetível de resultar na limitação da taxa pelo armazenamento da mesa quando aceder a uma divisória quente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificar entidades](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Registar dados anti-padrão
Normalmente, deve utilizar o armazenamento blob em vez do armazenamento de mesa para armazenar dados de registo.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de utilização comum para os dados de registo é recuperar uma seleção de entradas de registo para um intervalo específico de data/hora. Por exemplo, pretende encontrar todos os erros e mensagens críticas que a sua aplicação registou entre as 15:04 e as 15:06 numa data específica. Não pretende utilizar a data e a hora da mensagem de registo para determinar a partição para a qual guarda entidades de registo. Isto resulta numa partição quente porque em qualquer momento específico, todas as entidades de log partilharão o mesmo `PartitionKey` valor (ver [prepend/append anti-padrão).](#prepend-append-anti-pattern) Por exemplo, o esquema de entidade a seguir para uma mensagem de registo resulta numa partição quente, porque a aplicação escreve todas as mensagens de registo para a partição para a data e hora em atuais:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

Neste exemplo, `RowKey` inclui a data e a hora da mensagem de registo para garantir que as mensagens de registo são classificadas na ordem data/hora. O `RowKey` também inclui um ID de mensagem, caso várias mensagens de registo partilhem a mesma data e hora.  

Outra abordagem é usar uma `PartitionKey` que garanta que a aplicação escreve mensagens em várias divisórias. Por exemplo, se a origem da mensagem de registo fornecer uma forma de distribuir mensagens em muitas divisórias, pode utilizar o seguinte esquema de entidade:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Gráfico mostrando uma entidade de departamento e uma entidade de empregados":::

No entanto, o problema com este esquema é que para recuperar todas as mensagens de registo por um período de tempo específico, você deve pesquisar todas as divisões na tabela.

#### <a name="solution"></a>Solução
A secção anterior destacou o problema de tentar utilizar o armazenamento de mesa para armazenar entradas de registo, e sugeriu dois desenhos insatisfatórios. Uma solução levou a uma divisória quente com o risco de mau desempenho escrevendo mensagens de registo. A outra solução resultou num fraco desempenho de consulta, devido à exigência de digitalizar todas as divisórias da tabela para recuperar mensagens de registo para um período de tempo específico. O armazenamento blob oferece uma melhor solução para este tipo de cenário, e é assim que a Azure Storage analytics armazena os dados de registo que recolhe.  

Esta secção descreve como a Analytics de Armazenamento armazena dados de registo no armazenamento blob, como uma ilustração desta abordagem para armazenar dados que normalmente consulta por gama.  

A análise de armazenamento armazena mensagens de registo num formato delimitado em várias bolhas. O formato delimitado facilita a análise dos dados na mensagem de registo.  

A análise de armazenamento utiliza uma convenção de nomeação para bolhas que lhe permite localizar a bolha (ou bolhas) que contêm as mensagens de registo para as quais está a pesquisar. Por exemplo, uma bolha chamada "queue/2014/07/31/1800/000001.log" contém mensagens de registo que dizem respeito ao serviço de fila para a hora a partir das 18:00 de 31 de julho de 2014. O "000001" indica que este é o primeiro ficheiro de registo deste período. A análise de armazenamento também regista os termos tempotamos das primeira e últimas mensagens de registo armazenadas no ficheiro, como parte dos metadados da bolha. A API para armazenamento Blob permite localizar bolhas num recipiente com base num prefixo de nome. Para localizar todas as bolhas que contêm dados de registo de fila para a hora a partir das 18:00, pode utilizar o prefixo "fila/2014/07/31/1800".  

Os buffers de análise de armazenamento registam as mensagens internamente e, em seguida, atualiza periodicamente a bolha apropriada ou cria uma nova com o mais recente lote de entradas de registo. Isto reduz o número de escritos que deve executar para o armazenamento blob.  

Se estiver a implementar uma solução semelhante na sua própria aplicação, considere como gerir o trade-off entre fiabilidade e custo e escalabilidade. Por outras palavras, avalie o efeito de escrever cada entrada de registo no armazenamento blob à medida que acontece, em comparação com as atualizações de tamponamento na sua aplicação e escrevendo-as para o armazenamento blob em lotes.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de mesa que evite potenciais divisórias quentes, poderá descobrir que não pode aceder aos seus dados de registo de forma eficiente.  
* Para processar dados de registo, um cliente precisa muitas vezes de carregar muitos registos.  
* Embora os dados de registo sejam muitas vezes estruturados, o armazenamento blob pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta secção discute algumas das considerações a ter em conta quando implementa os padrões descritos nas secções anteriores. A maior parte desta secção utiliza exemplos escritos em C# que utilizam a Biblioteca do Cliente de Armazenamento (versão 4.3.0 no momento da escrita).  

### <a name="retrieve-entities"></a>Recuperar entidades
Como discutido na secção [Design para consulta,](#design-for-querying)a consulta mais eficiente é uma consulta pontual. No entanto, em alguns cenários poderá ser necessário recuperar várias entidades. Esta secção descreve algumas abordagens comuns para recuperar entidades utilizando a Biblioteca do Cliente de Armazenamento.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Executar uma consulta de pontos utilizando a Biblioteca do Cliente de Armazenamento
A maneira mais fácil de executar uma consulta de ponto é utilizar a operação da mesa **Recuperar.** Como mostrado no seguinte corte de código C#, esta operação recupera uma entidade com um `PartitionKey` valor "Vendas", e um `RowKey` de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Note como este exemplo espera que a entidade que recupera seja do tipo `EmployeeEntity` .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperar várias entidades utilizando o LINQ
Pode recuperar várias entidades utilizando o LINQ com a Biblioteca do Cliente de Armazenamento e especificando uma consulta com uma cláusula **onde.** Para evitar uma digitalização de tabela, deve incluir sempre o `PartitionKey` valor na cláusula "onde" e, se possível, o `RowKey` valor para evitar sondagens de mesa e partição. O armazenamento de mesa suporta um conjunto limitado de operadores de comparação (maiores do que iguais ou iguais, menos do que, menos ou iguais, iguais e não iguais) para serem utilizados na cláusula em que. O seguinte código de corte C# encontra todos os empregados cujo apelido começa por "B" (assumindo que as `RowKey` lojas o último nome) no departamento de Vendas (assumindo que as `PartitionKey` lojas o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Note como a consulta especifica tanto a `RowKey` a como a para garantir um melhor `PartitionKey` desempenho.  

A amostra de código que se segue mostra uma funcionalidade equivalente utilizando a API fluente (para obter mais informações sobre APIs fluentes em geral, consulte [as melhores práticas para a conceção de uma API fluente):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

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
> A amostra aninha vários `CombineFilters` métodos para incluir as três condições do filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperar um grande número de entidades a partir de uma consulta
Uma consulta ideal devolve uma entidade individual com base num `PartitionKey` valor e num `RowKey` valor. No entanto, em alguns cenários poderá ter a obrigação de devolver muitas entidades da mesma partição, ou mesmo de muitas divisórias. Deve sempre testar totalmente o desempenho da sua aplicação nesses cenários.  

Uma consulta contra o armazenamento de mesa pode devolver um máximo de 1.000 entidades de uma vez, e funcionar por um máximo de cinco segundos. O armazenamento de mesa devolve um token de continuação para permitir que o pedido do cliente solicite o próximo conjunto de entidades, se alguma das seguintes forem verdadeiras:

- O conjunto de resultados contém mais de 1.000 entidades.
- A consulta não terminou em cinco segundos.
- A consulta cruza o limite da partição. 

Para obter mais informações sobre como funcionam os tokens de continuação, consulte [o tempo limite de consulta e a paginação.](https://msdn.microsoft.com/library/azure/dd135718.aspx)  

Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, pode lidar automaticamente com fichas de continuação para si, uma vez que devolve entidades do armazenamento de mesa. Por exemplo, a seguinte amostra de código C# lida automaticamente com fichas de continuação se o armazenamento da mesa os devolver numa resposta:  

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

O seguinte código C# lida explicitamente com fichas de continuação:  

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

Ao utilizar tokens de continuação explicitamente, pode controlar quando a sua aplicação recupera o próximo segmento de dados. Por exemplo, se a aplicação do seu cliente permitir que os utilizadores a página através das entidades armazenadas numa tabela, um utilizador pode decidir não páginar através de todas as entidades recuperadas pela consulta. A sua aplicação apenas usaria um token de continuação para recuperar o segmento seguinte quando o utilizador tivesse terminado de ligar todas as entidades do segmento atual. Esta abordagem tem vários benefícios:  

* Pode limitar a quantidade de dados a obter do armazenamento de mesa e deslocar-se pela rede.  
* Pode executar I/O assíncronos em .NET.  
* Você pode serializar o token de continuação para armazenamento persistente, para que você possa continuar em caso de quebra de aplicação.  

> [!NOTE]
> Um símbolo de continuação normalmente devolve um segmento contendo 1.000 entidades, embora possa conter menos. Este é também o caso se limitar o número de entradas que uma consulta devolve usando **Take** para devolver as primeiras entidades n que correspondem aos seus critérios de procura. O armazenamento de mesas poderá devolver um segmento que contenha menos do que as entidades n, juntamente com um token de continuação para que possa recuperar as restantes entidades.  
> 
> 

O seguinte código C# mostra como modificar o número de entidades devolvidas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ter até 1 MB de tamanho. Quando consulta a tabela e recupera entidades, pode não precisar de todas as propriedades, e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode utilizar a projeção do lado do servidor para transferir apenas as propriedades necessárias. O exemplo a seguir recupera apenas a `Email` propriedade (juntamente `PartitionKey` `RowKey` com, `Timestamp` e ) das `ETag` entidades selecionadas pela consulta.  

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

Note como o `RowKey` valor está disponível mesmo que não esteja incluído na lista de propriedades para recuperar.  

### <a name="modify-entities"></a>Modificar entidades
A Biblioteca do Cliente de Armazenamento permite modificar as suas entidades armazenadas no armazenamento de mesa, inserindo, eliminando e atualizando entidades. Pode utilizar EGTs para lotar várias inserções, atualizar e eliminar operações em conjunto, para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da sua solução.  

As exceções lançadas quando a Biblioteca do Cliente de Armazenamento executa um EGT normalmente incluem o índice da entidade que fez com que o lote falhasse. Isto é útil quando está a depurar código que utiliza EGTs.  

Também deve considerar como o seu design afeta a forma como a sua aplicação de cliente lida com as operações de conuncy e atualização.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o armazenamento de mesa implementa controlos de concordância otimistas ao nível de entidades individuais para inserir, fundir e apagar operações, embora seja possível para um cliente forçar o armazenamento da Mesa a contornar estas verificações. Para obter mais informações, consulte [Managing concurrency no Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Fundir ou substituir
O `Replace` método da classe substitui sempre a entidade completa no armazenamento de `TableOperation` mesa. Se não incluir um imóvel no pedido quando esse imóvel existe na entidade armazenada, o pedido retira esse imóvel da entidade armazenada. A menos que queira remover um imóvel explicitamente de uma entidade armazenada, deve incluir todos os bens no pedido.  

Pode utilizar o `Merge` método da classe para reduzir a quantidade de `TableOperation` dados que envia para o armazenamento de mesa quando pretende atualizar uma entidade. O `Merge` método substitui quaisquer propriedades na entidade armazenada por valores patrimoniais da entidade incluída no pedido. Este método deixa intactas quaisquer propriedades na entidade armazenada que não estejam incluídas no pedido. Isto é útil se você tem grandes entidades, e apenas precisa atualizar um pequeno número de propriedades em um pedido.  

> [!NOTE]
> Os `*Replace` `Merge` métodos falham se a entidade não existir. Como alternativa, pode usar os `InsertOrReplace` `InsertOrMerge` métodos e métodos que criam uma nova entidade se ela não existir.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidades heterogéneas
O armazenamento de mesa é uma loja de mesa *sem esquemas.* Isto significa que uma única mesa pode armazenar entidades de vários tipos, proporcionando uma grande flexibilidade no seu design. O exemplo a seguir ilustra uma tabela que armazena tanto entidades de empregados como de departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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
<th>E-mail</th>
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
<th>E-mail</th>
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
<th>Contagem de Funcionários</th>
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
<th>E-mail</th>
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

Cada entidade ainda deve `PartitionKey` ter, `RowKey` e `Timestamp` valores, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada que indique o tipo de entidade a menos que escolha armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Prepend o tipo de entidade para o `RowKey` (ou possivelmente o `PartitionKey` ). Por exemplo, `EMPLOYEE_000123` ou `DEPARTMENT_SALES` como `RowKey` valores.  
* Utilize uma propriedade separada para registar o tipo de entidade, como mostrado na tabela seguinte.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>Contagem de Funcionários</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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

A primeira opção, pré-pendente do tipo de entidade ao `RowKey` , é útil se houver a possibilidade de duas entidades de diferentes tipos poderem ter o mesmo valor-chave. Também agru se junta entidades do mesmo tipo na partição.  

As técnicas discutidas nesta secção são especialmente relevantes para a discussão sobre[as relações de herança.](#inheritance-relationships)  

> [!NOTE]
> Considere incluir um número de versão no valor do tipo de entidade, para permitir que as aplicações do cliente evoluam objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta secção descreve algumas das funcionalidades na Biblioteca do Cliente de Armazenamento que facilitam o funcionamento com vários tipos de entidades na mesma tabela.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperar tipos de entidades heterogéneas
Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, tem três opções para trabalhar com vários tipos de entidades.  

Se conhecer o tipo de entidade armazenada com `RowKey` valores e `PartitionKey` valores específicos, então pode especificar o tipo de entidade quando recuperar a entidade. Viu isto nos dois exemplos anteriores que recuperam entidades do tipo : Executar uma consulta de `EmployeeEntity` ponto utilizando a Biblioteca do Cliente de [Armazenamento](#run-a-point-query-by-using-the-storage-client-library) e [Recuperar várias entidades utilizando o LINQ.](#retrieve-multiple-entities-by-using-linq)  

A segunda opção é usar o `DynamicTableEntity` tipo (um saco de propriedade), em vez de um tipo de entidade POCO de betão. Esta opção também pode melhorar o desempenho, porque não há necessidade de serializar e deserizar a entidade para os tipos .NET. O seguinte código C# potencialmente recupera várias entidades de diferentes tipos da tabela, mas devolve todas as entidades como `DynamicTableEntity` instâncias. Em seguida, utiliza a `EntityType` propriedade para determinar o tipo de cada entidade:  

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

Para recuperar outras propriedades, deve utilizar o `TryGetValue` método na propriedade da `Properties` `DynamicTableEntity` classe.  

Uma terceira opção é combinar usando o `DynamicTableEntity` tipo e um `EntityResolver` caso. Isto permite-lhe resolver vários tipos de POCO na mesma consulta. Neste exemplo, o `EntityResolver` delegado está a usar o `EntityType` imóvel para distinguir entre os dois tipos de entidade que a consulta devolve. O `Resolve` método utiliza o delegado para resolver `resolver` `DynamicTableEntity` casos em `TableEntity` casos.  

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
Não é preciso saber o tipo de entidade para o apagar, e sabe sempre o tipo de entidade quando a insere. No entanto, pode utilizar o `DynamicTableEntity` tipo para atualizar uma entidade sem conhecer o seu tipo, e sem utilizar uma classe de entidade POCO. A amostra de código que se segue recupera uma única entidade e verifica se a `EmployeeCount` propriedade existe antes de a atualizar.  

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
Pode utilizar fichas de assinatura de acesso partilhado (SAS) para permitir que as aplicações do cliente modifiquem (e questionem) entidades de mesa diretamente, sem necessidade de autenticar diretamente com o armazenamento da Mesa. Normalmente, existem três principais benefícios para a utilização de SAS na sua aplicação:  

* Não é necessário distribuir a chave da sua conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) para permitir que esse dispositivo aceda e modifique entidades no armazenamento de mesa.  
* Pode descarregar parte do trabalho que as funções web e trabalhadora desempenham na gestão das suas entidades. Pode descarregar para dispositivos clientes, como computadores de utilizador final e dispositivos móveis.  
* Pode atribuir um conjunto limitado de permissões a um cliente (como permitir o acesso apenas à leitura a recursos específicos).  

Para obter mais informações sobre a utilização de fichas SAS com armazenamento de mesa, consulte [utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, ainda deve gerar os tokens SAS que concedem um pedido de cliente às entidades de armazenamento de mesa. Faça isto num ambiente que tenha acesso seguro às chaves da sua conta de armazenamento. Normalmente, usa uma função web ou trabalhadora para gerar os tokens SAS e entregá-los às aplicações do cliente que precisam de acesso às suas entidades. Como ainda existe uma sobrecarga envolvida na geração e entrega de fichas SAS aos clientes, deve considerar a melhor forma de reduzir esta sobrecarga, especialmente em cenários de grande volume.  

É possível gerar um token SAS que dá acesso a um subconjunto das entidades numa tabela. Por padrão, cria-se um token SAS para uma mesa inteira. Mas também é possível especificar que o símbolo SAS concede acesso a uma gama de `PartitionKey` valores, ou a uma gama de `PartitionKey` `RowKey` valores. Pode optar por gerar fichas SAS para utilizadores individuais do seu sistema, de modo a que o token SAS de cada utilizador apenas lhes permita aceder às suas próprias entidades no armazenamento de mesa.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronos e paralelas
Desde que esteja a difundir os seus pedidos através de múltiplas divisórias, pode melhorar a produção e a capacidade de resposta do cliente utilizando consultas assíncronos ou paralelas.
Por exemplo, pode ter duas ou mais instâncias de trabalho a aceder em paralelo às suas tabelas. Pode ter funções individuais de trabalhadores responsáveis por determinados conjuntos de divisórias, ou simplesmente ter múltiplas instâncias de função dos trabalhadores, cada uma capaz de aceder a todas as divisórias numa tabela.  

Dentro de uma instância do cliente, você pode melhorar a produção executando operações de armazenamento assíncroneamente. A Biblioteca do Cliente de Armazenamento facilita a escrita de consultas e modificações assíncronos. Por exemplo, pode começar pelo método sincronizado que recupera todas as entidades numa partição, como mostra o seguinte código C#:  

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

Pode modificar facilmente este código de modo a que a consulta seja assíncronea, da seguinte forma:  

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

Neste exemplo assíncronos, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método agora inclui o `async` modificador, e devolve uma `Task` instância.  
* Em vez de chamar o `ExecuteSegmented` método para recuperar resultados, o método agora chama o `ExecuteSegmentedAsync` método. O método utiliza o `await` modificador para obter resultados assíncronos.  

A aplicação do cliente pode chamar este método várias vezes, com valores diferentes para o `department` parâmetro. Cada consulta funciona num fio separado.  

Não existe uma versão assíncronea do `Execute` método na `TableQuery` classe, porque a interface não suporta `IEnumerable` a enumeração assíncronea.  

Também pode inserir, atualizar e eliminar asincronizando asinerróneas. O exemplo C# a seguir mostra um método simples e sincronizado para inserir ou substituir uma entidade do empregado:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode modificar facilmente este código de modo a que a atualização seja assíncronea, da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncronos, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método agora inclui o `async` modificador, e devolve uma `Task` instância.  
* Em vez de chamar o `Execute` método para atualizar a entidade, o método agora chama o `ExecuteAsync` método. O método utiliza o `await` modificador para obter resultados assíncronos.  

A aplicação do cliente pode chamar vários métodos assíncronos como este, e cada invocação de método corre em um fio separado.  

