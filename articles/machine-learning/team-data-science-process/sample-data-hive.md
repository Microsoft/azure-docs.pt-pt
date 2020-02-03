---
title: Dados de exemplo nas tabelas Hive do Azure HDInsight - Team Data Science Process
description: Dimensionar os dados armazenados em tabelas de ramo de registo do Azure HDInsight com consultas do Hive para reduzir os dados para um tamanho mais gerenciável para análise.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719998"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo em tabelas do Hive do HDInsight
Este artigo descreve como dimensionar os dados armazenados em tabelas de ramo de registo do Azure HDInsight com consultas do Hive para reduzi-lo para um tamanho mais gerenciável para análise. Abrange três métodos de amostragem popularmente utilizados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem stratified

**Por que provar os seus dados?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa idéia para dimensionar os dados para reduzi-lo para um tamanho mais pequeno, mas representativo e mais gerenciável. Amostragem de baixo facilita a compreensão de dados, a exploração e a engenharia de funcionalidades. O processo de ciência de dados de equipa de sua função é ativar a criação de protótipos rápida das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Como submeter consultas do Hive
Consultas do Hive podem ser submetidas a partir da consola da linha de comandos do Hadoop no nó principal do cluster de Hadoop.  Entre no nó da cabeça do cluster Hadoop, abra a consola hadoop Command-Line e submeta as consultas da Colmeia a partir daí. Para obter instruções sobre a submissão de consultas de Hive na consola de Linha de Comando Hadoop, consulte [Como Submeter Consultas](move-hive-tables.md#submit)de Colmeia .

## <a name="uniform"></a>Amostragem aleatória uniforme
Amostragem aleatória uniforme significa que cada linha no conjunto de dados tem uma oportunidade igual de ser divididos em amostras. Ele pode ser implementado ao adicionar um rand() campo extra para o conjunto de dados na consulta "selecionar" interna e na consulta "selecionar" externa essa condição sobre esse campo aleatório.

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

## <a name="group"></a>Amostragem aleatória por grupos
Quando recolha de dados categóricos, pode querer incluir ou excluir todas as instâncias para um valor da variável categórica. Esse tipo de amostragem é chamado de "amostragem pelo grupo". Por exemplo, se tiver uma variável categórica "*Estado*", que tem valores como NY, MA, CA, NJ e PA, quer registos de cada estado para estarem juntos, sejam eles amostrados ou não.

Aqui está um exemplo de consulta que exemplos por grupo:

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

## <a name="stratified"></a>Amostragem estratificada
Amostragem aleatória é stratified em relação a uma variável categórica, quando os exemplos de obteve tem valores categóricos que estão presentes na mesma proporção como estavam na população principal. Seguindo o exemplo anterior, conforme apresentado acima, vamos supor que seus dados têm as seguintes observações por Estados: NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se especificar a taxa de amostragem stratified ser 0,5, em seguida, o exemplo de obteve deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respetivamente.

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

