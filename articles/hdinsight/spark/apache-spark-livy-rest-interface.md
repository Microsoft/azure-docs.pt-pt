---
title: Use Livy Spark para submeter empregos ao cluster Spark no Azure HDInsight
description: Aprenda a usar a Apache Spark REST API para submeter os trabalhos de Spark remotamente a um cluster Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206287"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Utilizar a API REST do Apache Spark para submeter trabalhos remotos para um cluster do HDInsight Spark

Aprenda a usar [Apache Livy](https://livy.incubator.apache.org/), o Apache Spark REST API, que é usado para submeter trabalhos remotos a um cluster Azure HDInsight Spark. Para obter documentação detalhada, consulte [Apache Livy.](https://livy.incubator.apache.org/docs/latest/rest-api.html)

Você pode usar Livy para executar conchas de faísca interativaou submeter trabalhos de lote para ser executado em Spark. Este artigo fala em usar a Livy para apresentar trabalhos de lote. Os cortes neste artigo usam cURL para fazer chamadas REST API para o ponto final de Livy Spark.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Submeta um lote apache Livy Spark

Antes de submeter uma função de lote, deve carregar o jarro de aplicação no armazenamento do cluster associado ao cluster. Para o fazer, pode utilizar [AzCopy](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos. Existem vários outros clientes que pode usar para fazer o upload de dados. Pode descobrir mais sobre eles em [dados de Upload para trabalhos Apache Hadoop em HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exemplos

* Se o ficheiro do frasco estiver no armazenamento do cluster (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Se quiser passar o nome do frasco e o nome de classe como parte de um ficheiro de entrada (neste exemplo, input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obtenha informações sobre lotes de Livy Spark em execução no cluster

Sintaxe:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exemplos

* Se quiser recuperar todos os lotes da Livy Spark em execução no cluster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Se quiser recuperar um lote específico com um determinado iD de lote

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Eliminar um lote de Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exemplo

Apagar um lote com identificação `5`do lote .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark e alta disponibilidade

Livy fornece alta disponibilidade para empregos spark em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço Livy cair depois de ter apresentado um trabalho remotamente a um cluster spark, o trabalho continua a funcionar em segundo plano. Quando a Livy está de volta, restaura o estado do trabalho e reporta-o de volta.
* Os cadernos jupyter para HDInsight são alimentados pela Livy no backend. Se um caderno estiver a executar um trabalho de Spark e o serviço Livy for reiniciado, o caderno continua a executar as células de código.

## <a name="show-me-an-example"></a>Mostre-me um exemplo

Nesta secção, analisamos exemplos para usar a Livy Spark para apresentar trabalho de lote, monitorizar o progresso do trabalho e, em seguida, apagá-lo. A aplicação que usamos neste exemplo é a desenvolvida no artigo [Criar uma aplicação Scala autónoma e executar no cluster HDInsight Spark](apache-spark-create-standalone-application.md). Os passos aqui assumem:

* Já copiou o frasco de aplicação para a conta de armazenamento associada ao cluster.
* Instalou o CuRL no computador onde está a experimentar estes passos.

Execute os seguintes passos:

1. Para facilitar a utilização, detete as variáveis ambientais. Este exemplo baseia-se num ambiente Windows, revendo variáveis conforme necessário para o seu ambiente. `CLUSTERNAME`Substitua, `PASSWORD` e com os valores apropriados.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Verifique se livy Spark está correndo no aglomerado. Podemos fazê-lo obtendo uma lista de lotes de execução. Se estás a gerir um trabalho usando a Livy pela primeira vez, a saída deve devolver zero.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Deve obter uma saída semelhante ao seguinte corte:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Note como a última linha da saída diz **total:0,** o que sugere que não há lotes de funcionamento.

1. Vamos agora apresentar um trabalho de lote. O seguinte corte utiliza um ficheiro de entrada (input.txt) para passar o nome do jarro e o nome da classe como parâmetros. Se estiver a executar estes passos a partir de um computador Windows, usar um ficheiro de entrada é a abordagem recomendada.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Os parâmetros da **entrada do ficheiro.txt** são definidos da seguinte forma:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Note como a última linha da saída diz **estado:início**. Também diz, **id:0.** Aqui, **0** é a identificação do lote.

1. Agora pode recuperar o estado deste lote específico utilizando o ID do lote.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A produção mostra agora **o estado:sucesso**, o que sugere que o trabalho foi concluído com sucesso.

1. Se quiser, pode agora apagar o lote.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Deverá ver um resultado semelhante ao seguinte fragmento:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A última linha da saída mostra que o lote foi eliminado com sucesso. Apagar um emprego, enquanto está a funcionar, também mata o trabalho. Se eliminar um trabalho que tenha concluído, com sucesso ou não, elimina completamente a informação de trabalho.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Atualizações para a configuração da Livy a partir da versão HDInsight 3.5

Os clusters HDInsight 3.5 e acima, por padrão, desativam a utilização de ficheiros locais para aceder a ficheiros de dados de amostras ou frascos. Encorajamo-lo a `wasbs://` usar o caminho em vez de aceder a frascos ou a amostrar ficheiros de dados do cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Submeter empregos à Livy para um cluster dentro de uma rede virtual Azure

Se se ligar a um cluster HDInsight Spark a partir de uma Rede Virtual Azure, pode ligar-se diretamente à Livy no cluster. Neste caso, o URL para O `http://<IP address of the headnode>:8998/batches`ponto final da Livy é . Aqui, **8998** é o porto onde Livy corre no nó do aglomerado. Para obter mais informações sobre o acesso a serviços em portos não públicos, consulte [portas utilizadas pelos serviços Apache Hadoop no HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Passos seguintes

* [Documentação Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
