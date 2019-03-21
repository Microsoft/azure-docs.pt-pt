---
title: Python UDF com o Apache Hive e Apache Pig - Azure HDInsight
description: Saiba como utilizar o Python definidas pelo utilizador funções (UDF) do Apache Hive e Apache Pig no HDInsight, a pilha de tecnologia do Apache Hadoop no Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f6a9d688169f0f8fdd6f0be7b664dbe9ebd71941
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295237"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Utilizar Python funções definidas pelo utilizador (UDF) com o Apache Hive e Apache Pig no HDInsight

Saiba como utilizar funções definidas pelo utilizador do Python (UDF) com o Apache Hive e Apache Pig no Apache Hadoop no HDInsight do Azure.

## <a name="python"></a>Python no HDInsight

Python2.7 é instalado por predefinição no HDInsight 3.0 e versões posteriores. Apache Hive pode ser utilizado com esta versão do Python para processamento de fluxo. Processamento de Stream utiliza STDOUT e STDIN para passar dados entre o Hive e a UDF.

HDInsight também inclui Jython, que é uma implementação de Python escrita em Java. Jython é executado diretamente na máquina Virtual de Java e não utiliza a transmissão em fluxo. Jython é o interpretador de Python recomendado quando utiliza o Python com o Pig.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do Hadoop no HDInsight**. Ver [introdução ao HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Um cliente SSH**. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para seu armazenamento primário de clusters. Isto poderá ser wasb: / / para o armazenamento do Azure, abfs: / / para a geração 2 de armazenamento do Azure Data Lake ou adl: / / para a geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento do Azure ou de geração 2 de armazenamento do Data Lake, o URI seria wasbs: / / ou abfss: / /, respectivamente Consulte também [transferência segura](../../storage/common/storage-require-secure-transfer.md).
* **Possíveis alterações à configuração de armazenamento.**  Ver [configuração de armazenamento](#storage-configuration) se utilizar o tipo de conta de armazenamento `BlobStorage`.
* Opcional.  Se planear utilizar o PowerShell, terá do [módulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) instalado.

> [!NOTE]  
> A conta de armazenamento utilizada neste artigo foi o armazenamento do Azure com [transferência segura](/../storage/common/storage-require-secure-transfer.md) ativado e, portanto, `wasbs` é usado em todo o artigo.

## <a name="storage-configuration"></a>Configuração do armazenamento
É necessária nenhuma ação se a conta de armazenamento utilizada é do tipo `Storage (general purpose v1)` ou `StorageV2 (general purpose v2)`.  O processo neste artigo produzirá um resultado para, pelo menos, `/tezstaging`.  Irá conter uma configuração predefinida do hadoop `/tezstaging` no `fs.azure.page.blob.dir` variável de configuração na `core-site.xml` para o serviço `HDFS`.  Esta configuração fará com que a saída para o diretório para blobs de páginas, que não são suportados para o tipo de conta de armazenamento `BlobStorage`.  Para utilizar `BlobStorage` neste artigo, remova `/tezstaging` partir o `fs.azure.page.blob.dir` variável de configuração.  A configuração pode ser acedida a partir da [IU do Ambari](../hdinsight-hadoop-manage-ambari.md).  Caso contrário, receberá a mensagem de erro: `Page blob is not supported for this account type.`

> [!WARNING]  
> Os passos neste documento partem as seguintes suposições:  
>
> * Criar os scripts de Python no seu ambiente de desenvolvimento local.
> * Carregar os scripts para o HDInsight usando o `scp` comando ou o script do PowerShell fornecido.
>
> Se pretender utilizar o [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabalhar com o HDInsight, em seguida, tem:
>
> * Crie scripts dentro do ambiente do cloud shell.
> * Utilize `scp` para carregar os ficheiros a partir da shell de cloud para o HDInsight.
> * Utilize `ssh` a partir da shell de cloud para ligar ao HDInsight e executar os exemplos.

## <a name="hivepython"></a>Apache Hive UDF

Python pode ser utilizado como uma UDF do Hive através do HiveQL `TRANSFORM` instrução. Por exemplo, o HiveQL seguinte invoca o `hiveudf.py` ficheiros armazenados na conta de armazenamento do Azure predefinido para o cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Eis o que faz este exemplo:

1. O `add file` instrução no início do ficheiro adiciona o `hiveudf.py` ficheiro para a cache distribuída, pelo que pode ser acedida por todos os nós do cluster.
2. O `SELECT TRANSFORM ... USING` instrução seleciona dados a partir do `hivesampletable`. Ele também passa os valores clientid, devicemake e devicemodel para o `hiveudf.py` script.
3. O `AS` cláusula descreve dos campos retornados pelo `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, crie um arquivo de texto chamado `hiveudf.py`. Utilize o seguinte código como o conteúdo do ficheiro:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Este script executa as seguintes ações:

1. Lê uma linha de dados de STDIN.
2. O caractere de nova linha à direita foi removido com `string.strip(line, "\n ")`.
3. Ao fazer o processamento de fluxos, uma única linha contém todos os valores com um caráter de tabulação entre cada valor. Então, `string.split(line, "\t")` pode ser utilizado para dividir a entrada em cada separador, apenas os campos a devolver.
4. Quando o processamento estiver concluído, o resultado deve ser escrito para STDOUT como uma única linha, com um separador entre cada campo. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O `while` loop se repetir até não `line` é de leitura.

Saída do script é uma concatenação dos valores introduzidos para `devicemake` e `devicemodel`e um hash do valor concatenado.

### <a name="upload-file-shell"></a>Carregar ficheiro (shell)
Os comandos abaixo, substitua `sshuser` com o nome de utilizador real, se for diferente.  Substitua `mycluster` com o nome de cluster real.  Certifique-se de que o seu diretório de trabalho é onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros no seu cluster do HDInsight. Editar e introduza o comando abaixo:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para ligar ao cluster.  Editar e introduza o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros de python carregados anteriormente para o armazenamento para o cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Utilizar o Hive UDF (shell)

1. Para ligar ao ramo de registo, utilize o seguinte comando da sua sessão SSH aberta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Este comando inicia o cliente de Beeline.

2. Introduza a seguinte consulta no `0: jdbc:hive2://headnodehost:10001/>` prompt:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de introduzir a última linha, a tarefa deve começar. Depois de concluída a tarefa, ele devolve resultados semelhantes ao seguinte exemplo:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para sair do Beeline, introduza o seguinte comando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Carregar ficheiro (PowerShell)

> [!IMPORTANT]  
> Estes scripts PowerShell não irão funcionar se [transferência segura](../../storage/common/storage-require-secure-transfer.md) está ativada.  Utilizar comandos da shell ou desativar a transferência segura.

PowerShell também pode ser usado para executar remotamente as consultas do Hive. Certifique-se de que seu diretório de trabalho é o local onde `hiveudf.py` está localizado.  Utilize o seguinte script do PowerShell para executar uma consulta do Hive que utiliza o `hiveudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Para obter mais informações sobre o carregamento de ficheiros, consulte a [carregar dados para as tarefas do Apache Hadoop no HDInsight](../hdinsight-upload-data.md) documento.


#### <a name="use-hive-udf"></a>Use Hive UDF


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o **Hive** tarefa deve ter um aspeto semelhante ao seguinte exemplo:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

Um script de Python pode ser utilizado como uma UDF do Pig por meio do `GENERATE` instrução. Pode executar o script usando Jython ou Python de C.

* Jython é executado no JVM e nativamente pode ser chamado de Pig.
* C Python é um processo externo, para que os dados do Pig no JVM são enviados para o script em execução num processo de Python. A saída do script de Python é enviada novamente para o Pig.

Para especificar o interpretador de Python, utilize `register` ao referenciar o script de Python. Os exemplos seguintes registrar scripts Pig como `myfuncs`:

* **Para utilizar Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Utilizar o Python de C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Quando utilizar Jython, o caminho para o ficheiro de pig_jython pode ser um caminho local ou um WASBS: / / caminho. No entanto, ao utilizar o Python de C, tem de referenciar um ficheiro no sistema de arquivos local do nó que está a utilizar para submeter a tarefa Pig.

Uma vez após o registo, o Pig Latin para este exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Eis o que faz este exemplo:

1. A primeira linha carrega o arquivo de dados de exemplo, `sample.log` em `LOGS`. Também define cada registo como um `chararray`.
2. A próxima linha filtra quaisquer valores nulos, armazenando o resultado da operação em `LOG`.
3. Em seguida, ele itera registros `LOG` e utiliza `GENERATE` para invocar a `create_structure` método contido no script de Python/Jython carregados como `myfuncs`. `LINE` é utilizado para transmitir o registo atual para a função.
4. Por fim, as saídas são despejadas para STDOUT usando o `DUMP` comando. Este comando apresenta os resultados depois de concluída a operação.

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, crie um arquivo de texto chamado `pigudf.py`. Utilize o seguinte código como o conteúdo do ficheiro:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

No exemplo Pig Latin, o `LINE` entrada é definida como um chararray porque não existe nenhum esquema consistente para a entrada. O script de Python transforma os dados num esquema consistente para saída.

1. O `@outputSchema` instrução define o formato dos dados que são devolvidos ao Pig. Neste caso, ele tem um **matriz de dados**, que é um tipo de dados do Pig. A matriz contém os seguintes campos, todos eles chararray (cadeias de caracteres):

   * data – a data que a entrada de registo foi criada
   * hora - a hora que a entrada de registo foi criada
   * ClassName - o nome da classe a entrada foi criado para
   * nível - o nível de registo
   * Detalhes - Detalhes verboso para a entrada de registo

2. Em seguida, o `def create_structure(input)` define a função que Pig passa os itens de linha para.

3. Os dados de exemplo, `sample.log`, principalmente se está em conformidade com a data, hora, classname, nível e esquema de detalhe. No entanto, ele contém algumas linhas que começam com `*java.lang.Exception*`. Estas linhas devem ser modificadas de acordo com o esquema. O `if` instrução verifica para aqueles que, em seguida, os manipulam os dados de entrada para mover o `*java.lang.Exception*` cadeia de caracteres para o final, levando a dados em linha com o esquema de saída esperada.

4. Em seguida, o `split` comando é utilizado para dividir os dados com o primeiro caractere de espaço de quatro. A saída é atribuída em `date`, `time`, `classname`, `level`, e `detail`.

5. Por fim, os valores são devolvidos para Pig.

Quando os dados são retornados para o Pig, ele tem um esquema consistente conforme definido no `@outputSchema` instrução.



### <a name="upload-file-shell"></a>Carregar ficheiro (shell)

Os comandos abaixo, substitua `sshuser` com o nome de utilizador real, se for diferente.  Substitua `mycluster` com o nome de cluster real.  Certifique-se de que o seu diretório de trabalho é onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros no seu cluster do HDInsight. Editar e introduza o comando abaixo:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para ligar ao cluster.  Editar e introduza o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros de python carregados anteriormente para o armazenamento para o cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Utilizar Pig UDF (shell)

1. Para ligar ao pig, utilize o seguinte comando da sua sessão SSH aberta:

    ```bash
    pig
    ```

2. Introduza as seguintes instruções no `grunt>` prompt:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de inserir a linha seguinte, a tarefa deve começar. Depois de concluída a tarefa, ele devolve resultados semelhantes aos dados seguintes:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilizar `quit` para sair da shell pesado e, em seguida, utilize o seguinte para editar o ficheiro de pigudf.py no sistema de arquivos local:

    ```bash
    nano pigudf.py
    ```

5. Uma vez no editor, anule os comentários a seguinte linha ao remover o `#` caráter desde o início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Esta linha modifica o script de Python para trabalhar com o Python de C em vez de Jython. Assim que a alteração foi feita, utilize **Ctrl + X** para sair do editor. Selecione **Y**e, em seguida **Enter** para guardar as alterações.

6. Utilize o `pig` comando para iniciar o shell novamente. Assim que estiver no `grunt>` linha de comandos, utilize o seguinte para executar o script de Python com o interpretador de Python de C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Quando esta tarefa for concluída, deverá ver o mesmo resultado como quando anteriormente executou o script usando Jython.


### <a name="upload-file-powershell"></a>Carregar ficheiro (PowerShell)

> [!IMPORTANT]  
> Estes scripts PowerShell não irão funcionar se [transferência segura](../../storage/common/storage-require-secure-transfer.md) está ativada.  Utilizar comandos da shell ou desativar a transferência segura.

PowerShell também pode ser usado para executar remotamente as consultas do Hive. Certifique-se de que seu diretório de trabalho é o local onde `pigudf.py` está localizado.  Utilize o seguinte script do PowerShell para executar uma consulta do Hive que utiliza o `pigudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Utilizar o Pig UDF (PowerShell)

> [!NOTE]  
> Ao submeter remotamente uma tarefa com o PowerShell, não é possível utilizar o Python de C como o interpretador.

PowerShell também pode ser usado para executar tarefas do Pig Latin. Para executar uma tarefa Pig Latin que utiliza o `pigudf.py` script, utilize o seguinte script do PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o **Pig** tarefa deve ter um aspeto semelhante para os seguintes dados:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="errors-when-running-jobs"></a>Erros ao executar tarefas

Durante a execução da tarefa do hive, pode encontrar um erro semelhante ao seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode dever-se se a fins de linha no ficheiro de Python. Muitos padrão de editores de Windows para utilizar CRLF como o fim da linha, mas as aplicações do Linux normalmente esperam LF.

Pode utilizar as seguintes declarações de PowerShell para remover os carateres de CR antes de carregar o ficheiro para o HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts do PowerShell de exemplo utilizados para executar os exemplos de conter uma linha Comentada que exibe a saída de erro para a tarefa. Se não está a ver o resultado esperado para a tarefa, anule os comentários a seguinte linha e ver se as informações de erro indicam um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado da tarefa (STDOUT) também são registrados para o armazenamento do HDInsight.

| Para esta tarefa... | Examine estes ficheiros no contentor de BLOBs |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Passos seguintes

Se precisar de carregar os módulos de Python que não são fornecidos por padrão, consulte [como implementar um módulo do Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para outras formas de utilizar o Pig, Hive e para saber mais sobre como utilizar o MapReduce, consulte os seguintes documentos:

* [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
