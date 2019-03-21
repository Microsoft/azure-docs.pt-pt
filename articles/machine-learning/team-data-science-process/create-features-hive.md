---
title: Criar características para dados num cluster do Hadoop - Team Data Science Process
description: Exemplos de consultas do Hive que geram recursos em dados armazenados num cluster do Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4d74b122f3b5567e8291ec5f3ff4e1dda7ff68f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835021"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar características para dados num cluster do Hadoop com consultas do Hive
Este documento mostra como criar características para dados armazenados num cluster do Azure HDInsight Hadoop com consultas do Hive. Estas consultas do Hive utilizam embedded Hive User-Defined funções (UDFs), os scripts para os quais são fornecidos.

As operações necessárias para criar recursos podem ser elevado consumo de memória. O desempenho das consultas do Hive se torna mais crítico nesses casos e pode ser aumentado com determinados parâmetros de otimização. O ajuste desses parâmetros é abordado na seção final.

Exemplos de consultas que são apresentados são específicos para o [dados de viagens de táxis NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos na [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para serem submetidas para executar. Na seção final, os parâmetros que utilizadores podem sintonizar para que o desempenho das consultas do Hive pode ser melhorado também são discutidos.

Esta tarefa é um passo na [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md)
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [personalizar o Azure HDInsight Hadoop Clusters do Advanced Analytics](customize-hadoop-cluster.md).
* Os dados tem sido carregados para tabelas do Hive em clusters do Hadoop de HDInsight do Azure. Se não tiver, siga [criar e carregar dados para tabelas do Hive](move-hive-tables.md) para carregar dados para tabelas do Hive em primeiro lugar.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [aceder a cabeça nó de Cluster do Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Geração de funcionalidade
Nesta secção são descritos vários exemplos de formas em que podem gerar recursos com consultas do Hive. Depois de gerar recursos adicionais, pode adicioná-los como colunas na tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que, em seguida, pode ser associado com a tabela original. Aqui estão os exemplos apresentados:

1. [Geração de recursos com base na frequência](#hive-frequencyfeature)
2. [Riscos de variáveis Categóricos em classificação binária](#hive-riskfeature)
3. [Extrair funcionalidades a partir do campo Datetime](#hive-datefeatures)
4. [Extrair funcionalidades a partir do campo de texto](#hive-textfeatures)
5. [Calcular a distância entre coordenadas do GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Geração de recursos com base na frequência
Muitas vezes é útil calcular as frequências dos níveis de uma variável categórica, ou as frequências de determinados combinações dos níveis de várias variáveis categóricas. Os utilizadores podem utilizar o seguinte script para calcular essas frequências:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Riscos de variáveis categóricos em classificação binária
Na classificação binária, variáveis de categóricas não numéricos devem ser convertidas em numérico funcionalidades quando os modelos a ser utilizados apenas numérico funcionalidades. Esta conversão é feita substituindo cada nível de não numéricos com um risco numérico. Esta secção mostra algumas consultas do Hive genéricas que calcular os valores de risco (probabilidades de log) de uma variável categórica.

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

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` estão definidas para suavizar os valores de risco calculados a partir dos dados. Riscos tem um intervalo entre -Inf e Inf. Um risco > 0 indica que a probabilidade de que o destino seja igual a 1 é maior que 0,5.

Depois do risco é calculada a tabela, os utilizadores podem atribuir valores de risco para uma tabela ao associá-lo com a tabela de risco. A consulta de junção do Hive foi fornecida na secção anterior.

### <a name="hive-datefeatures"></a>Extrair funcionalidades de campos de datetime
Hive vem com um conjunto de UDFs para processar os campos datetime. No ramo de registo, o formato de datetime de predefinido é ' aaaa-MM-dd 00:00:00 ' ('1970-01-01 12:21:32 "por exemplo). Esta secção mostra exemplos que extrair o dia do mês, o mês a partir de um campo datetime e outros exemplos de converter uma cadeia de caracteres de datetime num formato que o formato padrão para uma cadeia de caracteres de datetime Formatar em default.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Esta consulta do Hive parte do princípio de que o *<datetime field>* está no formato datetime padrão.

Se um campo datetime não estiver no formato predefinido, terá de converter o campo de datetime para o carimbo de data / hora Unix em primeiro lugar e, em seguida, converter o carimbo de data / hora Unix numa cadeia de datetime, que está no formato padrão. Quando a datetime em default é o formato, os utilizadores podem aplicar a datetime embedded UDFs para extrair recursos.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nesta consulta, se o *<datetime field>* com o padrão, como *26/03/2015 12:04:39*, o  *<pattern of the datetime field>'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os utilizadores podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

O *hivesampletable* nesta consulta vem pré-instalada em todos os clusters do Hadoop de HDInsight do Azure por predefinição quando são aprovisionados clusters.

### <a name="hive-textfeatures"></a>Extrair funcionalidades de campos de texto
Quando a tabela de Hive tem um campo de texto que contém uma cadeia de palavras que são delimitados por espaços, a seguinte consulta extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcular distâncias entre conjuntos de coordenadas do GPS
A consulta fornecida nesta secção pode ser aplicada diretamente para os dados de viagens de táxis NYC. O objetivo desta consulta é mostrar como aplicar uma função de matemática incorporada no Hive para gerar recursos.

Os campos que são utilizados nesta consulta são as coordenadas do GPS de localizações de recolha e de redução, com o nome *recolha\_longitude*, *recolha\_latitude*,  *redução\_longitude*, e *redução\_latitude*. As consultas que calculam a distância direta entre as coordenadas de recolha e de redução são:

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

As equações de matemáticas calcular a distância entre duas coordenadas do GPS podem ser encontradas no <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Scripts do tipo de Movable</a> site, criado por Peter Lapisu. Nesse Javascript, a função `toRad()` simplesmente *lat_or_lon*pi/180, que converte graus em radianos. Aqui, *lat_or_lon* é a latitude ou longitude. Uma vez que o ramo de registo não fornece a função `atan2`, mas fornece a função `atan`, o `atan2` função é implementada por `atan` função na consulta do Hive acima com a definição fornecida no <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Criar área de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de Hive UDFs incorporados que podem ser encontradas no **funções incorporadas** secção no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Tópicos avançados: Otimizar os parâmetros de Hive para melhorar a velocidade de consulta
As predefinições de parâmetro de cluster de Hive podem não ser adequadas para as consultas do Hive e os dados que as consultas estão a processar. Esta secção descreve alguns parâmetros que utilizadores podem sintonizar para melhorar o desempenho das consultas do Hive. Os utilizadores têm de adicionar o parâmetro ajustar consultas antes das consultas do processamento de dados.

1. **Espaço de área dinâmica para dados de Java**: Para consultas que envolvem ingressar em grandes conjuntos de dados ou processar registos de longo **ficar sem espaço de área dinâmica para dados** é um dos erros comuns. Este erro pode ser evitado ao definir os parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* para valores pretendidos. Segue-se um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Este parâmetro aloca memória de 4GB de espaço de área dinâmica para dados de Java e também torna a classificação mais eficiente ao alocar mais memória para o mesmo. É uma boa idéia para brincar com essas alocações, se houver qualquer tarefa relacionados ao espaço de área dinâmica para dados de erros de falha.

1. **Bloquear o DFS tamanho**: Este parâmetro define a menor unidade de dados que armazena o sistema de ficheiros. Por exemplo, se o tamanho do bloco DFS é 128 MB, em seguida, quaisquer dados e de tamanho menor e até 128 MB é armazenado num único bloco. Dados que é maiores do que 128 MB são alocados blocos extras. 
2. Escolher um tamanho de pequeno bloco faz com que grandes sobrecargas no Hadoop, uma vez que o nó de nome tem de processar mais pedidos para localizar o bloco relevante relativas ao ficheiro. Uma configuração recomendada quando lidando com gigabytes (ou superior) os dados são:

        set dfs.block.size=128m;

2. **Otimizando a operação de associação no Hive**: Enquanto as operações de associação do Framework de mapa/redução, normalmente ocorrem na fase de reduza, às vezes, enormes ganhos podem ser obtidos pelo agendamento associações na fase de mapa (também chamada de "mapjoins"). Para direcionar o Hive para fazer isso, sempre que possível, defina:
   
       set hive.auto.convert.join=true;

3. **Especificar o número de mapeadores Hive**: Enquanto o Hadoop permite ao utilizador para definir o número de redutores, o número de mapeadores é, normalmente, não estar definido pelo utilizador. Um truque que permite algum grau de controle neste número tem de escolher as variáveis de Hadoop *mapred.min.split.size* e *mapred.max.split.size* como o tamanho do mapa de cada tarefa é determinada por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalmente, o valor predefinido de:
    
   - *mapred.min.split.size* é 0, que de
   - *mapred.Max.split.size* é **Long.MAX** e do 
   - *DFS.Block.size* é 64 MB.

     Como podemos ver, dado o tamanho de dados, ajuste estes parâmetros por "configuração"-los nos permite ajustar o número de mapeadores utilizado.

4. Aqui estão outras mais algumas **opções avançadas** para otimizar o desempenho do Hive. Estas permitem-lhe definir a memória alocada para mapear e reduzir as tarefas e podem ser útil na realização de alterações de desempenho. Tenha em atenção que o *mapreduce.reduce.memory.mb* não pode ser maior que o tamanho de memória física de cada nó de trabalho do cluster de Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

