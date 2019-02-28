---
title: Criar tabelas do Azure Cosmos DB para suportar o dimensionamento e desempenho
description: 'Guia de Design da tabela de armazenamento do Azure: Desenvolvendo dimensionável e tabelas de alto desempenho no Azure Cosmos DB e tabelas de armazenamento do Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 12/07/2018
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 84749332c5b7ab5fec2905c0fc36d89863adc3d2
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960223"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guia de Design da tabela de armazenamento do Azure: Desenvolvendo dimensionável e de tabelas de alto desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Design dimensionável e tem de considerar vários fatores, tais como desempenho, escalabilidade e o custo de tabelas de alto desempenho. Se anteriormente tiver criado esquemas para bases de dados relacionais, estas considerações será familiares para, mas embora haja algumas semelhanças entre os modelos relacionais e o modelo de armazenamento do serviço de tabelas do Azure, também existem várias diferenças importantes. Essas diferenças, normalmente, levam a designs diferentes que pode parecer achem ou errada para alguém familiarizado com as bases de dados relacionais, mas isso faz sentido se está Projetando para um arquivo de chave/valor NoSQL como o serviço de tabelas do Azure. Muitas das diferenças de design refletirá o fato de que o serviço de tabela foi concebido para suportar aplicações de escala da cloud que podem conter bilhões de entidades (linhas na terminologia de base de dados relacional) de dados de ou para conjuntos de dados tem de suportar alta transação volumes: portanto, precisa pensar de forma diferente sobre como armazenar os seus dados e compreender como funciona o serviço de tabela. Um arquivo de dados NoSQL bem estruturado pode ativar a sua solução Dimensionar muito mais (e a um custo mais baixo) do que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço de tabelas do Azure
Esta secção destaca alguns dos principais recursos do serviço de tabela que são especialmente relevantes para o projeto para o desempenho e escalabilidade. Se estiver familiarizado com o armazenamento do Azure e o serviço de tabela, leia primeiro [introdução ao armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e [introdução ao armazenamento de tabelas do Azure com o .NET](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo . Embora o foco deste guia é o serviço de tabela, ele incluirá alguma discussão sobre os serviços de BLOBs e filas do Azure e como pode usá-los, juntamente com o serviço de tabela numa solução.  

O que é o serviço de tabela? Como pode esperar do nome, o serviço de tabela utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades de entidade. Cada entidade tem um par de chaves para identificar exclusivamente e uma coluna de timestamp que o serviço de tabela usa para controlar quando a entidade foi a última atualização (o campo timestamp é adicionado automaticamente e manualmente não é possível substituir o período de tempo com um valor arbitrário). O serviço de tabelas utiliza este timestamp (LMT) da última modificação para gerir a simultaneidade otimista.  

> [!NOTE]
> As operações de REST API do serviço de tabela também devolvem uma **ETag** valor que ela é derivada da última modificação timestamp (LMT). Este documento irá observar os termos de ETag e LMT alternadamente porque eles fazem referência aos mesmos dados subjacentes.  
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
<th>Nome do Departamento</th>
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


Até agora, esse design é semelhante a uma tabela na base de dados relacional com as diferenças principais sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, tal como **FirstName** ou **idade** tem um tipo de dados, como integer ou string, tal como uma coluna na base de dados relacional. Embora ao contrário de na base de dados relacional, a natureza de esquema do serviço tabela significa que uma propriedade não precisa ter os mesmo tipo em cada entidade de dados. Para armazenar tipos de dados complexos numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre os tipos de dados do serviço, como suporte de tabela, intervalos de datas suportados, as regras de nomenclatura e restrições de tamanho, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Como verá, à sua escolha **PartitionKey** e **RowKey** é fundamental para o design da tabela de bom. Todas as entidades armazenadas numa tabela tem de ter uma combinação única de **PartitionKey** e **RowKey**. Tal como acontece com as chaves numa tabela de base de dados relacional, o **PartitionKey** e **RowKey** valores são indexados para criar um índice em cluster que permite a rápida look-ups; no entanto, o serviço de tabela não cria qualquer índices secundários são apenas duas propriedades indexadas (alguns dos padrões descritos mais à frente mostram como pode contornar esta limitação aparente).  

Uma tabela é constituída por uma ou mais partições e como verá, muitas das decisões de design que tomar será em torno de escolher um adequado **PartitionKey** e **RowKey** para otimizar a sua solução. Uma solução poderia consistir apenas uma única tabela que contém todas as suas entidades organizadas em partições, mas, normalmente, uma solução terá várias tabelas. Tabelas ajudá-lo a logicamente organizar suas entidades, ajudar a gerir o acesso aos dados através de listas de controlo de acesso, e pode soltar uma tabela inteira através de uma operação de armazenamento única.  

### <a name="table-partitions"></a>Partições da tabela
O nome de conta, o nome da tabela, e **PartitionKey** em conjunto identificam a partição dentro do serviço de armazenamento onde o serviço de tabela armazena a entidade. Bem como a fazer parte do esquema de endereçamento para entidades, partições definem um âmbito para transações (consulte [transações de grupo de entidades](#entity-group-transactions) abaixo), e formar a base da forma como o serviço de tabela pode ser dimensionada. Para obter mais informações sobre as partições, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md).  

No serviço tabela, um nó individual dos serviços de um ou mais concluir as partições e as escalas de serviço por dinamicamente balanceamento de carga partições entre nós. Se for um nó sob carga, o serviço de tabela pode *dividir* o intervalo de partições atendidos por esse nó em diferentes nós; quando o tráfego diminua, o serviço pode *intercalação* os intervalos de partição de nós silencioso num único nó.  

Para obter mais informações sobre os detalhes internos do serviço tabela e, em particular, como o serviço gere as partições, consulte o documento [armazenamento do Microsoft Azure: Um serviço de armazenamento de Cloud de elevada disponibilidade com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transações do grupo de entidade
No serviço tabela, transações de grupo de entidades (EGTs) são o mecanismo interno apenas para a realização de atualizações atómicas em múltiplas entidades. EGTs são também denominados *do batch transações* em alguma documentação. EGTs só pode funcionar em entidades armazenadas na mesma partição (partilhar a mesma chave de partição numa determinada tabela), isso sempre que necessitar de comportamento transacional atómico em múltiplas entidades, que certifique-se de que essas entidades estão na mesma partição. Isto é, muitas vezes, um motivo para manter os vários tipos de entidade na mesma tabela (e partição) e não a utilizar várias tabelas para tipos de entidade diferentes. Um único EGT pode operar no máximo 100 entidades.  Se submeter vários EGTs simultâneas para processamento, é importante certificar-se de que esses EGTs não funciona em entidades que são comuns em EGTs como outra forma de processamento pode ser atrasada.

EGTs também introduzir uma potencial desvantagem para avaliar em seu design: a utilização de mais partições irá aumentar a escalabilidade do seu aplicativo uma vez que o Azure tem mais oportunidades para pedidos de balanceamento de carga entre nós, mas isso podem limitar a capacidade de seu aplicativo para executar transações atómicas e manter a consistência forte para os seus dados. Além disso, existem destinos de escalabilidade específico no nível de uma partição que pode limitar o débito de transações que pode esperar para um único nó: para obter mais informações sobre os destinos de escalabilidade para contas de armazenamento do Azure e o serviço de tabela, consulte [Metas de desempenho e escalabilidade do armazenamento do azure](../storage/common/storage-scalability-targets.md). As seções posteriores deste guia discutem várias estratégias de que o ajudam a gerir as vantagens e desvantagens, como este e descrevem a melhor maneira de escolher a sua chave de partição com base nos requisitos específicos da sua aplicação de cliente de design.  

### <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela seguinte inclui alguns dos valores chave estar ciente de quando está Projetando uma solução de serviço de tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas numa conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições numa tabela |Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades numa partição |Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho de uma entidade individual |Até 1 MB com um máximo de 255 propriedades (incluindo o **PartitionKey**, **RowKey**, e **Timestamp**) |
| Tamanho do **PartitionKey** |A cadeia de caracteres até 1 KB de tamanho |
| Tamanho do **RowKey** |A cadeia de caracteres até 1 KB de tamanho |
| Tamanho de uma transação de grupos de entidades |Uma transação pode incluir até 100 entidades e a carga tem de ser inferior a 4 MB de tamanho. Uma vez, um EGT só pode atualizar uma entidade. |

Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custos
Armazenamento de tabelas está relativamente mais barato, mas deve incluir estimativas de custo de utilização da capacidade e a quantidade de transações como parte de sua avaliação de qualquer solução que utiliza o serviço de tabela. No entanto, em muitos cenários de armazenar dados desnormalizados ou duplicados para melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida para tirar. Para obter mais informações sobre preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Orientações do design da tabela
Estas listas resumem algumas das orientações da chave que deve ter em consideração quando está a conceber as suas tabelas, e este guia irá resolvê-los em mais detalhes posteriormente. Essas diretrizes são diferentes da documentação de orientação, normalmente, siga para o design de banco de dados relacional.  

Conceber a sua solução de serviço de tabela para ser *ler* eficiente:

* ***Design para a consulta em aplicativos de leitura intensiva.*** Durante o design de suas tabelas, pensar sobre as consultas (especialmente a latência aqueles confidenciais), que executará a antes de pensar a respeito de como atualizar as entidades. Isso normalmente resulta numa solução eficiente e de elevado desempenho.  
* ***Especifique PartitionKey e RowKey nas suas consultas.*** *Do ponto de consultas* como estas são as consultas mais eficientes de serviço tabela.  
* ***Considere armazenar cópias duplicadas de entidades.*** Armazenamento de tabelas é barato por isso considere armazenar a mesma entidade várias vezes (com chaves diferentes) para ativar as consultas mais eficientes.  
* ***Considere desnormalizar os dados.*** Armazenamento de tabelas é barato por isso considere desnormalizar os dados. Por exemplo, armazene entidades de resumidas para que as consultas para agregar dados apenas precisam de aceder a uma única entidade.  
* ***Utilize valores de chave compostas.*** As chaves só tiver são **PartitionKey** e **RowKey**. Por exemplo, utilize os valores de chave compostos para ativar caminhos de com chave de acesso alternativo para entidades.  
* ***Utilize a projeção da consulta.*** Pode reduzir a quantidade de dados que transferir através da rede através de consultas que selecionar apenas os campos que precisa.  

Conceber a sua solução de serviço de tabela para ser *escrever* eficiente:  

* ***Não crie partições muito ativas.*** Escolha as chaves que permitem que distribuir os pedidos por várias partições em qualquer ponto no tempo.  
* ***Evite picos no tráfego.*** Suavizar o tráfego de um período de tempo razoável e evitar picos no tráfego.
* ***Não necessariamente criam uma tabela separada para cada tipo de entidade.*** Quando necessitar de transações atómicas em todos os tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição na mesma tabela.
* ***Considere o débito máximo que deve ser atingida.*** Tem de ter em consideração os destinos de escalabilidade para o serviço de tabela e certifique-se de que o seu design não irá causar que exceda-los.  

Enquanto lê este guia, verá exemplos que colocar todos esses princípios em prática.  

## <a name="design-for-querying"></a>Design das consultas
Soluções de serviço de tabela podem ser lido com uso intensivo, com uso intensivo de escrita ou uma combinação dos dois. Esta seção se concentra em coisas que deve ter em mente quando está a conceber o seu serviço de tabela para suportar operações de leitura com eficiência. Normalmente, um design que suporta operações de leitura com eficiência é também eficiente para operações de escrita. No entanto, existem considerações adicionais que deve ter em mente ao projetar para oferecer suporte a operações de escrita, discutidas na próxima seção, [Design para modificação de dados](#design-for-data-modification).

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

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta
Os exemplos seguintes partem do princípio do serviço de tabela é armazenar entidades de funcionários com a seguinte estrutura (a maioria dos exemplos omite a **Timestamp** propriedade por motivos de clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (nome de departamento) |Cadeia |
| **RowKey** (identificação do funcionário) |Cadeia |
| **FirstName** |Cadeia |
| **LastName** |Cadeia |
| **Idade** |Número inteiro |
| **EmailAddress** |String |

A descrição de geral de serviço de tabelas do Azure de seção anterior descreve alguns dos principais recursos do serviço de tabelas do Azure que tenham um diretamente um grande design para consulta. Estes resultam nas seguintes diretrizes gerais para a criação de consultas do serviço de tabela. É a sintaxe de filtro utilizada nos exemplos abaixo da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* R ***consulta ponto*** é a pesquisa mais eficiente para utilizar e é recomendada a ser utilizado para pesquisas de grande volume ou exigir que a latência mais baixa de pesquisas. Uma consulta deste tipo pode utilizar os índices para localizar uma entidade individual com eficiência, especificando ambos os **PartitionKey** e **RowKey** valores. Por exemplo: $filter = (PartitionKey eq "Vendas") e (RowKey eq '2')  
* Em segundo lugar e o melhor é um ***intervalo de consulta*** que utiliza o **PartitionKey** e filtros numa ampla gama de **RowKey** valores a devolver mais do que uma entidade. O **PartitionKey** valor identifica uma partição específica e o **RowKey** valores identificam a um subconjunto de entidades nessa partição. Por exemplo: $filter = PartitionKey eq "De" vendas e da ge RowKey"e RowKey lt T'"  
* Melhor terceiro é um ***analisar de partição*** que utiliza o **PartitionKey** e filtros em outra propriedade sem chave e que podem devolver mais do que uma entidade. O **PartitionKey** valor identifica uma partição específica e a propriedade de valores select para um subconjunto de entidades nessa partição. Por exemplo: $filter = PartitionKey eq "Vendas" e LastName eq "Santos"  
* R ***Table Scan*** não inclui o **PartitionKey** e é ineficiente porque ele pesquisa todas as partições que compõem a sua tabela, por sua vez, para qualquer entidades correspondentes. A operação executará uma análise de tabela, independentemente de estar ou não utiliza o seu filtro de **RowKey**. Por exemplo: $filter = LastName eq "Silva"  
* Consultas de armazenamento de tabela do Azure, que retornam várias entidades retorná-los a ordenados **PartitionKey** e **RowKey** ordem. Para evitar recorrer as entidades no cliente, escolha uma **RowKey** que define a ordem de classificação mais comuns. Resultados devolvidos pela API de tabela do Azure no Azure Cosmos DB não são ordenados pela chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de funcionalidade, consulte [diferenças entre a API de tabela no armazenamento do Azure Cosmos DB e tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Usando um "**ou**" para especificar um filtro com base nos **RowKey** valores resulta numa análise de partição e não é tratado como uma consulta de intervalo. Portanto, deve evitar consultas que usam filtros, tais como: $filter = PartitionKey eq "Vendas" e (RowKey eq '121' ou RowKey eq "322")  

Para obter exemplos de código do lado do cliente que utilizam a biblioteca de cliente de armazenamento para executar consultas eficientes, consulte:  

* [Execução de uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library)
* [A obter várias entidades usando o LINQ](#retrieving-multiple-entities-using-linq)
* [Projecção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que possa lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Escolher um PartitionKey apropriado
Sua escolha de **PartitionKey** devem equilibrar a necessidade de ativar a utilização de EGTs (para garantir a consistência) contra o requisito para distribuir as suas entidades em várias partições (para assegurar uma solução escalável).  

De um lado, pode armazenar todas as suas entidades numa única partição, mas isso pode limitar a escalabilidade da sua solução e impediria que o serviço de tabela de conseguir a pedidos de balanceamento de carga. No outro extremo, pode armazenar uma entidade por partição, que seria altamente escalável e que permite que o serviço tabela pedidos de balanceamento de carga, mas que impediria de usar transações do grupo de entidades.  

Um ideal **PartitionKey** é um que permite-lhe utilizar consultas eficientes e que tem partições suficientes para garantir a sua solução é escalável. Normalmente, encontrará-se que as entidades terão uma propriedade adequada que distribui as entidades em várias partições suficientes.

> [!NOTE]
> Por exemplo, num sistema que armazena informações sobre os utilizadores ou de funcionários, ID de utilizador pode ser uma boa PartitionKey. Pode ter várias entidades que utilizam um ID de utilizador especificado como a chave de partição. Cada entidade que armazena dados sobre um usuário é agrupada numa única partição e, portanto, essas entidades são acessíveis através de transações do grupo de entidades, ao mesmo tempo, altamente dimensionável.
> 
> 

Existem considerações adicionais na sua escolha de **PartitionKey** relacionadas com a forma como irá inserir, atualizar e eliminar entidades: consulte a secção [Design para modificação de dados](#design-for-data-modification) abaixo.  

### <a name="optimizing-queries-for-the-table-service"></a>Otimizar as consultas para o serviço de tabela
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores num único índice em cluster, por conseguinte, a razão pela qual o ponto de consultas são mais eficiente a utilizar. No entanto, existem sem índices que não seja o que no índice em cluster no **PartitionKey** e **RowKey**.

Muitos designs têm de cumprir os requisitos para ativar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionários com base no e-mail, id de funcionário ou apelido. Os padrões seguintes na secção [padrões de Design de tabela](#table-design-patterns) esses tipos de requisito de endereços e descrevem formas de trabalhar para resolver o fato de que o serviço de tabela não fornece índices secundários:  

* [Padrão de índice secundário de intra-partition](#intra-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores.  
* [Padrão de índice secundário de partição entre](#inter-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores em separar as partições ou separar em tabelas para permitir pesquisas rápidas e eficientes e de classificação alternativa pedidos utilizando diferentes **RowKey** valores.  
* [Padrão de entidades de índice](#index-entities-pattern) -manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

### <a name="sorting-data-in-the-table-service"></a>Classificação de dados no serviço tabela

Resultados retornados pelo serviço de tabela são ordenados por ordem, com base em ascendente **PartitionKey** e, em seguida, por **RowKey**.

> [!NOTE]
> Resultados devolvidos pela API de tabela do Azure no Azure DB não são ordenados pela chave de partição ou chave de linha. Para obter uma lista detalhada das diferenças de funcionalidade, consulte [diferenças entre a API de tabela no armazenamento do Azure Cosmos DB e tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Chaves na tabela de armazenamento do Azure são valores de cadeia de caracteres e para garantir que os valores numéricos são ordenados corretamente, deverá convertê-los num comprimento fixo e preenche-los com zeros. Por exemplo, se o valor de id de funcionário utilizar como a **RowKey** é um valor de número inteiro, deve converter a identificação do funcionário **123** para **00000123**. 

Muitos aplicativos têm requisitos para utilizar dados ordenados em ordens diferentes: por exemplo, classificação funcionários por nome, ou ao associar data. Os padrões seguintes na secção [padrões de Design de tabela](#table-design-patterns) abordou a forma de alternativo ordens de classificação para as entidades:  

* [Padrão de partição dentro de índice secundário](#intra-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes valores de RowKey (na mesma partição) para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena com diferentes valores de RowKey.  
* [Padrão de índice secundário de partição entre](#inter-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes valores de RowKey em partições separadas em tabelas separadas para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena com diferentes valores de RowKey .
* [Padrão de cauda do registo](#log-tail-pattern) -obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar uma **RowKey** valor ordena inversa de data e a ordem de tempo.  

## <a name="design-for-data-modification"></a>Design da modificação de dados
Esta secção concentra-se em considerações de design para otimizar as inserções, atualizações e eliminações. Em alguns casos, terá de avaliar os designs que otimizar para a consulta em relação a designs que otimizar para modificação de dados, tal como acontece no designs para bases de dados relacionais (embora as técnicas para gerenciar as compensações de design são diferentes aspetos diferentes na base de dados relacional). A secção [padrões de Design de tabela](#table-design-patterns) descreve alguns padrões de design detalhado para o serviço de tabela e destaca alguns dessas compensações. Na prática, verá que muitos designs otimizados para consultar entidades também funcionam bem para modificar entidades.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de inserir, atualizar e eliminar operações
Para atualizar ou eliminar uma entidade, deve conseguir identificá-lo utilizando o **PartitionKey** e **RowKey** valores. Nesse sentido, a sua escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhante à sua escolha para oferecer suporte a consultas de ponto de como deseja identificar entidades como forma mais eficiente possível. Não pretende utilizar uma análise de tabela ou da partição ineficiente localizar uma entidade para detetar os **PartitionKey** e **RowKey** valores tem de atualizar ou eliminá-lo.  

Os padrões seguintes na secção [padrões de Design de tabela](#table-design-patterns) atendem a otimizar o desempenho ou a inserção, atualização e operações de eliminação:  

* [Eliminar o volume alto padrão de](#high-volume-delete-pattern) -ativar a eliminação de um grande volume de entidades ao armazenar todas as entidades para eliminação simultânea em sua própria tabela separada; para eliminar as entidades a eliminar a tabela.  
* [Padrão de série de dados](#data-series-pattern) -série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  
* [Padrão de entidades ampla](#wide-entities-pattern) -utilizar várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de entidades grandes](#large-entities-pattern) -utilizar o armazenamento de BLOBs para armazenar os valores de propriedade grandes.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Garantindo a consistência nas suas entidades armazenadas
O fator principal que influencia à sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência com transações atômicas. Só pode utilizar um EGT para operar em entidades armazenadas na mesma partição.  

Os padrões seguintes na secção [padrões de Design de tabela](#table-design-patterns) consistência de gerenciamento de endereço:  

* [Padrão de índice secundário de intra-partition](#intra-partition-secondary-index-pattern) -Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores.  
* [Padrão de índice secundário de partição entre](#inter-partition-secondary-index-pattern) - Store várias cópias de cada entidade usando diferentes RowKey valores em partições separadas ou em tabelas separadas para permitem o rápido e eficientes pesquisas e de classificação alternativa ordena usando diferentes **RowKey** valores.  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) -ativar o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.
* [Padrão de entidades de índice](#index-entities-pattern) -manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern) -combinar dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  
* [Padrão de série de dados](#data-series-pattern) -série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

Para obter informações sobre transações de grupo de entidades, consulte a secção [transações de grupo de entidades](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir que seu design para modificações eficientes facilita a consultas eficientes
Em muitos casos, uma estrutura para os resultados da consultas eficientes modificações eficientes, mas sempre deve avaliar se for este o caso para o seu cenário específico. Alguns dos padrões na secção [padrões de Design de tabela](#table-design-patterns) explicitamente avaliar compromissos entre consultar e modificar entidades, e sempre deve ter em conta o número de cada tipo de operação.  

Os padrões seguintes na secção [padrões de Design de tabela](#table-design-patterns) compromissos entre o design para consultas eficientes e estruturar para modificação de dados eficiente de endereços:  

* [Padrão de chave composta](#compound-key-pattern) -utilize compostas **RowKey** valores para permitir que um cliente procurar relacionados com dados com um ponto único de consulta.  
* [Padrão de cauda do registo](#log-tail-pattern) -obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar uma **RowKey** valor ordena inversa de data e a ordem de tempo.  

## <a name="encrypting-table-data"></a>Encriptação de dados de tabela
Biblioteca de cliente de armazenamento do Azure de .NET suporta a encriptação das propriedades de entidade de cadeia de caracteres para inserir e substitua operações. As cadeias de caracteres encriptadas são armazenadas no serviço como propriedades binárias e elas são convertidas para cadeias de caracteres após desencriptação.    

Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito, qualquer um dos especificando um atributo [EncryptProperty] (para entidades POCO que derivam de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que assume uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Não é necessário fornecer estas informações ao ler ou consultar entidades.

Merge não é atualmente suportada. Uma vez que um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente as novas propriedades de mesclagem e atualizar os metadados resultará na perda de dados. Intercalar o requer a fazer chamadas de serviço extra para ler a entidade já existente do serviço ou usando uma nova chave por propriedade, que não são adequados por motivos de desempenho.     

Para obter informações sobre a encriptação de dados da tabela, consulte [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modelar relações
A criação de modelos de domínio é uma etapa importante no design de sistemas complexos. Normalmente, o processo de modelagem é utilizado para identificar as entidades e as relações entre eles, como uma forma de compreender o domínio de empresa e informar o design do seu sistema. Esta seção se concentra em como é possível converter alguns dos tipos de relação comuns encontrados em modelos de domínio para designs para o serviço de tabela. O processo de mapeamento de um modelo de dados lógico para um físico baseados em NoSQL-modelo de dados é diferente da usada ao conceber uma base de dados relacional. Design de bancos de dados relacionais normalmente pressupõe um processo de normalização de dados otimizado para minimizar a redundância – e uma capacidade de consulta declarativa que abstrai a como a implementação de como funciona a base de dados.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Existem várias formas de implementar as relações um-para-muitos no serviço tabela cada com prós e contras que podem ser relevantes para o cenário em particular.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionário em que cada departamento tem muitos funcionários e de cada funcionário como associados a um departamento específico. Uma abordagem é armazenar separado de departamento e entidades de funcionário como estas:  

![Entidade de departamento e de funcionário][1]

Este exemplo mostra uma relação um-para-muitos implícita entre tipos de acordo com o **PartitionKey** valor. Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e respetivas entidades de funcionários relacionados na mesma partição. Poderia optar por utilizar partições diferentes, tabelas ou contas de armazenamento até mesmo para os tipos de entidade diferentes.  

Uma abordagem alternativa é desnormalizar os dados e armazenar apenas as entidades de funcionários com dados de departamento desnormalizada, conforme mostrado no exemplo a seguir. Neste cenário específico, esta abordagem desnormalizada pode não ser a melhor se tiver um requisito para poder alterar os detalhes de um Gestor de departamento, porque para fazer isso tem de atualizar todos os funcionários do departamento.  

![Entidade Employee][2]

Para obter mais informações, consulte a [padrão de desnormalização](#denormalization-pattern) mais adiante neste guia.  

A tabela seguinte resume os prós e contras de cada uma das abordagens descritas acima para o armazenamento de funcionários e entidades de departamento que tenham uma relação um-para-muitos. Também deve considerar a frequência esperada executar várias operações: pode ser aceitável para ter um design que inclui uma operação dispendiosa, se essa operação só acontece com pouca frequência.  

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
<li>Terá de obter um funcionário e uma entidade do departamento para algumas atividades de cliente.</li>
<li>Operações de armazenamento acontecem na mesma partição. Em volumes de alta transação, isso pode resultar num ponto de acesso.</li>
<li>Não é possível mover um funcionário para um novo departamento, usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separada, partições diferentes, ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em volumes de alta transação, isto pode ajudar a distribuir a carga por mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Terá de obter um funcionário e uma entidade do departamento para algumas atividades de cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualizar/inserir/eliminar um funcionário e atualização de um departamento. Por exemplo, a atualizar um número de funcionários numa entidade de departamento.</li>
<li>Não é possível mover um funcionário para um novo departamento, usando um EGT.</li>
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
<li>Pode ser dispendioso para manter a consistência, se precisar de atualizar as informações do departamento (isso exigiria que Atualize todos os funcionários num departamento).</li>
</ul>
</td>
</tr>
</table>

Como escolher entre estas opções, e quais os prós e contras são mais significativas, depende do seus cenários de aplicação específica. Por exemplo, a frequência com que modifique entidades de departamento; todas as suas consultas de funcionário preciso obter mais informações departamentais; como fechar é para os limites de escalabilidade em suas partições ou a sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um para um
Modelos de domínio podem incluir um para um relações entre entidades. Se precisar de implementar uma relação unívoca no serviço tabela, também tem de escolher como ligar as duas entidades relacionadas, quando precisar de obter ambos. Esta ligação pode ser implícita, com base numa convenção dos valores de chave ou explícita ao armazenar uma ligação sob a forma de **PartitionKey** e **RowKey** valores em cada entidade para a entidade relacionada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma partição, consulte a secção [um-para-muitos relações](#one-to-many-relationships).  

Também existem considerações de implementação que podem levar a implementar as relações um para um no serviço de tabela:  

* Tratamento de entidades grandes (para obter mais informações, consulte [grandes entidades padrão](#large-entities-pattern)).  
* A implementação de controlos de acesso (para obter mais informações, consulte [controlar o acesso com assinaturas de acesso partilhado](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Junte-se no cliente
Embora existam formas para modelar relações no serviço tabela, deve não se esqueça de que os dois motivos principais para utilizar o serviço de tabela são a escalabilidade e desempenho. Se encontrar a que modelagem de relações de muitos que comprometem o desempenho e escalabilidade da sua solução, deve se perguntar se é necessário criar todas as relações de dados em seu design de tabela. Poderá conseguir simplificar o design e melhorar a escalabilidade e desempenho da sua solução se permitir que a aplicação cliente realizar qualquer uniões necessárias.  

Por exemplo, se tiver pequenas tabelas que contêm dados que não são alterados muitas vezes, em seguida, pode obter estes dados uma vez e cache no cliente. Isto pode evitar idas e voltas repetidas para obter os mesmos dados. Nos exemplos, temos analisado a neste guia, o conjunto de departamentos numa pequena organização, é provável que ser pequeno e alterar com pouca frequência fazendo um bom candidato para dados que a aplicação cliente pode transferir uma vez e cache, como dados de pesquisa.  

### <a name="inheritance-relationships"></a>Relações de herança
Se a aplicação cliente utiliza um conjunto de classes que fazem parte de uma relação de herança para representar as entidades comerciais, pode facilmente manter essas entidades no serviço tabela. Por exemplo, pode ter o seguinte conjunto de classes definidas na aplicação de cliente em que **pessoa** é uma classe abstrata.

![Diagrama de ER das relações de herança][3]

Pode manter instâncias das duas classes concretas no serviço de tabela com uma única tabela de pessoa com entidades aquela aparência como esta:  

![Diagrama da entidade Customer e entidade Employee][4]

Para obter mais informações sobre como trabalhar com vários tipos de entidade na mesma tabela no código do cliente, consulte a secção [trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types) mais adiante neste guia. Esta opção fornece exemplos de como a reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de Design de tabela
Nas seções anteriores, viu que alguns detalhadas discussões sobre como otimizar seu design de tabela para os dois ao obter dados de entidades através de consultas e para inserir, atualizar e eliminar dados de entidade. Esta secção descreve alguns padrões adequados para utilização com soluções de serviço de tabela. Além disso, irá ver como pode praticamente solucionar alguns problemas e compensações geradas anteriormente neste guia. O diagrama seguinte resume as relações entre os diferentes padrões:  

![Imagem de padrões de design de tabela][5]

O mapa padrão acima destaca algumas relações entre (azuis) de padrões e antipadrões (laranja) que estão documentados neste guia. Claro que existem muitos outros padrões que vale a pena examinar. Por exemplo, um dos principais cenários para o serviço de tabela é usar o [padrão de vista materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) partir a [comando segregação de responsabilidade da consulta (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) padrão.  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de partição dentro de índice secundário
Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores. As atualizações entre cópias podem ser mantidas consistentes com EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar a entidade com eficiência com estes valores. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma consulta de ponto para obter uma entidade employee individuais com o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**  valores). Um cliente também pode obter entidades ordenadas por id de funcionário dentro de cada departamento.

![Entidade Employee][6]

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não existe nenhuma opção para solicitar uma lista de funcionários ordenados numa ordem diferente daquela **RowKey** ordem.  

#### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, é possível armazenar várias cópias de cada entidade com cada cópia utilizando outro **RowKey** valor. Se armazenar uma entidade com as estruturas de mostrado abaixo, pode obter entidades de funcionários com base no id de e-mail endereço ou funcionário com eficiência. O prefixo de valores para o **RowKey**, "empid_" e "email_" permitem consultar para um único funcionário ou um intervalo de funcionários com um intervalo de endereços de e-mail ou ids de funcionários.  

![Entidade Employee com diferentes valores de RowKey][7]

Os seguintes critérios de filtro de dois (um procurar por id de funcionário e uma pesquisa por endereço de e-mail), ambos especificar consultas de ponto:  

* $filter = (PartitionKey eq "Vendas") e (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Vendas") e (RowKey eq 'email_jonesj@contoso.com")  

Se consultar um intervalo de entidades de funcionários, pode especificar um intervalo ordenados por ordem de id do funcionário ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com um id de funcionário no uso de intervalo 000100 para 000199: $filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000100") e (RowKey le "empid_000199")  
* Para localizar todos os funcionários do departamento de vendas com um endereço de e-mail que começam com a letra "a" utilização: $filter = (PartitionKey eq "Vendas") e (RowKey ge "email_a") e (RowKey lt "email_b")  
  
  É a sintaxe de filtro utilizada nos exemplos acima da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Armazenamento de tabelas é relativamente barato usar de modo que a sobrecarga de custos do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, deve sempre avaliar o custo de seu design com base nos seus requisitos de armazenamento previsto e apenas adicionar duplicadas entidades para suportar as consultas que a aplicação de cliente será executado.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, deve certificar-se de que não excedem os destinos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si utilizando EGTs para atualizar as duas cópias da entidade de atomicamente. Isso implica que deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte a secção [transações de grupo de entidades usando](#entity-group-transactions).  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Preenchimento de valores numéricos no **RowKey** (por exemplo, a identificação do funcionário 000223), permite corrigir de classificação e filtragem com base no superior e os limites inferiores.  
* Não precisa necessariamente duplicar todas as propriedades de entidade. Por exemplo, se as consultas que procuram as entidades com a mensagem de e-mail de endereços no **RowKey** nunca precisam de idade do funcionário, estas entidades poderiam ter a seguinte estrutura:

![Entidade Employee][8]

* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que possa recuperar todos os dados que precisa com uma única consulta, que usar uma consulta para localizar uma entidade e outro para pesquisar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente precisa obter entidades utilizando uma variedade de chaves diferentes, quando o cliente tem de obter entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. No entanto, deve se certificar de que excede os limites de escalabilidade de partição quando estiver a efetuar pesquisas de entidade usando o diferentes **RowKey** valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice de partição entre secundário](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações do grupo de entidade](#entity-group-transactions)
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice de partição entre secundário
Store várias cópias de cada entidade usando diferentes **RowKey** valores em separar as partições ou separar em tabelas a ativação rápida e eficientes pesquisas e a ordens de classificação alternativas ao utilizar diferentes **RowKey**valores.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar a entidade com eficiência com estes valores. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma consulta de ponto para obter uma entidade employee individuais com o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**  valores). Um cliente também pode obter entidades ordenadas por id de funcionário dentro de cada departamento.  

![Entidade Employee][9]

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não existe nenhuma opção para solicitar uma lista de funcionários ordenados numa ordem diferente daquela **RowKey** ordem.  

Prevendo o um grande volume de transações em relação a essas entidades e deseja minimizar o risco do seu cliente de limitação de taxas do serviço tabela.  

#### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, é possível armazenar várias cópias de cada entidade com cada cópia usando diferentes **PartitionKey** e **RowKey** valores. Se armazenar uma entidade com as estruturas de mostrado abaixo, pode obter entidades de funcionários com base no id de e-mail endereço ou funcionário com eficiência. O prefixo de valores para o **PartitionKey**, "empid_" e "email_" ative-o a identificar que o índice que pretende utilizar para uma consulta.  

![Entidade Employee com índice principal e a entidade Employee com índice secundário][10]

Os seguintes critérios de filtro de dois (um procurar por id de funcionário e uma pesquisa por endereço de e-mail), ambos especificar consultas de ponto:  

* $filter = (PartitionKey eq ' empid_Sales") e (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") e (RowKey eq 'jonesj@contoso.com")  

Se consultar um intervalo de entidades de funcionários, pode especificar um intervalo ordenados por ordem de id do funcionário ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com um id de funcionário no intervalo **000100** ao **000199** classificadas na utilização de ordem de id de funcionário: $filter = (PartitionKey eq ' empid_Sales") e (RowKey ge"000100") e (RowKey le "000199")  
* Para localizar todos os funcionários do departamento de vendas com um endereço de e-mail que começa com "a" classificado em utilização de ordem de endereço de e-mail: $filter = (PartitionKey eq ' email_Sales") e (RowKey ge"a") e (RowKey lt"b")  

Tenha em atenção que é a sintaxe de filtro utilizada nos exemplos acima da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistente entre si utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* Armazenamento de tabelas é relativamente barato usar de modo que a sobrecarga de custos do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, deve sempre avaliar o custo de seu design com base nos seus requisitos de armazenamento previsto e apenas adicionar duplicadas entidades para suportar as consultas que a aplicação de cliente será executado.  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Preenchimento de valores numéricos no **RowKey** (por exemplo, a identificação do funcionário 000223), permite corrigir de classificação e filtragem com base no superior e os limites inferiores.  
* Não precisa necessariamente duplicar todas as propriedades de entidade. Por exemplo, se as consultas que pesquisa as entidades com a mensagem de e-mail de endereços no **RowKey** nunca precisam de idade do funcionário, estas entidades poderiam ter a seguinte estrutura:
  
  ![Entidade Employee com índice secundário][11]
* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que possa recuperar todos os dados que precisa com uma única consulta que usar uma consulta para localizar a entidade com o índice secundário e outro para pesquisar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente precisa obter entidades utilizando uma variedade de chaves diferentes, quando o cliente tem de obter entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. Utilize este padrão quando deseja evitar exceder os limites de escalabilidade de partição quando estiver a efetuar pesquisas de entidade usando o diferentes **RowKey** valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de partição dentro de índice secundário](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações do grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ative o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs ativar transações atómicas em múltiplas entidades que partilham a mesma chave de partição. Por motivos de escalabilidade e de desempenho, pode optar por armazenar entidades que tenham requisitos de consistência em partições separadas ou num sistema de armazenamento separada: neste cenário, não é possível utilizar EGTs para manter a consistência. Por exemplo, pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, nas tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada no serviço tabela e um blob armazenados no serviço de Blobs.  
* Uma entidade armazenada no serviço de tabela e um arquivo de um sistema de ficheiros.  
* Um arquivo de entidade no serviço tabela ainda indexado com o serviço Azure Search.  

#### <a name="solution"></a>Solução
Ao utilizar as filas do Azure, pode implementar uma solução que fornece a consistência eventual entre dois ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, partem do princípio de que tem um requisito para conseguir arquivar antigo entidades de funcionários. Entidades do antigo funcionário raramente são consultadas e devem ser excluídas da quaisquer atividades que lidam com funcionários atuais. Para implementar esse requisito é armazenar funcionários ativos no **atual** tabela e funcionários antigos no **arquivo** tabela. Arquivamento de um funcionário, tem de eliminar a entidade dos **atual** da tabela e adicionar a entidade para o **arquivo** tabela, mas não é possível utilizar um EGT para efetuar estas duas operações. Para evitar o risco de uma falha faz com que uma entidade a aparecer nas tabelas de ambos ou nenhum, a operação de arquivo tem de ser eventualmente consistente. O diagrama de sequência seguinte descreve os passos nesta operação. Mais detalhes é fornecido para caminhos de exceção na seguinte texto.  

![Diagrama da solução para manter a consistência eventual][12]

Um cliente inicia a operação de arquivamento, colocando uma mensagem numa fila do Azure, neste exemplo, para arquivar o funcionário #456. Uma função de trabalho consulta a fila para novas mensagens; Quando encontrar uma, ele lê a mensagem e mantém uma cópia oculta na fila. A função de trabalho a seguir obtém uma cópia da entidade do **atual** tabela, insere uma cópia no **arquivo** da tabela e, em seguida, elimina o original do **atual** tabela. Por fim, se não houver nenhum erro dos passos anteriores, a função de trabalho elimina a mensagem oculta da fila.  

Neste exemplo, o passo 4 insere o funcionário para o **arquivo** tabela. Ele foi possível adicionar o funcionário para um blob no serviço Blob ou um ficheiro num sistema de ficheiros.  

#### <a name="recovering-from-failures"></a>Recuperar de falhas
É importante que as operações nas etapas **4** e **5** tem de ser *idempotentes* no caso da função de trabalho tem de reiniciar a operação de arquivamento. Se estiver a utilizar o serviço de tabela para o passo **4** deve utilizar uma operação de "Inserir ou substituir"; para o passo **5** deve usar um "eliminar se existe" operação na biblioteca de cliente que está a utilizar. Se estiver a utilizar outro sistema de armazenamento, tem de utilizar uma operação idempotente apropriado.  

Se a função de trabalho nunca completa a etapa **6**, em seguida, após um tempo limite a mensagem será exibida novamente na fila pronta para a função de trabalho tentar reprocessá-lo. A função de trabalho pode verificar o número de vezes que uma mensagem na fila foi ler e, se necessário, o sinalizador é uma mensagem "não processáveis" para investigação enviando-os para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificar a contagem de dequeue, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros de serviços de tabela e fila são erros transitórios, e a aplicação cliente deve incluir a lógica de repetição adequado para manipulá-las.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não fornece isolamento de transação. Por exemplo, um cliente poderia ler os **atual** e **arquivo** tabelas quando a função de trabalho foi entre passos **4** e **5**e ver um exibição inconsistente dos dados. Eventualmente, os dados serão consistentes.  
* Deve ter certeza de que os passos 4 e 5 são idempotentes para garantir a consistência eventual.  
* Pode dimensionar a solução ao utilizar várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja garantir a consistência eventual entre entidades que existem em partições diferentes ou tabelas. Pode estender esse padrão para assegurar a consistência eventual para operações entre o serviço de tabela e o serviço de BLOBs e outros armazenamento do Azure origens de dados, como a base de dados ou o sistema de ficheiros.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações do grupo de entidade](#entity-group-transactions)  
* [Intercalação ou substituição](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transação é importante para a sua solução, deve considerar a ter de redesenhar a sua tabelas para permitir que use EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode com eficiência obter uma entidade employee individuais utilizando o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**).  

![Entidade Employee][13]

Se pretender ser capaz de obter uma lista de entidades de funcionários com base no valor de outra propriedade exclusivos, como o sobrenome, também tem de utilizar uma análise de partição menos eficiente para localizar correspondências, em vez de usar um índice examiná-los diretamente. Isto acontece porque o serviço de tabela não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para ativar a pesquisa por apelido com a estrutura de entidade mostrada acima, tem de manter listas de ids de funcionários. Se quiser obter as entidades de funcionários com um nome de última específico, como Jones, tem de primeiro localizar a lista de ids de funcionário para funcionários com Jones como sobrenome e, em seguida, obter essas entidades de funcionários. Existem três opções principais para armazenar as listas de ids de funcionário:  

* Utilize o armazenamento de Blobs.  
* Crie entidades de índice na mesma partição que as entidades de funcionários.  
* Crie entidades de índice numa partição separada ou a tabela.  

<u>Opção #1: Utilizar o armazenamento de BLOBs</u>  

Para a primeira opção, crie um blob para cada nome de última exclusivo e, em cada armazenamento de BLOBs uma lista do **PartitionKey** (department) e **RowKey** valores (identificação do funcionário) para os funcionários que tenham esse nome passado. Quando adicionar ou eliminar um funcionário, deve garantir que o conteúdo do blob relevante é eventualmente consistente com as entidades de funcionários.  

<u>Opção #2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, utilize entidades de índice que armazenam os dados seguintes:  

![Entidade Employee com a cadeia de caracteres contendo uma lista de IDs de funcionário com o mesmo apelido][14]

O **EmployeeIDs** propriedade contém uma lista de ids de funcionário para os funcionários com o nome da última armazenado no **RowKey**.  

Os passos seguintes descrevem o processo que deve seguir quando estiver a adicionar um novo funcionário se estiver a utilizar a segunda opção. Neste exemplo, estamos a adicionar um funcionário com 000152 de Id e um apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Sales" e o **RowKey** valor "Jones." Guarde a ETag dessa entidade para utilizar no passo 2.  
2. Crie uma transação de entidade de grupo (ou seja, uma operação em lote) que insere a nova entidade de funcionário (**PartitionKey** valor "Sales" e **RowKey** valor "000152") e atualiza a entidade de índice (**PartitionKey** valor de "Sales" e **RowKey** valor "Jones") ao adicionar o novo id de funcionário à lista no campo EmployeeIDs. Para obter mais informações sobre transações de grupo de entidades, veja [transações de grupo de entidades](#entity-group-transactions).  
3. Se a transação do grupo de entidade falhar devido a um erro de simultaneidade otimista (outra pessoa modificou a entidade de índice), em seguida, terá de recomeçar novamente no passo 1.  

Pode usar uma abordagem semelhante para eliminar um funcionário, se estiver a utilizar a segunda opção. Alterar o sobrenome de um funcionário é um pouco mais complexo porque vai precisar de executar uma transação de grupo de entidade que atualiza as três entidades: entidade employee, a entidade de índice para o nome antigo do último e a entidade de índice para o novo apelido. Tem de obter cada entidade antes de fazer alterações para poder recuperar os valores de ETag que, em seguida, pode utilizar para efetuar as atualizações usando a simultaneidade otimista.  

Os passos seguintes descrevem o processo que deve seguir quando precisar de pesquisar todos os funcionários com um determinado nome de último num departamento, se estiver a utilizar a segunda opção. Neste exemplo, estamos indo dormir todos os funcionários com o apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Sales" e o **RowKey** valor "Jones."  
2. Analisar a lista de Ids no campo EmployeeIDs do funcionário.  
3. Se precisar de informações adicionais sobre cada uma destes funcionários (por exemplo, os endereços de e-mail), obter cada uma dessas entidades employee usando **PartitionKey** valor "Sales" e **RowKey** valores do lista de funcionários que obteve no passo 2.  

<u>Opção #3:</u> Criar entidades de índice numa partição separada ou de uma tabela  

Para a terceira opção, utilize entidades de índice que armazenam os dados seguintes:  

![Entidade Employee com a cadeia de caracteres contendo uma lista de IDs de funcionário com o mesmo apelido][15]

O **EmployeeIDs** propriedade contém uma lista de ids de funcionário para os funcionários com o nome da última armazenado no **RowKey**.  

Com a terceira opção, não é possível utilizar EGTs para manter a consistência, porque as entidades de índice são numa partição separada das entidades de funcionários. Deve garantir que as entidades de índice são eventualmente consistentes com as entidades de funcionários.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer, pelo menos, duas consultas para recuperar entidades correspondentes: um para consultar entidades de índice para obter a lista de **RowKey** valores e, em seguida, obter cada entidade na lista de consultas.  
* Uma vez que uma entidade individual tem um tamanho máximo de 1 MB, a opção #2 e a opção 3 # na solução partem do princípio de que a lista de ids de funcionário para qualquer determinado apelido nunca é superior a 1 MB. Se a lista de ids de funcionário é suscetível de ser maior que 1 MB de tamanho, utilize a opção #1 e armazenar os dados de índice no armazenamento de Blobs.  
* Se utilizar a opção #2 (usando EGTs para lidar com adicionar e eliminar os funcionários e alterar o sobrenome de um funcionário) tem de avaliar se o volume de transações abordará os limites de escalabilidade numa determinada partição. Se for este o caso, deve considerar uma solução eventualmente consistente (opção #1 ou a opção #3) que utiliza as filas para processar os pedidos de atualização e permite-lhe armazenar suas entidades de índice numa partição separada das entidades de funcionários.  
* Opção #2 nesta solução parte do princípio de que deseja procurar por apelido dentro de um departamento: por exemplo, deseja recuperar uma lista de funcionários com um apelido Jones no departamento de vendas. Se quiser ser capaz de procurar todos os funcionários com um apelido Jones em toda a organização, utilize a opção #1 ou opção #3.
* Pode implementar uma solução de baseada na fila de mensagens em fila que fornece a consistência eventual (veja a [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja pesquisar um conjunto de entidades que todas partilham um valor de propriedade comuns, como todos os funcionários com o nome da última Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Transações do grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente, normalizar dados para remover a duplicação, resultando em consultas que obtêm dados de várias tabelas. Se normalizar os dados nas tabelas do Azure, tem de certificar vários ida e volta do cliente para o servidor para obter os dados relacionados. Por exemplo, com a estrutura da tabela mostrada abaixo, tem duas ida e volta ao obter os detalhes de um departamento: um para obter a entidade de departamento, que inclui o id do gestor e, em seguida, outra solicitação para obter os detalhes do gestor numa entidade employee.  

![Entidade de departamento e entidade Employee][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade do departamento. Por exemplo:  

![Entidade de departamento desnormalizada e combinada][17]

Com as entidades de departamento armazenadas com estas propriedades, agora pode recuperar todos os detalhes que precisa sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Existe algum custo de sobrecarga associado ao armazenamento alguns dados duas vezes. O benefício de desempenho (resultantes de menos de pedidos para o serviço de armazenamento) normalmente vale mais que o marginal aumento nos custos de armazenamento (e este custo parcialmente é contrabalançado por uma redução no número de transações que necessita para obter os detalhes de um departamento ).  
* Tem de manter a consistência das duas entidades que armazenam informações sobre os gestores. Pode manipular o problema de consistência utilizando EGTs para atualizar várias entidades numa transação atômica única: neste caso, a entidade de departamento e a entidade employee para o Gestor de departamento são armazenadas na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa com frequência procurar informações relacionadas. Este padrão reduz o número de consultas de que seu cliente tem de efetuar para recuperar os dados que ela requer.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações do grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Utilize compostas **RowKey** valores para permitir que um cliente pesquisar dados relacionados com um ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é natural usar junções em consultas para retornar partes relacionadas de dados para o cliente numa única consulta. Por exemplo, poderá utilizar a identificação do funcionário para procurar uma lista de entidades relacionadas que contêm o desempenho e rever os dados para aquele funcionário.  

Suponha que estão a armazenar entidades de funcionários no serviço tabela usando a seguinte estrutura:  

![Entidade Employee][18]

Também precisa de armazenar dados históricos relacionadas com as revisões e desempenho para cada ano, que o funcionário trabalhou para a sua organização e tem de ser capaz de aceder a estas informações por ano. Uma opção é criar outra tabela que armazena as entidades com a seguinte estrutura:  

![Entidade de revisão de funcionário][19]

Tenha em atenção que esta abordagem poderá optar por duplicar algumas informações (como o nome próprio e apelido) na nova entidade que lhe permite obter os seus dados com um único pedido. No entanto, não é possível manter a consistência forte porque não é possível utilizar um EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Store um novo tipo de entidade na sua tabela original usando entidades com a seguinte estrutura:  

![Entidade Employee com a chave composta][20]

Observe como o **RowKey** agora é uma chave composta constituída pela identificação do funcionário e o ano dos dados de revisão que permite-lhe obter o desempenho do funcionário e consultar dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como recuperar todos os dados de revisão de um funcionário específico (por exemplo, o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000123") e (RowKey lt 'empid_000124') & $select = RowKey, Gestor de classificação, a classificação de ponto a ponto, comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um caráter de separação adequado que torna mais fácil de analisar os **RowKey** valor: por exemplo, **000123_2012**.  
* Também estão a armazenar esta entidade na mesma partição como outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que pode usar EGTs para manter a consistência forte.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é adequado.  Por exemplo, se acessará os dados de revisão com pouca frequência e os dados de funcionários principal, muitas vezes, deve evitar que elas entidades como separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver de armazenar um ou mais entidades relacionadas que consulta frequentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações do grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de cauda do registo
Obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar um **RowKey** valor ordena inversa de data e a ordem de tempo.  

> [!NOTE]
> Resultados devolvidos pela API de tabela do Azure no Azure DB não são ordenados pela chave de partição ou chave de linha. Portanto, esse padrão é adequado para armazenamento de tabelas do Azure e não o Azure Cosmos DB. Para obter uma lista detalhada das diferenças de funcionalidade, consulte [diferenças entre a API de tabela no Azure Cosmos DB e armazenamento de tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é conseguir obter as entidades recentemente criadas, por exemplo o mais recente dez afirmações enviadas por um funcionário de despesas. Suporte de consulta de tabela uma **$top** consultar operação para retornar o primeiro *n* entidades a partir de um conjunto: não existe nenhuma operação equivalente de consulta para devolver as último entidades n num conjunto.  

#### <a name="solution"></a>Solução
Store as entidades com um **RowKey** que naturalmente Ordena por ordem inversa de data/hora, utilizando a entrada por isso, a mais recente é sempre o primeiro na tabela.  

Por exemplo, para poder recuperar as dez declarações de despesas mais recentes enviadas por um funcionário, pode utilizar um valor de escala inversa derivado de data/hora atual. O seguinte exemplo de código do c# mostra uma forma de criar um valor de "invertido ticks" adequado para um **RowKey** que classifica da mais recente para o mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar para o valor de hora de data usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem o seguinte aspeto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Tem de preencher o valor de escala inversa aos principais zeros para garantir que o valor de cadeia de caracteres ordena conforme esperado.  
* Deve estar ciente dos destinos de escalabilidade no nível de uma partição. Tenha cuidado não criar partições de ponto de acesso.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa acessar as entidades na ordem inversa de data/hora ou quando precisa acessar as entidades adicionadas recentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Antipadrão de preceder /append](#prepend-append-anti-pattern)  
* [Obter entidades](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de volume elevado
Ativar a eliminação de um grande volume de entidades ao armazenar todas as entidades para eliminação simultânea em sua própria tabela separada; eliminar entidades ao eliminar a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos eliminar dados antigos que já não tem de estar disponíveis para uma aplicação cliente ou que o aplicativo tem arquivados para outro meio de armazenamento. Normalmente identificar esses dados por uma data: por exemplo, tem um requisito para eliminar registos de todos os início de sessão pedidos que têm mais de 60 dias.  

Um design possíveis é usar a data e hora do pedido no início de sessão do **RowKey**:  

![Entidade de tentativa de início de sessão][21]

Esta abordagem evita a pontos ativos de partição porque o aplicativo pode inserir e eliminar entidades para cada utilizador numa partição separada de início de sessão. No entanto, essa abordagem pode ser dispendioso e moroso se tiver um grande número de entidades, uma vez que primeiro precisa executar uma análise de tabela para identificar todas as entidades para eliminar e, em seguida, tem de eliminar cada entidade antiga. Pode reduzir o número de ida e volta para o servidor necessário para eliminar as entidades antigas através da criação de batches vários pedidos delete para EGTs.  

#### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de início de sessão. Pode usar a estrutura de entidade acima para evitar pontos ativos durante a inserção de entidades, e excluindo entidades antigas agora é simplesmente uma questão de eliminação de todos os dias de uma tabela (uma operação de armazenamento única), em vez de localizar e eliminar centenas e milhares de pessoa sinais nas entidades todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas do aplicativo usará os dados, como a pesquisa de entidades específicas, com outros dados, ou gerar agregam informações de ligação?  
* O design evitar pontos ativos ao são inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de tabela depois excluí-lo. É melhor sempre usar nomes de tabela exclusivo.  
* Espere alguns limitação de velocidade quando usar primeiro uma nova tabela, enquanto o serviço de tabela aprende os padrões de acesso e distribui as partições entre nós. Deve considerar a frequência tem de criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tem um grande volume de entidades que tem de eliminar ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações do grupo de entidade](#entity-group-transactions)
* [Modificar entidades](#modifying-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é para uma aplicação armazenar uma série de dados que, normalmente, precisar de obter ao mesmo tempo. Por exemplo, seu aplicativo pode gravar o número de mensagens Instantâneas de mensagens, cada funcionário envia a cada hora e, em seguida, utilize estas informações para desenhar quantas mensagens cada usuário enviado através das 24 horas anteriores. Um design poderá estar a armazenar 24 entidades de cada funcionário:  

![Entidade de estatísticas de mensagem][22]

Com esta estrutura, pode facilmente localizar e atualizar a entidade a atualizar para cada funcionário, sempre que a aplicação tem de atualizar o valor de contagem de mensagens. No entanto, para obter as informações para desenhar um gráfico da atividade para as anteriores 24 horas, tem de obter 24 entidades.  

#### <a name="solution"></a>Solução
Utilize a seguinte estrutura com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagem com propriedades separadas][23]

Com esta estrutura, pode usar uma operação de intercalação para atualizar a contagem de mensagens para um funcionário durante uma hora específica. Agora, pode recuperar todas as informações necessárias desenhar o gráfico através de um pedido para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se sua série completa de dados não se encaixam numa única entidade (uma entidade pode ter até 252 propriedades), utilize um arquivo de dados alternativo, como um blob.  
* Se tiver vários clientes, atualizar uma entidade em simultâneo, terá de utilizar o **ETag** para implementar a simultaneidade otimista. Se tiver muitos clientes, pode provocar a contenção elevada.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa atualizar e obter uma série de dados associada a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Intercalação ou substituição](#merge-or-replace)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) (se estiver armazenando a série de dados num blob)  

### <a name="wide-entities-pattern"></a>Padrão de entidades grande
Utilize várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter mais do que 252 propriedades (excluindo as propriedades do sistema obrigatórios) e não é possível armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente obteria arredondar limites sobre o tamanho de uma linha ao adicionar uma nova tabela e a imposição de uma relação de 1 para 1 entre elas.  

#### <a name="solution"></a>Solução
Com o serviço tabela, pode armazenar várias entidades para representar um objeto de empresas de grande única com mais de 252 propriedades. Por exemplo, se quiser armazenar uma contagem do número de mensagens Instantâneas enviados por cada funcionário para os últimos 365 dias, poderia usar a estrutura seguinte, que utiliza duas entidades com esquemas diferentes:  

![Entidade de estatísticas de mensagem com Rowkey 01 e mensagem de entidades de estado com Rowkey 02][24]

Se precisar de fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma operação de intercalação único para atualizar a contagem de mensagens para um dia específico. Para obter todos os dados para um funcionário tem de obter as duas entidades, que pode ser feito com duas solicitações eficientes que utilizam ambos um **PartitionKey** e uma **RowKey** valor.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Obter uma entidade lógica concluída envolve a, pelo menos, duas transações de armazenamento: um para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa armazenar entidades cujo tamanho ou número de propriedades exceder os limites para uma entidade individual no serviço tabela.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações do grupo de entidade](#entity-group-transactions)
* [Intercalação ou substituição](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Utilize o armazenamento de BLOBs para armazenar valores de propriedade grandes.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não é possível armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenam valores que fazer com que o tamanho total da sua entidade exceder este valor, não é possível armazenar a entidade inteira no serviço tabela.  

#### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho, porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no serviço de BLOBs e, em seguida, armazenar o endereço do blob numa propriedade na entidade. Por exemplo, pode armazenar a fotografia de um empregado no armazenamento de BLOBs e armazenar uma ligação para a foto na **fotos** propriedade da sua entidade employee:  

![Entidade Employee com a cadeia de caracteres para Foto que aponta para o armazenamento de BLOBs][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no serviço tabela e os dados no serviço Blob, utilize o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter suas entidades.
* Obter uma entidade completa envolve a, pelo menos, duas transações de armazenamento: um para recuperar a entidade e outra para recuperar os dados de Blobs.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisar armazenar entidades cujo tamanho exceda os limites para uma entidade individual no serviço tabela.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades grande](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão preceder/acrescentar
Aumente a escalabilidade quando tiver um grande volume de inserções ao propagar as inserções em várias partições.  

#### <a name="context-and-problem"></a>Contexto e problema
Prefixação ou acrescentando entidades para as suas entidades armazenadas normalmente resulta na aplicação a adicionar novas entidades na partição do primeiro ou último de uma sequência de partições. Neste caso, todas as inserções em qualquer momento estão a ocorrer na mesma partição, a criação de um ponto de acesso que impede que o serviço de tabela de balanceamento de carga insere em vários nós e, possivelmente, fazendo com que seu aplicativo atingir as metas de escalabilidade para partição. Por exemplo, se tiver uma aplicação que aceder a recursos e de rede de registos por funcionários, em seguida, uma estrutura de entidades, como mostrado a seguir pode resultar na partição a hora atual tornar-se de um ponto de acesso se o volume de transações de atingir o destino de escalabilidade para um partição individual:  

![Entidade Employee][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de acesso em qualquer partição específica, como os eventos de registos de aplicações:  

![Entidade Employee com RowKey para piorar o ano, mês, dia, hora e o ID de evento][27]

Observe que com este exemplo de como os dois o **PartitionKey** e **RowKey** são as chaves compostas. O **PartitionKey** utiliza o id do departamento e o funcionário a distribuir o registo por várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa evita a criação de partições muito ativas em inserções com eficiência suporta as consultas que a aplicação de cliente faz?  
* O volume antecipado de transações significa que é provável que a atingir as metas de escalabilidade para uma partição individual e limitados pelo serviço de armazenamento?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o antipadrão prepend/acrescentar quando o volume de transações é provável que resultará na taxa de limitação pelo serviço de armazenamento ao aceder a uma partição de acesso frequente.  

#### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de cauda do registo](#log-tail-pattern)  
* [Modificar entidades](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Padrão de anti de dados de registo
Normalmente, deve utilizar o serviço de BLOBs, em vez do serviço de tabela para armazenar dados de registo.  

#### <a name="context-and-problem"></a>Contexto e problema
Caso de utilização de uma comum para dados de registo são obter uma seleção de entradas de registo de um intervalo específico de data/hora: por exemplo, pretender localizar todos os erros e mensagens críticas que seu aplicativo registado entre 15:04 e 15:06 numa data específica. Não pretende utilizar a data e hora da mensagem de registo para determinar a partição de entidades de registo para o guardou: que resulta numa partição de acesso frequente, porque a qualquer momento, todas as entidades de registo irão partilhar o mesmo **PartitionKey** valor (consulte a seção [antipadrão Prepend/acrescentar](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de registo resulta numa partição de acesso frequente como o aplicativo grava todas as mensagens de registo para a partição para a data atual e a hora:  

![Entidade de mensagem do registo][28]

Neste exemplo, o **RowKey** inclui a data e hora da mensagem de registo para garantir que as mensagens de registo são armazenadas, ordenados por ordem de data/hora e inclui um id de mensagem no caso de várias mensagens de registo partilham a mesma data e hora.  

Outra abordagem é usar um **PartitionKey** que garante que a aplicação escreve mensagens numa variedade de partições. Por exemplo, se a origem da mensagem de registo fornece uma forma de distribuir as mensagens por várias partições, pode utilizar o esquema de entidade seguintes:  

![Entidade de mensagem do registo][29]

No entanto, o problema com este esquema é que, para recuperar todas as mensagens de registo para um intervalo de tempo específico precisar procurar cada partição na tabela.

#### <a name="solution"></a>Solução
A secção anterior destacam-se o problema de tentar utilizar o serviço de tabela para armazenar as entradas de registo e dois sugeridas, sempre insatisfatória, designs. Uma solução que levou a uma partição frequente com o risco de fraco desempenho escrever as mensagens do registo; a outra solução resultou no desempenho da consulta fraco devido ao requisito analise cada partição da tabela para obter mensagens de registo para um intervalo de tempo específico. Armazenamento de BLOBs oferece uma solução melhor para este tipo de cenário e é os arquivos de análise de armazenamento como o Azure os dados de registo que coleta.  

Esta seção descreve como a análise de armazenamento armazena os dados de registo no armazenamento de BLOBs para ilustrar essa abordagem para armazenar dados que normalmente consulta por intervalo.  

Análise de armazenamento armazena as mensagens do registo num formato delimitado em vários blobs. O formato delimitado torna mais fácil para um aplicativo de cliente analisar os dados na mensagem de registo.  

Análise de armazenamento utiliza uma convenção de nomenclatura de blobs que permite localizar o blob (ou os blobs) que contêm as mensagens de registo para o qual está a procurar. Por exemplo, um blob com o nome "queue/2014/07/31/1800/000001.log" contém mensagens de registo relacionados com o serviço de fila para a hora, começando às 18:00 em 31 de Julho de 2014. "000001" indica que este é o primeiro ficheiro de registo para este período. Análise de armazenamento também regista os carimbos de data / das mensagens de log de primeiro e último armazenadas no ficheiro como parte dos metadados do blob. A API para ativa de armazenamento de BLOBs localizar os blobs num contentor com base num prefixo de nome: para localizar todos os blobs que contêm dados de registo de fila para a hora, começando às 18:00, pode usar o prefixo "fila/2014/07/31/1800".  

Memórias intermédias de análise de armazenamento registar mensagens internamente e, em seguida, periodicamente atualizar o blob apropriado ou cria um novo com o lote mais recente das entradas de log. Isso reduz o número de gravações que ele deve ser executado para o serviço de Blobs.  

Se estiver a implementar uma solução semelhante em seu próprio aplicativo, deve considerar como gerir o equilíbrio entre a fiabilidade (escrever cada entrada de registo para o armazenamento de BLOBs, por acaso) e custo e a escalabilidade (na memória intermédia atualizações na sua aplicação e a gravação -los para o blob storage em lotes).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de tabela que evita potenciais partições muito ativas, pode achar que não é possível aceder aos dados de registo com eficiência.  
* Para processar dados de registo, um cliente, muitas vezes, precisa de carregar o número de registos.  
* Embora muitas vezes são estruturados de dados de registo, o armazenamento de BLOBs pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta secção descreve algumas das considerações a ter em mente ao implementar os padrões descritos nas secções anteriores. Grande parte dessa seção utiliza exemplos escritos em c# que utilizam a biblioteca de cliente de armazenamento (versão versão 4.3.0 no momento da escrita).  

### <a name="retrieving-entities"></a>Obter entidades
Como discutido na seção [Design para consultar o](#design-for-querying), mais eficiente consulta é uma consulta de ponto. No entanto, em alguns cenários poderá ter de obter várias entidades. Esta secção descreve algumas abordagens comuns para a recuperação de entidades usando a biblioteca de cliente de armazenamento.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Execução de uma consulta de ponto usando a biblioteca de cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é utilizar o **obter** operação de tabela, conforme mostrado na seguinte c# trecho de código que obtém uma entidade com um **PartitionKey** de valor de "Sales" e um  **RowKey** do valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como neste exemplo espera que a entidade obtém seja do tipo **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>A obter várias entidades usando o LINQ
Pode recuperar várias entidades usando o LINQ com biblioteca de clientes de armazenamento e especificando uma consulta com um **onde** cláusula. Para evitar uma análise de tabela, deve incluir sempre o **PartitionKey** valor na where cláusula e se for possível os **RowKey** valor para evitar verificações de tabela e partição. O serviço de tabela suporta um conjunto limitado de operadores de comparação (maior que, maior que ou iguais, menos a, menos do que ou iguais, iguais e não é iguais a) para utilizar no where cláusula. O seguinte trecho de código do c# localiza todos os funcionários cuja último começa de nome com "B" (supondo que o **RowKey** armazena o sobrenome) do departamento de vendas (supondo que o **PartitionKey** armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta Especifica tanto uma **RowKey** e uma **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código seguinte mostra as funcionalidade equivalente usando a API Fluente (para obter mais informações sobre fluentes APIs em geral, consulte [melhores práticas para criar uma API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> O exemplo aninha várias **CombineFilters** métodos para incluir as três condições de filtro.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>A obter um grande número de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base numa **PartitionKey** valor e um **RowKey** valor. No entanto, em alguns cenários pode ter um requisito para retornar muitas entidades, da mesma partição ou mesmo a partir de várias partições.  

Sempre totalmente, deve testar o desempenho da sua aplicação em tais cenários.  

Uma consulta contra o serviço de tabela pode devolver um máximo de 1000 entidades em simultâneo e pode executar para um máximo de cinco segundos. Se o conjunto de resultados contém mais de 1.000 entidades, se a consulta não foi concluída dentro de cinco segundos, ou se a consulta cruza o limite da partição, o serviço de tabela devolve um token de continuação para permitir que o aplicativo de cliente solicitar o próximo conjunto de entidades. Para obter mais informações sobre como a continuação tokens trabalho, consulte [tempo limite de consulta e paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se estiver a utilizar a biblioteca de cliente de armazenamento, pode processar de automaticamente tokens de continuação para como ela retorna entidades a partir do serviço tabela. O seguinte código exemplo c# usando a biblioteca de cliente de armazenamento automaticamente manipula os tokens de continuação, se o serviço de tabela retorna numa resposta:  

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

Ao utilizar tokens de continuação explicitamente, pode controlar quando a aplicação obtém o segmento seguinte de dados. Por exemplo, se a aplicação de cliente permite aos usuários navegarem por entidades armazenadas numa tabela, um usuário pode decidir não para a página por meio de todas as entidades obtidas pela consulta, para que seu aplicativo só utilizaria um token de continuação para recuperar o próximo segmento quando o utilizador se concluído paginação por meio de todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Permite-lhe limitar a quantidade de dados para recuperar a partir do serviço tabela e de que mova através da rede.  
* Permite-lhe realizar e/s assíncrona no .NET.  
* Permite-lhe serializar o token de continuação para armazenamento persistente, para poder continuar em caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente devolve um segmento que contém 1.000 entidades, embora possa ser menos. Isso também é o caso, se limitar o número de entradas de uma consulta devolve usando **tirar** para retornar as primeiro n as entidades que correspondem aos critérios de pesquisa: o serviço de tabela pode devolver um segmento que contenham menos de entidades n juntamente com um token de continuação para permitem-lhe obter as entidades restantes.  
> 
> 

O código do c# seguinte mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projecção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ser até 1 MB de tamanho. Quando consulta a tabela e recuperar entidades, pode não necessitar de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para o ajudar a reduzir a latência e o custo). Pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo seguinte obtém apenas os **E-Mail** propriedade (juntamente com **PartitionKey**, **RowKey**, **Timestamp**e o **ETag**) de entidades selecionadas pela consulta.  

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

Observe como o **RowKey** valor está disponível, apesar de não estava incluído na lista de propriedades para recuperar.  

### <a name="modifying-entities"></a>Modificar entidades
A biblioteca de cliente de armazenamento permite-lhe modificar suas entidades armazenadas no serviço tabela, inserir, eliminar e a atualização de entidades. Pode usar EGTs várias inserções, atualizações e as operações de exclusão para reduzir o número de ida e volta necessárias do batch e melhorar o desempenho da sua solução.  

Exceções geradas quando a biblioteca de cliente de armazenamento é executado um EGT normalmente incluem o índice da entidade que causou o batch efetuar a ativação. Isso é útil quando está a depurar o código que usa EGTs.  

Também deve considerar como seu design afeta como a aplicação cliente lida com operações de atualização e de simultaneidade.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o serviço de tabela implementa verifica no nível de entidades individuais para a simultaneidade otimista **inserir**, **intercalar**, e **eliminar** operações, embora ele é possível que um cliente forçar o serviço de tabela para ignorar estas verificações. Para obter mais informações sobre como o serviço de tabela gere a simultaneidade, consulte [gerenciamento de simultaneidade no armazenamento do Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Intercalação ou substituição
O **substitua** método o **TableOperation** classe substitui sempre a entidade completa no serviço tabela. Se não incluir uma propriedade no pedido quando essa propriedade existe na entidade armazenada, o pedido remove essa propriedade da entidade armazenada. A menos que queira remover uma propriedade explicitamente de uma entidade armazenada, tem de incluir todas as propriedades no pedido.  

Pode utilizar o **intercalar** método o **TableOperation** classe para reduzir a quantidade de dados que enviar para o serviço de tabela para atualizar uma entidade. O **intercalar** método substitui quaisquer propriedades na entidade armazenada com valores de propriedade da entidade incluídos no pedido, mas deixa intactos quaisquer propriedades na entidade armazenada que não estão incluídas no pedido. Isto é útil se tiver entidades grandes e só precisa de atualizar um pequeno número de propriedades numa solicitação.  

> [!NOTE]
> O **substitua** e **intercalar** métodos falharem se a entidade não existe. Como alternativa, pode utilizar o **InsertOrReplace** e **InsertOrMerge** métodos que se não existir, crie uma nova entidade.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidade heterogênea
O serviço de tabela é um *esquema* armazenamento de tabela, que significa que uma única tabela pode armazenar entidades de vários tipos, fornecendo enorme flexibilidade em seu design. O exemplo seguinte ilustra uma tabela de armazenamento dos funcionários e entidades de departamento:  

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
<th>Nome do Departamento</th>
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

Cada entidade tem de ter ainda **PartitionKey**, **RowKey**, e **Timestamp** valores, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que optar por armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Preceder o tipo de entidade para o **RowKey** (ou, possivelmente, o **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como **RowKey** valores.  
* Utilize uma propriedade separada para registar o tipo de entidade conforme mostrado na tabela abaixo.  

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
<th>Nome do Departamento</th>
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

A primeira opção, a prefixação a entidade de tipo para o **RowKey**, é útil se existe a possibilidade de que a duas entidades de diferentes tipos podem ter o mesmo valor de chave. Ele também agrupa as entidades do mesmo tipo em conjunto na partição.  

As técnicas apresentadas nesta seção são especialmente relevantes para a discussão [relações de herança](#inheritance-relationships) anteriormente neste guia na secção modelando as relações.  

> [!NOTE]
> Deve considerar a inclusão de um número de versão no valor de tipo de entidade para ativar aplicações de cliente evoluir POCO objetos e trabalhar com versões diferentes.  
> 
> 

O resto desta secção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Obter tipos de entidade heterogênea
Se estiver a utilizar a biblioteca de cliente de armazenamento, tem três opções para trabalhar com vários tipos de entidade.  

Se souber o tipo da entidade armazenado com um específico **RowKey** e **PartitionKey** valores, em seguida, pode especificar o tipo de entidade ao obter a entidade, conforme mostrado nos dois exemplos anteriores que obter entidades do tipo **EmployeeEntity**: [Execução de uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library) e [recuperar várias entidades usando o LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção consiste em utilizar o **DynamicTableEntity** tipo (uma matriz de propriedades), em vez de um tipo de entidade POCO concreto (esta opção também pode melhorar o desempenho porque não é necessário para serializar e desserializar a entidade para tipos .NET). O seguinte código c# potencialmente obtém várias entidades de diferentes tipos de tabela, mas retorna todas as entidades como **DynamicTableEntity** instâncias. Em seguida, utiliza a **EntityType** propriedade para determinar o tipo de cada entidade:  

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

Para obter outras propriedades, tem de utilizar o **TryGetValue** método no **propriedades** propriedade do **DynamicTableEntity** classe.  

Uma terceira opção é combinar com o **DynamicTableEntity** tipo e uma **EntityResolver** instância. Isto permite-lhe resolver para vários tipos POCO da mesma consulta. Neste exemplo, o **EntityResolver** delegado está a utilizar o **EntityType** propriedade para distinguir entre os dois tipos de entidade que a consulta devolve. O **resolver** método utiliza a **resolvedor** delegado para resolver **DynamicTableEntity** instâncias para **TableEntity** instâncias.  

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

#### <a name="modifying-heterogeneous-entity-types"></a>Tipos de entidade heterogênea de modificação
Não é necessário saber o tipo de uma entidade para eliminá-lo e, sempre sabe o tipo de uma entidade ao inseri-lo. No entanto, pode usar **DynamicTableEntity** tipo para atualizar uma entidade sem saber o seu tipo e sem utilizar uma classe de entidade POCO. O código de exemplo seguinte obtém uma única entidade e verifica a **EmployeeCount** propriedade existe antes de atualizá-lo.  

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

### <a name="controlling-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso partilhado
Pode utilizar tokens de assinatura de acesso partilhado (SAS) para permitir que aplicativos de cliente modificar (e consultar) entidades da tabela diretamente sem a necessidade de se autenticar diretamente com o serviço de tabela. Normalmente, há três benefícios principais para através da SAS em seu aplicativo:  

* Não é necessário distribuir a chave de conta de armazenamento para uma plataforma insegura (por exemplo, um dispositivo móvel) para permitir que o dispositivo aceder e modificar entidades no serviço tabela.  
* Pode descarregar o parte do trabalho que executam de funções web e de trabalho no gerenciamento de suas entidades para dispositivos de cliente, como computadores de usuários finais e dispositivos móveis.  
* Pode atribuir um restrita e tempo conjunto limitado de permissões para um cliente (por exemplo, permitindo o acesso só de leitura a recursos específicos).  

Para obter mais informações sobre como utilizar SAS tokens com o serviço de tabela, consulte [usando partilhado assinaturas de acesso (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, tem ainda de gerar os tokens SAS que concedem um aplicativo de cliente para as entidades no serviço de tabela: fazê-lo no ambiente que tem acesso seguro para as chaves de conta de armazenamento. Normalmente, utiliza uma função web ou de trabalho para gerar os tokens de SAS e entregue-o para as aplicações de cliente que precisam de acesso para as suas entidades. Uma vez que ainda há uma sobrecarga envolvida na geração e fornecer SAS tokens para os clientes, deve considerar a melhor maneira, para reduzir esta sobrecarga, especialmente em cenários de volume elevado.  

É possível gerar um token SAS que concede acesso a um subconjunto de entidades numa tabela. Por padrão, criar um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceder acesso a qualquer um diversas **PartitionKey** valores ou um intervalo de **PartitionKey** e **RowKey** valores. Pode optar por gerar tokens SAS para utilizadores individuais do seu sistema, de modo a que o token SAS de cada utilizador só lhes permite aceder às suas próprias entidades no serviço tabela.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que são propagar os pedidos entre várias partições, pode melhorar a capacidade de resposta de débito e o cliente utilizando consultas assíncronas ou paralelas.
Por exemplo, pode ter dois ou mais trabalho instâncias de função acessar suas tabelas em paralelo. Poderia ter funções de trabalho individuais responsáveis por determinada conjuntos de partições ou simplesmente ter várias funções instâncias de trabalho, cada capazes de aceder a todas as partições numa tabela.  

Dentro de uma instância de cliente, pode melhorar o débito ao executar operações de armazenamento de forma assíncrona. A biblioteca de cliente de armazenamento torna mais fácil escrever consultas assíncronas e modificações. Por exemplo, pode começar com o método síncrono que recupera todas as entidades numa partição conforme mostrado no código a seguir em C#:  

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

Pode facilmente modificar esse código para que a consulta é executada de forma assíncrona, da seguinte forma:  

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

* A assinatura do método agora inclui a **async** modificador e devolve um **tarefa** instância.  
* Em vez de chamar o **ExecuteSegmented** chama o método para recuperar resultados, o método agora a **ExecuteSegmentedAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar esse método várias vezes (com valores diferentes para o **departamento** parâmetro), e cada consulta será executada num thread separado.  

Há uma versão assíncrona do **Execute** método na **TableQuery** classe porque o **IEnumerable** interface não suporta a enumeração assíncrona.  

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

Pode facilmente modificar esse código para que a atualização seja executada de forma assíncrona, da seguinte forma:  

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

* A assinatura do método agora inclui a **async** modificador e devolve um **tarefa** instância.  
* Em vez de chamar o **Execute** chama o método para atualizar a entidade, o método agora a **ExecuteAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar vários métodos assíncronos como este, e cada invocação do método será executada num thread separado.  

### <a name="credits"></a>Ficha técnica
Gostaríamos de agradecer os seguintes membros da equipa do Azure por suas contribuições: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah e Serdar Ozler, bem como Tom Hollander da Microsoft DX. 

Também gostaríamos de agradecer a seguinte MVPs da Microsoft pelos seus valiosos comentários durante os ciclos de revisão: Igor Papirov e Edward Bakker.

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

