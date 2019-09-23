---
title: UDF do Python com Apache Hive e Apache Pig-Azure HDInsight
description: Saiba como usar UDF (funções definidas pelo usuário) do Python de Apache Hive e Apache Pig no HDInsight, a pilha de tecnologia Apache Hadoop no Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: de738461776be7bdfd1abc45dde24dc1202d3a3c
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180746"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Usar funções definidas pelo usuário do Python (UDF) com Apache Hive e Apache Pig no HDInsight

Saiba como usar as funções definidas pelo usuário (UDF) do Python com o Apache Hive e o Apache Pig no Apache Hadoop no Azure HDInsight.

## <a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão no HDInsight 3,0 e posterior. Apache Hive pode ser usado com esta versão do Python para processamento de fluxo. O processamento de fluxo usa STDOUT e STDIN para passar dados entre o hive e o UDF.

O HDInsight também inclui Jython, que é uma implementação do Python escrita em Java. O Jython é executado diretamente no Máquina Virtual Java e não usa streaming. Jython é o intérprete do Python recomendado ao usar o Python com o Pig.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop no HDInsight**. Consulte [introdução ao HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Um cliente SSH**. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário de clusters. Isso seria `wasb://` para o armazenamento do Azure `abfs://` , por Azure data Lake Storage Gen2 ou ADL://para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, o URI seria wasbs://.  Consulte também a [transferência segura](../../storage/common/storage-require-secure-transfer.md).
* **Alteração possível na configuração de armazenamento.**  Consulte [configuração de armazenamento](#storage-configuration) se estiver usando o `BlobStorage`tipo de conta de armazenamento.
* Opcional.  Se estiver planejando usar o PowerShell, você precisará do [módulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) instalado.

> [!NOTE]  
> A conta de armazenamento usada neste artigo foi o armazenamento do Azure com a [transferência segura](../../storage/common/storage-require-secure-transfer.md) habilitada e, portanto, `wasbs` é usada em todo o artigo.

## <a name="storage-configuration"></a>Configuração do armazenamento
Nenhuma ação será necessária se a conta de armazenamento usada for do `Storage (general purpose v1)` tipo `StorageV2 (general purpose v2)`ou.  O processo neste artigo produzirá a saída para pelo menos `/tezstaging`.  Uma configuração padrão do Hadoop conterá `/tezstaging` `fs.azure.page.blob.dir` na variável de configuração `core-site.xml` no para `HDFS`o serviço.  Essa configuração fará com que a saída para o diretório seja blobs de páginas, que não têm suporte para o `BlobStorage`tipo de conta de armazenamento.  Para usar `BlobStorage` neste artigo, remova `/tezstaging` da variável de `fs.azure.page.blob.dir` configuração.  A configuração pode ser acessada na [interface do usuário do amAmbari](../hdinsight-hadoop-manage-ambari.md).  Caso contrário, você receberá a mensagem de erro:`Page blob is not supported for this account type.`

> [!WARNING]  
> As etapas neste documento fazem as seguintes suposições:  
>
> * Você cria os scripts do Python em seu ambiente de desenvolvimento local.
> * Você carrega os scripts no HDInsight usando o `scp` comando ou o script do PowerShell fornecido.
>
> Se você quiser usar o [Azure cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabalhar com o HDInsight, deverá:
>
> * Crie os scripts dentro do ambiente do Cloud Shell.
> * Use `scp` para carregar os arquivos do Cloud Shell para o HDInsight.
> * Use `ssh` do Cloud Shell para se conectar ao HDInsight e executar os exemplos.

## <a name="hivepython"></a>UDF Apache Hive

O Python pode ser usado como um UDF do hive por meio `TRANSFORM` da instrução HiveQL. Por exemplo, o HiveQL a seguir invoca o `hiveudf.py` arquivo armazenado na conta de armazenamento do Azure padrão para o cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Veja o que este exemplo faz:

1. A `add file` instrução no início do arquivo adiciona o `hiveudf.py` arquivo ao cache distribuído, portanto, ele é acessível por todos os nós no cluster.
2. A `SELECT TRANSFORM ... USING` instrução seleciona dados `hivesampletable`do. Ele também passa os valores ClientID, devicemake e devicemodel para o `hiveudf.py` script.
3. A `AS` cláusula descreve os campos retornados de `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Criar arquivo

Em seu ambiente de desenvolvimento, crie um arquivo de `hiveudf.py`texto chamado. Use o código a seguir como o conteúdo do arquivo:

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

Esse script executa as seguintes ações:

1. Lê uma linha de dados de STDIN.
2. O caractere de nova linha à direita é `string.strip(line, "\n ")`removido usando.
3. Ao fazer o processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada valor. Portanto `string.split(line, "\t")` , pode ser usado para dividir a entrada em cada guia, retornando apenas os campos.
4. Quando o processamento é concluído, a saída deve ser gravada em STDOUT como uma única linha, com uma Tabulação entre cada campo. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O `while` loop se repete até que `line` não seja lido.

A saída do script é uma concatenação dos valores de entrada `devicemake` para `devicemodel`e e um hash do valor concatenado.

### <a name="upload-file-shell"></a>Carregar arquivo (Shell)
Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente.  Substituir `mycluster` pelo nome real do cluster.  Verifique se o diretório de trabalho é onde o arquivo está localizado.

1. Use `scp` para copiar os arquivos para o cluster HDInsight. Edite e insira o comando a seguir:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use o SSH para se conectar ao cluster.  Edite e insira o comando a seguir:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão do SSH, adicione os arquivos do Python carregados anteriormente para o armazenamento do cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Usar UDF do hive (Shell)

1. Para se conectar ao Hive, use o seguinte comando em sua sessão SSH aberta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Esse comando inicia o cliente beeline.

2. Insira a seguinte consulta no `0: jdbc:hive2://headnodehost:10001/>` prompt:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de inserir a última linha, o trabalho deve ser iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante ao exemplo a seguir:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para sair do beeline, digite o seguinte comando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Carregar arquivo (PowerShell)

O PowerShell também pode ser usado para executar consultas de Hive remotamente. Verifique se o diretório de trabalho `hiveudf.py` está localizado.  Use o seguinte script do PowerShell para executar uma consulta de Hive que `hiveudf.py` usa o script:

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
> Para obter mais informações sobre como carregar arquivos, consulte o documento [carregar dados para trabalhos de Apache Hadoop no HDInsight](../hdinsight-upload-data.md) .


#### <a name="use-hive-udf"></a>Usar UDF do hive


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

A saída para o trabalho do **Hive** deve ser semelhante ao exemplo a seguir:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>UDF do Apache Pig

Um script Python pode ser usado como um UDF do Pig por meio `GENERATE` da instrução. Você pode executar o script usando o Jython ou o Python C.

* Jython é executado na JVM e pode ser chamado nativamente de Pig.
* O Python C é um processo externo, portanto, os dados do Pig na JVM são enviados para o script em execução em um processo do Python. A saída do script Python é enviada de volta ao Pig.

Para especificar o intérprete do Python, use `register` ao referenciar o script Python. Os exemplos a seguir registram scripts com `myfuncs`Pig como:

* **Para usar o Jython**:`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para usar o Python C**:`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Ao usar Jython, o caminho para o arquivo pig_jython pode ser um caminho local ou um caminho de WASBS://. No entanto, ao usar o Python C, você deve fazer referência a um arquivo no sistema de arquivos local do nó que você está usando para enviar o trabalho Pig.

Após o registro anterior, o Pig Latin para este exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Veja o que este exemplo faz:

1. A primeira linha carrega o arquivo `sample.log` de dados de exemplo em. `LOGS` Ele também define cada registro como um `chararray`.
2. A próxima linha filtra quaisquer valores nulos, armazenando o resultado da operação em `LOG`.
3. Em seguida, ele itera sobre os registros no `LOG` e usa `GENERATE` para invocar `create_structure` o método contido no script Python/Jython carregado como `myfuncs`. `LINE`é usado para passar o registro atual para a função.
4. Por fim, as saídas são despejadas para stdout `DUMP` usando o comando. Esse comando exibe os resultados após a conclusão da operação.

### <a name="create-file"></a>Criar arquivo

Em seu ambiente de desenvolvimento, crie um arquivo de `pigudf.py`texto chamado. Use o código a seguir como o conteúdo do arquivo:

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

No exemplo de Pig Latin, a `LINE` entrada é definida como um matriz porque não há esquema consistente para a entrada. O script Python transforma os dados em um esquema consistente para saída.

1. A `@outputSchema` instrução define o formato dos dados que são retornados para Pig. Nesse caso, é um recipiente de **dados**, que é um tipo de dados Pig. A bolsa contém os campos a seguir, todos os quais são matriz (cadeias de caracteres):

   * data-a data em que a entrada de log foi criada
   * hora-a hora em que a entrada de log foi criada
   * ClassName-o nome de classe para o qual a entrada foi criada
   * nível-o nível de log
   * detalhe-detalhes detalhados para a entrada de log

2. Em seguida, `def create_structure(input)` o define a função para a qual Pig passa itens de linha.

3. Os dados de exemplo `sample.log`,, em geral, estão em conformidade com a data, hora, ClassName, nível e esquema de detalhes. No entanto, ele contém algumas linhas que começam `*java.lang.Exception*`com. Essas linhas devem ser modificadas para corresponder ao esquema. A `if` instrução verifica isso e, em seguida, movimenta os dados de entrada `*java.lang.Exception*` para mover a cadeia de caracteres para o final, colocando os dados em linha com o esquema de saída esperado.

4. Em seguida, `split` o comando é usado para dividir os dados nos primeiros quatro caracteres de espaço. A saída é atribuída em `date` `classname`, `time`,, `level`e `detail`.

5. Por fim, os valores são retornados para Pig.

Quando os dados são retornados para Pig, ele tem um esquema consistente, conforme definido na `@outputSchema` instrução.



### <a name="upload-file-shell"></a>Carregar arquivo (Shell)

Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente.  Substituir `mycluster` pelo nome real do cluster.  Verifique se o diretório de trabalho é onde o arquivo está localizado.

1. Use `scp` para copiar os arquivos para o cluster HDInsight. Edite e insira o comando a seguir:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use o SSH para se conectar ao cluster.  Edite e insira o comando a seguir:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão do SSH, adicione os arquivos do Python carregados anteriormente para o armazenamento do cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Usar UDF Pig (Shell)

1. Para se conectar ao Pig, use o seguinte comando em sua sessão SSH aberta:

    ```bash
    pig
    ```

2. Insira as seguintes instruções no `grunt>` prompt:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de inserir a linha a seguir, o trabalho deve ser iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante aos seguintes dados:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Use `quit` para sair do Shell Grunt e, em seguida, use o seguinte para editar o arquivo pigudf.py no sistema de arquivos local:

    ```bash
    nano pigudf.py
    ```

5. Uma vez no editor, remova a marca de comentário da linha a `#` seguir removendo o caractere do início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Essa linha modifica o script Python para trabalhar com o Python C em vez de Jython. Depois que a alteração tiver sido feita, use **Ctrl + X** para sair do editor. Selecione **Y**e, em seguida, **Enter** para salvar as alterações.

6. Use o `pig` comando para iniciar o Shell novamente. Quando você estiver no `grunt>` prompt, use o seguinte para executar o script Python usando o interpretador do Python C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Quando esse trabalho for concluído, você deverá ver a mesma saída como quando executou o script anteriormente usando Jython.


### <a name="upload-file-powershell"></a>Carregar arquivo (PowerShell)

O PowerShell também pode ser usado para executar consultas de Hive remotamente. Verifique se o diretório de trabalho `pigudf.py` está localizado.  Use o seguinte script do PowerShell para executar uma consulta de Hive que `pigudf.py` usa o script:

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

### <a name="use-pig-udf-powershell"></a>Usar UDF Pig (PowerShell)

> [!NOTE]  
> Ao enviar remotamente um trabalho usando o PowerShell, não é possível usar o Python C como o intérprete.

O PowerShell também pode ser usado para executar trabalhos do Pig Latin. Para executar um trabalho Pig Latin que usa o `pigudf.py` script, use o seguinte script do PowerShell:

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

A saída para o trabalho **Pig** deve ser semelhante aos seguintes dados:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="errors-when-running-jobs"></a>Erros ao executar trabalhos

Ao executar o trabalho do hive, você pode encontrar um erro semelhante ao seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo Python. Muitos editores do Windows usam o padrão CRLF como terminação de linha, mas os aplicativos Linux geralmente esperam LF.

Você pode usar as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo para o HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts do PowerShell de exemplo usados para executar os exemplos contêm uma linha comentada que exibe a saída de erro para o trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova a marca de comentário da linha a seguir e veja se as informações do erro indicam um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também são registrados no armazenamento do HDInsight.

| Para este trabalho... | Examinar esses arquivos no contêiner de BLOBs |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Passos seguintes

Se você precisar carregar módulos Python que não são fornecidos por padrão, consulte [como implantar um módulo no Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para outras maneiras de usar o Pig, o hive e para saber mais sobre como usar o MapReduce, consulte os seguintes documentos:

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
