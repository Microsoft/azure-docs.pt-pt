---
title: Desempenho da Phoenix em Azure HDInsight
description: Melhores práticas para otimizar o desempenho da Apache Phoenix para clusters Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 0dfb93db1af807459c37653189a90b754c933aa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504796"
---
# <a name="apache-phoenix-performance-best-practices"></a>Melhores práticas de desempenho do Apache Phoenix

O aspeto mais importante do desempenho da [Apache Phoenix](https://phoenix.apache.org/) é otimizar o [Apache HBase](https://hbase.apache.org/)subjacente. A Phoenix cria um modelo de dados relacional no topo da HBase que converte consultas SQL em operações HBase, tais como digitalizações. O design do seu esquema de mesa, a seleção e ordenação dos campos na sua chave primária, e o seu uso de índices afetam o desempenho da Phoenix.

## <a name="table-schema-design"></a>Design de esquema de mesa

Quando se cria uma tabela em Phoenix, essa mesa é armazenada numa tabela HBase. A tabela HBase contém grupos de colunas (famílias de colunas) que são acedidas em conjunto. Uma linha na tabela Phoenix é uma linha na tabela HBase, onde cada linha é composta por células versadas associadas a uma ou mais colunas. Logicamente, uma única linha HBase é uma coleção de pares de valores-chave, cada um com o mesmo valor rowkey. Ou seja, cada par de valores-chave tem um atributo rowkey, e o valor desse atributo rowkey é o mesmo para uma determinada linha.

O design de esquema de uma tabela Phoenix inclui o design primário da chave, design de família de colunas, design de colunas individuais, e como os dados são divididos.

### <a name="primary-key-design"></a>Design de chave primária

A chave primária definida numa tabela em Phoenix determina como os dados são armazenados dentro da linha da tabela HBase subjacente. Na HBase, a única forma de aceder a uma determinada linha é com a linha rowkey. Além disso, os dados armazenados numa tabela HBase são classificados pela linha. Phoenix constrói o valor rowkey concatenando os valores de cada uma das colunas na linha, na ordem que são definidas na chave primária.

Por exemplo, uma tabela para contactos tem o primeiro nome, apelido, número de telefone e endereço, tudo na mesma família de colunas. Pode definir uma chave primária com base num número de sequência crescente:

|rowkey|       address|   telefone| nomePróprio| apelido|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji|

No entanto, se consultar frequentemente até ao último Nome, esta chave primária pode não ter um bom desempenho, porque cada consulta requer uma verificação completa da tabela para ler o valor de cada último Nome. Em vez disso, pode definir uma chave primária no último Nome, primeiro Nome e colunas de número de segurança social. Esta última coluna é para desambiguar dois residentes no mesmo endereço com o mesmo nome, como um pai e um filho.

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

Com esta nova chave primária, as teclas de linha geradas pela Phoenix seriam:

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

Na primeira linha acima, os dados para o rowkey são representados como mostrado:

|rowkey|       key|   valor|
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|telefone |1-425-000-0002|  
|  Dole-John-111|nomePróprio |John|  
|  Dole-John-111|apelido |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Este rowkey armazena agora uma cópia duplicada dos dados. Considere o tamanho e o número de colunas que inclui na sua chave primária, porque este valor está incluído em todas as células da tabela HBase subjacente.

Além disso, se a chave primária tiver valores que estão a aumentar monotonicamente, deve criar a tabela com *baldes de sal* para ajudar a evitar a criação de hotspots de escrita - consulte [os dados da partição](#partition-data).

### <a name="column-family-design"></a>Design familiar da coluna

Se algumas colunas forem acedidas com mais frequência do que outras, deve criar várias famílias de colunas para separar as colunas frequentemente acedidas de colunas raramente acedidas.

Além disso, se certas colunas tendem a ser acedidas em conjunto, coloque essas colunas na mesma família de colunas.

### <a name="column-design"></a>Design de coluna

* Mantenha as colunas VARCHAR abaixo de cerca de 1 MB devido aos custos de E/S de colunas grandes. Ao processar consultas, a HBase materializa as células na íntegra antes de enviá-las para o cliente, e o cliente recebe-as na íntegra antes de as entregar ao código de aplicação.
* Armazenar valores de coluna usando um formato compacto como protobuf, Avro, msgpack ou BSON. JSON não é recomendado, já que é maior.
* Considere comprimir os dados antes do armazenamento para reduzir os custos de latência e I/S.

### <a name="partition-data"></a>Dados de partição

A Phoenix permite-lhe controlar o número de regiões onde os seus dados são distribuídos, o que pode aumentar significativamente o desempenho de leitura/escrita. Ao criar uma tabela Phoenix, pode salgar ou pré-dividir os seus dados.

Para salgar uma mesa durante a criação, especifique o número de baldes de sal:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Esta salga divide a tabela ao longo dos valores das teclas primárias, escolhendo automaticamente os valores. 

Para controlar onde ocorrem as divisões da tabela, pode pré-dividir a tabela, fornecendo os valores de intervalo ao longo dos quais ocorre a divisão. Por exemplo, criar uma mesa dividida ao longo de três regiões:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Design do índice

Um índice Phoenix é uma tabela HBase que armazena uma cópia de alguns ou todos os dados da tabela indexada. Um índice melhora o desempenho para tipos específicos de consultas.

Quando tem vários índices definidos e, em seguida, consulta uma tabela, Phoenix seleciona automaticamente o melhor índice para a consulta. O índice primário é criado automaticamente com base nas teclas primárias selecionadas.

Para consultas antecipadas, também pode criar índices secundários especificando as suas colunas.

Ao desenhar os seus índices:

* Crie apenas os índices de que precisa.
* Limitar o número de índices em tabelas frequentemente atualizadas. As atualizações para uma tabela traduzem-se em escritas tanto para a tabela principal como para as tabelas de índices.

## <a name="create-secondary-indexes"></a>Criar índices secundários

Os índices secundários podem melhorar o desempenho da leitura transformando o que seria uma varredura de mesa completa em um ponto de procura, ao custo do espaço de armazenamento e velocidade de escrita. Os índices secundários podem ser adicionados ou removidos após a criação da tabela e não requerem alterações nas consultas existentes – as consultas são mais rápidas. Dependendo das suas necessidades, considere criar índices cobertos, índices funcionais ou ambos.

### <a name="use-covered-indexes"></a>Utilizar índices cobertos

Os índices cobertos são índices que incluem dados da linha, além dos valores indexados. Depois de encontrar a entrada de índice desejada, não há necessidade de aceder à tabela primária.

Por exemplo, na tabela de contactos de exemplo, pode criar um índice secundário apenas na coluna SocialSecurityNum. Este índice secundário aceleraria as consultas que filtram pelos valores socialSecurityNum, mas recuperar outros valores de campo exigirá outra leitura contra a tabela principal.

|rowkey|       address|   telefone| nomePróprio| apelido| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Rio Calvin|Raji| 222 |

No entanto, se normalmente quiser procurar o primeiro Nome e último Nome dado o SocialSecurityNum, pode criar um índice coberto que inclua o primeiro Nome e último Nome como dados reais na tabela de índices:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Este índice coberto permite que a seguinte consulta adquira todos os dados apenas através da leitura da tabela que contém o índice secundário:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Use índices funcionais

Os índices funcionais permitem criar um índice sobre uma expressão arbitrária que espera ser usado em consultas. Uma vez que tenha um índice funcional no lugar e uma consulta utilize essa expressão, o índice pode ser usado para recuperar os resultados em vez da tabela de dados.

Por exemplo, pode criar um índice que lhe permita fazer pesquisas insensíveis a casos no primeiro nome combinado e no último nome de uma pessoa:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Design de consulta

As principais considerações em design de consulta são:

* Compreenda o plano de consulta e verifique o seu comportamento esperado.
* Junte-se eficientemente.

### <a name="understand-the-query-plan"></a>Compreenda o plano de consulta

Na [SQLLine,](http://sqlline.sourceforge.net/)use o EXPLAIN seguido da sua consulta SQL para visualizar o plano de operações que a Phoenix irá realizar. Verifique se o plano:

* Usa a chave primária quando apropriado.
* Utiliza índices secundários apropriados, em vez da tabela de dados.
* Utilize RANGE SCAN ou SKIP SCAN sempre que possível, em vez de SCAN DE MESA.

#### <a name="plan-examples"></a>Exemplos do plano

Como exemplo, digamos que tem uma tabela chamada FLIGHTS que armazena informações sobre atrasos de voo.

Para selecionar todos os voos com um avião `19805` de, onde a airlineid é um campo que não está na chave primária ou em qualquer índice:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Executar o comando de explicação da seguinte forma:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

O plano de consulta é assim:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

Neste plano, note a frase FULL SCAN OVER FLIGHTS. Esta frase indica que a execução faz uma TOMOGRAFIA DE MESA em todas as linhas da tabela, em vez de utilizar a opção RANGE SCAN ou SKIP SCAN mais eficiente.

Agora, digamos que quer consultar os voos a 2 de janeiro de 2014 para a transportadora onde o `AA` seu número de voo foi superior a 1. Vamos assumir que as colunas ano, mês, dia domês, transportadora e número de voo existem na mesa de exemplo, e são todas parte da chave primária composta. A consulta seria a seguinte:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Vamos examinar o plano para esta consulta com:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

O plano resultante é:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

Os valores em parênteses quadrados são a gama de valores para as teclas primárias. Neste caso, os valores de gama são fixados com o ano de 2014, mês 1 e dia 2, mas permitem valores para o número de voos a partir de 2 e em cima ( `*` ). Este plano de consulta confirma que a chave primária está a ser usada como esperado.

Em seguida, crie um índice na tabela FLIGHTS nomeado `carrier2_idx` apenas no campo da transportadora. Este índice também inclui data de voo, tailnum, origem e número de voo como colunas cobertas cujos dados também são armazenados no índice.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Diga que quer levar a transportadora juntamente com a hora de voo e o tailnum, como na seguinte consulta:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Deve ver este índice a ser usado:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Para obter uma listagem completa dos itens que podem aparecer nos resultados do plano de explicação, consulte a secção Planos de Explicação no [Guia de Afinação Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Junte-se eficientemente

Geralmente, você quer evitar as juntas a menos que um lado seja pequeno, especialmente em consultas frequentes.

Se necessário, pode fazer grandes juntas com a `/*+ USE_SORT_MERGE_JOIN */` dica, mas uma grande junção é uma operação cara sobre um grande número de linhas. Se o tamanho total de todas as tabelas do lado direito exceder a memória disponível, use a `/*+ NO_STAR_JOIN */` sugestão.

## <a name="scenarios"></a>Cenários

As seguintes diretrizes descrevem alguns padrões comuns.

### <a name="read-heavy-workloads"></a>Cargas de trabalho pesadas de leitura

Para casos de uso pesado de leitura, certifique-se de que está a usar índices. Além disso, para poupar sobrecarga de tempo de leitura, considere a criação de índices cobertos.

### <a name="write-heavy-workloads"></a>Cargas de trabalho pesadas

Para cargas de trabalho pesadas em que a chave primária está a aumentar monotonicamente, crie baldes de sal para ajudar a evitar a escrita de hotspots, em detrimento da produção geral de leitura devido aos exames adicionais necessários. Além disso, ao utilizar o UPSERT para escrever um grande número de registos, desligue o autoCompremit e emseça os registos.

### <a name="bulk-deletes"></a>Eliminação a granel

Ao eliminar um grande conjunto de dados, ligue o autoComprindo antes de emitir a consulta DELETE, de modo a que o cliente não precise de se lembrar das teclas de linha para todas as linhas eliminadas. O AutoCommit impede o cliente de tamponar as linhas afetadas pelo DELETE, para que a Phoenix possa eliminá-las diretamente nos servidores da região sem a despesa de as devolver ao cliente.

### <a name="immutable-and-append-only"></a>Imutável e apenas apêndice

Se o seu cenário favorecer a escrita da velocidade sobre a integridade dos dados, considere desativar o registo de escrita antecipada ao criar as suas tabelas:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Para mais detalhes sobre esta e outras opções, consulte [a Gramática Apache Phoenix.](https://phoenix.apache.org/language/index.html#options)

## <a name="next-steps"></a>Passos seguintes

* [Guia de Afinação De Fénix Apache](https://phoenix.apache.org/tuning_guide.html)
* [Índices Secundários](https://phoenix.apache.org/secondary_indexing.html)
