---
title: Criar funcionalidades para dados num cluster de Hadoop Azure HDInsight - Team Data Science Process
description: Exemplos de consultas da Hive que geram funcionalidades em dados armazenados num cluster Hadoop Azure HDInsight.
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
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721783"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar funcionalidades para dados num cluster Hadoop usando consultas da Hive
Este documento mostra como criar funcionalidades para dados armazenados num cluster de Hadoop Azure HDInsight usando consultas da Hive. Estas consultas de Hive utilizam funções definidas pelo utilizador incorporadas (UDFs), os scripts para os quais são fornecidos.

As operações necessárias para criar funcionalidades podem ser intensivas na memória. O desempenho das consultas da Hive torna-se mais crítico nestes casos e pode ser melhorado afinando certos parâmetros. A afinação destes parâmetros é discutida na secção final.

Exemplos das consultas apresentadas são específicos dos cenários de Dados da [Viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de Táxi de NYC também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já têm dados de esquema saem e estão prontos para serem submetidos a execução. Na secção final, são também discutidos parâmetros que os utilizadores podem sintonizar para que o desempenho das consultas da Hive possa ser melhorado.

Esta tarefa é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Forprovisionou um cluster Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Customize Azure HDInsight Hadoop Clusters for Advanced Analytics](customize-hadoop-cluster.md).
* Os dados foram enviados para as tabelas da Hive em clusters Hadoop Azure HDInsight. Se não tiver, siga a [Create e carregue os dados para as tabelas da Hive](move-hive-tables.md) para fazer o upload de dados para as tabelas da Colmeia primeiro.
* Acesso remoto ativado ao cluster. Se precisar de instruções, consulte [Aceda ao nó de cabeça do aglomerado de hadoop](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Geração de recursos
Nesta secção são descritos vários exemplos das formas pelas quais as funcionalidades podem estar a gerar usando consultas da Hive. Depois de ter gerado funcionalidades adicionais, pode adicioná-las como colunas à tabela existente ou criar uma nova tabela com as características adicionais e a chave primária, que depois pode ser unida à tabela original. Aqui estão os exemplos apresentados:

1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de Variáveis Categóricas na Classificação Binária](#hive-riskfeature)
3. [Características de extrato do Campo datetime](#hive-datefeatures)
4. [Características de extrato do Campo de Texto](#hive-textfeatures)
5. [Calcular distância entre as coordenadas gps](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Geração de recursos baseado em frequência
É frequentemente útil calcular as frequências dos níveis de uma variável categórica, ou as frequências de certas combinações de níveis de múltiplas variáveis categóricas. Os utilizadores podem utilizar o seguinte script para calcular estas frequências:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária
Na classificação binária, as variáveis categóricas não numéricas devem ser convertidas em características numéricas quando os modelos que estão a ser utilizados apenas têm características numéricas. Esta conversão é feita substituindo cada nível não numérico por um risco numérico. Esta secção mostra algumas consultas genéricas da Hive que calculam os valores de risco (odds de registo) de uma variável categórica.

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

Neste exemplo, as `smooth_param1` `smooth_param2` variáveis e são definidas para suavizar os valores de risco calculados a partir dos dados. Os riscos têm um intervalo entre a Inf e a Inf. Um risco > 0 indica que a probabilidade de o alvo ser igual a 1 é superior a 0,5.

Após a calculação da tabela de risco, os utilizadores podem atribuir valores de risco a uma tabela, unindo-os à tabela de risco. A consulta de junção da Colmeia foi fornecida em secção anterior.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Características de extrato dos campos de data
A Colmeia vem com um conjunto de UDFs para processamento de campos de data. Na Colmeia, o formato de data padrão é 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32' por exemplo). Esta secção mostra exemplos que extraem o dia de um mês, o mês de um campo de data, e outros exemplos que convertem uma cadeia de data num formato diferente do formato padrão para uma cadeia de data em formato padrão.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Esta consulta da Hive * \<* pressupõe que o campo de data>está no formato de data padrão.

Se um campo de data não estiver no formato padrão, tem de converter primeiro o campo de data em carimbo de tempo Unix e, em seguida, converter o carimbo de tempo Unix numa cadeia de data que se encontra no formato padrão. Quando a data estiver em formato predefinido, os utilizadores podem aplicar os UDFs de data incorporada para extrair funcionalidades.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nesta consulta, se * \<* o campo de data>tem o padrão de *03/26/2015 12:04:39* `'MM/dd/yyyy HH:mm:ss'`, o * \<padrão do campo* de data>' deve ser. Para testá-lo, os utilizadores podem correr

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A *colmeia* nesta consulta vem pré-instalada em todos os clusters hadoop Azure HDInsight por padrão quando os clusters são provisionados.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Características de extrato de campos de texto
Quando a tabela Da Colmeia tem um campo de texto que contém uma série de palavras que são delimitadas por espaços, a seguinte consulta extrai o comprimento da corda, e o número de palavras na corda.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Calcular distâncias entre conjuntos de coordenadas gps
A consulta dada nesta secção pode ser aplicada diretamente aos Dados da Viagem de Táxi de NYC. O objetivo desta consulta é mostrar como aplicar uma função matemática incorporada na Colmeia para gerar características.

Os campos que são usados nesta consulta são as coordenadas GPS de locais de recolha e entrega, chamados *longitude\_pickup,* *latitude pickup,\_* *longitude dropoff\_* e latitude *dropoff\_*. As consultas que calculam a distância direta entre as coordenadas de recolha e de entrega são:

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

As equações matemáticas que calculam a distância entre duas coordenadas gps podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">de Scripts do Tipo Móvel,</a> da autoria de Peter Lapisu. Neste Javascript, a `toRad()` função é apenas *lat_or_lon*pi/180, que converte graus em radians. Aqui, *lat_or_lon* é a latitude ou longitude. Uma vez que a `atan2`Hive não fornece `atan`a `atan2` função, `atan` mas fornece a função, a função é implementada por função na consulta da Colmeia acima usando a definição fornecida na <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia.</a>

![Criar área de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de UDFs incorporados pela Colmeia pode ser encontrada na secção **funções incorporadas** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Wiki Apache Hive</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Tópicos avançados: Afinar parâmetros da Colmeia para melhorar a velocidade de consulta
As definições padrão do parâmetro do cluster hive podem não ser adequadas para as consultas da Colmeia e os dados que as consultas estão a processar. Esta secção discute alguns parâmetros que os utilizadores podem sintonizar para melhorar o desempenho das consultas da Hive. Os utilizadores precisam de adicionar as consultas de afinação do parâmetro antes das consultas de processamento dos dados.

1. **Espaço de pilha de Java**: Para consultas que envolvam a junção de grandes conjuntos de dados, ou o processamento de registos longos, ficar **sem espaço de pilha** é um dos erros comuns. Este erro pode ser evitado definindo parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* aos valores desejados. Segue-se um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Este parâmetro atribui a memória de 4-GB ao espaço heap de Java e também torna a classificação mais eficiente, alocando mais memória para ele. É uma boa ideia brincar com estas dotações se houver erros de falha de emprego relacionados com o espaço heap.

1. **Tamanho do bloco DFS**: Este parâmetro define a menor unidade de dados que o sistema de ficheiros armazena. Como exemplo, se o tamanho do bloco DFS for de 128 MB, então quaisquer dados de tamanho inferior a 128 MB são armazenados num único bloco. Os dados que são superiores a 128 MB são blocos extra atribuídos. 
2. Escolher um pequeno tamanho de bloco causa grandes despesas em Hadoop, uma vez que o nó de nome tem que processar muitos mais pedidos para encontrar o bloco relevante relativo ao arquivo. Uma definição recomendada ao lidar com dados de gigabytes (ou maiores) é:

        set dfs.block.size=128m;

2. **Otimizar a operação de adesão na Hive**: Ao juntar operações no quadro de mapa/redução normalmente ocorrem na fase de redução, por vezes, enormes ganhos podem ser alcançados através do agendamento de juntas na fase do mapa (também chamados de "mapjoins"). Definir esta opção:
   
       set hive.auto.convert.join=true;

3. **Especificando o número de mappers para**a Hive : Enquanto hadoop permite ao utilizador definir o número de redutores, o número de mappers normalmente não é definido pelo utilizador. Um truque que permite algum grau de controlo neste número é escolher as variáveis Hadoop *mapred.min.split.size* e *mapred.max.split.size* como o tamanho de cada tarefa do mapa é determinado por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Tipicamente, o valor padrão de:
    
   - *mapred.min.split.size* é 0, o de
   - *mapred.max.split.size* é **Long.MAX** e o de 
   - *dfs.block.size* é de 64 MB.

     Como podemos ver, dado o tamanho dos dados, a finar estes parâmetros através da "definição" permite-nos afinar o número de mappers utilizados.

4. Aqui estão algumas outras opções mais **avançadas** para otimizar o desempenho da Colmeia. Estas opções permitem definir a memória atribuída para mapear e reduzir tarefas, e podem ser úteis para ajustar o desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior do que o tamanho da memória física de cada nó de trabalhador no cluster Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

