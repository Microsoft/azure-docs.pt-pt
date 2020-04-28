---
title: Desempenho da Phoenix no Azure HDInsight
description: Boas práticas para otimizar o desempenho da Apache Phoenix para clusters Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552649"
---
# <a name="apache-phoenix-performance-best-practices"></a>Melhores práticas de desempenho do Apache Phoenix

O aspeto mais importante do desempenho da [Apache Phoenix](https://phoenix.apache.org/) é otimizar a [Base HBase Apache](https://hbase.apache.org/)subjacente. A Phoenix cria um modelo de dados relacional no topo da HBase que converte consultas SQL em operações De Base H, tais como digitalizações. O design do seu esquema de mesa, a seleção e encomenda dos campos na sua chave primária, e o seu uso de índices, todos afetam o desempenho da Phoenix.

## <a name="table-schema-design"></a>Design de esquema de mesa

Quando se cria uma mesa em Phoenix, essa mesa é armazenada numa mesa HBase. A tabela HBase contém grupos de colunas (famílias de colunas) que são acedidas em conjunto. Uma linha na tabela Phoenix é uma linha na tabela HBase, onde cada linha é composta por células versonizadas associadas a uma ou mais colunas. Logicamente, uma única linha HBase é uma coleção de pares de valor-chave, cada um com o mesmo valor de rowkey. Ou seja, cada par de valor-chave tem um atributo chave de linha, e o valor desse atributo de chave de linha é o mesmo para uma determinada linha.

O design de esquemade uma tabela Phoenix inclui o design principal da chave, design familiar de colunas, design de colunaindividual, e como os dados são divididos.

### <a name="primary-key-design"></a>Design de chave primária

A chave principal definida numa tabela em Phoenix determina como os dados são armazenados dentro da chave de linha da tabela HBase subjacente. Na Base H, a única forma de aceder a uma determinada linha é com a chave de linha. Além disso, os dados armazenados numa tabela HBase são classificados pela chave de linha. Phoenix constrói o valor da chave de linha concatenando os valores de cada uma das colunas da linha, na ordem que são definidas na chave primária.

Por exemplo, uma tabela para contactos tem o primeiro nome, apelido, número de telefone e endereço, tudo na mesma família de colunas. Pode definir uma chave primária com base num número de sequência crescente:

|rowkey|       address|   telefone| nomePróprio| apelido|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji|

No entanto, se consulta frequentemente por apelido esta chave primária pode não ter um bom desempenho, porque cada consulta requer uma digitalização completa da tabela para ler o valor de cada último nome. Em vez disso, pode definir uma chave primária nas colunas de números de último nome, primeiro nome e segurança social. Esta última coluna é para desambiguar dois residentes no mesmo endereço com o mesmo nome, como pai e filho.

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNo |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

Com esta nova chave primária, as chaves da linha geradas pela Phoenix seriam:

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNo |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

Na primeira linha acima, os dados para a chave de linha são representados como mostrado:

|rowkey|       key|   valor|
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|telefone |1-425-000-0002|  
|  Dole-John-111|nomePróprio |John|  
|  Dole-John-111|apelido |Dole|  
|  Dole-John-111|socialSecurityNo |111|

Esta chave de remo agora armazena uma cópia duplicada dos dados. Considere o tamanho e o número de colunas que inclui na sua chave principal, porque este valor está incluído em todas as células da tabela HBase subjacente.

Além disso, se a chave primária tem valores que estão a aumentar monotonicamente, deve criar a tabela com baldes de *sal* para ajudar a evitar a criação de hotspots de escrita - ver [dados de partição](#partition-data).

### <a name="column-family-design"></a>Design familiar de coluna

Se algumas colunas forem acedidas com mais frequência do que outras, deve criar várias famílias de colunas para separar as colunas frequentemente acedidas de colunas raramente acedidas.

Além disso, se certas colunas tendem a ser acedidas juntas, coloque essas colunas na mesma família de colunas.

### <a name="column-design"></a>Design de coluna

* Mantenha as colunas VARCHAR abaixo de cerca de 1 MB devido aos custos de I/S das grandes colunas. Ao processar consultas, a HBase materializa as células na íntegra antes de enviá-las para o cliente, e o cliente recebe-as na íntegra antes de as entregar ao código de aplicação.
* Guarde os valores da coluna utilizando um formato compacto como protobuf, Avro, msgpack ou BSON. JSON não é recomendado, já que é maior.
* Considere comprimir os dados antes do armazenamento para reduzir os custos de latência e I/S.

### <a name="partition-data"></a>Dados de partição

A Phoenix permite-lhe controlar o número de regiões onde os seus dados são distribuídos, o que pode aumentar significativamente o desempenho de leitura/escrita. Ao criar uma tabela Phoenix, pode salar ou pré-dividir os seus dados.

Para salgar uma mesa durante a criação, especifique o número de baldes de sal:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Esta salga divide a tabela ao longo dos valores das teclas primárias, escolhendo automaticamente os valores. 

Para controlar onde ocorrem as divisões da tabela, pode pré-dividir a tabela fornecendo os valores de gama ao longo dos quais ocorre a divisão. Por exemplo, criar uma tabela dividida ao longo de três regiões:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Design do índice

Um índice Phoenix é uma tabela HBase que armazena uma cópia de alguns ou todos os dados da tabela indexada. Um índice melhora o desempenho para tipos específicos de consultas.

Quando se tem vários índices definidos e, em seguida, consulta uma tabela, a Phoenix seleciona automaticamente o melhor índice para a consulta. O índice primário é criado automaticamente com base nas teclas primárias selecionadas.

Para consultas antecipadas, também pode criar índices secundários especificando as suas colunas.

Ao desenhar os seus índices:

* Só crie os índices de que precisa.
* Limite o número de índices em tabelas frequentemente atualizadas. As atualizações de uma tabela traduzem-se em escritos tanto para a tabela principal como para as tabelas de índices.

## <a name="create-secondary-indexes"></a>Criar índices secundários

Os índices secundários podem melhorar o desempenho da leitura, transformando o que seria uma digitalização completa da tabela numa procura de pontos, ao custo do espaço de armazenamento e da velocidade de escrita. Os índices secundários podem ser adicionados ou removidos após a criação da tabela e não requerem alterações às consultas existentes – as consultas são mais rápidas. Dependendo das suas necessidades, considere criar índices cobertos, índices funcionais, ou ambos.

### <a name="use-covered-indexes"></a>Utilizar índices cobertos

Os índices cobertos são índices que incluem dados da linha, para além dos valores indexados. Depois de encontrar a entrada deíndice desejada, não há necessidade de aceder à mesa primária.

Por exemplo, na tabela de contactos, por exemplo, pode criar um índice secundário apenas na coluna socialSecurityNum. Este índice secundário aceleraria as consultas que filtram pelos valores socialSecurityNum, mas recuperar outros valores de campo exigirá outra leitura contra a tabela principal.

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNo |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

No entanto, se normalmente quiser procurar o primeiro Nome e apelido dado o socialSecurityNum, pode criar um índice coberto que inclua o primeiro nome e apelido como dados reais na tabela de índices:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Este índice coberto permite que a seguinte consulta adquira todos os dados apenas através da leitura da tabela que contém o índice secundário:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Use índices funcionais

Os índices funcionais permitem criar um índice sobre uma expressão arbitrária que espera ser usado em consultas. Uma vez que tenha um índice funcional no lugar e uma consulta use essa expressão, o índice pode ser usado para recuperar os resultados em vez da tabela de dados.

Por exemplo, pode criar um índice que lhe permita fazer pesquisas insensíveis a casos sobre o primeiro nome combinado e o apelido de uma pessoa:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Design de consulta

As principais considerações no design de consulta são:

* Compreenda o plano de consulta e verifique o seu comportamento esperado.
* Junte-se eficientemente.

### <a name="understand-the-query-plan"></a>Compreenda o plano de consulta

No [SQLLine,](http://sqlline.sourceforge.net/)use EXPLAIN seguido da sua consulta SQL para ver o plano de operações que a Phoenix irá executar. Verifique se o plano:

* Usa a sua chave principal quando apropriado.
* Utiliza índices secundários apropriados, em vez da tabela de dados.
* Utiliza RANGE SCAN ou SKIP SCAN sempre que possível, em vez de table scan.

#### <a name="plan-examples"></a>Exemplos de planos

Como exemplo, diga que tem uma mesa chamada FLIGHTS que armazena informações de atraso de voo.

Para selecionar todos os voos `19805`com um aviado de, onde o aviado é um campo que não está na chave primária ou em qualquer índice:

    select * from "FLIGHTS" where airlineid = '19805';

Executar o comando de explicação da seguinte forma:

    explain select * from "FLIGHTS" where airlineid = '19805';

O plano de consulta é assim:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Neste plano, note a frase FULL SCAN OVER FLIGHTS. Esta frase indica que a execução faz uma tabela de digitalização sobre todas as linhas da tabela, em vez de utilizar a opção RANGE SCAN ou SKIP SCAN mais eficiente.

Agora, digamos que quer consultar os voos a 2 de `AA` janeiro de 2014 para a transportadora onde o seu voo foi superior a 1. Vamos supor que as colunas ano, mês, dia, transportadora e voo numérica existem na tabela de exemplos, e fazem parte da chave primária composta. A consulta seria a seguinte:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Vamos examinar o plano para esta consulta com:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

O plano resultante é:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Os valores nos suportes quadrados são a gama de valores para as teclas primárias. Neste caso, os valores de gama são fixados com o ano de 2014, mês 1 e`*`dia 2, mas permitem valores para o voo a partir de 2 e em cima . Este plano de consulta confirma que a chave principal está a ser usada como esperado.

Em seguida, crie um `carrier2_idx` índice na tabela FLIGHTS com o nome que se encontra apenas no campo de transporte. Este índice também inclui data de voo, tailnum, origem e voo num como colunas cobertas cujos dados também são armazenados no índice.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Diga que quer levar a transportadora juntamente com a data de voo e o tailnum, como na seguinte consulta:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Devia ver este índice a ser usado:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Para obter uma listagem completa dos itens que podem aparecer nos resultados do plano de explicação, consulte a secção Planos de Explicação no Guia de [Afinação Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Junte-se eficientemente

Geralmente, você deve evitar juntas a menos que um lado seja pequeno, especialmente em consultas frequentes.

Se necessário, você pode fazer `/*+ USE_SORT_MERGE_JOIN */` grandes juntas com a dica, mas uma grande adesão é uma operação cara sobre um grande número de linhas. Se o tamanho total de todas as tabelas do lado `/*+ NO_STAR_JOIN */` direito exceder a memória disponível, use a sugestão.

## <a name="scenarios"></a>Cenários

As seguintes diretrizes descrevem alguns padrões comuns.

### <a name="read-heavy-workloads"></a>Cargas de trabalho pesadas

Para casos de utilização pesada, certifique-se de que está a usar índices. Além disso, para poupar as despesas de leitura, considere a criação de índices cobertos.

### <a name="write-heavy-workloads"></a>Cargas de trabalho pesadas

Para cargas de trabalho pesadas onde a chave principal está a aumentar monotonicamente, crie baldes de sal para ajudar a evitar a escrita de hotspots, em detrimento da leitura geral da dose devido às análises adicionais necessárias. Além disso, ao utilizar a UPSERT para escrever um grande número de discos, desligue automaticamente E emita os registos.

### <a name="bulk-deletes"></a>A granel elimina

Ao eliminar um grande conjunto de dados, ligue automaticamente Antes de emitir a consulta DELETE, de modo a que o cliente não precise de se lembrar das teclas de linha para todas as linhas apagadas. A AutoCommit impede o cliente de tamponar as linhas afetadas pelo DELETE, para que a Phoenix possa eliminá-las diretamente nos servidores da região sem a despesa de as devolver ao cliente.

### <a name="immutable-and-append-only"></a>Imutável e apenas apêndice

Se o seu cenário favorecer a velocidade de escrita sobre a integridade dos dados, considere desativar o registo escrito-à frente ao criar as suas tabelas:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Para mais detalhes sobre esta e outras opções, consulte [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Passos seguintes

* [Guia de afinação Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Índices Secundários](https://phoenix.apache.org/secondary_indexing.html)
