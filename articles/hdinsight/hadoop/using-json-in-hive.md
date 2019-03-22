---
title: Analisar e processar documentos JSON com o Apache Hive - Azure HDInsight
description: Saiba como utilizar documentos JSON e analisá-los utilizando o Apache Hive no HDInsight do Azure
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: hrasheed
ms.openlocfilehash: 89902da9668d32c28e5cc88b7e4468c0dbd0b861
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335917"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar documentos JSON com o Apache Hive no HDInsight do Azure

Saiba como processar e analisar ficheiros JavaScript Object Notation (JSON) utilizando o Apache Hive no HDInsight do Azure. Este tutorial utiliza o documento JSON seguinte:

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

O ficheiro pode ser encontrado em **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. Para obter mais informações sobre como utilizar o armazenamento de Blobs do Azure com o HDInsight, consulte [compatível com o uso HDFS Blob storage do Azure com o Apache Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Pode copiar o ficheiro para o contentor predefinido do cluster.

Neste tutorial, vai utilizar a consola Apache Hive. Para obter instruções sobre como abrir a consola do Hive, consulte [utilização Apache Ambari Hive View com o Apache Hadoop no HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Nivelamento de documentos JSON
Os métodos listados na secção seguinte requerem que o documento JSON ser composto por uma única linha. Por isso, tem de nivelamento o documento JSON para uma cadeia de caracteres. Se o documento JSON já foi simplificado, pode ignorar este passo e ir diretamente para a próxima seção sobre análise de dados JSON. Aplaná o documento JSON, execute o seguinte script:

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

O ficheiro JSON não processado está localizado em **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. O **StudentsRaw** Hive pontos de tabela para o documento JSON não processado que não foi simplificado.

O **StudentsOneLine** tabela do Hive armazena os dados no sistema de ficheiros predefinido HDInsight sob a **/json/estudantes/** caminho.

O **inserir** instrução preenche a **StudentOneLine** tabela com os dados JSON simplificadas.

O **SELECIONE** instrução devolve apenas uma linha.

Eis a saída do **SELECIONE** instrução:

![Mesclar o documento JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no Hive
Hive fornece três mecanismos diferentes para executar consultas nos documentos JSON ou pode escrever seu próprio:

* Use a função no get_json_object definidas pelo utilizador (UDF).
* Utilize o json_tuple UDF.
* Utilize o serializador/desserializador personalizado (SerDe).
* Escreva seu próprio UDF com o Python ou outras linguagens. Para obter mais informações sobre como executar seu próprio código de Python com o Hive, consulte [UDF Python com o Apache Hive e Apache Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Utilizar o get_json_object UDF
Ramo de registo fornece uma UDF interna chamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode realizar uma consulta de JSON durante o tempo de execução. Este método aceita dois argumentos – o nome da tabela e o nome de método, que tem o documento JSON simplificado e o campo JSON que tem de ser analisado. Vamos examinar um exemplo para ver como funciona este UDF.

A seguinte consulta devolve o nome próprio e apelido para cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Eis a saída quando executar esta consulta na janela da consola:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existem limitações do get_json_object UDF:

* Uma vez que cada campo na consulta requer reparsing da consulta, afeta o desempenho.
* **Obtenha\_JSON_OBJECT()** devolve a representação de cadeia de caracteres de uma matriz. Para converter essa matriz para uma matriz de Hive, precisa usar expressões regulares para substituir os colchetes "[" e "]", e, em seguida, também precisa chamar split para obter a matriz.

É por isso o wiki do Hive recomenda o uso de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utilizar o json_tuple UDF
Denomina-se outro UDF fornecida do Hive [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que executa melhor do que [get _ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Esse método usa um conjunto de chaves e uma cadeia de caracteres do JSON e retorna uma cadeia de identificação de valores, utilizando uma função. A seguinte consulta devolve o ID de estudante e o nível do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída deste script na consola do Hive:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple UDF utiliza a [lateral exibição](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) sintaxe no Hive, que permite que o json\_tupla para criar uma tabela virtual aplicando a função UDT para cada linha da tabela original. JSONs complexas, tornar-se muito difícil, devido à utilização repetida de **LATERAL do vista**. Além disso, **JSON_TUPLE** não consegue processar JSONs aninhados.

### <a name="use-a-custom-serde"></a>Utilizar um SerDe personalizado
SerDe é a melhor opção para aninhados de documentos JSON de análise. Permite-lhe definir o esquema JSON e, em seguida, pode utilizar o esquema para analisar os documentos. Para obter instruções, consulte [como utilizar um SerDe de JSON personalizado com o Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo
Concluindo, o tipo de operador JSON no Hive que escolher depende do seu cenário. Se tem um documento JSON simple e se tiver apenas um campo para pesquisar no, pode optar por utilizar o get_json_object UDF do Hive. Se tiver mais de uma chave para pesquisar no, em seguida, pode utilizar json_tuple. Se tiver um documento aninhado, em seguida, deve usar o SerDe de JSON.

## <a name="next-steps"></a>Passos Seguintes

Para artigos relacionados, consulte:

* [Utilizar o Apache Hive e o HiveQL com o Apache Hadoop no HDInsight para analisar um ficheiro do Apache log4j de exemplo](../hdinsight-use-hive.md)
* [Analisar dados de atraso de voo com o Apache Hive no HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)
* [Analisar dados do Twitter com o Apache Hive no HDInsight](../hdinsight-analyze-twitter-data-linux.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

