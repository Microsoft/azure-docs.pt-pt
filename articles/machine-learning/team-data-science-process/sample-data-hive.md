---
title: Dados da amostra em tabelas da Hive Azure HDInsight - Processo de Ciência de Dados da Equipa
description: Dados de amostra saem armazenados em tabelas de Colmeia SionHD, utilizando consultas da Hive para reduzir os dados a um tamanho mais manejável para análise.
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
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76719998"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo em tabelas do Hive do HDInsight
Este artigo descreve como reduzir os dados armazenados em tabelas de colmeiaS Azure HDInsight usando consultas da Hive para reduzi-lo a um tamanho mais manejável para análise. Abrange três métodos de amostragem popularmente utilizados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem estratificada

**Por que provar os seus dados?**
Se o conjunto de dados que planeia analisar é grande, normalmente é uma boa ideia recolher os dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A amostragem facilita a compreensão de dados, a exploração e a engenharia de recursos. O seu papel no Processo de Ciência de Dados da Equipa é permitir a prototipagem rápida das funções de processamento de dados e dos modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Como submeter consultas da Hive
As consultas de colmeia podem ser submetidas a partir da consola hadoop Command-Line no nó de cabeça do cluster Hadoop.  Entre no nó da cabeça do cluster Hadoop, abra a consola hadoop Command-Line e submeta as consultas da Colmeia a partir daí. Para obter instruções sobre a submissão de consultas de Hive na consola de Linha de Comando Hadoop, consulte [Como Submeter Consultas](move-hive-tables.md#submit)de Colmeia .

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Amostragem aleatória uniforme
Uma amostragem aleatória uniforme significa que cada linha do conjunto de dados tem a mesma chance de ser amostrada. Pode ser implementado adicionando um rand de campo extra ao conjunto de dados na consulta interior "select" e na consulta exterior "select" essa condição nesse campo aleatório.

Segue-se uma consulta de exemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` especifica a proporção de registos que os utilizadores querem provar.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Amostragem aleatória por grupos
Ao recolher dados categóricos, pode querer incluir ou excluir todas as instâncias para algum valor da variável categórica. Este tipo de amostragem chama-se "amostragem por grupo". Por exemplo, se tiver uma variável categórica "*Estado*", que tem valores como NY, MA, CA, NJ e PA, quer registos de cada estado para estarem juntos, sejam eles amostrados ou não.

Aqui está um exemplo de consulta que amostras por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified-sampling"></a><a name="stratified"></a>Amostragem estratificada
A amostragem aleatória é estratificada em relação a uma variável categórica quando as amostras obtidas têm valores categóricos que estão presentes na mesma proporção que estavam na população-mãe. Usando o mesmo exemplo que acima, suponha que os seus dados têm as seguintes observações por estados: NJ tem 100 observações, NY tem 60 observações, e WA tem 300 observações. Se especificar a taxa de amostragem estratificada em 0,5, então a amostra obtida deverá ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respectivamente.

Segue-se uma consulta de exemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre métodos de amostragem mais avançados disponíveis na Colmeia, consulte A [Amostragem Manual de Idiomas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

