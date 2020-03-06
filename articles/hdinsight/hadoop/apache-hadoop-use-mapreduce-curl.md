---
title: Use mapReduce e Curl com Apache Hadoop em HDInsight - Azure
description: Aprenda a executar remotamente mapReduce jobs com Apache Hadoop no HDInsight usando Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302717"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Executar mapeia Empregos com Apache Hadoop no HDInsight usando REST

Aprenda a usar a Apache Hive WebHCat REST API para executar mapReduce jobs em um Apache Hadoop em cluster HDInsight. O curl é usado para demonstrar como pode interagir com o HDInsight usando pedidos CRUHTTP para executar trabalhos MapReduce.

> [!NOTE]  
> Se já está familiarizado com a utilização de servidores Hadoop baseados em Linux, mas é novo no HDInsight, veja o que precisa de saber sobre o [Apache Hadoop baseado em Linux no documento HDInsight.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

Ou:
  * Windows PowerShell ou,
  * [Caracol](https://curl.haxx.se/) com [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Executar um trabalho mapReduce

> [!NOTE]  
> Quando utilizar o Curl ou qualquer outra comunicação REST com o WebHCat, deve autenticar os pedidos fornecendo o nome de utilizador e palavra-passe do administrador do cluster HDInsight. Deve utilizar o nome do cluster como parte do URI que é utilizado para enviar os pedidos para o servidor.
>
> A API REST é protegida utilizando a [autenticação de acesso básico.](https://en.wikipedia.org/wiki/Basic_access_authentication) Deve sempre fazer pedidos utilizando https para garantir que as suas credenciais são enviadas de forma segura para o servidor.

### <a name="curl"></a>Caracol

1. Para facilitar a utilização, desloque as variáveis abaixo. Este exemplo baseia-se num ambiente Windows, revisto conforme necessário para o seu ambiente.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: Indica o nome do utilizador e a palavra-passe utilizada para autenticar o pedido
   * **-G**: Indica que esta operação é um pedido get

   O início do URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todos os pedidos.

    Recebe uma resposta semelhante à seguinte JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Para submeter um trabalho MapReduce, utilize o seguinte comando. Modifique o caminho para **o jq** conforme necessário.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    O fim do URI (/mapreduce/jar) diz ao WebHCat que este pedido inicia um trabalho MapReduce de uma classe num ficheiro de frascos. Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: `-G` não é utilizado, pelo que o pedido não se aplica ao método POST. `-d` especifica os valores de dados que são enviados com o pedido.
     * **user.name**: O utilizador que está a comandar o comando
     * **jar**: A localização do arquivo do frasco que contém classe a ser ranuosa
     * **classe**: A classe que contém a lógica MapReduce
     * **arg**: Os argumentos a serem transmitidos para o trabalho MapReduce. Neste caso, o ficheiro de texto de entrada e o diretório que são utilizados para a saída

    Este comando deve devolver uma identificação de trabalho que possa ser usada para verificar o estado do trabalho:

       job_1415651640909_0026

1. Para verificar o estado do trabalho, utilize o seguinte comando. Substitua o valor por `JOBID` pelo valor **real** devolvido na etapa anterior. Reveja a localização do **JQ** conforme necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Para facilitar a utilização, desloque as variáveis abaixo. Substitua `CLUSTERNAME` pelo seu nome real de cluster. Execute o comando e introduza a senha de login do cluster quando solicitado.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. utilize o seguinte comando para verificar se pode ligar-se ao seu cluster HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Recebe uma resposta semelhante à seguinte JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Para submeter um trabalho MapReduce, utilize o seguinte comando:

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

    O fim do URI (/mapreduce/jar) diz ao WebHCat que este pedido inicia um trabalho MapReduce de uma classe num ficheiro de frascos. Os parâmetros utilizados neste comando são os seguintes:

    * **user.name**: O utilizador que está a comandar o comando
    * **jar**: A localização do arquivo do frasco que contém classe a ser ranuosa
    * **classe**: A classe que contém a lógica MapReduce
    * **arg**: Os argumentos a serem transmitidos para o trabalho MapReduce. Neste caso, o ficheiro de texto de entrada e o diretório que são utilizados para a saída

   Este comando deve devolver uma identificação de trabalho que possa ser usada para verificar o estado do trabalho:

       job_1415651640909_0026

1. Para verificar o estado do trabalho, utilize o seguinte comando:

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

1. Se o trabalho estiver concluído, o Estado devolvido é `SUCCEEDED`.

1. Quando o estado do trabalho tiver mudado para `SUCCEEDED`, pode recuperar os resultados do trabalho a partir do armazenamento da Blob Azure. O parâmetro `statusdir` que é passado com a consulta contém a localização do ficheiro de saída. Neste exemplo, a localização é `/example/curl`. Este endereço armazena a saída do trabalho nos clusters de armazenamento padrão em `/example/curl`.

Pode listar e descarregar estes ficheiros utilizando o [Azure CLI](/cli/azure/install-azure-cli). Para obter mais informações sobre a utilização do Azure CLI para trabalhar com o armazenamento Azure Blob, consulte [Quickstart: Create, download e list blobs com Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight:

* [Use mapReduce com Hadoop Apache no HDInsight](hdinsight-use-mapreduce.md)
* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre a interface REST que é utilizada neste artigo, consulte a [Referência WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
