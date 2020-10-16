---
title: Criar funcionalidades para dados num cluster Azure HDInsight Hadoop - Processo de Ciência de Dados de Equipa
description: Exemplos de consultas de Hive que geram funcionalidades em dados armazenados num cluster Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6261e31fd84b9471fa4ea5d30e1d6a4afbac9115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085383"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar funcionalidades para dados num cluster Hadoop usando consultas de Colmeia
Este documento mostra como criar funcionalidades para dados armazenados num cluster Azure HDInsight Hadoop usando consultas de Hive. Estas consultas de Colmeia usam hive incorporado User-Defined Funções (UDFs), os scripts para os quais são fornecidos.

As operações necessárias para criar funcionalidades podem ser intensivas na memória. O desempenho das consultas de Colmeia torna-se mais crítico nestes casos e pode ser melhorado através da afinação de certos parâmetros. A sintonização destes parâmetros é discutida na secção final.

Exemplos das consultas apresentadas são específicos dos cenários de Datas de [Viagem de Táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório gitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) Estas consultas já têm esquema de dados especificado e estão prontas para serem submetidas a execução. Na secção final, são também discutidos os parâmetros que os utilizadores podem sintonizar para que o desempenho das consultas de Hive possa ser melhorado.

Esta tarefa é um passo no Processo de Ciência de Dados de [Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Aderiu um cluster Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Os Clusters Hadoop Azure HDInsight para Análise Avançada](customize-hadoop-cluster.md).
* Os dados foram enviados para as tabelas hive em clusters Azure HDInsight Hadoop. Caso contrário, siga [os dados da Criação e carregue os dados das tabelas da Hive](move-hive-tables.md) para enviar primeiro os dados para as tabelas da Hive.
* Habilitado acesso remoto ao cluster. Se precisar de instruções, consulte [o nó de cabeça do Conjunto Hadoop](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Geração de recursos
Nesta secção, são descritos vários exemplos das formas pelas quais as funcionalidades podem gerar usando consultas de Hive. Uma vez geradas funcionalidades adicionais, pode adicioná-las como colunas à mesa existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, que podem ser juntas com a tabela original. Aqui estão os exemplos apresentados:

1. [Geração de recursos baseada em frequências](#hive-frequencyfeature)
2. [Riscos de variáveis categóricas na classificação binária](#hive-riskfeature)
3. [Características de extrato do Campo datetime](#hive-datefeatures)
4. [Características de extrato do Campo de Texto](#hive-textfeatures)
5. [Calcular distância entre as coordenadas GPS](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Geração de recursos baseados em frequência
É frequentemente útil calcular as frequências dos níveis de uma variável categórica, ou as frequências de certas combinações de níveis de múltiplas variáveis categóricas. Os utilizadores podem utilizar o seguinte script para calcular estas frequências:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária
Na classificação binária, as variáveis categóricas não numéricas devem ser convertidas em características numéricas quando os modelos utilizados apenas assumem características numéricas. Esta conversão é feita substituindo cada nível não numérico por um risco numérico. Esta secção mostra algumas consultas genéricas da Colmeia que calculam os valores de risco (odds de registo) de uma variável categórica.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados a partir dos dados. Os riscos têm um intervalo entre -Inf e Inf. Um risco > 0 indica que a probabilidade de o alvo ser igual a 1 é maior do que 0,5.

Após a calculada tabela de risco, os utilizadores podem atribuir valores de risco a uma tabela juntando-a à tabela de risco. A consulta de adesão da Colmeia foi fornecida na secção anterior.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Características de extrato dos campos de data
A Colmeia vem com um conjunto de UDFs para processar campos de data. Na Hive, o formato de data padrão é 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32' por exemplo). Esta secção mostra exemplos que extraem o dia de um mês, o mês de um campo de datas, e outros exemplos que convertem uma cadeia de datas num formato diferente do formato padrão para uma cadeia de datas em formato predefinido.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Esta consulta de Colmeia pressupõe que *\<datetime field>* o está no formato de data padrão.

Se um campo de data não estiver no formato predefinido, é necessário converter primeiro o campo de data em carimbo de tempo Unix e, em seguida, converter o carimbo de tempo Unix para uma cadeia de datas que esteja no formato predefinido. Quando a data estiver em formato predefinido, os utilizadores podem aplicar os UDFs embutidas para extrair funcionalidades.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

Nesta consulta, se o *\<datetime field>* padrão como *03/26/2015 12:04:39*, o * \<pattern of the datetime field> '* deve ser `'MM/dd/yyyy HH:mm:ss'` . Para testá-lo, os utilizadores podem correr

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

O *hivesampletable* nesta consulta vem pré-instalado em todos os clusters Azure HDInsight Hadoop por padrão quando os clusters são a provisionados.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Características de extrato dos campos de texto
Quando a tabela Hive tem um campo de texto que contém uma série de palavras que são delimitadas por espaços, a seguinte consulta extrai o comprimento da corda, e o número de palavras na corda.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Calcular distâncias entre conjuntos de coordenadas GPS
A consulta dada nesta secção pode ser aplicada diretamente aos Dados de Viagem de Táxi de NYC. O objetivo desta consulta é mostrar como aplicar uma função matemática incorporada na Colmeia para gerar características.

Os campos que são utilizados nesta consulta são as coordenadas GPS dos locais de recolha e entrega, longitude de *recolha, \_ longitude* *de pickup, \_ latitude de recolha,* * \_ longitude de entrega,* e * \_ latitude de dropoff*. As consultas que calculam a distância direta entre as coordenadas de recolha e entrega são:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

As equações matemáticas que calculam a distância entre duas coordenadas de GPS podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts,</a> da autoria de Peter Lapisu. Neste Javascript, a função `toRad()` é apenas *lat_or_lon*pi/180, que converte graus em radianos. Aqui, *lat_or_lon* é a latitude ou longitude. Uma vez que a Hive não fornece a `atan2` função, mas fornece a função, a `atan` `atan2` função é implementada por `atan` função na consulta de Colmeia acima, utilizando a definição fornecida na <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia.</a>

![Criar área de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de UDFs incorporados por Colmeia pode ser encontrada na secção **funções incorporadas** no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Wiki Apache Hive).</a>  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Tópicos avançados: Sintonizar os parâmetros da Colmeia para melhorar a velocidade da consulta
As definições de parâmetros padrão do cluster hive podem não ser adequadas para as consultas de Colmeia e os dados que as consultas estão a processar. Esta secção discute alguns parâmetros que os utilizadores podem sintonizar para melhorar o desempenho das consultas de Hive. Os utilizadores precisam de adicionar as consultas de afinação de parâmetros antes das consultas de dados de processamento.

1. **Java heap space**: Para consultas que envolvam a junção de grandes conjuntos de dados, ou o processamento de longos registos, **ficar sem espaço é** um dos erros comuns. Este erro pode ser evitado definindo parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* para valores desejados. Segue-se um exemplo:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Este parâmetro aloca a memória de 4 GB ao espaço de pilha de Java e também torna a triagem mais eficiente alocar mais memória para ele. É uma boa ideia brincar com estas dotações se houver algum erro de falha de emprego relacionado com o espaço de amontoados.

1. **Tamanho do bloco DFS**: Este parâmetro define a menor unidade de dados que o sistema de ficheiros armazena. Como exemplo, se o tamanho do bloco DFS for de 128 MB, então quaisquer dados de tamanho inferior e até 128 MB são armazenados num único bloco. Os dados que são maiores que 128 MB são distribuídos blocos extra. 
2. Escolher um pequeno bloco de tamanho causa grandes sobrecargas em Hadoop, uma vez que o nó de nome tem que processar muitos mais pedidos para encontrar o bloco relevante relativo ao arquivo. Uma definição recomendada ao lidar com dados gigabytes (ou maiores) é:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Otimização da operação de junção na Colmeia**: Enquanto as operações de junção no quadro de mapa/redução ocorrem normalmente na fase de redução, por vezes, ganhos enormes podem ser alcançados através do agendamento de juntas na fase do mapa (também chamados "mapjojos"). Desa esta opção:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Especificando o número de mappers para a Colmeia**: Enquanto hadoop permite ao utilizador definir o número de redutores, o número de mappers normalmente não é definido pelo utilizador. Um truque que permite algum grau de controlo neste número é escolher as variáveis Hadoop *mapred.min.split.size* e *mapred.max.split.size* como o tamanho de cada tarefa de mapa é determinado por:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Tipicamente, o valor padrão de:
    
   - *mapred.min.split.size* é 0, o de
   - *mapred.max.split.size* é **Long.MAX** e o de 
   - *dfs.block.size* é 64 MB.

     Como podemos ver, dado o tamanho dos dados, afinação destes parâmetros ao "definição" permite-nos sintonizar o número de mappers utilizados.

4. Aqui estão algumas outras opções mais **avançadas** para otimizar o desempenho da Hive. Estas opções permitem definir a memória alocada para mapear e reduzir tarefas, e podem ser úteis para ajustar o desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior do que o tamanho da memória física de cada nó de trabalhador no cluster Hadoop.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```

