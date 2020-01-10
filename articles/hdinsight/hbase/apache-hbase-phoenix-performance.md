---
title: Desempenho de Phoenix no Azure HDInsight
description: Práticas recomendadas para otimizar o desempenho de Apache Phoenix para clusters do Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552649"
---
# <a name="apache-phoenix-performance-best-practices"></a>Melhores práticas de desempenho do Apache Phoenix

O aspecto mais importante do desempenho de [Apache Phoenix](https://phoenix.apache.org/) é otimizar o [Apache HBase](https://hbase.apache.org/)subjacente. O Phoenix cria um modelo de dados relacional acima do HBase que converte consultas SQL em operações do HBase, como verificações. O design do seu esquema de tabela, a seleção e a ordenação dos campos em sua chave primária, e o uso de índices todos afetam o desempenho da Phoenix.

## <a name="table-schema-design"></a>Design de esquema de tabela

Quando você cria uma tabela no Phoenix, essa tabela é armazenada em uma tabela do HBase. A tabela do HBase contém grupos de colunas (famílias de colunas) que são acessados juntos. Uma linha na tabela Phoenix é uma linha na tabela do HBase, em que cada linha consiste em células com controle de versão associadas a uma ou mais colunas. Logicamente, uma única linha do HBase é uma coleção de pares chave-valor, cada um com o mesmo valor de RowKey. Ou seja, cada par chave-valor tem um atributo RowKey e o valor desse atributo RowKey é o mesmo para uma linha específica.

O design de esquema de uma tabela Phoenix inclui o design da chave primária, o design da família de colunas, o design de coluna individual e como os dados são particionados.

### <a name="primary-key-design"></a>Design de chave primária

A chave primária definida em uma tabela no Phoenix determina como os dados são armazenados no RowKey da tabela do HBase subjacente. No HBase, a única maneira de acessar uma linha específica é com o RowKey. Além disso, os dados armazenados em uma tabela do HBase são classificados pelo RowKey. O Phoenix cria o valor RowKey concatenando os valores de cada uma das colunas na linha, na ordem em que eles são definidos na chave primária.

Por exemplo, uma tabela de contatos tem o nome, o sobrenome, o número de telefone e o endereço, tudo na mesma família de colunas. Você pode definir uma chave primária com base em um número de sequência crescente:

|RowKey|       endereço|   telemóvel| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    João|Giros|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

No entanto, se você consulta com frequência por lastName, essa chave primária pode não ser bem executada, porque cada consulta requer uma verificação de tabela completa para ler o valor de cada sobrenome. Em vez disso, você pode definir uma chave primária nas colunas lastName, firstName e número do seguro social. Esta última coluna é desambiguar dois residentes no mesmo endereço com o mesmo nome, como um pai e um filho.

|RowKey|       endereço|   telemóvel| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    João|Giros| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Com essa nova chave primária, as chaves de linha geradas pelo Phoenix seriam:

|RowKey|       endereço|   telemóvel| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    João|Giros| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Na primeira linha acima, os dados para o RowKey são representados como mostrado:

|RowKey|       key|   valor|
|------|--------------------|---|
|  Dole-John-111|endereço |1111 San Gabriel Dr.|  
|  Dole-John-111|telemóvel |1-425-000-0002|  
|  Dole-John-111|firstName |João|  
|  Dole-John-111|lastName |Giros|  
|  Dole-John-111|socialSecurityNum |111|

Esse RowKey agora armazena uma cópia duplicada dos dados. Considere o tamanho e o número de colunas que você inclui em sua chave primária, pois esse valor é incluído em cada célula na tabela do HBase subjacente.

Além disso, se a chave primária tiver valores que estão aumentando de forma monotônico, você deverá criar a tabela com *buckets Salt* para ajudar a evitar a criação de hotspots de gravação-consulte [dados de partição](#partition-data).

### <a name="column-family-design"></a>Design de família de colunas

Se algumas colunas forem acessadas com mais frequência do que outras, você deverá criar várias famílias de colunas para separar as colunas acessadas com frequência das colunas raramente acessadas.

Além disso, se certas colunas tendem a ser acessadas juntas, coloque essas colunas na mesma família de colunas.

### <a name="column-design"></a>Design de coluna

* Mantenha as colunas VARCHAR em cerca de 1 MB devido aos custos de e/s de colunas grandes. Ao processar consultas, o HBase materializa as células de forma completa antes de enviá-las ao cliente, e o cliente as recebe de forma completa antes de entregá-las ao código do aplicativo.
* Armazene valores de coluna usando um formato compacto como protobuf, Avro, msgpack ou BSON. JSON não é recomendado, pois é maior.
* Considere compactar os dados antes do armazenamento para cortar a latência e os custos de e/s.

### <a name="partition-data"></a>Dados de partição

O Phoenix permite controlar o número de regiões em que os dados são distribuídos, o que pode aumentar significativamente o desempenho de leitura/gravação. Ao criar uma tabela Phoenix, é possível Salt ou dividir previamente os dados.

Para Salt de uma tabela durante a criação, especifique o número de buckets de Salt:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Esse Salt divide a tabela ao longo dos valores de chaves primárias, escolhendo os valores automaticamente. 

Para controlar onde as divisões de tabela ocorrem, você pode dividir previamente a tabela fornecendo os valores de intervalo durante os quais a divisão ocorre. Por exemplo, para criar uma divisão de tabela juntamente com três regiões:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Design do índice

Um índice Phoenix é uma tabela do HBase que armazena uma cópia de alguns ou todos os dados da tabela indexada. Um índice melhora o desempenho para tipos específicos de consultas.

Quando você tiver vários índices definidos e, em seguida, consultar uma tabela, o Phoenix selecionará automaticamente o melhor índice para a consulta. O índice primário é criado automaticamente com base nas chaves primárias que você selecionar.

Para consultas antecipadas, você também pode criar índices secundários especificando suas colunas.

Ao criar seus índices:

* Crie apenas os índices necessários.
* Limite o número de índices em tabelas atualizadas com frequência. As atualizações em uma tabela são transvertidas em gravações na tabela principal e nas tabelas de índice.

## <a name="create-secondary-indexes"></a>Criar índices secundários

Os índices secundários podem melhorar o desempenho de leitura, transformando o que seria uma verificação completa de tabela em uma pesquisa de ponto, no custo do espaço de armazenamento e na velocidade de gravação. Os índices secundários podem ser adicionados ou removidos após a criação da tabela e não exigem alterações nas consultas existentes – as consultas simplesmente são executadas mais rapidamente. Dependendo de suas necessidades, considere a criação de índices cobertos, índices funcionais ou ambos.

### <a name="use-covered-indexes"></a>Usar índices cobertos

Índices cobertos são índices que incluem dados da linha além dos valores que são indexados. Depois de encontrar a entrada de índice desejada, não há necessidade de acessar a tabela primária.

Por exemplo, na tabela de exemplo de contato, você pode criar um índice secundário apenas na coluna socialSecurityNum. Esse índice secundário agilizaria as consultas que filtram por valores socialSecurityNum, mas recuperar outros valores de campo exigirá outra leitura na tabela principal.

|RowKey|       endereço|   telemóvel| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    João|Giros| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

No entanto, se você normalmente quiser pesquisar o firstName e o lastName Considerando o socialSecurityNum, poderá criar um índice coberto que inclua o firstName e lastName como dados reais na tabela de índices:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Esse índice abordado permite que a consulta a seguir adquira todos os dados apenas lendo da tabela que contém o índice secundário:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Usar índices funcionais

Os índices funcionais permitem que você crie um índice em uma expressão arbitrária que você espera que seja usada em consultas. Quando você tiver um índice funcional em vigor e uma consulta usar essa expressão, o índice poderá ser usado para recuperar os resultados em vez da tabela de dados.

Por exemplo, você pode criar um índice para permitir que você faça pesquisas que não diferenciam maiúsculas de minúsculas no nome e sobrenome combinados de uma pessoa:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Design de consulta

As principais considerações no design de consulta são:

* Entenda o plano de consulta e verifique seu comportamento esperado.
* Ingresse com eficiência.

### <a name="understand-the-query-plan"></a>Entender o plano de consulta

Em [sqlline](http://sqlline.sourceforge.net/), use explicar seguido por sua consulta SQL para exibir o plano de operações que o Phoenix executará. Verifique se o plano:

* Usa sua chave primária quando apropriado.
* Usa índices secundários apropriados, em vez da tabela de dados.
* Usa verificação de intervalo ou ignorar verificação sempre que possível, em vez de verificação de tabela.

#### <a name="plan-examples"></a>Exemplos de plano

Por exemplo, digamos que você tenha uma tabela chamada vôos que armazena informações de atraso de voo.

Para selecionar todos os vôos com um traço de linha de `19805`, em que a linha de entrada é um campo que não está na chave primária ou em qualquer índice:

    select * from "FLIGHTS" where airlineid = '19805';

Execute o comando de explicação da seguinte maneira:

    explain select * from "FLIGHTS" where airlineid = '19805';

O plano de consulta tem a seguinte aparência:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Neste plano, observe a frase verificação completa sobre vôos. Essa frase indica que a execução faz uma verificação de tabela em todas as linhas da tabela, em vez de usar a opção de verificação de intervalo mais eficiente ou ignorar verificação.

Agora, digamos que você deseja consultar vôos em 2 de janeiro de 2014 para a transportadora `AA` em que seu voo foi maior que 1. Vamos pressupor que as colunas ano, mês, DayOfMonth, Carrier e voo existam na tabela de exemplo e que fazem parte da chave primária composta. A consulta teria a seguinte aparência:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Vamos examinar o plano para essa consulta com:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

O plano resultante é:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Os valores entre colchetes são o intervalo de valores para as chaves primárias. Nesse caso, os valores de intervalo são corrigidos com o ano 2014, mês 1 e DAYOFMONTH 2, mas permitem valores para voo começando com 2 e em cima (`*`). Esse plano de consulta confirma que a chave primária está sendo usada conforme o esperado.

Em seguida, crie um índice na tabela de vôos denominada `carrier2_idx` que está somente no campo da transportadora. Esse índice também inclui voo, cauda, Origin e voo como colunas cobertas cujos dados também são armazenados no índice.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Digamos que você deseja obter a portadora junto com o voo e o cauda, como na seguinte consulta:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Você deve ver esse índice sendo usado:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Para obter uma lista completa dos itens que podem aparecer em explicar os resultados do plano, consulte a seção explicar planos no [Guia de ajuste do Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Ingresse com eficiência

Em geral, você deseja evitar junções, a menos que um lado seja pequeno, especialmente em consultas frequentes.

Se necessário, você pode fazer junções grandes com a dica de `/*+ USE_SORT_MERGE_JOIN */`, mas uma grande junção é uma operação cara em grandes números de linhas. Se o tamanho geral de todas as tabelas do lado direito exceder a memória disponível, use a dica `/*+ NO_STAR_JOIN */`.

## <a name="scenarios"></a>Cenários

As diretrizes a seguir descrevem alguns padrões comuns.

### <a name="read-heavy-workloads"></a>Cargas de trabalho pesadas de leitura

Para casos de uso intensivo de leitura, verifique se você está usando índices. Além disso, para economizar sobrecarga no tempo de leitura, considere a criação de índices cobertos.

### <a name="write-heavy-workloads"></a>Cargas de trabalho pesadas de gravação

Para cargas de trabalho com excesso de gravação em que a chave primária está aumentando de forma monotônico, crie buckets Salt para ajudar a evitar pontos de interrupções de gravação, às custas da taxa de transferência de leitura geral, devido às verificações adicionais necessárias. Além disso, ao usar UPSERT para gravar um grande número de registros, desative a confirmação automática e faça o lote dos registros.

### <a name="bulk-deletes"></a>Exclusões em massa

Ao excluir um conjunto de dados grande, ative a confirmação automática antes de emitir a consulta de exclusão, para que o cliente não precise se lembrar das chaves de linha de todas as linhas excluídas. A confirmação automática impede que o cliente faça o buffer das linhas afetadas pela exclusão, para que o Phoenix possa excluí-las diretamente nos servidores de região sem a despesa de retorná-las para o cliente.

### <a name="immutable-and-append-only"></a>Imutável e somente acréscimo

Se seu cenário favorecer a velocidade de gravação sobre a integridade dos dados, considere desabilitar o log write-ahead ao criar suas tabelas:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Para obter detalhes sobre essa e outras opções, consulte [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Passos seguintes

* [Guia de ajuste Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Índices secundários](https://phoenix.apache.org/secondary_indexing.html)
