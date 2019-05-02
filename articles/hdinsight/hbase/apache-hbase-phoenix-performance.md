---
title: Desempenho do Phoenix no HDInsight do Azure
description: Melhores práticas para otimizar o desempenho do Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 4fc4d1843ddb8d007ca062d928ebbddf90909583
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690038"
---
# <a name="apache-phoenix-performance-best-practices"></a>Melhores práticas de desempenho do Apache Phoenix

O aspecto mais importante da [Apache Phoenix](https://phoenix.apache.org/) desempenho é otimizar subjacentes [Apache HBase](https://hbase.apache.org/). Phoenix cria um modelo de dados relacionais sobre o HBase que converte consultas SQL em operações de HBase, como análises. O design de seu esquema de tabela, a seleção e ordenação dos campos na sua chave primária e a sua utilização de índices, todos os afetar o desempenho do Phoenix.

## <a name="table-schema-design"></a>Design do esquema de tabela

Quando cria uma tabela no Phoenix, essa tabela é armazenada numa tabela de HBase. A tabela HBase contém os grupos de colunas (famílias de colunas), que são acedidos em conjunto. Uma linha na tabela Phoenix é uma linha na tabela HBase, onde cada linha consiste em células com a versão associadas uma ou mais colunas. Logicamente, uma única linha de HBase é uma coleção de pares chave-valor, cada um com o mesmo valor de rowkey. Ou seja, cada par de chave-valor tem um atributo de rowkey e o valor desse atributo rowkey é o mesmo para uma linha específica.

O design do esquema de uma tabela de Phoenix inclui primário design, design de famílias de coluna, design de coluna individual e como os dados estão particionados de chaves.

### <a name="primary-key-design"></a>Design de chave primário

A chave primária definida numa tabela no Phoenix determina como os dados são armazenados dentro de rowkey da tabela HBase subjacente. No HBase, a única forma de acessar uma linha particular é com o rowkey. Além disso, os dados armazenados numa tabela do HBase são ordenados pelo rowkey. Phoenix baseia-se o valor de rowkey, concatenar os valores de cada uma das colunas na linha, pela ordem em que são definidas na chave primária.

Por exemplo, uma tabela de contactos tem o nome próprio último nome, número de telefone e endereço, todos da mesma família de colunas. É possível definir uma chave primária com base num número crescente de sequência:

|rowkey|       endereço|   telefone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dinis|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

No entanto, se consultar frequentemente por apelido esta chave primária não executar bem, uma vez que cada consulta requer a análise da tabela completa para ler o valor de cada lastName. Em vez disso, pode definir uma chave primária no lastName, firstName e colunas de número da Previdência social. Esta última coluna é para eliminar a ambiguidade dois residentes no mesmo endereço com o mesmo nome, como um pai e filho.

|rowkey|       endereço|   telefone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dinis| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Com esta nova chave primária a linha chaves geradas pelo Phoenix seria:

|rowkey|       endereço|   telefone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dinis| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Na primeira linha acima, os dados para o rowkey são representados como mostrado:

|rowkey|       key|   value| 
|------|--------------------|---|
|  Dole-John-111|endereço |1111 San Gabriel Dr.|  
|  Dole-John-111|telefone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dinis|  
|  Dole-John-111|socialSecurityNum |111| 

Agora, este rowkey armazena uma cópia duplicada de dados. Considere o tamanho e número de colunas a que incluir na sua chave primária, uma vez que este valor está incluído com cada célula da tabela de HBase subjacente.

Além disso, se a chave primária tem valores de que forma monótona estão a aumentar, deve criar a tabela com *salt buckets* para ajudar a evitar a criação de pontos de acesso de escrita - veja [particionar dados](#partition-data).

### <a name="column-family-design"></a>Design de família de coluna

Se algumas colunas são acedidas com mais frequência do que outras, deve criar várias famílias de coluna para separar as colunas acedidas com frequência das colunas são raramente acedidas.

Além disso, se determinadas colunas tendem a ser acedidos em conjunto, coloque essas colunas da mesma família de colunas.

### <a name="column-design"></a>Design de coluna

* Mantenha as colunas VARCHAR em cerca de 1 MB devido aos custos de e/s de colunas de grandes dimensões. Durante o processamento de consultas, o HBase materializa células por completo antes de os enviar ao longo para o cliente e o cliente recebe-las por completo antes de manuseio-los ao código do aplicativo.
* Store valores de coluna com um formato compacto, como o protobuf, Avro, msgpack ou BSON. Não é recomendável JSON, conforme é maior.
* Considere a comprimir os dados antes de armazenamento para reduzir a latência e os custos de e/s.

### <a name="partition-data"></a>Dados de partição

Phoenix permite-lhe controlar o número de regiões em que os seus dados são distribuídos, que pode aumentar significativamente o desempenho de leitura/escrita. Ao criar uma tabela de Phoenix, pode salt ou previamente dividir os dados.

Para salt uma tabela durante a criação, especifique o número de salt buckets:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Essa inclusão de Salt divide a tabela juntamente com os valores de chaves primárias, escolha os valores automaticamente. 

Para controlar onde ocorrem as divisões de tabela, previamente, pode dividir a tabela ao fornecer os valores de intervalo ao longo do que a divisão ocorre. Por exemplo criar uma tabela dividido ao longo de três regiões:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Design do índice

Um índice de Phoenix é uma tabela de HBase que armazena uma cópia de alguns ou todos os dados da tabela indexada. Um índice melhora o desempenho para tipos específicos de consultas.

Quando tiver vários índices definidos e, em seguida, consultar uma tabela, Phoenix seleciona automaticamente o melhor índice para a consulta. O índice principal é criado automaticamente com base nas chaves primárias que selecionar.

Para consultas antecipadas, também pode criar índices secundários, especificando as respetivas colunas.

Ao conceber os índices:

* Apenas crie os índices que precisa.
* Limite o número de índices em tabelas com frequência é atualizados. Atualizações para uma tabela implica escritas para a tabela principal e as tabelas de índice.

## <a name="create-secondary-indexes"></a>Criar índices secundários

Índices secundários podem melhorar o desempenho de leitura ao ativar o que poderia ser uma análise da tabela completa numa lookup ponto, ao custo do espaço de armazenamento e velocidade de escrita. Índices secundários podem ser adicionados ou removidos após a criação de tabela e não necessitam de alterações nas consultas existentes – consultas apenas são executadas mais depressa. Consoante as suas necessidades, considere a criação de índices abrangidos, índices funcionais ou ambos.

### <a name="use-covered-indexes"></a>Utilize índices abrangidos

Índices abrangidos são índices que incluem dados da linha, além dos valores que são indexados. Depois de localizar a entrada de índice desejado, não é necessário para aceder à tabela principal.

Por exemplo, no exemplo contacte tabela foi possível criar um índice secundário no apenas a coluna de socialSecurityNum. Este índice secundário seria acelerar as consultas que filtrar por valores socialSecurityNum, mas a obter outros valores de campo exigirá outro ler com base na tabela principal.

|rowkey|       endereço|   telefone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dinis| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

No entanto, se pretender pesquisar o firstName e lastName, tendo em conta o socialSecurityNum normalmente, pode criar um índice coberto que inclui o firstName e lastName como dados reais na tabela de índice:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Este índice coberta permite que a consulta seguinte para obter todos os dados apenas através da leitura da tabela que contém o índice secundário:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Utilize índices funcionais

Índices funcionais permitem-lhe criar um índice numa expressão arbitrária que espera a ser utilizado em consultas. Depois de ter um índice funcional no local e uma consulta usa essa expressão, o índice pode ser utilizado para obter os resultados em vez da tabela de dados.

Por exemplo, pode criar um índice para permitir que faça pesquisas de maiúsculas e minúsculas no nome do próprio combinado e último nome de uma pessoa:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Design de consulta

As principais considerações de design de consulta são:

* Compreender o plano de consulta e verifique se o comportamento esperado.
* Junte-se com eficiência.

### <a name="understand-the-query-plan"></a>Compreender o plano de consulta

Na [SQLLine](http://sqlline.sourceforge.net/), utilize EXPLICATIVO seguido pela sua consulta SQL para ver o plano de operações que irão efetuar a Phoenix. Verifique se o plano:

* Utiliza a chave primária quando apropriado.
* Utiliza as apropriadas índices secundários, em vez da tabela de dados.
* Utiliza o intervalo de análise ou análise de ignorar sempre que possível, em vez de tabela de análise.

#### <a name="plan-examples"></a>Exemplos de plano

Por exemplo, suponhamos que tem uma tabela chamada VOOS, que armazena informações de atraso de voo.

Para selecionar todos os voos com um airlineid de `19805`, em que airlineid é um campo não estiver a ser a chave primária ou em qualquer índice:

    select * from "FLIGHTS" where airlineid = '19805';

Execute o comando de explicar da seguinte forma:

    explain select * from "FLIGHTS" where airlineid = '19805';

O plano de consulta tem o seguinte aspeto:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Neste plano, tenha em atenção a frase de análise completa sobre o VOOS. Esta expressão indica que a execução faz uma verificação de tabela em todas as linhas na tabela, em vez de utilizar a opção de verificação de intervalo ou Ignorar verificação mais eficiente.

Agora, digamos que queira consultar voos em 2 de Janeiro de 2014 para a deteção de carrier `AA` onde seu flightnum era maior que 1. Vamos supor que o ano de colunas, mês, dayofmonth, deteção de carrier e flightnum existem na tabela de exemplo e fazem parte da chave primária composta. A consulta teria o aspeto da seguinte forma:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Vamos examinar o plano para esta consulta com:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

O plano resultante é:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Os valores entre parêntesis Retos são o intervalo de valores para as chaves primárias. Neste caso, os valores de intervalo são fixos com dayofmonth 2, 1 do mês e ano de 2014, mas permitir valores para flightnum iniciar com 2 e, na cópia de segurança (`*`). Este plano de consulta confirma que a chave primária está sendo usada conforme esperado.

Em seguida, crie um índice na tabela de VOOS, com o nome `carrier2_idx` isso estiver nos apenas o campo de operadora. Este índice inclui também flightdate, tailnum, origem e flightnum como colunas cobertas cujos dados também são armazenados no índice.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Digamos que queira obter a operadora juntamente com o flightdate e tailnum, tal como a seguinte consulta:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Deverá ver este índice a ser utilizado:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Para obter uma listagem completa dos itens que podem aparecer explicar resultados de plano, consulte a secção de planos de explicar no [guia de ajuste do Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Junte-se de forma eficiente

Em geral, deseja evitar associações, a menos que um lado é pequeno, especialmente em consultas frequentes.

Se necessário, pode fazer associações grandes com o `/*+ USE_SORT_MERGE_JOIN */` sugestão, mas uma grande União é uma operação dispendiosa ao longo de grandes quantidades de linhas. Se o tamanho geral de todas as tabelas da direita lado iria exceder a memória disponível, utilize o `/*+ NO_STAR_JOIN */` sugestão.

## <a name="scenarios"></a>Cenários

As diretrizes seguintes descrevem alguns padrões comuns.

### <a name="read-heavy-workloads"></a>Cargas de trabalho de leitura intensiva

Para leitura intensiva casos de utilização, certifique-se de que está a utilizar os índices. Além disso, para guardar a leitura tempo de sobrecarga, considere a criação de índices abrangidos.

### <a name="write-heavy-workloads"></a>Cargas de trabalho pesadas de escrita

Para cargas de trabalho pesadas de escrita em que a chave primária forma monótona está aumentando, crie registos de salt para ajudar a evitar pontos de acesso de escrita, às custas de débito de leitura global devido a verificações adicionais necessários. Além disso, quando utilizar UPSERT para escrever um grande número de registos, desative a confirmação automática e em lote, os registos.

### <a name="bulk-deletes"></a>Exclusões em massa

Ao eliminar um grande conjunto de dados, ative a confirmação automática antes de emitir a consulta de eliminação, para que o cliente não precisa se lembrar as chaves de linha para todas as linhas eliminadas. Confirmação automática impede que o cliente na memória intermédia as linhas afetadas pela eliminação, para que esse Phoenix pode eliminá-los diretamente nos servidores de região sem a despesa de retornando-os para o cliente.

### <a name="immutable-and-append-only"></a>Imutável e só de acréscimo

Se o seu cenário de favorece velocidade de escrita sobre a integridade dos dados, considere desativar o registo de escrita-ahead durante a criação de tabelas:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Para obter detalhes sobre esta e outras opções, consulte [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Passos Seguintes

* [Guia de ajuste do Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Índices secundários](https://phoenix.apache.org/secondary_indexing.html)
