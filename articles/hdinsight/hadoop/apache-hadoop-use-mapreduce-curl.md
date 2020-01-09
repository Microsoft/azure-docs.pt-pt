---
title: Usar o MapReduce e a rotação com Apache Hadoop no HDInsight – Azure
description: Saiba como executar remotamente trabalhos do MapReduce com o Apache Hadoop no HDInsight usando a ondulação.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645009"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Executar trabalhos MapReduce com Apache Hadoop no HDInsight usando REST

Saiba como usar a API REST do Apache Hive WebHCat para executar trabalhos MapReduce em um Apache Hadoop no cluster HDInsight. A ondulação é usada para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar trabalhos MapReduce.

> [!NOTE]  
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for novo no HDInsight, consulte o documento o [que você precisa saber sobre Apache Hadoop baseado em Linux no hdinsight](../hdinsight-hadoop-linux-information.md) .


## <a id="prereq"></a>Pré-requisitos

* Um Hadoop no cluster HDInsight
* Windows PowerShell ou [ondulado](https://curl.haxx.se/) e [JQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>Executar um trabalho MapReduce

> [!NOTE]  
> Ao usar a rotação ou qualquer outra comunicação REST com o WebHCat, você deve autenticar as solicitações fornecendo o nome de usuário e a senha do administrador do cluster HDInsight. Você deve usar o nome do cluster como parte do URI usado para enviar as solicitações ao servidor.
>
> A API REST é protegida usando a [autenticação de acesso básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. Para definir o logon do cluster que é usado pelos scripts neste documento, use um dos seguintes comandos:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Para definir o nome do cluster, use um dos seguintes comandos:

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

    Você recebe uma resposta semelhante ao JSON a seguir:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: indica o nome de usuário e a senha usados para autenticar a solicitação
   * **-G**: indica que esta operação é uma solicitação get

   O início do URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações.

4. Para enviar um trabalho MapReduce, use o seguinte comando:

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

    O final do URI (/MapReduce/jar) informa ao WebHCat que essa solicitação inicia um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: `-G` não é usado, portanto, a solicitação assume como padrão o método post. `-d` especifica os valores de dados que são enviados com a solicitação.
     * **User.Name**: o usuário que está executando o comando
     * **jar**: o local do arquivo JAR que contém a classe a ser executada
     * **classe**: a classe que contém a lógica do MapReduce
     * **ARG**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório que são usados para a saída

   Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:

       job_1415651640909_0026

5. Para verificar o status do trabalho, use o seguinte comando:

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

    Se o trabalho for concluído, o estado retornado será `SUCCEEDED`.

   > [!NOTE]  
   > Essa solicitação de rotação retorna um documento JSON com informações sobre o trabalho. JQ é usado para recuperar apenas o valor de estado.

6. Quando o estado do trabalho for alterado para `SUCCEEDED`, você poderá recuperar os resultados do trabalho do armazenamento de BLOBs do Azure. O parâmetro `statusdir` que é passado com a consulta contém o local do arquivo de saída. Neste exemplo, o local é `/example/curl`. Esse endereço armazena a saída do trabalho no armazenamento padrão de clusters em `/example/curl`.

Você pode listar e baixar esses arquivos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como trabalhar com BLOBs da CLI do Azure, consulte o documento [usando o CLI do Azure com o armazenamento do Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) .

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre trabalhos MapReduce no HDInsight:

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter mais informações sobre a interface REST usada neste artigo, consulte a referência de [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
