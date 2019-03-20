---
title: Utilizar o MapReduce e o Curl com o Apache Hadoop no HDInsight - Azure
description: Saiba como executar remotamente tarefas de MapReduce com o Apache Hadoop no HDInsight com o Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: b422074c33f52a6819d2a05144a85768a2e484a0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011929"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Executar tarefas de MapReduce com o Apache Hadoop no HDInsight com REST

Saiba como utilizar a API REST do WebHCat de ramo de registo Apache para executar tarefas de MapReduce num Apache Hadoop num cluster do HDInsight. Curl é utilizado para demonstrar como pode interagir com o HDInsight através da utilização de pedidos HTTP não processados para executar tarefas de MapReduce.

> [!NOTE]  
> Se já estiver familiarizado com a utilização do Hadoop baseado em Linux servidores, mas estiver familiarizado com o HDInsight, consulte a [o que precisa saber sobre baseado em Linux Apache Hadoop no HDInsight](../hdinsight-hadoop-linux-information.md) documento.


## <a id="prereq"></a>Pré-requisitos

* Um Hadoop num cluster do HDInsight
* Windows PowerShell ou [Curl](https://curl.haxx.se/) e [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Executar uma tarefa de MapReduce

> [!NOTE]  
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, fornecendo o nome de utilizador do administrador de cluster do HDInsight e a palavra-passe. Tem de utilizar o nome do cluster como parte do URI que é utilizado para enviar os pedidos para o servidor.
>
> É protegida com a API REST [autenticação de acesso básico](https://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos através de HTTPS para garantir que as suas credenciais são enviadas de forma segura para o servidor.

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

3. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Receber uma resposta semelhante à seguinte JSON:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: Indica o nome de utilizador e palavra-passe utilizada para autenticar o pedido
   * **-G**: Indica que esta operação é uma solicitação GET

   O início do URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos.

4. Para submeter uma tarefa de MapReduce, utilize o seguinte comando:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    O final do URI (/ mapreduce/jar) informa ao WebHCat que essa solicitação inicia uma tarefa de MapReduce de uma classe num ficheiro jar. Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: `-G` não for utilizado, pelo que o pedido é predefinido para o método POST. `-d` Especifica os valores de dados que são enviados com o pedido.
     * **user.name**: O utilizador que está a executar o comando
     * **jar**: A localização do ficheiro jar que contém a classe para ser executada
     * **classe**: A classe que contém a lógica de MapReduce
     * **arg**: Os argumentos transmitidos para a tarefa de MapReduce. Neste caso, o ficheiro de texto de entrada e o diretório que são utilizados para a saída

   Este comando deverá devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa:

       job_1415651640909_0026

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

    Se a tarefa estiver concluída, o estado devolvido é `SUCCEEDED`.

   > [!NOTE]  
   > Este pedido Curl devolve um documento JSON com informações sobre a tarefa. Jq é usado para recuperar apenas o valor de estado.

6. Quando o estado da tarefa foi alterado para `SUCCEEDED`, pode recuperar os resultados da tarefa de armazenamento de Blobs do Azure. O `statusdir` parâmetro que é passado com a consulta contém a localização do ficheiro de saída. Neste exemplo, a localização é `/example/curl`. Este endereço armazena o resultado da tarefa no armazenamento do padrão de clusters em `/example/curl`.

Pode listar e transferir estes ficheiros ao utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como trabalhar com blobs a partir da CLI do Azure, consulte a [utilizar a CLI do Azure com o armazenamento de Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) documento.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter mais informações sobre a interface REST, que é utilizada neste artigo, consulte a [referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
