---
title: Analisar & processo JSON com Apache Hive-Azure HDInsight
description: Saiba como usar documentos JSON e analisá-los usando Apache Hive no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73097990"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar documentos JSON usando Apache Hive no Azure HDInsight

Saiba como processar e analisar arquivos de JavaScript Object Notation (JSON) usando Apache Hive no Azure HDInsight. Este artigo usa o seguinte documento JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

O arquivo pode ser encontrado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Para obter mais informações sobre como usar o armazenamento de BLOBs do Azure com o HDInsight, consulte [usar o armazenamento de BLOBs do Azure compatível com HDFS com o Apache Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o contêiner padrão do seu cluster.

Neste artigo, você usa o console do Apache Hive. Para obter instruções sobre como abrir o console do hive, consulte [usar o modo de exibição do Apache Ambari Hive com o Apache Hadoop no HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Mesclar documentos JSON

Os métodos listados na próxima seção exigem que o documento JSON seja composto por uma única linha. Portanto, você deve mesclar o documento JSON com uma cadeia de caracteres. Se o documento JSON já estiver nivelado, você poderá ignorar esta etapa e ir direto para a próxima seção sobre como analisar dados JSON. Para mesclar o documento JSON, execute o seguinte script:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

O arquivo JSON bruto está localizado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. A tabela Hive **StudentsRaw** aponta para o documento JSON bruto que não é achatado.

A tabela hive do **StudentsOneLine** armazena os dados no sistema de arquivos padrão do HDInsight no caminho **/JSON/Students/** .

A instrução **Insert** popula a tabela **StudentOneLine** com os dados JSON mesclados.

A instrução **Select** retorna apenas uma linha.

Aqui está a saída da instrução **Select** :

![O HDInsight mescla o documento JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no hive

O hive fornece três mecanismos diferentes para executar consultas em documentos JSON, ou você pode escrever o seu próprio:

* Use a UDF (função definida pelo usuário) get_json_object.
* Use o UDF json_tuple.
* Use o serializador/desserializador personalizado (SerDe).
* Escreva seu próprio UDF usando Python ou outras linguagens. Para saber mais sobre como executar seu próprio código Python com o Hive, confira [UDF do Python com Apache Hive e Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Usar o UDF get_json_object

O hive fornece um UDF interno chamado [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode executar consultas JSON durante o tempo de execução. Esse método usa dois argumentos: o nome da tabela e o nome do método, que tem o documento JSON nivelado e o campo JSON que precisa ser analisado. Vejamos um exemplo para ver como esse UDF funciona.

A consulta a seguir retorna o nome e o sobrenome de cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Aqui está a saída quando você executa essa consulta na janela do console:

![Apache Hive obter UDF do objeto JSON](./media/using-json-in-hive/hdinsight-get-json-object.png)

Há limitações do UDF get_json_object:

* Como cada campo na consulta requer nova análise da consulta, ele afeta o desempenho.
* **GET\_JSON_OBJECT ()** retorna a representação de cadeia de caracteres de uma matriz. Para converter essa matriz em uma matriz do hive, você precisa usar expressões regulares para substituir os colchetes "[" e "]" e, em seguida, você também precisa chamar Split para obter a matriz.

É por isso que o wiki do hive recomenda que você use o **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Usar o UDF json_tuple

Outro UDF fornecido pelo hive é chamado de [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que tem um desempenho melhor do que o [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Esse método usa um conjunto de chaves e uma cadeia de caracteres JSON e retorna uma tupla de valores usando uma função. A consulta a seguir retorna a ID de aluno e a classificação do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída desse script no console do hive:

![Apache Hive resultados da consulta JSON](./media/using-json-in-hive/hdinsight-json-tuple.png)

O UDF json_tuple usa a sintaxe de [exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) no hive, que permite que o JSON\_tupla crie uma tabela virtual aplicando a função UDT a cada linha da tabela original. JSONs complexos se tornam muito difíceis devido ao uso repetido da **exibição lateral**. Além disso, **JSON_TUPLE** não pode manipular jsons aninhados.

### <a name="use-a-custom-serde"></a>Usar um SerDe personalizado

SerDe é a melhor opção para analisar documentos JSON aninhados. Ele permite que você defina o esquema JSON e, em seguida, você pode usar o esquema para analisar os documentos. Para obter instruções, consulte [como usar um SERDE JSON personalizado com Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo

Concluindo, o tipo de operador JSON no hive que você escolher dependerá do seu cenário. Se você tiver um documento JSON simples e tiver apenas um campo para pesquisar, poderá optar por usar o UDF **get_json_object**do hive. Se você tiver mais de uma chave para pesquisar, poderá usar **json_tuple**. Se você tiver um documento aninhado, deverá usar o **SerDe JSON**.

## <a name="next-steps"></a>Passos Seguintes

Para artigos relacionados, consulte:

* [Usar Apache Hive e HiveQL com Apache Hadoop no HDInsight para analisar um arquivo Log4J do Apache de exemplo](../hdinsight-use-hive.md)
* [Analisar dados de atraso de voo usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analisar dados do Twitter usando o Apache Hive no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
