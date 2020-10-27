---
title: Analise & processo JSON com Apache Hive - Azure HDInsight
description: Aprenda a usar documentos JSON e analise-os utilizando a Hive Apache em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 9a7d3992ecd2c74947eaa1071b97b2032000c749
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547610"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar documentos JSON utilizando a Colmeia Apache em Azure HDInsight

Aprenda a processar e analisar ficheiros JavaScript Object Notation (JSON) utilizando a Hive Apache em Azure HDInsight. Este artigo utiliza o seguinte documento JSON:

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

O ficheiro pode ser encontrado em `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Para obter mais informações sobre como utilizar o armazenamento Azure Blob com HDInsight, consulte [use o armazenamento Azure Blob compatível com HDFS com Apache Hadoop em HDInsight](../hdinsight-hadoop-use-blob-storage.md). Pode copiar o ficheiro para o recipiente predefinido do seu cluster.

Neste artigo, você usa a consola Apache Hive. Para obter instruções sobre como abrir a consola Hive, consulte [Use Apache Ambari Hive View com Apache Hadoop em HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> A Hive View já não está disponível em HDInsight 4.0.

## <a name="flatten-json-documents"></a>Documentos de Flatten JSON

Os métodos enumerados na secção seguinte exigem que o documento JSON seja composto por uma única linha. Então, tens de achatar o documento do JSON numa corda. Se o seu documento JSON já estiver achatado, pode saltar este passo e ir diretamente para a secção seguinte sobre a análise dos dados do JSON. Para aplainar o documento JSON, execute o seguinte script:

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

O ficheiro JSON cru está localizado em `wasb://processjson@hditutorialdata.blob.core.windows.net/` . A mesa **StudentsRaw** Hive aponta para o documento JSON cru que não é achatado.

A tabela **StudentsOneLine** Hive armazena os dados no sistema de ficheiros predefinidos HDInsight sob o caminho **/json/students/.**

A declaração **INSERT** povoa a tabela **StudentOneLine** com os dados JSON achatados.

A declaração **SELECT** apenas devolve uma linha.

Aqui está a saída da declaração **SELECT:**

![HDInsight achatando o documento JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON na Colmeia

A Hive fornece três mecanismos diferentes para executar consultas em documentos JSON, ou pode escrever o seu próprio:

* Utilize a função definida get_json_object pelo utilizador (UDF).
* Usa o json_tuple UDF.
* Utilize o Serializer/Deserializador personalizado (SerDe).
* Escreva o seu próprio UDF usando Python ou outras línguas. Para obter mais informações sobre como executar o seu próprio código Python com Hive, consulte [Python UDF com Apache Hive e Apache Pig.](./python-udf-hdinsight.md)

### <a name="use-the-get_json_object-udf"></a>Use o get_json_object UDF

A Hive fornece um UDF incorporado chamado [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que consulta json durante o tempo de funcionação. Este método requer dois argumentos: o nome da mesa e o nome do método. O nome do método tem o documento JSON achatado e o campo JSON que precisa de ser analisado. Vamos ver um exemplo para ver como funciona este UDF.

A seguinte consulta devolve o primeiro nome e o apelido para cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Aqui está a saída quando executar esta consulta na janela da consola:

![Apache Hive recebe json objeto UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Existem limitações da get_json_object UDF:

* Como cada campo na consulta requer a reparas da consulta, afeta o desempenho.
* **OBTER \_ JSON_OBJECT()** devolve a representação de cordas de uma matriz. Para converter esta matriz numa matriz de Colmeia, tem de usar expressões regulares para substituir os suportes quadrados "[" e "]", e depois também tem de ligar para obter a matriz.

Esta conversão é a razão pela qual a Colmeia wiki recomenda que utilize **json_tuple** .  

### <a name="use-the-json_tuple-udf"></a>Use o json_tuple UDF

Outro UDF fornecido pela Hive é chamado [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que faz melhor do que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método requer um conjunto de chaves e uma corda JSON. Em seguida, devolve uma túlple de valores. A seguinte consulta devolve o ID do aluno e a nota do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída deste script na consola Hive:

![Apache Hive json resultados de consulta](./media/using-json-in-hive/hdinsight-json-tuple.png)

O `json_tuple` UDF utiliza a sintaxe [de visão lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) na Colmeia, que permite que o tuple json crie uma tabela virtual \_ aplicando a função UDT a cada linha da tabela original. Os JSONs complexos tornam-se demasiado desajeitados devido à utilização repetida da **VISTA LATERAL** . Além disso, **JSON_TUPLE** não consegue lidar com JSONs aninhados.

### <a name="use-a-custom-serde"></a>Use um SerDe personalizado

SerDe é a melhor escolha para analisar documentos JSON aninhados. Permite definir o esquema JSON, e depois pode usar o esquema para analisar os documentos. Para obter instruções, consulte [Como utilizar um SerDe JSON personalizado com o Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo

O tipo de operador JSON na Colmeia que escolher depende do seu cenário. Com um documento JSON simples e um campo para olhar para cima, escolha o **get_json_object** hive UDF . Se tiver mais do que uma chave para olhar para cima, então pode **usá json_tuple** . Para documentos aninhados, utilize o **JSON SerDe** .

## <a name="next-steps"></a>Passos seguintes

Para artigos relacionados, consulte:

* [Use a Colmeia Apache e a HiveQL com Apache Hadoop em HDInsight para analisar uma amostra do ficheiro Apache log4j](./hdinsight-use-hive.md)
* [Analise os dados do atraso de voo utilizando a Consulta Interativa em HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)