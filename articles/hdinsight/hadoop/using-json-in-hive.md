---
title: Analise & processo JSON com Apache Hive - Azure HDInsight
description: Aprenda a usar documentos JSON e analise-os utilizando a Apache Hive em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73097990"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processe e analise documentos jSON usando Apache Hive em Azure HDInsight

Saiba como processar e analisar ficheiros de Notação de Objetos JavaScript (JSON) utilizando a Hive Apache no Azure HDInsight. Este artigo utiliza o seguinte documento JSON:

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

O ficheiro pode `wasb://processjson@hditutorialdata.blob.core.windows.net/`ser encontrado em . Para obter mais informações sobre como utilizar o armazenamento Azure Blob com o HDInsight, consulte Use o [armazenamento Azure Blob compatível com HDFS com Apache Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Pode copiar o ficheiro para o recipiente predefinido do seu cluster.

Neste artigo, usas a consola Apache Hive. Para obter instruções sobre como abrir a consola [Hive, consulte Use Apache Ambari Hive View com Apache Hadoop em HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Achate documentos JSON

Os métodos enumerados na secção seguinte exigem que o documento JSON seja composto por uma única linha. Então, tens de aplainar o documento JSON numa corda. Se o seu documento JSON já estiver achatado, pode saltar este passo e ir diretamente para a secção seguinte na análise de dados da JSON. Para aplainar o documento JSON, execute o seguinte script:

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

O ficheiro JSON cru `wasb://processjson@hditutorialdata.blob.core.windows.net/`está localizado em . A tabela **StudentsRaw** Hive aponta para o documento json cru que não é achatado.

A tabela **StudentsOneLine** Hive armazena os dados no sistema de ficheiros padrão HDInsight sob o **caminho /json/estudantes/**

A declaração **INSERT** povoa a tabela **StudentOneLine** com os dados JSON achatados.

A declaração **SELECT** só devolve uma linha.

Aqui está a saída da declaração **SELECT:**

![HDInsight achatando o documento JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos da JSON na Colmeia

A Hive fornece três mecanismos diferentes para executar consultas em documentos JSON, ou pode escrever o seu próprio:

* Utilize a função get_json_object definida pelo utilizador (UDF).
* Use o json_tuple UDF.
* Utilize o Serializer/Deserializer personalizado (SerDe).
* Escreva o seu próprio UDF usando Python ou outras línguas. Para obter mais informações sobre como executar o seu próprio código Python com hive, consulte [Python UDF com Apache Hive e Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Use o get_json_object UDF

A Hive fornece uma UDF incorporada chamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode realizar consulta sons JSON durante o tempo de execução. Este método requer dois argumentos- o nome da tabela e o nome do método, que tem o documento JSON achatado e o campo JSON que precisa de ser analisado. Vejamos um exemplo para ver como funciona esta UDF.

A seguinte consulta devolve o primeiro nome e apelido para cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Aqui está a saída quando executa esta consulta na janela da consola:

![Apache Hive obter json objeto UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Existem limitações da Get_json_object UDF:

* Como cada campo na consulta requer reparso da consulta, afeta o desempenho.
* **GET\_JSON_OBJECT()** devolve a representação de cordas de uma matriz. Para converter esta matriz numa matriz da Colmeia, tem de usar expressões regulares para substituir os suportes quadrados "[" e "]", e depois também tem de chamar a divisão para obter a matriz.

É por isso que o wiki da Colmeia recomenda que use **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Use o json_tuple UDF

Outra UDF fornecida pela Hive chama-se [json_tuple,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)que tem um desempenho melhor do que [get_ _object json.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) Este método pega num conjunto de teclas e uma corda JSON, e devolve uma túnica de valores utilizando uma função. A seguinte consulta devolve a identificação do aluno e a nota do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída deste script na consola da Colmeia:

![Resultados da consulta de Apache Hive json](./media/using-json-in-hive/hdinsight-json-tuple.png)

A json_tuple UDF usa a sintaxe de [visão](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) lateral\_na Colmeia, o que permite que json tuple crie uma tabela virtual aplicando a função UDT em cada linha da tabela original. Os JSONs complexos tornam-se demasiado desajeitados devido à utilização repetida de **VISÃO LATERAL**. Além disso, **JSON_TUPLE** não consegue lidar com JSONs aninhados.

### <a name="use-a-custom-serde"></a>Use um SerDe personalizado

SerDe é a melhor escolha para analisar documentos da JSON aninhados. Permite-lhe definir o esquema JSON, e depois pode usar o esquema para analisar os documentos. Para obter instruções, consulte [Como utilizar um JSON SerDe personalizado com o Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo

Em conclusão, o tipo de operador JSON na Colmeia que escolher depende do seu cenário. Se tiver um simples documento JSON e tiver apenas um campo para olhar para cima, pode optar por utilizar a **Get_json_object**Da UDF da Colmeia . Se tiver mais do que uma chave para olhar para cima, então pode usar **json_tuple.** Se tem um documento aninhado, então deve utilizar o **JSON SerDe**.

## <a name="next-steps"></a>Passos seguintes

Para artigos relacionados, consulte:

* [Use Apache Hive e HiveQL com Apache Hadoop em HDInsight para analisar uma amostra de ficheiro Log4j Apache](../hdinsight-use-hive.md)
* [Analise os dados do atraso de voo utilizando a Consulta Interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analise os dados do Twitter usando a Hive Apache no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
