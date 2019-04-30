---
title: Utilizar DataFu do Apache com Apache Pig no HDInsight - Azure
description: Apache DataFu Pig é uma coleção de bibliotecas para utilização com o Apache Pig no Apache Hadoop. Saiba como pode utilizar DataFu com Pig no seu cluster do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: 59065c1eefedc2dce9eb56394c4c7db862744366
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110836"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>Utilizar DataFu de Apache Pig com o Apache Pig no HDInsight

Saiba como utilizar o Apache DataFu Pig com o HDInsight.

Apache DataFu Pig é uma coleção de bibliotecas de código-fonte aberto para utilização com o Apache Pig no Apache Hadoop.
Para obter mais informações sobre o DataFu Pig, consulte [ https://datafu.apache.org/ ](https://datafu.apache.org/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

* Um cluster do HDInsight do Azure (Linux ou Windows, com base)

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Uma familiaridade básica com [com Apache Pig no HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Instalar DataFu no HDInsight baseado em Linux

> [!IMPORTANT]  
> DataFu é instalado na versão de clusters baseados em Linux 3.3 e superior e em clusters baseados em Windows. Não é instalada em clusters baseados em Linux em anteriores ao 3.3.
>
> Se estiver a utilizar um cluster baseado em Windows ou um cluster baseado em Linux superior à versão 3.3, ignore esta secção.

DataFu pode ser baixado e instalado a partir do repositório Maven. Utilize os seguintes passos para encontrar a versão que precisa e adicioná-lo ao seu cluster do HDInsight:

> [!WARNING]  
> Versões de DataFu podem ter requisitos que não seja atendidos pelo HDInsight. Por exemplo, se utilizar uma versão antiga do DataFu, podem exigir uma versão diferente do Pig que o que é incluído no HDInsight.

### <a name="find-a-version"></a>Encontrar uma versão

1. No seu browser, navegue para https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig e encontrar a versão que precisa.

2. Selecione o número de versão ligado.

3. Selecione __ver todos os__ para ver todos os ficheiros.

4. Na lista de ficheiros, localize o ficheiro. JAR. Normalmente, este ficheiro é o maior listado, pois inclui todas as dependências. Faça duplo clique na ligação e copie o endereço de link.

### <a name="download-datafu-to-hdinsight"></a>Transferir DataFu para HDInsight

1. Ligar ao cluster do HDInsight baseado em Linux através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para transferir o ficheiro. jar DataFu usando o utilitário de wget:

    > [!IMPORTANT]  
    > Substitua o URL que copiou anteriormente o link no comando.

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. Em seguida, carregue o ficheiro para o armazenamento padrão para o seu cluster do HDInsight. Colocar o arquivo em default armazenamento torna-o disponível para todos os nós do cluster.

    > [!IMPORTANT]  
    > Substitua o número de versão no nome do ficheiro com a versão que transferiu.

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > O comando anterior armazena jar no `/example/jars` , uma vez que este diretório já existe no armazenamento de cluster. Pode usar qualquer local que desejar no armazenamento de cluster do HDInsight.

## <a name="use-datafu-with-pig"></a>Utilizar DataFu com Pig

Os passos nesta secção partem do princípio de que está familiarizado com a utilizar o Pig no HDInsight. Para obter mais informações sobre como utilizar o Pig com o HDInsight, consulte [utilizar o Pig com o HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]  
> Se instalou manualmente DataFu utilizando os passos na secção anterior, tem de registá-lo antes de o utilizar.
>
> * Se o cluster utiliza armazenamento do Azure, utilize um `wasb://` caminho. Por exemplo, `register wasb:///example/jars/datafu-pig-1.4.0.jar`.
>
> * Se o cluster utiliza a geração 2 do Azure Data Lake Store, utilize um `abfs://` caminho. Por exemplo, `register abfs://home/example/jars/datafu-pig-1.4.0.jar`.
>
> * Se o cluster utiliza o Azure Data Lake Store Gen1, utilize um `adl://` caminho. Por exemplo, `register adl://home/example/jars/datafu-pig-1.4.0.jar`.

Muitas vezes, é possível definir um alias para funções de DataFu. O exemplo seguinte define um alias de `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Em seguida, pode utilizar este alias num script Pig Latin para gerar um valor de hash para os dados de entrada. Por exemplo, o código a seguir substitui a localização dos dados de entrada com um valor de hash:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Ele gera o seguinte resultado:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre DataFu ou Pig, consulte os seguintes documentos:

* [Apache Pig de DataFu introdução](https://datafu.apache.org/docs/datafu/getting-started.html).
* [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
