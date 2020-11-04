---
title: Dados da amostra em tabelas de colmeia Azure HDInsight - Processo de Ciência de Dados de Equipa
description: Dados de amostras de down-sample armazenados em tabelas de Colmeia Azure HDInsight usando consultas de Hive para reduzir os dados para um tamanho mais manejável para análise.
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
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321883"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo em tabelas do Hive do HDInsight
Este artigo descreve como reduzir os dados da amostra armazenados em tabelas de Colmeia Azure HDInsight usando consultas de Hive para reduzi-lo a um tamanho mais manejável para análise. Abrange três métodos de amostragem popularmente utilizados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem estratificada

**Por que provar os seus dados?**
Se o conjunto de dados que pretende analisar é grande, é geralmente uma boa ideia reduzir a amostragem dos dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A recolha de amostras facilita a compreensão, exploração e engenharia de recursos. O seu papel no Processo de Ciência de Dados de Equipa é permitir uma rápida prototipagem das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de Dados de [Equipa (TDSP)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Como submeter consultas de Colmeia
As consultas de colmeia podem ser submetidas a partir da consola Hadoop Command-Line no nó de cabeça do cluster Hadoop.  Inicie o nó de cabeça do cluster Hadoop, abra a consola Hadoop Command-Line e submeta as consultas da Colmeia a partir daí. Para obter instruções sobre a submissão de consultas de Colmeia na consola Hadoop Command-Line, consulte [Como Submeter consultas de colmeia](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Amostragem aleatória uniforme
Uma amostragem aleatória uniforme significa que cada linha do conjunto de dados tem igual possibilidade de ser amostrada. Pode ser implementado adicionando um rand de campo extra() ao conjunto de dados na consulta interna "selecione" e na consulta externa "selecione" essa condição nesse campo aleatório.

Segue-se uma consulta de exemplo:

```python
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
```

Aqui, `<sample rate, 0-1>` especifica a proporção de registos que os utilizadores querem recolher.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Amostragem aleatória por grupos
Ao recolher amostras de dados categóricos, pode querer incluir ou excluir todos os casos para algum valor da variável categórica. Este tipo de amostragem chama-se "amostragem por grupo". Por exemplo, se tiver uma variável categórica " *Estado* ", que tem valores como NY, MA, CA, NJ e PA, quer que os registos de cada estado estejam juntos, sejam eles amostrados ou não.

Aqui está uma consulta de exemplo que as amostras por grupo:

```python
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
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Amostragem estratificada
A amostragem aleatória é estratificada em relação a uma variável categórica quando as amostras obtidas têm valores categóricos presentes na mesma proporção que estavam na população-mãe. Usando o mesmo exemplo acima, suponha que os seus dados têm as seguintes observações por estados: NJ tem 100 observações, NY tem 60 observações, e WA tem 300 observações. Se especificar a taxa de amostragem estratificada para ser de 0,5, então a amostra obtida deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respectivamente.

Segue-se uma consulta de exemplo:

```hiveql
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
```

Para obter informações sobre métodos de amostragem mais avançados disponíveis na Colmeia, consulte [a Amostragem LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).