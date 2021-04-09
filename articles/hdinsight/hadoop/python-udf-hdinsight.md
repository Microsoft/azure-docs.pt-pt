---
title: Python UDF com Apache Hive e Apache Pig - Azure HDInsight
description: Aprenda a usar Funções Definidas do Utilizador Python (UDF) da Apache Hive e Apache Pig em HDInsight, a pilha de tecnologia Apache Hadoop em Azure.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 593b809813f949cd1d0bcc17e1d1b7255ea19130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944283"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Use funções definidas do utilizador Python (UDF) com a Colmeia Apache e o Porco Apache em HDInsight

Aprenda a utilizar funções definidas pelo utilizador Python (UDF) com Apache Hive e Apache Pig em Apache Hadoop em Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python em HDInsight

Python2.7 é instalado por padrão em HDInsight 3.0 e mais tarde. A Hive Apache pode ser usada com esta versão de Python para processamento de fluxo. O processamento de fluxo utiliza STDOUT e STDIN para transmitir dados entre a Colmeia e a UDF.

HDInsight também inclui Jython, que é uma implementação python escrita em Java. Jython corre diretamente na Máquina Virtual Java e não usa streaming. Jython é o intérprete python recomendado ao usar Python com Porco.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop em HDInsight**. Ver [Começar com HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Um cliente SSH**. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 ou adl:// para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Azure Storage, o URI será wasbs://.  Consulte também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)
* **Possível alteração para a configuração de armazenamento.**  Consulte [a configuração de armazenamento](#storage-configuration) se utilizar o tipo de conta de armazenamento `BlobStorage` .
* Opcional.  Se planeia utilizar o PowerShell, necessitará do [módulo AZ](/powershell/azure/new-azureps-module-az) instalado.

> [!NOTE]  
> A conta de armazenamento utilizada neste artigo foi Azure Storage com [transferência segura](../../storage/common/storage-require-secure-transfer.md) ativada e, portanto, é usada ao longo `wasbs` do artigo.

## <a name="storage-configuration"></a>Configuração do armazenamento

Não é necessária qualquer ação se a conta de armazenamento utilizada for do tipo `Storage (general purpose v1)` ou `StorageV2 (general purpose v2)` . .  O processo neste artigo produzirá, pelo menos, a `/tezstaging` produção.  Uma configuração de hadoop padrão conterá `/tezstaging` na `fs.azure.page.blob.dir` variável de configuração `core-site.xml` para o serviço `HDFS` .  Esta configuração fará com que a saída para o diretório sejam bolhas de página, que não são suportadas para o tipo de conta de armazenamento `BlobStorage` .  Para utilizar `BlobStorage` este artigo, retire `/tezstaging` da variável de `fs.azure.page.blob.dir` configuração.  A configuração pode ser acedida a partir da [UI Ambari.](../hdinsight-hadoop-manage-ambari.md)  Caso contrário, receberá a mensagem de erro: `Page blob is not supported for this account type.`

> [!WARNING]  
> Os passos deste documento fazem os seguintes pressupostos:  
>
> * Você cria os scripts Python no seu ambiente de desenvolvimento local.
> * Você faz o upload dos scripts para HDInsight usando o `scp` comando ou o script PowerShell fornecido.
>
> Se quiser usar a [Azure Cloud Shell (bash)](../../cloud-shell/overview.md) para trabalhar com HDInsight, então deve:
>
> * Crie os scripts dentro do ambiente da casca da nuvem.
> * Utilize `scp` para fazer o upload dos ficheiros da casca de nuvem para o HDInsight.
> * Utilize `ssh` a partir da casca de nuvem para ligar ao HDInsight e executar os exemplos.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python pode ser usado como um UDF da Colmeia através da declaração de HiveQL. `TRANSFORM` Por exemplo, o seguinte HiveQL invoca o `hiveudf.py` ficheiro armazenado na conta de Armazenamento Azure padrão para o cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Eis o que este exemplo faz:

1. A `add file` declaração no início do ficheiro adiciona o ficheiro à cache `hiveudf.py` distribuída, pelo que é acessível por todos os nós do cluster.
2. A `SELECT TRANSFORM ... USING` declaração seleciona dados do `hivesampletable` . Também transmite os valores clienteid, devicemake e devicemodel para o `hiveudf.py` script.
3. A `AS` cláusula descreve os campos devolvidos de `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, crie um ficheiro de texto chamado `hiveudf.py` . Utilize o seguinte código como o conteúdo do ficheiro:

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

1. Lê uma linha de dados da STDIN.
2. O carácter da newline em fuga é removido `string.strip(line, "\n ")` utilizando.
3. Ao fazer o processamento de streaming, uma única linha contém todos os valores com um caráter de separador entre cada valor. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada separador, devolvendo apenas os campos.
4. Quando o processamento estiver concluído, a saída deve ser escrita para STDOUT como uma única linha, com um separador entre cada campo. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O `while` laço repete-se até que não `line` seja lido.

A saída do script é uma concatenação dos valores de entrada para `devicemake` e , e um `devicemodel` haxixe do valor concatenated.

### <a name="upload-file-shell"></a>Ficheiro de upload (concha)

Nos comandos abaixo, `sshuser` substitua-o pelo nome de utilizador real se for diferente.  `mycluster`Substitua-o pelo nome do cluster real.  Certifique-se de que o seu diretório de trabalho é onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros para o seu cluster HDInsight. Editar e inserir o comando abaixo:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para ligar ao cluster.  Editar e inserir o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros python enviados previamente ao armazenamento para o cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Use Hive UDF (concha)

1. Para ligar à Colmeia, utilize o seguinte comando a partir da sessão SSH aberta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Este comando inicia o cliente Beeline.

2. Introduza a seguinte consulta no `0: jdbc:hive2://headnodehost:10001/>` pedido:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de entrar na última linha, o trabalho deve começar. Uma vez concluído o trabalho, devolve a saída semelhante ao seguinte exemplo:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Para sair da Beeline, insira o seguinte comando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Ficheiro de upload (PowerShell)

PowerShell também pode ser usado para executar remotamente consultas de Colmeia. Certifique-se de que o seu diretório de trabalho `hiveudf.py` está localizado.  Utilize o seguinte script PowerShell para executar uma consulta de Colmeia que utiliza o `hiveudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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
> Para obter mais informações sobre o upload de ficheiros, consulte os dados do [Upload para trabalhos apache Hadoop no documento HDInsight.](../hdinsight-upload-data.md)

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

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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

A saída para o trabalho da **Colmeia** deve ser semelhante ao seguinte exemplo:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Pig UDF

Um script Python pode ser usado como um UDF de Pig através da `GENERATE` declaração. Pode executar o script usando Jython ou C Python.

* Jython corre no JVM, e pode ser chamado de Pig.
* C Python é um processo externo, por isso os dados do Pig no JVM são enviados para o script em execução num processo Python. A saída do guião Python é enviada de volta para o Porco.

Para especificar o intérprete Python, utilize `register` quando se refere ao script Python. Os seguintes exemplos registam scripts com Porco `myfuncs` como:

* **Para usar Jython:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para usar C Python:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Ao utilizar jython, o caminho para o ficheiro pig_jython pode ser um caminho local ou um caminho WASBS://. No entanto, ao utilizar o C Python, deve fazer referência a um ficheiro no sistema de ficheiros local do nó que está a usar para submeter o trabalho de Porco.

Uma vez registado, o Pig Latin para este exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Eis o que este exemplo faz:

1. A primeira linha carrega o ficheiro de dados da amostra `sample.log` em `LOGS` . Também define cada registo como um `chararray` .
2. A linha seguinte filtra quaisquer valores nulos, armazenando o resultado da operação em `LOG` .
3. Em seguida, itera sobre os registos `LOG` e usa para invocar o método contido no script `GENERATE` `create_structure` Python/Jython carregado como `myfuncs` . `LINE` é usado para passar o registo atual para a função.
4. Finalmente, as saídas são despejadas para STDOUT usando o `DUMP` comando. Este comando apresenta os resultados após a conclusão da operação.

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, crie um ficheiro de texto chamado `pigudf.py` . Utilize o seguinte código como o conteúdo do ficheiro:

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

No exemplo do Pig Latin, a `LINE` entrada é definida como uma chararray porque não há um esquema consistente para a entrada. O script Python transforma os dados num esquema consistente de saída.

1. A `@outputSchema` declaração define o formato dos dados que são devolvidos ao Pig. Neste caso, é um saco de **dados,** que é um tipo de dados de porco. O saco contém os seguintes campos, todos os quais são cararray (cordas):

   * data - a data em que a entrada de registo foi criada
   * tempo - o tempo que a entrada de registo foi criado
   * nome de classe - o nome de classe para a entrada foi criado para
   * nível - o nível de registo
   * detalhe - detalhes verbosos para a entrada de log

2. Em seguida, define `def create_structure(input)` a função a que o Porco passa os itens de linha.

3. Os dados de `sample.log` exemplo, na sua maioria em conformidade com a data, hora, nome de classe, nível e esquema de detalhe. No entanto, contém algumas linhas que começam com `*java.lang.Exception*` . Estas linhas devem ser modificadas para combinar com o esquema. A `if` declaração verifica esses dados e, em seguida, massaja os dados de entrada para mover a cadeia para o `*java.lang.Exception*` fim, alinhando os dados com o esquema de saída esperado.

4. Em seguida, o `split` comando é usado para dividir os dados nos primeiros quatro caracteres espaciais. A saída é atribuída `date` `time` a, `classname` , , e `level` `detail` .

5. Finalmente, os valores são devolvidos ao Porco.

Quando os dados são devolvidos à Pig, tem um esquema consistente, tal como definido no `@outputSchema` comunicado.

### <a name="upload-file-shell"></a>Ficheiro de upload (concha)

Nos comandos abaixo, `sshuser` substitua-o pelo nome de utilizador real se for diferente.  `mycluster`Substitua-o pelo nome do cluster real.  Certifique-se de que o seu diretório de trabalho é onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros para o seu cluster HDInsight. Editar e inserir o comando abaixo:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para ligar ao cluster.  Editar e inserir o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros python enviados previamente ao armazenamento para o cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Use pig uDF (concha)

1. Para ligar ao porco, utilize o seguinte comando da sessão SSH aberta:

    ```bash
    pig
    ```

2. Introduza as seguintes declarações no `grunt>` imediato:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de entrar na seguinte linha, o trabalho deve começar. Uma vez concluído o trabalho, devolve a produção semelhante aos seguintes dados:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Use `quit` para sair da concha Grunt e, em seguida, use o seguinte para editar o ficheiro pigudf.py no sistema de ficheiros local:

    ```bash
    nano pigudf.py
    ```

5. Uma vez no editor, descompromei-se a seguinte linha removendo o `#` personagem do início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Esta linha modifica o script Python para trabalhar com C Python em vez de Jython. Uma vez feita a alteração, utilize **ctrl+X** para sair do editor. Selecione **Y** e, em seguida, **introduza** para guardar as alterações.

6. Use o `pig` comando para ligar a casca novamente. Uma vez que esteja no `grunt>` pedido, use o seguinte para executar o script Python usando o intérprete C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Uma vez concluído este trabalho, deverá ver a mesma saída que quando executou previamente o script usando Jython.

### <a name="upload-file-powershell"></a>Ficheiro de upload (PowerShell)

PowerShell também pode ser usado para executar remotamente consultas de Colmeia. Certifique-se de que o seu diretório de trabalho `pigudf.py` está localizado.  Utilize o seguinte script PowerShell para executar uma consulta de Colmeia que utiliza o `pigudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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

### <a name="use-pig-udf-powershell"></a>Use uDF de porco (PowerShell)

> [!NOTE]  
> Ao submeter remotamente um trabalho utilizando o PowerShell, não é possível utilizar c python como intérprete.

PowerShell também pode ser usado para gerir trabalhos latinos de porco. Para executar um trabalho em latim pig que usa o `pigudf.py` script, use o seguinte script PowerShell:

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

A produção para o trabalho **do Suíno** deve ser semelhante aos seguintes dados:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-when-running-jobs"></a>Erros ao executar empregos

Ao executar a função de colmeia, poderá encontrar um erro semelhante ao seguinte texto:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Este problema pode ser causado pelas terminações de linha no ficheiro Python. Muitos editores do Windows padrão para usar CRLF como o fim da linha, mas as aplicações Linux geralmente esperam LF.

Pode utilizar as seguintes declarações do PowerShell para remover os caracteres CR antes de enviar o ficheiro para HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts PowerShell

Ambos os scripts PowerShell usados para executar os exemplos contêm uma linha comentada que exibe saída de erro para o trabalho. Se não está a ver a saída esperada para o trabalho, desaprote o seguinte modo e veja se a informação de erro indica um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também estão registados no seu armazenamento HDInsight.

| Para este trabalho... | Olhe para estes ficheiros no recipiente blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

Se precisar de carregar módulos Python que não são fornecidos por predefinição, consulte [Como implantar um módulo no Azure HDInsight](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Para outras formas de utilizar o Porco, Colmeia e aprender a usar o MapReduce, consulte os seguintes documentos:

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hdinsight-use-mapreduce.md)