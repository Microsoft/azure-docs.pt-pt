---
title: Projetar Azure Cosmos DB tabelas para dimensionamento e desempenho
description: 'Guia de design do armazenamento de tabelas do Azure: tabelas escalonáveis e de alto desempenho no Azure Cosmos DB e no armazenamento de tabelas do Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 74bd22de81e385a4fbd9129a70616e24b594b0b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441325"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guia de design da tabela de armazenamento de tabelas do Azure: tabelas escalonáveis e de alto desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para criar tabelas escalonáveis e de alto desempenho, você deve considerar uma variedade de fatores, incluindo custo. Se você tiver criado anteriormente esquemas para bancos de dados relacionais, essas considerações serão familiares para você. Mas embora haja algumas semelhanças entre o armazenamento de tabelas do Azure e os modelos relacionais, também há muitas diferenças importantes. Essas diferenças normalmente levam a diferentes designs que podem parecer um contador intuitivo ou errado para alguém familiarizado com bancos de dados relacionais, mas isso faz sentido se você estiver criando um repositório de chave/valor NoSQL, como o armazenamento de tabelas.

O armazenamento de tabela foi projetado para dar suporte a aplicativos em escala de nuvem que podem conter bilhões de entidades ("linhas" na terminologia do banco de dados relacional) ou para conjuntos de dados que devem dar suporte a grandes volumes de transações. Portanto, você precisa pensar de maneira diferente sobre como armazenar seus dados e entender como funciona o armazenamento de tabelas. Um armazenamento de dados NoSQL bem projetado pode permitir que sua solução seja dimensionada muito além (e com um custo menor) do que uma solução que usa um banco de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-azure-table-storage"></a>Sobre o armazenamento de tabelas do Azure
Esta seção destaca alguns dos principais recursos do armazenamento de tabelas que são especialmente relevantes para o design de desempenho e escalabilidade. Se você for novo no armazenamento do Azure e no armazenamento de tabelas, consulte [introdução ao armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e introdução ao [armazenamento de tabelas do Azure usando o .net](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia seja no armazenamento de tabelas, ele inclui algumas discussões sobre o armazenamento de filas do Azure e o armazenamento de BLOBs do Azure e como você pode usá-los juntamente com o armazenamento de tabelas em uma solução.  

O armazenamento de tabelas usa um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades de entidade. Cada entidade tem um par de chaves para identificá-la exclusivamente e uma coluna de carimbo de data/hora que o armazenamento de tabela usa para controlar quando a entidade foi atualizada pela última vez. O campo de carimbo de data/hora é adicionado automaticamente e você não pode substituir manualmente o carimbo de data/hora por um valor arbitrário. O armazenamento de tabelas usa esse carimbo de data/hora modificado por último (LMT) para gerenciar a simultaneidade otimista.  

> [!NOTE]
> As operações da API REST do armazenamento de tabela também retornam um valor `ETag` que deriva do LMT. Neste documento, os termos ETag e LMT são usados de maneira intercambiável, pois eles se referem aos mesmos dados subjacentes.  
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
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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


Até agora, esse design é semelhante a uma tabela em um banco de dados relacional. As principais diferenças são as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **age**, tem um tipo de dados, como inteiro ou cadeia de caracteres, assim como uma coluna em um banco de dado relacional. No entanto, ao contrário de um banco de dados relacional, a natureza sem esquema do armazenamento de tabelas significa que uma propriedade não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para obter mais informações, consulte [noções básicas sobre o modelo de dados de armazenamento de tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Sua escolha de `PartitionKey` e `RowKey` é fundamental para um bom design de tabela. Cada entidade armazenada em uma tabela deve ter uma combinação exclusiva de `PartitionKey` e `RowKey`. Assim como acontece com as chaves em uma tabela de banco de dados relacional, os valores de `PartitionKey` e `RowKey` são indexados para criar um índice clusterizado que permite buscas rápidas. O armazenamento de tabela, no entanto, não cria índices secundários, portanto, essas são as únicas duas propriedades indexadas (alguns dos padrões descritos posteriormente mostram como você pode contornar essa limitação aparente).  

Uma tabela é composta de uma ou mais partições, e muitas das decisões de design feitas estarão relacionadas à escolha de uma `PartitionKey` adequada e `RowKey` para otimizar sua solução. Uma solução pode consistir apenas em uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução tem várias tabelas. As tabelas ajudam você a organizar logicamente suas entidades e ajudá-lo a gerenciar o acesso aos dados usando listas de controle de acesso. Você pode remover uma tabela inteira usando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Partições da tabela
O nome da conta, o nome da tabela e `PartitionKey` juntos identificam a partição no serviço de armazenamento em que o armazenamento de tabela armazena a entidade. Além de ser parte do esquema de endereçamento para entidades, as partições definem um escopo para transações (consulte a seção mais adiante neste artigo, [Transações de grupo de entidades](#entity-group-transactions)) e formam a base de como as tabelas de armazenamento são dimensionadas. Para obter mais informações sobre partições de tabela, consulte [lista de verificação de desempenho e escalabilidade para armazenamento de tabelas](../storage/tables/storage-performance-checklist.md).  

No armazenamento de tabelas, um nó individual instala uma ou mais partições completas, e o serviço é dimensionado por meio de balanceamento de carga dinâmico de partições entre nós. Se um nó estiver sob carga, o armazenamento de tabela poderá dividir o intervalo de partições atendidas por esse nó em nós diferentes. Quando o tráfego é sublado, o armazenamento de tabela pode mesclar os intervalos de partição de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do armazenamento de tabelas e, em particular, como ele gerencia partições, consulte [armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transações de grupo de entidades
No armazenamento de tabelas, as transações de grupo de entidades (EGTs) são o único mecanismo interno para executar atualizações atômicas em várias entidades. EGTs também são chamados de *transações em lote*. EGTs só pode operar em entidades armazenadas na mesma partição (compartilhando a mesma chave de partição em uma tabela específica), portanto, sempre que você precisar de um comportamento transacional atômico em várias entidades, certifique-se de que essas entidades estejam na mesma partição. Isso geralmente é um motivo para manter vários tipos de entidade na mesma tabela (e partição) e não usar várias tabelas para tipos de entidade diferentes. Um único EGT pode operar no máximo 100 entidades.  Se você enviar vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operem em entidades comuns em EGTs. Caso contrário, você correrá o risco de atrasar o processamento.

O EGTs também introduz uma possível compensação para você avaliar em seu design. O uso de mais partições aumenta a escalabilidade do seu aplicativo, pois o Azure tem mais oportunidades para balancear a carga de solicitações entre nós. Mas isso pode limitar a capacidade de seu aplicativo executar transações atômicas e manter uma consistência forte para seus dados. Além disso, há metas de escalabilidade específicas no nível de uma partição que pode limitar a taxa de transferência de transações que você pode esperar para um único nó.

Para obter mais informações sobre metas de escalabilidade para contas de armazenamento do Azure, consulte [metas de escalabilidade para contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre metas de escalabilidade para o armazenamento de tabelas, consulte [escalabilidade e metas de desempenho para o armazenamento de tabelas](../storage/tables/scalability-targets.md). As seções posteriores deste guia discutem várias estratégias de que o ajudam a gerir as vantagens e desvantagens, como este e descrevem a melhor maneira de escolher a sua chave de partição com base nos requisitos específicos da sua aplicação de cliente de design.  

### <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela a seguir inclui alguns dos principais valores a serem considerados quando você está criando uma solução de armazenamento de tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas numa conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de partições numa tabela |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de entidades numa partição |Limitado apenas pela capacidade da conta de armazenamento. |
| Tamanho de uma entidade individual |Até 1 MB, com um máximo de 255 Propriedades (incluindo o `PartitionKey`, `RowKey`e `Timestamp`). |
| Tamanho do `PartitionKey` |Uma cadeia de caracteres de até 1 KB de tamanho. |
| Tamanho do `RowKey` |Uma cadeia de caracteres de até 1 KB de tamanho. |
| Tamanho de uma transação de grupo de entidades |Uma transação pode incluir no máximo 100 entidades e a carga deve ter menos de 4 MB de tamanho. Uma vez, um EGT só pode atualizar uma entidade. |

Para obter mais informações, consulte [noções básicas sobre o modelo de dados do serviço tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custos
O armazenamento de tabela é relativamente barato, mas você deve incluir estimativas de custo para o uso de capacidade e a quantidade de transações como parte da sua avaliação de qualquer solução que usa o armazenamento de tabela. Em muitos cenários, no entanto, armazenar dados desnormalizados ou duplicados para melhorar o desempenho ou a escalabilidade de sua solução é uma abordagem válida a ser tomada. Para obter mais informações sobre preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Orientações do design da tabela
Essas listas resumem algumas das principais diretrizes que você deve ter em mente quando estiver criando suas tabelas. Este guia aborda todos eles mais detalhadamente mais tarde. Essas diretrizes são diferentes das diretrizes que você normalmente seguiria para o design de banco de dados relacional.  

Criando o armazenamento de tabela para ser eficiente de *leitura* :

* **Design para a consulta em aplicativos de leitura intensiva.** Ao criar suas tabelas, pense nas consultas (especialmente as que fazem distinção de latência) que você executará antes de pensar em como atualizará suas entidades. Isso normalmente resulta numa solução eficiente e de elevado desempenho.  
* **Especifique `PartitionKey` e `RowKey` em suas consultas.** *Consultas de ponto* como essas são as consultas de armazenamento de tabela mais eficientes.  
* **Considere armazenar cópias duplicadas de entidades.** O armazenamento de tabela é barato, portanto, considere armazenar a mesma entidade várias vezes (com chaves diferentes) para permitir consultas mais eficientes.  
* **Considere desnormalizar os dados.** O armazenamento de tabela é barato, portanto, considere desnormalizar seus dados. Por exemplo, armazene entidades de resumidas para que as consultas para agregar dados apenas precisam de aceder a uma única entidade.  
* **Utilize valores de chave compostas.** As únicas chaves que você tem são `PartitionKey` e `RowKey`. Por exemplo, utilize os valores de chave compostos para ativar caminhos de com chave de acesso alternativo para entidades.  
* **Utilize a projeção da consulta.** Pode reduzir a quantidade de dados que transferir através da rede através de consultas que selecionar apenas os campos que precisa.  

Criando o armazenamento de tabelas para ser eficiente em termos de *gravação* :  

* **Não crie partições ativas.** Escolha as chaves que permitem que distribuir os pedidos por várias partições em qualquer ponto no tempo.  
* **Evite picos no tráfego.** Distribua o tráfego durante um período de tempo razoável e evite picos de tráfego.
* **Não necessariamente criam uma tabela separada para cada tipo de entidade.** Quando necessitar de transações atómicas em todos os tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição na mesma tabela.
* **Considere o débito máximo que deve ser atingida.** Você deve estar ciente das metas de escalabilidade para o armazenamento de tabelas e garantir que seu design não fará com que você as exceda.  

Posteriormente neste guia, você verá exemplos que colocam todos esses princípios em prática.  

## <a name="design-for-querying"></a>Design das consultas
O armazenamento de tabela pode ser de leitura intensiva, com uso intensivo de gravação ou uma combinação dos dois. Esta seção considera o design para dar suporte a operações de leitura com eficiência. Normalmente, um design que suporta operações de leitura com eficiência é também eficiente para operações de escrita. No entanto, há considerações adicionais ao projetar para dar suporte a operações de gravação. Eles são discutidos na próxima seção, [design para modificação de dados](#design-for-data-modification).

Um bom ponto de partida para permitir que você leia os dados com eficiência é perguntar "quais consultas meu aplicativo precisará executar para recuperar os dados de que precisa?"  

> [!NOTE]
> Com o armazenamento de tabelas, é importante que o design seja atualizado antecipadamente, pois é difícil e caro alterá-lo mais tarde. Por exemplo, em um banco de dados relacional, geralmente é possível resolver problemas de desempenho simplesmente adicionando índices a um banco de dados existente. Isso não é uma opção com o armazenamento de tabelas.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Como sua escolha de `PartitionKey` e `RowKey` afeta o desempenho da consulta
Os exemplos a seguir pressupõem que o armazenamento de tabela está armazenando entidades de funcionário com a seguinte estrutura (a maioria dos exemplos omite a propriedade `Timestamp` para fins de clareza):  

| Nome da coluna | Data type |
| --- | --- |
| `PartitionKey` (nome do departamento) |Cadeia |
| `RowKey` (ID do funcionário) |Cadeia |
| `FirstName` |Cadeia |
| `LastName` |Cadeia |
| `Age` |Número inteiro |
| `EmailAddress` |Cadeia |

Aqui estão algumas diretrizes gerais para a criação de consultas de armazenamento de tabelas. A sintaxe de filtro usada nos exemplos a seguir é da API REST de armazenamento de tabela. Para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Uma *consulta de ponto* é a pesquisa mais eficiente a ser usada e é recomendada para pesquisas de alto volume ou pesquisas que exigem a menor latência. Essa consulta pode usar os índices para localizar uma entidade individual com eficiência, especificando os valores `PartitionKey` e `RowKey`. Por exemplo: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* O segundo melhor é uma *consulta de intervalo*. Ele usa a `PartitionKey`e filtra em um intervalo de valores de `RowKey` para retornar mais de uma entidade. O valor de `PartitionKey` identifica uma partição específica e os valores de `RowKey` identificam um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* A terceira melhor é uma *verificação de partição*. Ele usa a `PartitionKey`e filtra em outra propriedade que não seja de chave e pode retornar mais de uma entidade. O valor de `PartitionKey` identifica uma partição específica e os valores de propriedade selecionam um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Uma *verificação de tabela* não inclui o `PartitionKey`e é ineficiente porque pesquisa todas as partições que compõem sua tabela para qualquer entidade correspondente. Ele executa uma verificação de tabela, independentemente de o filtro usar ou não o `RowKey`. Por exemplo: `$filter=LastName eq 'Jones'`.  
* As consultas de armazenamento de tabelas do Azure que retornam várias entidades as classificam em `PartitionKey` e `RowKey` ordem. Para evitar a reclassificação das entidades no cliente, escolha uma `RowKey` que defina a ordem de classificação mais comum. Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Usar um "**or**" para especificar um filtro com base em valores de `RowKey` resulta em uma verificação de partição e não é tratado como uma consulta de intervalo. Portanto, evite consultas que usam filtros como: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Para obter exemplos de código do lado do cliente que usam a biblioteca de cliente de armazenamento para executar consultas eficientes, consulte:  

* [Executar uma consulta de ponto usando a biblioteca de cliente de armazenamento](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperar várias entidades usando o LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projecção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que possa lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Escolha um `PartitionKey` apropriado
Sua escolha de `PartitionKey` deve balancear a necessidade de habilitar o uso de EGTs (para garantir a consistência) em relação à necessidade de distribuir suas entidades entre várias partições (para garantir uma solução escalonável).  

Em um extremo, você pode armazenar todas as suas entidades em uma única partição. Mas isso pode limitar a escalabilidade de sua solução e impedir que o armazenamento de tabelas seja capaz de balancear a carga de solicitações. No outro extremo, você pode armazenar uma entidade por partição. Isso é altamente escalonável e habilita o armazenamento de tabelas para balancear a carga de solicitações, mas impede que você use transações de grupo de entidades.  

Um `PartitionKey` ideal permite que você use consultas eficientes e tenha partições suficientes para garantir que sua solução seja escalonável. Normalmente, você descobrirá que suas entidades terão uma propriedade adequada que distribui suas entidades em partições suficientes.

> [!NOTE]
> Por exemplo, em um sistema que armazena informações sobre usuários ou funcionários, `UserID` pode ser uma boa `PartitionKey`. Você pode ter várias entidades que usam um determinado `UserID` como a chave de partição. Cada entidade que armazena dados sobre um usuário é agrupada em uma única partição. Essas entidades podem ser acessadas via EGTs, enquanto ainda são altamente escalonáveis.
> 
> 

Há considerações adicionais em sua escolha de `PartitionKey` relacionados à forma como você insere, atualiza e exclui entidades. Para obter mais informações, consulte [design para modificação de dados](#design-for-data-modification) mais adiante neste artigo.  

### <a name="optimize-queries-for-table-storage"></a>Otimizar consultas para armazenamento de tabelas
O armazenamento de tabela indexa automaticamente suas entidades usando os valores `PartitionKey` e `RowKey` em um único índice clusterizado. Esse é o motivo pelo qual as consultas de ponto são as mais eficientes de usar. No entanto, não há nenhum índice diferente daquele no índice clusterizado na `PartitionKey` e `RowKey`.

Muitos designs têm de cumprir os requisitos para ativar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionário com base em email, ID de funcionário ou sobrenome. Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam esses tipos de requisitos. Os padrões também descrevem maneiras de contornar o fato de que o armazenamento de tabela não fornece índices secundários.  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` (na mesma partição). Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` em partições separadas ou em tabelas separadas. Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  
* [Padrão de entidades de índice](#index-entities-pattern): manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

### <a name="sort-data-in-table-storage"></a>Classificar dados no armazenamento de tabelas

O armazenamento de tabela retorna os resultados da consulta classificados em ordem crescente, com base em `PartitionKey` e, em seguida, por `RowKey`.

> [!NOTE]
> Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

As chaves no armazenamento de tabela são valores de cadeia de caracteres. Para garantir que os valores numéricos sejam classificados corretamente, converta-os em um comprimento fixo e os preencha com zeros. Por exemplo, se o valor de ID de funcionário usado como o `RowKey` for um valor inteiro, você deverá converter a ID de funcionário **123** em **00000123**. 

Muitos aplicativos têm requisitos para utilizar dados ordenados em ordens diferentes: por exemplo, classificação funcionários por nome, ou ao associar data. Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam como alternar ordens de classificação para suas entidades:  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` (na mesma partição). Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` em partições separadas em tabelas separadas. Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.
* [Padrão de cauda do log](#log-tail-pattern): recupere as entidades *n* adicionadas mais recentemente a uma partição, usando um valor `RowKey` que classifica em ordem de data e hora inversa.  

## <a name="design-for-data-modification"></a>Design da modificação de dados
Esta secção concentra-se em considerações de design para otimizar as inserções, atualizações e eliminações. Em alguns casos, você precisará avaliar a compensação entre os designs que otimizam a consulta em relação a designs que otimizam a modificação de dados. Essa avaliação é semelhante ao que você faz em designs para bancos de dados relacionais (embora as técnicas para gerenciar as compensações de design sejam diferentes em um banco de dados relacional). A seção [padrões de design de tabela](#table-design-patterns) descreve alguns padrões de design detalhados para o armazenamento de tabelas e realça algumas dessas compensações. Na prática, você descobrirá que muitos designs otimizados para consultar entidades também funcionam bem para modificar entidades.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de operações de inserção, atualização e exclusão
Para atualizar ou excluir uma entidade, você deve ser capaz de identificá-la usando os valores `PartitionKey` e `RowKey`. Nesse sentido, sua escolha de `PartitionKey` e `RowKey` para modificar entidades deve seguir critérios semelhantes à sua escolha para dar suporte a consultas de ponto. Você deseja identificar as entidades com a maior eficiência possível. Você não deseja usar uma verificação de partição ou de tabela ineficiente para localizar uma entidade a fim de descobrir a `PartitionKey` e `RowKey` valores necessários para atualizá-la ou excluí-la.  

Os seguintes padrões na seção [padrões de design de tabela](#table-design-patterns) abordam a otimização do desempenho de suas operações de inserção, atualização e exclusão:  

* [Padrão de exclusão de alto volume](#high-volume-delete-pattern): habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada. Exclua as entidades excluindo a tabela.  
* [Padrão de série de dados](#data-series-pattern): armazene a série de dados completa em uma única entidade para minimizar o número de solicitações feitas.  
* [Padrão de entidades largas](#wide-entities-pattern): use várias entidades físicas para armazenar entidades lógicas com mais de 252 Propriedades.  
* [Padrão de entidades grandes](#large-entities-pattern): Use o armazenamento de BLOBs para armazenar grandes valores de propriedade.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Garanta a consistência em suas entidades armazenadas
O fator principal que influencia à sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência com transações atômicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma partição.  

Os seguintes padrões na seção [padrões de design de tabela](#table-design-patterns) abordam o gerenciamento de consistência:  

* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` (na mesma partição). Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern): armazene várias cópias de cada entidade usando valores diferentes de `RowKey` em partições separadas ou em tabelas separadas. Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern): habilitar comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.
* [Padrão de entidades de índice](#index-entities-pattern): manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern): Combine dados relacionados juntos em uma única entidade, para permitir que você recupere todos os dados necessários com uma única consulta de ponto.  
* [Padrão de série de dados](#data-series-pattern): armazene a série de dados completa em uma única entidade para minimizar o número de solicitações feitas.  

Para obter mais informações, consulte [Transações de grupo de entidades](#entity-group-transactions) mais adiante neste artigo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garanta que seu design para modificações eficientes facilite consultas eficientes
Em muitos casos, uma estrutura para os resultados da consultas eficientes modificações eficientes, mas sempre deve avaliar se for este o caso para o seu cenário específico. Alguns dos padrões na seção padrões de [design de tabela](#table-design-patterns) avaliam explicitamente as compensações entre consulta e modificação de entidades, e você deve sempre levar em conta o número de cada tipo de operação.  

Os padrões a seguir na seção [padrões de design de tabela](#table-design-patterns) abordam as compensações entre o design para consultas eficientes e o design para uma modificação eficiente de dados:  

* [Padrão de chave composta](#compound-key-pattern): use valores de `RowKey` compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  
* [Padrão de cauda do log](#log-tail-pattern): recupere as entidades *n* adicionadas mais recentemente a uma partição, usando um valor `RowKey` que classifica em ordem de data e hora inversa.  

## <a name="encrypt-table-data"></a>Criptografar dados da tabela
A biblioteca de cliente de armazenamento do .NET Azure dá suporte à criptografia de propriedades de entidade de cadeia de caracteres para operações de inserção e substituição. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias e são convertidas novamente em cadeias de caracteres após a descriptografia.    

Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Especifique um atributo `EncryptProperty` (para entidades POCO que derivam de `TableEntity`) ou especifique um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, uma chave de linha e um nome de propriedade e retorna um booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usa essas informações para decidir se uma propriedade deve ser criptografada durante a gravação na conexão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografe A propriedade A; caso contrário, criptografe as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

A mesclagem não tem suporte no momento. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. A mesclagem requer fazer chamadas de serviço extras para ler a entidade pré-existente do serviço ou usar uma nova chave por propriedade. Nenhuma delas é adequada por motivos de desempenho.     

Para obter informações sobre como criptografar dados de tabela, consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relações de modelo
A criação de modelos de domínio é uma etapa importante no design de sistemas complexos. Normalmente, você usa o processo de modelagem para identificar entidades e as relações entre elas, como uma maneira de entender o domínio de negócios e informar o design do seu sistema. Esta seção se concentra em como você pode converter alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para designs de armazenamento de tabelas. O processo de mapeamento de um modelo de dados lógico para um modelo de dados físico baseado em NoSQL é diferente daquele usado ao criar um banco de dados relacional. O design de bancos de dados relacionais normalmente pressupõe que um processo de normalização seja otimizado para minimizar a redundância. Tal design também pressupõe um recurso de consulta declarativa que abstrai a implementação de como o banco de dados funciona.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Há várias maneiras de implementar relações um-para-muitos no armazenamento de tabelas, cada uma com prós e contras que podem ser relevantes para o cenário específico.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionários. Cada departamento tem muitos funcionários e cada funcionário é associado a um departamento específico. Uma abordagem é armazenar entidades de departamento e funcionários separadas, como as seguintes:  

![Gráfico mostrando uma entidade de departamento e uma entidade de funcionário][1]

Este exemplo mostra uma relação implícita de um-para-muitos entre os tipos, com base no valor de `PartitionKey`. Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e respetivas entidades de funcionários relacionados na mesma partição. Você pode optar por usar partições diferentes, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidade.  

Uma abordagem alternativa é desnormalizar seus dados e armazenar somente entidades de funcionário com dados de departamento desnormalizados, conforme mostrado no exemplo a seguir. Nesse cenário específico, essa abordagem desnormalizada pode não ser a melhor se você tiver um requisito para poder alterar os detalhes de um gerente de departamento. Para fazer isso, você precisa atualizar todos os funcionários do departamento.  

![Gráfico da entidade Employee][2]

Para obter mais informações, consulte a [padrão de desnormalização](#denormalization-pattern) mais adiante neste guia.  

A tabela a seguir resume os prós e contras de cada uma das abordagens para armazenar entidades de funcionário e departamento que têm uma relação um-para-muitos. Você também deve considerar a frequência com que espera executar várias operações. Pode ser aceitável ter um design que inclua uma operação cara se essa operação ocorrer apenas com pouca frequência.  

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
<li>Talvez seja necessário recuperar uma entidade de funcionário e de departamento para algumas atividades do cliente.</li>
<li>Operações de armazenamento acontecem na mesma partição. Em grandes volumes de transações, isso pode resultar em um HotSpot.</li>
<li>Não é possível mover um funcionário para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separada, partições diferentes, ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em grandes volumes de transações, isso pode ajudar a distribuir a carga entre mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez seja necessário recuperar uma entidade de funcionário e de departamento para algumas atividades do cliente.</li>
<li>Você não pode usar o EGTs para manter a consistência ao atualizar/inserir/excluir um funcionário e atualizar um departamento. Por exemplo, a atualizar um número de funcionários numa entidade de departamento.</li>
<li>Não é possível mover um funcionário para um novo departamento usando um EGT.</li>
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
<li>Pode ser dispendioso manter a consistência se você precisar atualizar as informações do departamento (isso exigirá que você atualize todos os funcionários de um departamento).</li>
</ul>
</td>
</tr>
</table>

Como você escolhe entre essas opções, e quais dos prós e contras são mais significativos, depende de seus cenários de aplicativo específicos. Por exemplo, com que frequência você modifica entidades de departamento? Todas as suas consultas de funcionários precisam de informações departamentais adicionais? Quão perto você tem para os limites de escalabilidade em suas partições ou sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um para um
Os modelos de domínio podem incluir relações um-para-um entre entidades. Se você precisar implementar uma relação um-para-um no armazenamento de tabela, também deverá escolher como vincular as duas entidades relacionadas quando precisar recuperá-las. Esse link pode ser implícito, com base em uma convenção nos valores de chave, ou explícito, armazenando um link na forma de `PartitionKey` e `RowKey` valores em cada entidade para sua entidade relacionada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma partição, consulte a secção [um-para-muitos relações](#one-to-many-relationships).  

Também há considerações de implementação que podem levar você a implementar relações um-para-um no armazenamento de tabelas:  

* Tratando grandes entidades (para obter mais informações, consulte [padrão de entidades grandes](#large-entities-pattern)).  
* Implementando controles de acesso (para obter mais informações, consulte [controlar o acesso com assinaturas de acesso compartilhado](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Junte-se no cliente
Embora haja maneiras de modelar relações no armazenamento de tabelas, não se esqueça de que os dois principais motivos para usar o armazenamento de tabelas são a escalabilidade e o desempenho. Se você achar que está modelando muitas relações que comprometem o desempenho e a escalabilidade de sua solução, deverá se perguntar se é necessário criar todas as relações de dados em seu design de tabela. Você pode simplificar o design e melhorar a escalabilidade e o desempenho de sua solução, se você permitir que o aplicativo cliente execute todas as junções necessárias.  

Por exemplo, se você tiver tabelas pequenas que contêm dados que não são alterados com frequência, você poderá recuperar esses dados uma vez e armazená-los no cliente. Isto pode evitar idas e voltas repetidas para obter os mesmos dados. Nos exemplos que vimos neste guia, o conjunto de departamentos em uma pequena organização provavelmente será pequeno e mudará com pouca frequência. Isso o torna um bom candidato para dados que um aplicativo cliente pode baixar uma vez e armazenar em cache como dados de pesquisa.  

### <a name="inheritance-relationships"></a>Relações de herança
Se o aplicativo cliente usar um conjunto de classes que fazem parte de uma relação de herança para representar entidades comerciais, você poderá persistir facilmente essas entidades no armazenamento de tabelas. Por exemplo, você pode ter o seguinte conjunto de classes definido em seu aplicativo cliente, em que `Person` é uma classe abstrata.

![Diagrama de relações de herança][3]

Você pode persistir instâncias das duas classes concretas no armazenamento de tabelas usando uma única tabela de `Person`. Use entidades parecidas com as seguintes:  

![Gráfico mostrando entidade de cliente e entidade de funcionário][4]

Para obter mais informações sobre como trabalhar com vários tipos de entidade na mesma tabela no código do cliente, consulte [trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types) posteriormente neste guia. Esta opção fornece exemplos de como a reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design da tabela
Nas seções anteriores, você aprendeu sobre como otimizar o design da tabela para a recuperação de dados de entidade usando consultas e para inserir, atualizar e excluir dados de entidade. Esta seção descreve alguns padrões apropriados para uso com o armazenamento de tabelas. Além disso, você verá como é possível lidar praticamente com alguns dos problemas e compensações gerados anteriormente neste guia. O diagrama a seguir resume as relações entre os diferentes padrões:  

![Diagrama de padrões de design de tabela][5]

O mapa padrão realça algumas relações entre padrões (azuis) e antipadrões (laranja) documentados neste guia. Claro que existem muitos outros padrões que vale a pena examinar. Por exemplo, um dos principais cenários para o armazenamento de tabelas é usar o [padrão de exibição materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) do padrão de [segregação de responsabilidade de consulta de comando](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de partição dentro de índice secundário
Armazene várias cópias de cada entidade usando valores diferentes de `RowKey` (na mesma partição). Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`. As atualizações entre as cópias podem ser mantidas consistentes usando EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabela indexa automaticamente as entidades usando os valores `PartitionKey` e `RowKey`. Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a seguinte estrutura de tabela, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores `PartitionKey` e `RowKey`). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Gráfico da entidade Employee][6]

Se você também quiser localizar uma entidade de funcionário com base no valor de outra propriedade, como endereço de email, deverá usar uma verificação de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o armazenamento de tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem `RowKey`.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade, com cada cópia usando um valor de `RowKey` diferente. Se você armazenar uma entidade com as estruturas a seguir, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para `RowKey`, `empid_`e `email_` permitem consultar um único funcionário ou um intervalo de funcionários, usando um intervalo de endereços de email ou IDs de funcionários.  

![Gráfico mostrando a entidade Employee com valores variados de RowKey][7]

Os dois critérios de filtro a seguir (um pesquisado por ID de funcionário e uma pesquisa por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey eq "Vendas") e (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Vendas") e (RowKey eq 'email_jonesj@contoso.com")  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado em ordem de ID de funcionário ou um intervalo classificado em ordem de endereço de email. Consulta para entidades com o prefixo apropriado na `RowKey`.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de 000100 a 000199, use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' empid_000100 ') e (RowKey Le ' empid_000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que começa com a letra "a", use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' email_a ') e (RowKey lt ' email_b ')  
  
A sintaxe de filtro usada nos exemplos anteriores é da API REST de armazenamento de tabela. Para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, você sempre deve avaliar o custo do seu design com base em seus requisitos de armazenamento antecipados e adicionar somente entidades duplicadas para dar suporte às consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, certifique-se de que você não exceda as metas de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si utilizando EGTs para atualizar as duas cópias da entidade de atomicamente. Isso implica que deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte [usar transações de grupo de entidades](#entity-group-transactions).  
* O valor usado para o `RowKey` deve ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Os valores numéricos de preenchimento na `RowKey` (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email na `RowKey` nunca precisarem da idade do funcionário, essas entidades podem ter a seguinte estrutura:

  ![Gráfico da entidade Employee][8]

* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- Seu aplicativo cliente precisa recuperar entidades usando uma variedade de chaves diferentes.
- Seu cliente precisa recuperar entidades em diferentes ordens de classificação.
- Você pode identificar cada entidade usando uma variedade de valores exclusivos.

No entanto, certifique-se de não exceder os limites de escalabilidade de partição quando estiver executando pesquisas de entidade usando valores de `RowKey` diferentes.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de índice de partição entre secundário](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações de grupo de entidades](#entity-group-transactions)
* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice de partição entre secundário
Armazene várias cópias de cada entidade usando valores diferentes de `RowKey` em partições separadas ou em tabelas separadas. Isso permite pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de `RowKey`.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabela indexa automaticamente as entidades usando os valores `PartitionKey` e `RowKey`. Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a seguinte estrutura de tabela, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores `PartitionKey` e `RowKey`). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![Gráfico da entidade Employee][9]

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o armazenamento de tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem `RowKey`.  

Você está prevendo um alto volume de transações em relação a essas entidades e deseja minimizar o risco de a taxa de armazenamento de tabela limitar seu cliente.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade, com cada cópia usando valores diferentes de `PartitionKey` e `RowKey`. Se você armazenar uma entidade com as estruturas a seguir, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para `PartitionKey`, `empid_`e `email_` permitem que você identifique qual índice deseja usar para uma consulta.  

![Gráfico mostrando entidade de funcionário com índice primário e entidade de funcionário com índice secundário][10]

Os dois critérios de filtro a seguir (um pesquisado por ID de funcionário e uma pesquisa por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey eq ' empid_Sales") e (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") e (RowKey eq 'jonesj@contoso.com")  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado em ordem de ID de funcionário ou um intervalo classificado em ordem de endereço de email. Consulta para entidades com o prefixo apropriado na `RowKey`.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de **000100** a **000199**, classificado na ordem de ID do funcionário, use: $Filter = (PartitionKey EQ ' empid_Sales ') e (RowKey ge ' 000100 ') e (RowKey Le ' 000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que começa com "a", classificado em ordem de endereço de email, use: $filter = (PartitionKey EQ ' email_Sales ') e (RowKey ge ' a ') e (RowKey lt ' b ')  

Observe que a sintaxe de filtro usada nos exemplos anteriores é da API REST de armazenamento de tabela. Para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistente entre si utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, sempre avalie o custo do seu design com base em seus requisitos de armazenamento antecipados e adicione somente entidades duplicadas para dar suporte às consultas que o aplicativo cliente executará.  
* O valor usado para o `RowKey` deve ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Os valores numéricos de preenchimento na `RowKey` (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email na `RowKey` nunca precisarem da idade do funcionário, essas entidades podem ter a seguinte estrutura:
  
  ![Gráfico mostrando a entidade Employee com o índice secundário][11]
* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para pesquisar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando:

- Seu aplicativo cliente precisa recuperar entidades usando uma variedade de chaves diferentes.
- Seu cliente precisa recuperar entidades em diferentes ordens de classificação.
- Você pode identificar cada entidade usando uma variedade de valores exclusivos.

Use esse padrão quando desejar evitar exceder os limites de escalabilidade de partição ao executar pesquisas de entidade usando valores de `RowKey` diferentes.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de partição dentro de índice secundário](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ative o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs ativar transações atómicas em múltiplas entidades que partilham a mesma chave de partição. Por motivos de desempenho e escalabilidade, você pode decidir armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado. Nesse cenário, você não pode usar o EGTs para manter a consistência. Por exemplo, pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, nas tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada no armazenamento de tabelas e um blob armazenado no armazenamento de BLOBs.  
* Uma entidade armazenada no armazenamento de tabelas e um arquivo em um sistema de arquivos.  
* Uma entidade armazenada no armazenamento de tabela, ainda indexada usando Pesquisa Cognitiva do Azure.  

#### <a name="solution"></a>Solução
Ao utilizar as filas do Azure, pode implementar uma solução que fornece a consistência eventual entre dois ou mais partições ou sistemas de armazenamento.

Para ilustrar essa abordagem, suponha que você tenha um requisito para poder arquivar entidades de funcionário antigas. As entidades de funcionário anteriores raramente são consultadas e devem ser excluídas de quaisquer atividades que lidam com os funcionários atuais. Para implementar esse requisito, você armazena os funcionários ativos na tabela **atual** e os funcionários antigos na tabela de **arquivamento** . O arquivamento de um funcionário exige que você exclua a entidade da tabela **atual** e adicione a entidade à tabela de **arquivamento** .

Mas você não pode usar um EGT para executar essas duas operações. Para evitar o risco de uma falha faz com que uma entidade a aparecer nas tabelas de ambos ou nenhum, a operação de arquivo tem de ser eventualmente consistente. O diagrama de sequência seguinte descreve os passos nesta operação.  

![Diagrama da solução para manter a consistência eventual][12]

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure (neste exemplo, para arquivar #456 de funcionários). Uma função de trabalho consulta a fila para novas mensagens; Quando encontrar uma, ele lê a mensagem e mantém uma cópia oculta na fila. A função de trabalho a seguir obtém uma cópia da entidade do **atual** tabela, insere uma cópia no **arquivo** da tabela e, em seguida, elimina o original do **atual** tabela. Por fim, se não houver nenhum erro dos passos anteriores, a função de trabalho elimina a mensagem oculta da fila.  

Neste exemplo, a etapa 4 do diagrama insere o funcionário na tabela de **arquivo morto** . Ele pode adicionar o funcionário a um blob no armazenamento de BLOBs ou a um arquivo em um sistema de arquivos.  

#### <a name="recover-from-failures"></a>Recuperar de falhas
É importante que as operações nas etapas 4-5 no diagrama sejam *idempotentes* , caso a função de trabalho precise reiniciar a operação de arquivamento. Se você estiver usando o armazenamento de tabela, na etapa 4, você deve usar uma operação "inserir ou substituir"; para a etapa 5, você deve usar uma operação "excluir se houver" na biblioteca de cliente que você está usando. Se você estiver usando outro sistema de armazenamento, deverá usar uma operação idempotente apropriada.  

Se a função de trabalho nunca concluir a etapa 6 no diagrama, depois de um tempo limite, a mensagem será exibida novamente na fila pronta para que a função de trabalho tente processá-la novamente. A função de trabalho pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "suspeita" para investigação enviando-a para uma fila separada. Para obter mais informações sobre como ler mensagens da fila e verificar a contagem da remoção da fila, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros de armazenamento de tabela e armazenamento de fila são erros transitórios e seu aplicativo cliente deve incluir uma lógica de repetição adequada para tratá-los.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Essa solução não fornece isolamento de transação. Por exemplo, um cliente pode ler as tabelas **atual** e **arquivo morto** quando a função de trabalho esteve entre as etapas 4-5 no diagrama e ver uma exibição inconsistente dos dados. Eventualmente, os dados serão consistentes.  
* Você deve ter certeza de que as etapas 4-5 são idempotentes para garantir a consistência eventual.  
* Pode dimensionar a solução ao utilizar várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja garantir a consistência eventual entre entidades que existem em partições diferentes ou tabelas. Você pode estender esse padrão para garantir a consistência eventual para operações no armazenamento de tabelas e no armazenamento de BLOBs, além de outras fontes de dados de armazenamento não Azure, como um Database ou o sistema de arquivos.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)  
* [Intercalação ou substituição](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação for importante para sua solução, considere reformular suas tabelas para permitir que você use o EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabela indexa automaticamente as entidades usando os valores `PartitionKey` e `RowKey`. Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura de tabela a seguir, um aplicativo cliente pode recuperar com eficiência uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (o `PartitionKey` e `RowKey`).  

![Gráfico da entidade Employee][13]

Se você também deseja ser capaz de recuperar uma lista de entidades de funcionário com base no valor de outra propriedade não exclusiva, como sobrenome, você deve usar uma verificação de partição menos eficiente. Essa verificação localiza correspondências, em vez de usar um índice para examiná-las diretamente. Isso ocorre porque o armazenamento de tabela não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para habilitar a pesquisa por sobrenome com a estrutura de entidade anterior, você deve manter listas de IDs de funcionário. Se você quiser recuperar as entidades de funcionário com um sobrenome específico, como Jones, deverá primeiro localizar a lista de IDs de funcionários para funcionários com Jones como seu sobrenome e, em seguida, recuperar essas entidades de funcionário. Há três opções principais para armazenar as listas de IDs de funcionários:  

* Use o armazenamento de BLOBs.  
* Crie entidades de índice na mesma partição que as entidades de funcionários.  
* Crie entidades de índice numa partição separada ou a tabela.  

Opção 1: usar o armazenamento de BLOBs  

Crie um blob para cada sobrenome exclusivo e, em cada repositório de BLOB, uma lista dos valores de `PartitionKey` (departamento) e `RowKey` (ID de funcionário) para os funcionários que têm esse sobrenome. Ao adicionar ou excluir um funcionário, verifique se o conteúdo do blob relevante é eventualmente consistente com as entidades de funcionário.  

Opção 2: criar entidades de índice na mesma partição  

Use entidades de índice que armazenem os seguintes dados:  

![Gráfico mostrando a entidade Employee, com cadeia de caracteres contendo uma lista de IDs de funcionários com o mesmo sobrenome][14]

A propriedade `EmployeeIDs` contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no `RowKey`.  

As etapas a seguir descrevem o processo que você deve seguir quando estiver adicionando um novo funcionário. Neste exemplo, estamos adicionando um Employee com ID 000152 e Last Name Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor `PartitionKey` "Sales" e o valor de `RowKey` "Jones". Guarde a ETag dessa entidade para utilizar no passo 2.  
2. Crie uma transação de grupo de entidades (ou seja, uma operação em lote) que insere a nova entidade Employee (`PartitionKey` valor "Sales" e o valor de `RowKey` "000152") e atualiza a entidade de índice (`PartitionKey` valor "Sales" e `RowKey` valor "Jones"). O EGT faz isso adicionando a nova ID de funcionário à lista no campo EmployeeIDs. Para obter mais informações sobre EGTs, consulte [Transações de grupo de entidades](#entity-group-transactions).  
3. Se o EGT falhar devido a um erro de simultaneidade otimista (ou seja, outra pessoa modificou a entidade de índice), você precisará começar na etapa 1.  

Você pode usar uma abordagem semelhante para excluir um funcionário se estiver usando a segunda opção. Alterar o sobrenome de um funcionário é um pouco mais complexo, pois você precisa executar um EGT que atualiza três entidades: a entidade Employee, a entidade de índice para o sobrenome antigo e a entidade index para o novo sobrenome. Você deve recuperar cada entidade antes de fazer qualquer alteração, a fim de recuperar os valores de ETag que você pode usar para executar as atualizações usando a simultaneidade otimista.  

As etapas a seguir descrevem o processo que você deve seguir quando precisar pesquisar todos os funcionários com um sobrenome específico em um departamento. Neste exemplo, Estamos pesquisando todos os funcionários com o sobrenome Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor `PartitionKey` "Sales" e o valor de `RowKey` "Jones".  
2. Analise a lista de IDs de funcionários no campo `EmployeeIDs`.  
3. Se você precisar de informações adicionais sobre cada um desses funcionários (como seus endereços de email), recupere cada uma das entidades de funcionário usando `PartitionKey` valor "vendas" e `RowKey` valores da lista de funcionários obtidos na etapa 2.  

Opção 3: criar entidades de índice em uma partição ou tabela separada  

Para essa opção, use entidades de índice que armazenem os seguintes dados:  

![Gráfico mostrando a entidade Employee, com cadeia de caracteres contendo uma lista de IDs de funcionários com o mesmo sobrenome][15]

A propriedade `EmployeeIDs` contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no `RowKey`.  

Você não pode usar EGTs para manter a consistência, pois as entidades de índice estão em uma partição separada das entidades de funcionário. Verifique se as entidades de índice são eventualmente consistentes com as entidades de funcionário.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades de índice para obter a lista de valores de `RowKey` e, em seguida, consultas para recuperar cada entidade na lista.  
* Como uma entidade individual tem um tamanho máximo de 1 MB, a opção 2 e a opção 3 na solução pressupõem que a lista de IDs de funcionários para qualquer sobrenome específico nunca seja maior que 1 MB. Se a lista de IDs de funcionário provavelmente tiver mais de 1 MB de tamanho, use a opção 1 e armazene os dados de índice no armazenamento de BLOBs.  
* Se você usar a opção 2 (usando EGTs para lidar com a adição e exclusão de funcionários e alterar o sobrenome de um funcionário), será necessário avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se esse for o caso, considere uma solução eventualmente consistente (opção 1 ou opção 3). Elas usam filas para lidar com as solicitações de atualização e permitem que você armazene suas entidades de índice em uma partição separada das entidades de funcionário.  
* A opção 2 nesta solução pressupõe que você deseja procurar por sobrenome dentro de um departamento. Por exemplo, você deseja recuperar uma lista de funcionários com um sobrenome Jones no departamento de vendas. Se você quiser ser capaz de Pesquisar todos os funcionários com um sobrenome Jones em toda a organização, use a opção 1 ou a opção 3.
* Você pode implementar uma solução baseada em fila que fornece consistência eventual. Para obter mais detalhes, consulte o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando desejar pesquisar um conjunto de entidades que compartilham um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, normalmente você normaliza dados para remover a duplicação que ocorre quando as consultas recuperam dados de várias tabelas. Se normalizar os dados nas tabelas do Azure, tem de certificar vários ida e volta do cliente para o servidor para obter os dados relacionados. Por exemplo, com a estrutura de tabela a seguir, você precisa de duas viagens de ida e volta para recuperar os detalhes de um departamento. Uma viagem busca a entidade Department que inclui a ID do gerente, e a segunda viagem busca os detalhes do gerente em uma entidade Employee.  

![Gráfico de entidade de departamento e entidade de funcionário][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade do departamento. Por exemplo:  

![Gráfico de entidade de departamento desnormalizado e combinado][17]

Com as entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes necessários sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Existe algum custo de sobrecarga associado ao armazenamento alguns dados duas vezes. O benefício de desempenho resultante de menos solicitações para o armazenamento de tabelas normalmente supera o aumento marginal nos custos de armazenamento. Além disso, esse custo é parcialmente deslocado por uma redução no número de transações que você precisa para buscar os detalhes de um departamento.  
* Tem de manter a consistência das duas entidades que armazenam informações sobre os gestores. Você pode lidar com o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica. Nesse caso, a entidade de departamento e a entidade Employee para o gerente do departamento são armazenadas na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa com frequência procurar informações relacionadas. Este padrão reduz o número de consultas de que seu cliente tem de efetuar para recuperar os dados que ela requer.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Use valores de `RowKey` compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é natural usar junções em consultas para retornar informações relacionadas ao cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contenham dados de desempenho e de revisão para esse funcionário.  

Suponha que você esteja armazenando entidades de funcionário no armazenamento de tabela usando a seguinte estrutura:  

![Gráfico da entidade Employee][18]

Você também precisa armazenar dados históricos relacionados a revisões e desempenho para cada ano que o funcionário trabalhou para sua organização, e você precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena as entidades com a seguinte estrutura:  

![Gráfico da entidade de revisão do funcionário][19]

Com essa abordagem, você pode optar por duplicar algumas informações (como nome e sobrenome) na nova entidade, para permitir que você recupere seus dados com uma única solicitação. No entanto, não é possível manter uma consistência forte porque você não pode usar um EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Gráfico da entidade Employee com chave composta][20]

Observe como a `RowKey` agora é uma chave composta, composta pela ID do funcionário e pelo ano dos dados de revisão. Isso permite que você recupere o desempenho do funcionário e examine os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como recuperar todos os dados de revisão de um funcionário específico (por exemplo, o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000123") e (RowKey lt 'empid_000124') & $select = RowKey, Gestor de classificação, a classificação de ponto a ponto, comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve usar um caractere separador adequado que facilita a análise do valor `RowKey`: por exemplo, **000123_2012**.  
* Você também está armazenando essa entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário. Isso significa que você pode usar o EGTs para manter a consistência forte.
* Você deve considerar com que frequência você consultará os dados para determinar se esse padrão é apropriado. Por exemplo, se você acessar os dados de revisão com pouca frequência e os dados principais do funcionário com frequência, deverá mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver de armazenar um ou mais entidades relacionadas que consulta frequentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogêneas](#work-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de cauda do registo
Recupere as entidades *n* adicionadas mais recentemente a uma partição usando um valor `RowKey` que classifica em ordem de data e hora inversa.  

> [!NOTE]
> Os resultados da consulta retornados pelo API de Tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou chave de linha. Portanto, embora esse padrão seja adequado para o armazenamento de tabelas, ele não é adequado para Azure Cosmos DB. Para obter uma lista detalhada das diferenças de recursos, consulte [diferenças entre API de tabela em Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é conseguir obter as entidades recentemente criadas, por exemplo o mais recente dez afirmações enviadas por um funcionário de despesas. As consultas de tabela dão suporte a uma `$top` operação de consulta para retornar as primeiras *n* entidades de um conjunto. Não há nenhuma operação de consulta equivalente para retornar as últimas *n* entidades em um conjunto.  

#### <a name="solution"></a>Solução
Armazene as entidades usando uma `RowKey` que é classificada naturalmente na ordem inversa de data/hora, portanto, a entrada mais recente é sempre a primeira na tabela.  

Por exemplo, para poder recuperar as dez declarações de despesas mais recentes enviadas por um funcionário, pode utilizar um valor de escala inversa derivado de data/hora atual. O exemplo C# de código a seguir mostra uma maneira de criar um valor adequado de "tiques invertidos" para um `RowKey` que se classifica do mais recente para o mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode voltar para o valor de data/hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem o seguinte aspeto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve preencher o valor de tique inverso com zeros à esquerda, para garantir que o valor da cadeia de caracteres seja classificado conforme o esperado.  
* Deve estar ciente dos destinos de escalabilidade no nível de uma partição. Tenha cuidado para não criar partições de ponto de acesso.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar acessar entidades na ordem inversa de data/hora ou quando precisar acessar as entidades adicionadas mais recentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Antipadrão de preceder /append](#prepend-append-anti-pattern)  
* [Recuperar entidades](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de volume elevado
Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada. Exclua as entidades excluindo a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos eliminar dados antigos que já não tem de estar disponíveis para uma aplicação cliente ou que o aplicativo tem arquivados para outro meio de armazenamento. Normalmente, você identifica esses dados por uma data. Por exemplo, você tem um requisito para excluir registros de todas as solicitações de entrada com mais de 60 dias de idade.  

Um design possível é usar a data e a hora da solicitação de entrada no `RowKey`:  

![Gráfico da entidade de tentativa de logon][21]

Essa abordagem evita pontos de acesso de partição, porque o aplicativo pode inserir e excluir entidades de entrada para cada usuário em uma partição separada. No entanto, essa abordagem pode ser dispendiosa e demorada se você tiver um grande número de entidades. Primeiro, você precisa executar uma verificação de tabela para identificar todas as entidades a serem excluídas e, em seguida, deve excluir cada entidade antiga. Pode reduzir o número de ida e volta para o servidor necessário para eliminar as entidades antigas através da criação de batches vários pedidos delete para EGTs.  

#### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de início de sessão. Você pode usar o design de entidade anterior para evitar pontos de HotSpot quando estiver inserindo entidades. Excluir entidades antigas agora é simplesmente uma questão de excluir uma tabela todos os dias (uma única operação de armazenamento), em vez de localizar e excluir centenas e milhares de entidades de entrada individuais todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O design dá suporte a outras maneiras em que seu aplicativo usará os dados, como Pesquisar entidades específicas, vincular a outros dados ou gerar informações agregadas?  
* O design evitar pontos ativos ao são inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de tabela depois excluí-lo. É melhor sempre usar nomes de tabela exclusivo.  
* Espere uma limitação de taxa ao usar pela primeira vez uma nova tabela, enquanto o armazenamento de tabela aprende os padrões de acesso e distribui as partições entre os nós. Deve considerar a frequência tem de criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tem um grande volume de entidades que tem de eliminar ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)
* [Modificar entidades](#modify-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é para uma aplicação armazenar uma série de dados que, normalmente, precisar de obter ao mesmo tempo. Por exemplo, seu aplicativo pode gravar o número de mensagens Instantâneas de mensagens, cada funcionário envia a cada hora e, em seguida, utilize estas informações para desenhar quantas mensagens cada usuário enviado através das 24 horas anteriores. Um design poderá estar a armazenar 24 entidades de cada funcionário:  

![Gráfico da entidade de estatísticas de mensagem][22]

Com esta estrutura, pode facilmente localizar e atualizar a entidade a atualizar para cada funcionário, sempre que a aplicação tem de atualizar o valor de contagem de mensagens. No entanto, para obter as informações para desenhar um gráfico da atividade para as anteriores 24 horas, tem de obter 24 entidades.  

#### <a name="solution"></a>Solução
Use o design a seguir, com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Gráfico mostrando a entidade de estatísticas de mensagem com propriedades separadas][23]

Com esta estrutura, pode usar uma operação de intercalação para atualizar a contagem de mensagens para um funcionário durante uma hora específica. Agora, você pode recuperar todas as informações necessárias para plotar o gráfico usando uma solicitação para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se sua série de dados completa não couber em uma única entidade (uma entidade pode ter até 252 Propriedades), use um armazenamento de dados alternativo, como um blob.  
* Se você tiver vários clientes atualizando uma entidade simultaneamente, use a **ETag** para implementar a simultaneidade otimista. Se você tiver muitos clientes, poderá enfrentar uma alta contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa atualizar e obter uma série de dados associada a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Intercalação ou substituição](#merge-or-replace)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em um blob)  

### <a name="wide-entities-pattern"></a>Padrão de entidades grande
Utilize várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter até 252 Propriedades (excluindo as propriedades obrigatórias do sistema) e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, normalmente você configuraria quaisquer limites no tamanho de uma linha adicionando uma nova tabela e impondo uma relação de 1 para 1 entre elas.  

#### <a name="solution"></a>Solução
Usando o armazenamento de tabela, você pode armazenar várias entidades para representar um único objeto de negócios grande com mais de 252 Propriedades. Por exemplo, se você quiser armazenar uma contagem do número de mensagens INSTANTÂNEAs enviadas por cada funcionário pelos últimos 365 dias, poderá usar o design a seguir que usa duas entidades com esquemas diferentes:  

![Gráfico mostrando a entidade de estatísticas de mensagem com RowKey 01 e a entidade de estatísticas de mensagem com RowKey 02][24]

Se precisar de fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma operação de intercalação único para atualizar a contagem de mensagens para um dia específico. Para recuperar todos os dados de um funcionário individual, você deve recuperar ambas as entidades. Você pode fazer isso com duas solicitações eficientes que usam um `PartitionKey` e um valor de `RowKey`.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere o seguinte ponto ao decidir como implementar esse padrão:  

* Obter uma entidade lógica concluída envolve a, pelo menos, duas transações de armazenamento: um para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades exceda os limites de uma entidade individual no armazenamento de tabelas.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidades](#entity-group-transactions)
* [Intercalação ou substituição](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use o armazenamento de BLOBs para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenarem valores que fazem com que o tamanho total da entidade exceda esse valor, você não poderá armazenar toda a entidade no armazenamento de tabelas.  

#### <a name="solution"></a>Solução
Se sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você poderá armazenar dados no armazenamento de BLOBs e, em seguida, armazenar o endereço do blob em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento de BLOBs e armazenar um link para a foto na propriedade `Photo` da sua entidade Employee:  

![Gráfico mostrando a entidade Employee com cadeia de caracteres para a foto apontando para o armazenamento de BLOBs][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no armazenamento de tabelas e os dados no armazenamento de BLOBs, use o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter suas entidades.
* Obter uma entidade completa envolve a, pelo menos, duas transações de armazenamento: um para recuperar a entidade e outra para recuperar os dados de Blobs.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho exceda os limites de uma entidade individual no armazenamento de tabelas.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades grande](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão preceder/acrescentar
Quando você tem um alto volume de inserções, aumente a escalabilidade distribuindo as inserções entre várias partições.  

#### <a name="context-and-problem"></a>Contexto e problema
Prefixação ou acrescentando entidades para as suas entidades armazenadas normalmente resulta na aplicação a adicionar novas entidades na partição do primeiro ou último de uma sequência de partições. Nesse caso, todas as inserções em qualquer momento específico estão ocorrendo na mesma partição, criando um ponto de vida. Isso impede o armazenamento de tabelas de inserções de balanceamento de carga em vários nós e, possivelmente, faz com que seu aplicativo atinja as metas de escalabilidade para a partição. Por exemplo, considere o caso de um aplicativo que registra o acesso de rede e de recursos por funcionários. Uma estrutura de entidade como a seguinte pode fazer com que a partição da hora atual se torne um ponto de alcance, se o volume de transações atingir o destino de escalabilidade para uma partição individual:  

![Gráfico da entidade Employee][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de HotSpot em qualquer partição específica, pois o aplicativo registra eventos:  

![Gráfico mostrando a entidade Employee com RowKey compostando o ano, o mês, o dia, a hora e a ID do evento][27]

Observe com este exemplo como os `PartitionKey` e `RowKey` são chaves compostas. O `PartitionKey` usa a ID do departamento e do funcionário para distribuir o registro em log entre várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa evita a criação de partições muito ativas em inserções com eficiência suporta as consultas que a aplicação de cliente faz?  
* O volume previsto de transações significa que é provável que você atinja as metas de escalabilidade de uma partição individual e seja limitado pelo armazenamento de tabelas?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o antipadrão de preceder/acrescentar quando o volume de transações provavelmente resultar em limitação de taxa pelo armazenamento de tabelas quando você acessar uma partição ativa.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem também podem ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de cauda do registo](#log-tail-pattern)  
* [Modificar entidades](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Padrão de anti de dados de registo
Normalmente, você deve usar o armazenamento de BLOB em vez do armazenamento de tabela para armazenar dados de log.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum para dados de log é recuperar uma seleção de entradas de log para um intervalo de data/hora específico. Por exemplo, você deseja localizar todas as mensagens de erro e críticas que o aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e a hora da mensagem de log para determinar a partição para a qual você salva as entidades de log. Isso resulta em uma partição ativa porque, em qualquer momento específico, todas as entidades de log compartilharão o mesmo valor de `PartitionKey` (consulte o [antipadrão de preceder/acrescentar](#prepend-append-anti-pattern)). Por exemplo, o esquema de entidade a seguir para uma mensagem de log resulta em uma partição ativa, pois o aplicativo grava todas as mensagens de log na partição para a data e hora atuais:  

![Gráfico da entidade de mensagem de log][28]

Neste exemplo, a `RowKey` inclui a data e a hora da mensagem de log para garantir que as mensagens de log sejam classificadas em ordem de data/hora. O `RowKey` também inclui uma ID de mensagem, caso várias mensagens de log compartilhem a mesma data e hora.  

Outra abordagem é usar um `PartitionKey` que garanta que o aplicativo grave mensagens em um intervalo de partições. Por exemplo, se a origem da mensagem de log fornecer uma maneira de distribuir mensagens em várias partições, você poderá usar o seguinte esquema de entidade:  

![Gráfico da entidade de mensagem de log][29]

No entanto, o problema com esse esquema é que, para recuperar todas as mensagens de log de um período de tempo específico, você deve pesquisar todas as partições na tabela.

#### <a name="solution"></a>Solução
A seção anterior realçou o problema de tentativa de usar o armazenamento de tabela para armazenar entradas de log e sugeriu dois designs insatisfatórios. Uma solução levou a uma partição ativa com o risco de mau desempenho na gravação de mensagens de log. A outra solução resultou em um desempenho de consulta insatisfatório, devido ao requisito de verificar cada partição na tabela para recuperar mensagens de log para um período de tempo específico. O armazenamento de blob oferece uma solução melhor para esse tipo de cenário, e é assim que a análise de armazenamento do Azure armazena os dados de log coletados.  

Esta seção descreve como a análise de armazenamento armazena dados de log no armazenamento de BLOBs, como uma ilustração dessa abordagem para armazenar dados que você normalmente consulta por intervalo.  

A análise de armazenamento armazena mensagens de log em um formato delimitado em vários BLOBs. O formato delimitado torna mais fácil para um aplicativo de cliente analisar os dados na mensagem de registo.  

A análise de armazenamento usa uma Convenção de nomenclatura para BLOBs que permite que você localize o blob (ou BLOBs) que contém as mensagens de log para as quais você está pesquisando. Por exemplo, um blob chamado "Queue/2014/07/31/1800/000001. log" contém mensagens de log relacionadas ao serviço fila para a hora a partir de 18:00 em 31 de julho de 2014. "000001" indica que este é o primeiro ficheiro de registo para este período. A análise de armazenamento também registra os carimbos de data/hora das mensagens do primeiro e do último log armazenadas no arquivo, como parte dos metadados do blob. A API para armazenamento de BLOBs permite localizar BLOBs em um contêiner com base em um prefixo de nome. Para localizar todos os blobs que contêm dados de log da fila para a hora a partir de 18:00, você pode usar o prefixo "Queue/2014/07/31/1800".  

O Storage Analytics armazena em buffer as mensagens de log internamente e, em seguida, atualiza periodicamente o blob apropriado ou cria um novo com o lote mais recente de entradas de log. Isso reduz o número de gravações que ele deve executar para o armazenamento de BLOBs.  

Se você estiver implementando uma solução semelhante em seu próprio aplicativo, considere como gerenciar a compensação entre confiabilidade e custo e escalabilidade. Em outras palavras, avalie o efeito de gravar cada entrada de log no armazenamento de BLOBs conforme ele acontece, em comparação com as atualizações de buffer em seu aplicativo e gravá-las no armazenamento de BLOBs em lotes.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se você criar um design de tabela que evite possíveis partições Hot, talvez ache que não é possível acessar seus dados de log com eficiência.  
* Para processar dados de registo, um cliente, muitas vezes, precisa de carregar o número de registos.  
* Embora os dados de log sejam geralmente estruturados, o armazenamento de BLOBs pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta secção descreve algumas das considerações a ter em mente ao implementar os padrões descritos nas secções anteriores. Grande parte dessa seção utiliza exemplos escritos em c# que utilizam a biblioteca de cliente de armazenamento (versão versão 4.3.0 no momento da escrita).  

### <a name="retrieve-entities"></a>Recuperar entidades
Como discutido na seção [Design para consultar o](#design-for-querying), mais eficiente consulta é uma consulta de ponto. No entanto, em alguns cenários, talvez seja necessário recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a biblioteca de cliente de armazenamento.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Executar uma consulta de ponto usando a biblioteca de cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação de **recuperação** de tabela. Conforme mostrado no trecho de C# código a seguir, essa operação recupera uma entidade com uma `PartitionKey` de valor "Sales" e uma `RowKey` de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como este exemplo espera que a entidade que ele recupera seja do tipo `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperar várias entidades usando o LINQ
Você pode recuperar várias entidades usando o LINQ com a biblioteca de cliente de armazenamento e especificando uma consulta com uma cláusula **Where** . Para evitar uma verificação de tabela, você deve sempre incluir o valor de `PartitionKey` na cláusula WHERE e, se possível, o valor de `RowKey` para evitar verificações de tabela e de partição. O armazenamento de tabela dá suporte a um conjunto limitado de operadores de comparação (maior que, maior ou igual, menor que, menor que ou igual a, igual e diferente) para usar na cláusula WHERE. O trecho C# de código a seguir localiza todos os funcionários cujo sobrenome começa com "B" (supondo que o `RowKey` armazena o sobrenome) no departamento de vendas (supondo que a `PartitionKey` armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta especifica uma `RowKey` e uma `PartitionKey` para garantir um melhor desempenho.  

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
> O exemplo Aninha vários métodos `CombineFilters` para incluir as três condições de filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperar grandes números de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base em um valor de `PartitionKey` e um valor de `RowKey`. No entanto, em alguns cenários, você pode ter um requisito para retornar muitas entidades da mesma partição ou até mesmo de muitas partições. Sempre totalmente, deve testar o desempenho da sua aplicação em tais cenários.  

Uma consulta no armazenamento de tabelas pode retornar um máximo de 1.000 entidades ao mesmo tempo e ser executada por um máximo de cinco segundos. O armazenamento de tabelas retorna um token de continuação para permitir que o aplicativo cliente solicite o próximo conjunto de entidades, se qualquer uma das seguintes opções for verdadeira:

- O conjunto de resultados contém mais de 1.000 entidades.
- A consulta não foi concluída em cinco segundos.
- A consulta cruza o limite da partição. 

Para obter mais informações sobre como os tokens de continuação funcionam, consulte [tempo limite de consulta e paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a biblioteca de cliente de armazenamento, ela poderá manipular automaticamente tokens de continuação para você, pois retorna entidades do armazenamento de tabela. Por exemplo, o exemplo C# de código a seguir trata automaticamente dos tokens de continuação se o armazenamento de tabelas os retornar em uma resposta:  

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

Ao utilizar tokens de continuação explicitamente, pode controlar quando a aplicação obtém o segmento seguinte de dados. Por exemplo, se o aplicativo cliente permitir que os usuários façam a página pelas entidades armazenadas em uma tabela, um usuário poderá decidir não paginar todas as entidades recuperadas pela consulta. Seu aplicativo usaria apenas um token de continuação para recuperar o próximo segmento quando o usuário concluiu a paginação por todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Você pode limitar a quantidade de dados a serem recuperados do armazenamento de tabelas e movê-los pela rede.  
* Você pode executar e/s assíncrona no .NET.  
* Você pode serializar o token de continuação para o armazenamento persistente, para que possa continuar no caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento que contém 1.000 entidades, embora possa conter menos. Esse também é o caso se você limitar o número de entradas que uma consulta retorna usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de pesquisa. O armazenamento de tabela pode retornar um segmento que contém menos de n entidades, juntamente com um token de continuação para permitir que você recupere as entidades restantes.  
> 
> 

O código do c# seguinte mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projecção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ser até 1 MB de tamanho. Quando você consulta a tabela e recupera entidades, talvez não precise de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo a seguir recupera apenas a propriedade `Email` (juntamente com `PartitionKey`, `RowKey`, `Timestamp`e `ETag`) das entidades selecionadas pela consulta.  

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

Observe como o valor de `RowKey` está disponível, embora não esteja incluído na lista de propriedades a serem recuperados.  

### <a name="modify-entities"></a>Modificar entidades
A biblioteca de cliente de armazenamento permite que você modifique suas entidades armazenadas no armazenamento de tabelas inserindo, excluindo e atualizando entidades. Você pode usar o EGTs para agrupar várias operações de inserção, atualização e exclusão, para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da solução.  

As exceções geradas quando a biblioteca de cliente de armazenamento executa um EGT normalmente incluem o índice da entidade que causou a falha do lote. Isso é útil quando está a depurar o código que usa EGTs.  

Também deve considerar como seu design afeta como a aplicação cliente lida com operações de atualização e de simultaneidade.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por padrão, o armazenamento de tabela implementa verificações de simultaneidade otimista no nível de entidades individuais para operações de inserção, mesclagem e exclusão, embora seja possível que um cliente Force o armazenamento de tabela a ignorar essas verificações. Para obter mais informações, consulte [Gerenciando a simultaneidade no armazenamento do Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Intercalação ou substituição
O método `Replace` da classe `TableOperation` sempre substitui a entidade completa no armazenamento de tabelas. Se você não incluir uma propriedade na solicitação quando essa propriedade existir na entidade armazenada, a solicitação removerá essa propriedade da entidade armazenada. A menos que queira remover uma propriedade explicitamente de uma entidade armazenada, tem de incluir todas as propriedades no pedido.  

Você pode usar o método `Merge` da classe `TableOperation` para reduzir a quantidade de dados que você envia para o armazenamento de tabelas quando deseja atualizar uma entidade. O método `Merge` substitui todas as propriedades na entidade armazenada por valores de propriedade da entidade incluída na solicitação. Esse método deixa intactas todas as propriedades na entidade armazenada que não estão incluídas na solicitação. Isso será útil se você tiver grandes entidades e precisar apenas atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os métodos `*Replace` e `Merge` falham se a entidade não existir. Como alternativa, você pode usar os métodos `InsertOrReplace` e `InsertOrMerge` que criam uma nova entidade, caso ela não exista.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidade heterogêneas
O armazenamento de tabela é um repositório *de tabela sem esquema* . Isso significa que uma única tabela pode armazenar entidades de vários tipos, fornecendo grande flexibilidade em seu design. O exemplo seguinte ilustra uma tabela de armazenamento dos funcionários e entidades de departamento:  

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
<th>Apelido</th>
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
<th>Apelido</th>
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
<th>Apelido</th>
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

Cada entidade ainda deve ter valores `PartitionKey`, `RowKey`e `Timestamp`, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que você opte por armazenar essas informações em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Preceda o tipo de entidade para a `RowKey` (ou possivelmente a `PartitionKey`). Por exemplo, `EMPLOYEE_000123` ou `DEPARTMENT_SALES` como valores `RowKey`.  
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
<th>entityType</th>
<th>FirstName</th>
<th>Apelido</th>
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
<th>entityType</th>
<th>FirstName</th>
<th>Apelido</th>
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
<th>Apelido</th>
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

A primeira opção, predependendo do tipo de entidade para a `RowKey`, será útil se houver uma possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor de chave. Ele também agrupa as entidades do mesmo tipo em conjunto na partição.  

As técnicas discutidas nesta seção são especialmente relevantes para a discussão sobre[relações de herança](#inheritance-relationships).  

> [!NOTE]
> Considere incluir um número de versão no valor de tipo de entidade para permitir que aplicativos cliente evoluam objetos POCO e trabalhem com versões diferentes.  
> 
> 

O resto desta secção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperar tipos de entidade heterogênea
Se você estiver usando a biblioteca de cliente de armazenamento, terá três opções para trabalhar com vários tipos de entidade.  

Se você souber o tipo da entidade armazenada com `RowKey` específicos e valores de `PartitionKey`, poderá especificar o tipo de entidade ao recuperar a entidade. Você viu isso nos dois exemplos anteriores que recuperam entidades do tipo `EmployeeEntity`: [executar uma consulta de ponto usando a biblioteca de cliente de armazenamento](#run-a-point-query-by-using-the-storage-client-library) e [recuperar várias entidades usando o LINQ](#retrieve-multiple-entities-by-using-linq).  

A segunda opção é usar o tipo de `DynamicTableEntity` (um recipiente de propriedades), em vez de um tipo de entidade POCO concreto. Essa opção também pode melhorar o desempenho, pois não há necessidade de serializar e desserializar a entidade para tipos .NET. O código C# a seguir recupera potencialmente várias entidades de tipos diferentes da tabela, mas retorna todas as entidades como instâncias de `DynamicTableEntity`. Em seguida, ele usa a propriedade `EntityType` para determinar o tipo de cada entidade:  

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

Para recuperar outras propriedades, você deve usar o método `TryGetValue` na propriedade `Properties` da classe `DynamicTableEntity`.  

Uma terceira opção é combinar usando o tipo de `DynamicTableEntity` e uma instância de `EntityResolver`. Isto permite-lhe resolver para vários tipos POCO da mesma consulta. Neste exemplo, o `EntityResolver` delegado está usando a propriedade `EntityType` para distinguir entre os dois tipos de entidade que a consulta retorna. O método `Resolve` usa o delegado `resolver` para resolver `DynamicTableEntity` instâncias para `TableEntity` instâncias.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Modificar tipos de entidade heterogênea
Você não precisa saber o tipo de uma entidade para excluí-la e sempre sabe o tipo de uma entidade ao inseri-la. No entanto, você pode usar o tipo de `DynamicTableEntity` para atualizar uma entidade sem saber seu tipo e sem usar uma classe de entidade POCO. O exemplo de código a seguir recupera uma única entidade e verifica se a propriedade `EmployeeCount` existe antes de atualizá-la.  

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

### <a name="control-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso compartilhado
Você pode usar tokens de SAS (assinatura de acesso compartilhado) para permitir que aplicativos cliente modifiquem (e consultem) as entidades de tabela diretamente, sem a necessidade de autenticação direta com o armazenamento de tabelas. Normalmente, há três benefícios principais para através da SAS em seu aplicativo:  

* Você não precisa distribuir sua chave de conta de armazenamento para uma plataforma não segura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no armazenamento de tabelas.  
* Você pode descarregar parte do trabalho que as funções Web e de trabalho executam no gerenciamento de suas entidades. Você pode descarregar para dispositivos cliente, como computadores de usuário final e dispositivos móveis.  
* Você pode atribuir um conjunto de permissões restritas e limitadas por tempo a um cliente (como permitir acesso somente leitura a recursos específicos).  

Para obter mais informações sobre como usar tokens SAS com o armazenamento de tabela, consulte [usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem a um aplicativo cliente as entidades no armazenamento de tabelas. Faça isso em um ambiente que tenha acesso seguro às suas chaves de conta de armazenamento. Normalmente, utiliza uma função web ou de trabalho para gerar os tokens de SAS e entregue-o para as aplicações de cliente que precisam de acesso para as suas entidades. Uma vez que ainda há uma sobrecarga envolvida na geração e fornecer SAS tokens para os clientes, deve considerar a melhor maneira, para reduzir esta sobrecarga, especialmente em cenários de volume elevado.  

É possível gerar um token SAS que concede acesso a um subconjunto de entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira. Mas também é possível especificar que o token SAS conceda acesso a um intervalo de valores de `PartitionKey` ou a um intervalo de valores de `PartitionKey` e `RowKey`. Você pode optar por gerar tokens SAS para usuários individuais do seu sistema, de modo que o token SAS de cada usuário só permita acesso às suas próprias entidades no armazenamento de tabelas.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que são propagar os pedidos entre várias partições, pode melhorar a capacidade de resposta de débito e o cliente utilizando consultas assíncronas ou paralelas.
Por exemplo, pode ter dois ou mais trabalho instâncias de função acessar suas tabelas em paralelo. Você pode ter funções de trabalho individuais responsáveis por determinados conjuntos de partições ou simplesmente ter várias instâncias de função de trabalho, cada uma capaz de acessar todas as partições em uma tabela.  

Em uma instância de cliente, você pode melhorar a taxa de transferência executando operações de armazenamento de forma assíncrona. A biblioteca de cliente de armazenamento torna mais fácil escrever consultas assíncronas e modificações. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, conforme mostrado no código a C# seguir:  

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

Você pode facilmente modificar esse código para que a consulta seja executada de forma assíncrona, da seguinte maneira:  

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

* A assinatura do método agora inclui o modificador de `async` e retorna uma instância de `Task`.  
* Em vez de chamar o método `ExecuteSegmented` para recuperar resultados, o método agora chama o método `ExecuteSegmentedAsync`. O método usa o modificador `await` para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar esse método várias vezes, com valores diferentes para o parâmetro `department`. Cada consulta é executada em um thread separado.  

Não há nenhuma versão assíncrona do método `Execute` na classe `TableQuery`, porque a interface `IEnumerable` não dá suporte à enumeração assíncrona.  

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

Você pode facilmente modificar esse código para que a atualização seja executada de forma assíncrona, da seguinte maneira:  

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

* A assinatura do método agora inclui o modificador de `async` e retorna uma instância de `Task`.  
* Em vez de chamar o método `Execute` para atualizar a entidade, o método agora chama o método `ExecuteAsync`. O método usa o modificador `await` para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar vários métodos assíncronos como esse, e cada invocação de método é executada em um thread separado.  


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

