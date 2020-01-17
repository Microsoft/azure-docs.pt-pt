---
title: Usar o MapReduce e a rotação com Apache Hadoop no HDInsight – Azure
description: Saiba como executar remotamente trabalhos do MapReduce com o Apache Hadoop no HDInsight usando a ondulação.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: 607020f1d540e83a4d049b96b9ab9a4ebcd385f0
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157258"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Executar trabalhos MapReduce com Apache Hadoop no HDInsight usando REST

Saiba como usar a API REST do Apache Hive WebHCat para executar trabalhos MapReduce em um Apache Hadoop no cluster HDInsight. A ondulação é usada para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar trabalhos MapReduce.

> [!NOTE]  
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for novo no HDInsight, consulte o documento o [que você precisa saber sobre Apache Hadoop baseado em Linux no hdinsight](../hdinsight-hadoop-linux-information.md) .

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

Faça o seguinte:
  * Windows PowerShell ou,
  * [Rotação](https://curl.haxx.se/) com [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Executar um trabalho MapReduce

> [!NOTE]  
> Ao usar a rotação ou qualquer outra comunicação REST com o WebHCat, você deve autenticar as solicitações fornecendo o nome de usuário e a senha do administrador do cluster HDInsight. Você deve usar o nome do cluster como parte do URI usado para enviar as solicitações ao servidor.
>
> A API REST é protegida usando a [autenticação de acesso básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.

### <a name="curl"></a>Curl

1. Para facilitar o uso, defina as variáveis abaixo. Este exemplo se baseia em um ambiente Windows, revise conforme necessário para seu ambiente.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: indica o nome de usuário e a senha usados para autenticar a solicitação
   * **-G**: indica que esta operação é uma solicitação get

   O início do URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações.

    Você recebe uma resposta semelhante ao JSON a seguir:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Para enviar um trabalho MapReduce, use o comando a seguir. Modifique o caminho para **JQ** conforme necessário.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    O final do URI (/MapReduce/jar) informa ao WebHCat que essa solicitação inicia um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: `-G` não é usado, portanto, a solicitação assume como padrão o método post. `-d` especifica os valores de dados que são enviados com a solicitação.
     * **User.Name**: o usuário que está executando o comando
     * **jar**: o local do arquivo JAR que contém a classe a ser executada
     * **classe**: a classe que contém a lógica do MapReduce
     * **ARG**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório que são usados para a saída

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:

       job_1415651640909_0026

1. Para verificar o status do trabalho, use o comando a seguir. Substitua o valor de `JOBID` pelo valor **real** retornado na etapa anterior. Revisar o local do **JQ** conforme necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Para facilitar o uso, defina as variáveis abaixo. Substitua `CLUSTERNAME` pelo nome real do cluster. Execute o comando e insira a senha de logon do cluster quando solicitado.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Use o comando a seguir para verificar se você pode se conectar ao seu cluster HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Você recebe uma resposta semelhante ao JSON a seguir:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Para enviar um trabalho MapReduce, use o seguinte comando:

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

    * **User.Name**: o usuário que está executando o comando
    * **jar**: o local do arquivo JAR que contém a classe a ser executada
    * **classe**: a classe que contém a lógica do MapReduce
    * **ARG**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório que são usados para a saída

   Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:

       job_1415651640909_0026

1. Para verificar o status do trabalho, use o seguinte comando:

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

### <a name="both-methods"></a>Ambos os métodos

1. Se o trabalho for concluído, o estado retornado será `SUCCEEDED`.

1. Quando o estado do trabalho for alterado para `SUCCEEDED`, você poderá recuperar os resultados do trabalho do armazenamento de BLOBs do Azure. O parâmetro `statusdir` que é passado com a consulta contém o local do arquivo de saída. Neste exemplo, o local é `/example/curl`. Esse endereço armazena a saída do trabalho no armazenamento padrão de clusters em `/example/curl`.

Você pode listar e baixar esses arquivos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como trabalhar com BLOBs da CLI do Azure, consulte o documento [usando o CLI do Azure com o armazenamento do Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) .

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre a interface REST usada neste artigo, consulte a referência de [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
