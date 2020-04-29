---
title: Use a Colmeia De Hadoop Apache com Caracol em HDInsight - Azure
description: Saiba como submeter remotamente os empregos de Apache Pig ao Azure HDInsight usando o Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79298755"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Executar consultas de Hiv Apache com Apache Hadoop em HDInsight usando REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Aprenda a usar a WebHCat REST API para executar consultas apache hive com Apache Hadoop no cluster Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente rest. Este documento utiliza [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) no Windows PowerShell e [Curl](https://curl.haxx.se/) on [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Se utilizar o Bash, também vai precisar de jq, um processador JSON de linha de comando.  Vê. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

## <a name="base-uri-for-rest-api"></a>Base URI para Descanso API

O identificador de recursos uniformes base (URI) para `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`a `CLUSTERNAME` API REST no HDInsight é, onde está o nome do seu cluster.  Os nomes de cluster em URIs são **sensíveis a casos.**  Enquanto o nome do cluster na parte de domínio totalmente`CLUSTERNAME.azurehdinsight.net`qualificado (FQDN) do URI () é insensível a casos, outras ocorrências no URI são sensíveis a casos.

## <a name="authentication"></a>Autenticação

Ao utilizar cURL ou qualquer outra comunicação REST com o WebHCat, deve autenticar os pedidos fornecendo o nome de utilizador e a palavra-passe para o administrador do cluster HDInsight. A API de REST está protegida por [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que as suas credenciais são enviadas de forma segura para o servidor, faça sempre pedidos utilizando o Secure HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Configuração (Preservar credenciais)

Preserve as suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado num passo separado.

**A. Bash**  
Edite o script `PASSWORD` abaixo substituindo pela sua senha real.  Em seguida, entre no comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Execute o código abaixo e introduza as suas credenciais na janela pop-up:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifique o nome do cluster devidamente arquivado

O invólucro real do nome do cluster pode ser diferente do que se espera, dependendo de como o cluster foi criado.  Os passos aqui mostrarão o invólucro real, e depois armazená-lo-ão numa variável para todos os exemplos posteriores.

Edite os scripts `CLUSTERNAME` abaixo para substituir pelo nome do cluster. Em seguida, entre no comando. (O nome do cluster para o FQDN não é sensível a casos.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. Para verificar se pode ligar-se ao seu cluster HDInsight, utilize um dos seguintes comandos:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Recebe uma resposta semelhante ao seguinte texto:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * `-u`- O nome de utilizador e a palavra-passe utilizados para autenticar o pedido.
    * `-G`- Indica que este pedido é uma operação GET.

1. O início da `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`URL, é o mesmo para todos os pedidos. O caminho, `/status`indica que o pedido é devolver um estado de WebHCat (também conhecido como Templeton) para o servidor. Também pode solicitar a versão da Colmeia utilizando o seguinte comando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Este pedido devolve uma resposta semelhante ao seguinte texto:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Utilize o seguinte para criar uma tabela chamada **log4jLogs:**

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Este pedido utiliza o método POST, que envia dados como parte do pedido à API REST. Os seguintes valores de dados são enviados com o pedido:

     * `user.name`- O utilizador que está a comandar o comando.
     * `execute`- As declarações da HiveQL para executar.
     * `statusdir`- O diretório a que o estatuto para este trabalho está escrito.

   Estas declarações realizam as seguintes ações:

   * `DROP TABLE`- Se a mesa já existe, é apagada.
   * `CREATE EXTERNAL TABLE`- Cria uma nova tabela 'externa' na Colmeia. As tabelas externas armazenam apenas a definição de mesa na Colmeia. Os dados são deixados no local original.

     > [!NOTE]  
     > As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo automatizado de upload de dados ou outra operação MapReduce.
     >
     > Deixar cair uma tabela externa **não** elimina os dados, apenas a definição de tabela.

   * `ROW FORMAT`- Como os dados são formatados. Os campos em cada tronco são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION`- Quando os dados forem armazenados (o exemplo/diretório de dados) e que são armazenados como texto.
   * `SELECT`- Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Esta declaração devolve um valor de **3,** uma vez que existem três linhas que contêm este valor.

     > [!NOTE]  
     > Note que os espaços entre as declarações da HiveQL são substituídos pelo `+` personagem quando utilizados com Curl. Os valores citados que contenham um espaço, como `+`o delimitador, não devem ser substituídos por .

      Este comando devolve uma identificação de trabalho que pode ser usada para verificar o estado do trabalho.

1. Para verificar o estado do trabalho, utilize o seguinte comando:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

    Se o trabalho tiver terminado, o Estado será **bem sucedido.**

1. Uma vez que o estado do trabalho tenha mudado para **SUCCEED,** você pode recuperar os resultados do trabalho a partir do armazenamento Azure Blob. O `statusdir` parâmetro passado com a consulta contém a localização do ficheiro de saída; neste caso, `/example/rest`. Este endereço armazena `example/curl` a saída no diretório no armazenamento predefinido dos clusters.

    Pode listar e descarregar estes ficheiros utilizando o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre a utilização do Azure CLI com armazenamento Azure, consulte o [Use Azure CLI com](https://docs.microsoft.com/azure/storage/storage-azure-cli) o documento de armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight:

* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)
* [Use mapReduce com Hadoop Apache no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre a API REST utilizada neste documento, consulte o documento de [referência WebHCat.](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)