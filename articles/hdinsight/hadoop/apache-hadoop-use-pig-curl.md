---
title: Utilizar o Apache Hadoop Pig com o REST no HDInsight - Azure
description: Saiba como utilizar o REST para executar tarefas do Pig Latin num cluster do Apache Hadoop no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 52ad40a2521f21efee3b9f98b46c2e2e6343b656
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098309"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Executar tarefas do Apache Pig com o Apache Hadoop no HDInsight com REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como executar tarefas do Apache Pig Latin fazendo pedidos REST para um cluster do HDInsight do Azure. Curl é utilizado para demonstrar como pode interagir com o HDInsight com a API de REST de WebHCat.

> [!NOTE]  
> Se já estiver familiarizado com a utilização de servidores do Apache Hadoop baseado em Linux, mas se estiver familiarizado com o HDInsight, consulte [Dicas do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou no Windows)

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Executar tarefas do Apache Pig com o Curl


> [!NOTE]
> A API REST está protegida por [autenticação de acesso básico](https://en.wikipedia.org/wiki/Basic_access_authentication). Sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para se certificar de que as suas credenciais são enviadas de forma segura para o servidor.
>
> Quando utilizar os comandos nesta secção, substitua `USERNAME` com o utilizador autenticar para o cluster e substitua `PASSWORD` com a palavra-passe da conta de utilizador. Substitua `CLUSTERNAME` pelo nome do cluster.
>


1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber a resposta JSON seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: O nome de utilizador e palavra-passe utilizada para autenticar o pedido
   * **-G**: Indica que este pedido é uma solicitação GET

     O início da URL; **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos. O caminho **/status**, indica que o pedido deve retornar o status de WebHCat (também conhecido como Templeton) para o servidor.

2. Utilize o seguinte código para submeter uma tarefa de Pig Latin para o cluster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: Uma vez que `-G` não for utilizado, o pedido é predefinido para o método POST. `-d` Especifica os valores de dados que são enviados com o pedido.

   * **user.name**: O utilizador que está a executar o comando
   * **executar**: As instruções em Pig Latin para executar
   * **statusdir**: O diretório que o estado para esta tarefa é escrito

     > [!NOTE]  
     > Tenha em atenção que os espaços de instruções em Pig Latin são substituídos pelo `+` caráter quando utilizado com o Curl.

     Este comando deverá devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa, por exemplo:

       {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Substitua `JOBID` com o valor devolvido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, `JOBID` é `job_1415651640909_0026`.

    Se a tarefa foi concluída, o estado é **bem-sucedido**.

    > [!NOTE]  
    > Este pedido Curl retorna um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa e jq é usada para recuperar apenas o valor de estado.

## <a id="results"></a>Ver os resultados

Quando o estado da tarefa foi alterado para **bem-sucedido**, pode recuperar os resultados da tarefa. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; nesse caso, `/example/pigcurl`.

HDInsight pode utilizar o armazenamento do Azure ou o armazenamento do Azure Data Lake, como o arquivo de dados padrão. Existem várias formas de chegar aos dados, dependendo de qual delas utilizar. Para obter mais informações, consulte a secção de armazenamento do [informações do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage) documento.

## <a id="summary"></a>Resumo

Como demonstrado neste documento, pode utilizar um pedido HTTP não processado para executar, monitorizar e ver os resultados de tarefas do Pig no seu cluster do HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte a [referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Pig no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
