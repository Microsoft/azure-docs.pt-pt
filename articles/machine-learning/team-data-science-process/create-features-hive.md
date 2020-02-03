---
title: Criar funcionalidades para dados num cluster de Hadoop Azure HDInsight - Team Data Science Process
description: Exemplos de consultas do Hive que geram recursos em dados armazenados num cluster do Azure HDInsight Hadoop.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721783"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar características para dados num cluster do Hadoop com consultas do Hive
Este documento mostra como criar características para dados armazenados num cluster do Azure HDInsight Hadoop com consultas do Hive. Estas consultas do Hive utilizam embedded Hive User-Defined funções (UDFs), os scripts para os quais são fornecidos.

As operações necessárias para criar recursos podem ser elevado consumo de memória. O desempenho das consultas do Hive se torna mais crítico nesses casos e pode ser aumentado com determinados parâmetros de otimização. O ajuste desses parâmetros é abordado na seção final.

Exemplos das consultas apresentadas são específicos dos cenários de Dados da [Viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de Táxi de NYC também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para serem submetidas para executar. Na seção final, os parâmetros que utilizadores podem sintonizar para que o desempenho das consultas do Hive pode ser melhorado também são discutidos.

Esta tarefa é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Customize Azure HDInsight Hadoop Clusters for Advanced Analytics](customize-hadoop-cluster.md).
* Os dados tem sido carregados para tabelas do Hive em clusters do Hadoop de HDInsight do Azure. Se não tiver, siga a [Create e carregue os dados para as tabelas da Hive](move-hive-tables.md) para fazer o upload de dados para as tabelas da Colmeia primeiro.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [Aceda ao nó de cabeça do aglomerado de hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Geração de recursos
Nesta secção são descritos vários exemplos de formas em que podem gerar recursos com consultas do Hive. Depois de gerar recursos adicionais, pode adicioná-los como colunas na tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que, em seguida, pode ser associado com a tabela original. Aqui estão os exemplos apresentados:

1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de Variáveis Categóricas na Classificação Binária](#hive-riskfeature)
3. [Características de extrato do Campo datetime](#hive-datefeatures)
4. [Características de extrato do Campo de Texto](#hive-textfeatures)
5. [Calcular distância entre as coordenadas gps](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Geração de recursos baseado em frequência
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


### <a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária
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

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados a partir dos dados. Riscos tem um intervalo entre -Inf e Inf. Um risco > 0 indica que a probabilidade de que o destino seja igual a 1 é maior que 0,5.

Depois do risco é calculada a tabela, os utilizadores podem atribuir valores de risco para uma tabela ao associá-lo com a tabela de risco. A consulta de junção do Hive foi fornecida na secção anterior.

### <a name="hive-datefeatures"></a>Características de extrato dos campos de data
Hive vem com um conjunto de UDFs para processar os campos datetime. No ramo de registo, o formato de datetime de predefinido é ' aaaa-MM-dd 00:00:00 ' ('1970-01-01 12:21:32 "por exemplo). Esta secção mostra exemplos que extrair o dia do mês, o mês a partir de um campo datetime e outros exemplos de converter uma cadeia de caracteres de datetime num formato que o formato padrão para uma cadeia de caracteres de datetime Formatar em default.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Esta consulta da Hive pressupõe que o *campo de data\<>* está no formato de data padrão.

Se um campo datetime não estiver no formato predefinido, terá de converter o campo de datetime para o carimbo de data / hora Unix em primeiro lugar e, em seguida, converter o carimbo de data / hora Unix numa cadeia de datetime, que está no formato padrão. Quando a datetime em default é o formato, os utilizadores podem aplicar a datetime embedded UDFs para extrair recursos.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nesta consulta, se o *campo de data\<>* tem o padrão como *03/26/2015 12:04:39*, o padrão\<do campo de *data>'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os utilizadores podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A *colmeia* nesta consulta vem pré-instalada em todos os clusters hadoop Azure HDInsight por padrão quando os clusters são provisionados.

### <a name="hive-textfeatures"></a>Características de extrato de campos de texto
Quando a tabela de Hive tem um campo de texto que contém uma cadeia de palavras que são delimitados por espaços, a seguinte consulta extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcular distâncias entre conjuntos de coordenadas gps
A consulta fornecida nesta secção pode ser aplicada diretamente para os dados de viagens de táxis NYC. O objetivo desta consulta é mostrar como aplicar uma função de matemática incorporada no Hive para gerar recursos.

Os campos que são usados nesta consulta são as coordenadas GPS de locais de recolha e entrega, nomeados *pickup\_longitude,* *pickup\_latitude,* *dropoff\_longitude,* e *dropoff\_latitude.* As consultas que calculam a distância direta entre as coordenadas de recolha e de redução são:

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

As equações matemáticas que calculam a distância entre duas coordenadas gps podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">de Scripts do Tipo Móvel,</a> da autoria de Peter Lapisu. Neste Javascript, a função `toRad()` é apenas *lat_or_lon*pi/180, que converte graus em radians. Aqui, *lat_or_lon* é a latitude ou longitude. Uma vez que a Hive não fornece a função `atan2`, mas fornece a função `atan`, a função `atan2` é implementada por `atan` função na consulta da Colmeia acima utilizando a definição fornecida na <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia.</a>

![Criar área de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de UDFs incorporados pela Colmeia pode ser encontrada na secção **funções incorporadas** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Wiki Apache Hive</a>).  

## <a name="tuning"></a>Tópicos avançados: Afinar parâmetros da Colmeia para melhorar a velocidade de consulta
As predefinições de parâmetro de cluster de Hive podem não ser adequadas para as consultas do Hive e os dados que as consultas estão a processar. Esta secção descreve alguns parâmetros que utilizadores podem sintonizar para melhorar o desempenho das consultas do Hive. Os utilizadores têm de adicionar o parâmetro ajustar consultas antes das consultas do processamento de dados.

1. **Espaço de pilha de Java**: Para consultas que envolvam a junção de grandes conjuntos de dados, ou o processamento de registos longos, ficar **sem espaço de pilha** é um dos erros comuns. Este erro pode ser evitado definindo parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* aos valores desejados. Segue-se um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Este parâmetro atribui a memória de 4-GB ao espaço heap de Java e também torna a classificação mais eficiente, alocando mais memória para ele. É uma boa idéia para brincar com essas alocações, se houver qualquer tarefa relacionados ao espaço de área dinâmica para dados de erros de falha.

1. **Tamanho do bloco DFS**: Este parâmetro define a menor unidade de dados que o sistema de ficheiros armazena. Por exemplo, se o tamanho do bloco DFS é 128 MB, em seguida, quaisquer dados e de tamanho menor e até 128 MB é armazenado num único bloco. Dados que é maiores do que 128 MB são alocados blocos extras. 
2. Escolher um tamanho de pequeno bloco faz com que grandes sobrecargas no Hadoop, uma vez que o nó de nome tem de processar mais pedidos para localizar o bloco relevante relativas ao ficheiro. Uma configuração recomendada quando lidando com gigabytes (ou superior) os dados são:

        set dfs.block.size=128m;

2. **Otimizar a operação de adesão na Hive**: Ao juntar operações no quadro de mapa/redução normalmente ocorrem na fase de redução, por vezes, enormes ganhos podem ser alcançados através do agendamento de juntas na fase do mapa (também chamados de "mapjoins"). Definir esta opção:
   
       set hive.auto.convert.join=true;

3. **Especificando o número de mappers para**a Hive : Enquanto hadoop permite ao utilizador definir o número de redutores, o número de mappers normalmente não é definido pelo utilizador. Um truque que permite algum grau de controlo neste número é escolher as variáveis Hadoop *mapred.min.split.size* e *mapred.max.split.size* como o tamanho de cada tarefa do mapa é determinado por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalmente, o valor predefinido de:
    
   - *mapred.min.split.size* é 0, o de
   - *mapred.max.split.size* é **Long.MAX** e o de 
   - *dfs.block.size* é de 64 MB.

     Como podemos ver, dado o tamanho de dados, ajuste estes parâmetros por "configuração"-los nos permite ajustar o número de mapeadores utilizado.

4. Aqui estão algumas outras opções mais **avançadas** para otimizar o desempenho da Colmeia. Estas opções permitem definir a memória atribuída para mapear e reduzir tarefas, e podem ser úteis para ajustar o desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior do que o tamanho da memória física de cada nó de trabalhador no cluster Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

