---
title: Utilizar o Apache Hadoop Hive com o Curl no HDInsight - Azure
description: Saiba como remotamente submeter as tarefas de Apache Pig para HDInsight com o Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 00fd697b42c7d93cb04392e91deea23133cf398a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407244"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Executar consultas do Apache Hive com o Apache Hadoop no HDInsight com REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como utilizar a API de REST do WebHCat para executar consultas do Apache Hive com o Apache Hadoop no cluster de HDInsight do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hadoop baseado em Linux em clusters do HDInsight versão 3.4 ou superior.

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente REST. Este documento usa o Windows PowerShell e [Curl](https://curl.haxx.se/) exemplos.

    > [!NOTE]  
    > O Azure PowerShell fornece cmdlets dedicados para trabalhar com o Hive no HDInsight. Para obter mais informações, consulte a [utilizar o Apache Hive com o Azure PowerShell](apache-hadoop-use-hive-powershell.md) documento.

Este documento também usa o Windows PowerShell e [Jq](https://stedolan.github.io/jq/) para processar os dados JSON retornados de pedidos REST.

## <a id="curl"></a>Executar uma consulta do Hive

> [!NOTE]  
> Quando utilizar cURL ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos ao fornecer o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight.
>
> A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que as suas credenciais são enviadas de forma segura para o servidor, sempre efetuar pedidos utilizando HTTP Secure (HTTPS).

1. Para definir o início de sessão do cluster que é usado pelos scripts neste documento, utilize um dos seguintes comandos:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Para definir o nome do cluster, utilize um dos seguintes comandos:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Para verificar se consegue ligar ao seu cluster do HDInsight, utilize um dos seguintes comandos:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Receber uma resposta semelhante ao seguinte texto:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * `-u` -O nome de utilizador e palavra-passe utilizada para autenticar o pedido.
    * `-G` -Indica que este pedido é uma operação de obtenção.

   O início do URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todos os pedidos. O caminho, `/status`, indica que o pedido deve retornar um status de WebHCat (também conhecido como Templeton) para o servidor. Também pode solicitar a versão do ramo de registo utilizando o seguinte comando:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Este pedido devolve uma resposta semelhante ao seguinte texto:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Utilize o seguinte para criar uma tabela chamada **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Esse pedido usa o método de mensagem, que envia dados como parte do pedido para a API REST. Os seguintes valores de dados são enviados com o pedido:

     * `user.name` -O utilizador que está a executar o comando.
     * `execute` -As declarações HiveQL para executar.
     * `statusdir` -O diretório que o estado para esta tarefa é escrito.

   Essas instruções executam as seguintes ações:
   
   * `DROP TABLE` -Se a tabela já existir, este é eliminado.
   * `CREATE EXTERNAL TABLE` -Cria uma nova tabela de "externa" no Hive. Tabelas externas armazenam apenas a definição de tabela no Hive. Os dados são deixados na localização original.

     > [!NOTE]  
     > Tabelas externas devem ser usadas quando espera que os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automatizada ou outra operação de MapReduce.
     >
     > Remover uma tabela externa faz **não** eliminar os dados, apenas a definição da tabela.

   * `ROW FORMAT` -Como os dados estiverem formatados. Os campos em cada registo são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION` -Onde os dados são armazenados (o diretório/dados de exemplo) e que são armazenados como texto.
   * `SELECT` -Seleciona uma contagem de todas as linhas em que coluna **t4** contém o valor **[erro]**. Esta declaração devolve um valor de **3** porque há três linhas que contêm este valor.

     > [!NOTE]  
     > Tenha em atenção que os espaços entre declarações HiveQL são substituídos pelo `+` caráter quando utilizado com o Curl. Valores com aspas simples que contém um espaço como, por exemplo, o delimitador, não devem ser substituídos por `+`.

      Este comando devolve uma ID da tarefa que pode ser utilizado para verificar o estado da tarefa.

5. Para verificar o estado da tarefa, utilize o seguinte comando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Se a tarefa foi concluída, o estado é **bem-sucedido**.

6. Assim que o estado da tarefa foi alterado para **bem-sucedido**, pode recuperar os resultados da tarefa de armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; nesse caso, `/example/rest`. Este endereço armazena a saída no `example/curl` diretório no armazenamento do padrão de clusters.

    Pode listar e transferir estes ficheiros ao utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como utilizar a CLI do Azure com armazenamento do Azure, consulte a [CLI do Azure de utilização com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) documento.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Hive com o HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar com o Hive no Tez, consulte os seguintes documentos para informações de depuração:

* [Utilize a vista do Apache Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

Para obter mais informações sobre a API REST utilizada neste documento, consulte a [referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) documento.

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


