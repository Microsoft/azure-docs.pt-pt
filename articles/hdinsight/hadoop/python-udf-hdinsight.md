---
title: Python UDF com Apache Hive e Apache Pig - Azure HDInsight
description: Aprenda a utilizar funções definidas pelo utilizador Python (UDF) da Apache Hive e apache pig no HDInsight, a pilha de tecnologia Apache Hadoop no Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74148401"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Utilize funções definidas pelo utilizador Python (UDF) com hiv apache e porco Apache em HDInsight

Aprenda a utilizar funções definidas pelo utilizador python (UDF) com Apache Hive e Apache Pig em Apache Hadoop no Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python no HDInsight

Python2.7 é instalado por padrão no HDInsight 3.0 e posteriormente. A Apache Hive pode ser usada com esta versão de Python para processamento de fluxo. O processamento de fluxo utiliza STDOUT e STDIN para passar dados entre a Hive e a UDF.

HDInsight também inclui Jython, que é uma implementação python escrita em Java. Jython corre diretamente na Máquina Virtual Java e não usa streaming. Jython é o intérprete python recomendado ao usar Python com Porco.

## <a name="prerequisites"></a>Pré-requisitos

* **Um aglomerado de Hadoop no HDInsight**. Ver [Começar com HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Um cliente SSH**. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o `abfs://` Armazenamento Azure, para o Azure Data Lake Storage Gen2 ou adl:// para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI será wasbs://.  Ver também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)
* **Possível alteração na configuração de armazenamento.**  Consulte a [configuração](#storage-configuration) do `BlobStorage`armazenamento se utilizar a conta de armazenamento tipo .
* Opcional.  Se planejar utilizar o PowerShell, necessitará do [módulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) instalado.

> [!NOTE]  
> A conta de armazenamento utilizada neste artigo foi o `wasbs` Armazenamento Azure com transferência [segura](../../storage/common/storage-require-secure-transfer.md) ativada e, portanto, é utilizada em todo o artigo.

## <a name="storage-configuration"></a>Configuração do armazenamento

Não é necessária qualquer ação se a `Storage (general purpose v1)` `StorageV2 (general purpose v2)`conta de armazenamento utilizada for amável ou .  O processo neste artigo produzirá, `/tezstaging`pelo menos, a produção .  Uma configuração de hadoop `fs.azure.page.blob.dir` predefinido `core-site.xml` conterá `/tezstaging` na variável de configuração para o serviço `HDFS`.  Esta configuração fará com que a saída para o diretório seja blobs de página, que não são suportados para o tipo `BlobStorage`de conta de armazenamento .  Para `BlobStorage` utilizar para este `/tezstaging` artigo, remova da `fs.azure.page.blob.dir` variável de configuração.  A configuração pode ser acedida a partir do [Ambari UI](../hdinsight-hadoop-manage-ambari.md).  Caso contrário, receberá a mensagem de erro:`Page blob is not supported for this account type.`

> [!WARNING]  
> Os passos deste documento fazem os seguintes pressupostos:  
>
> * Cria os scripts Python no seu ambiente de desenvolvimento local.
> * Faça o upload dos scripts para `scp` HDInsight utilizando o comando ou o script PowerShell fornecido.
>
> Se quiser utilizar a Casca de [Nuvem Azure (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabalhar com o HDInsight, então deve:
>
> * Crie os scripts dentro do ambiente de nuvens.
> * Utilize `scp` para fazer o upload dos ficheiros da casca da nuvem para o HDInsight.
> * Utilize `ssh` a partir da casca de nuvem para ligar ao HDInsight e executar os exemplos.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python pode ser usado como uma UDF da `TRANSFORM` Hive através da declaração da HiveQL. Por exemplo, o seguinte HiveQL invoca o `hiveudf.py` ficheiro armazenado na conta de armazenamento de Armazenamento Azure padrão para o cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Aqui está o que este exemplo faz:

1. A `add file` declaração no início do `hiveudf.py` ficheiro adiciona o ficheiro à cache distribuída, por isso é acessível por todos os nós do cluster.
2. A `SELECT TRANSFORM ... USING` declaração seleciona `hivesampletable`dados do . Também passa os valores do clienteid, dispositivoe modelação para o `hiveudf.py` script.
3. A `AS` cláusula descreve os `hiveudf.py`campos devolvidos de .

<a name="streamingpy"></a>

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, `hiveudf.py`crie um ficheiro de texto chamado . Utilize o seguinte código como conteúdo do ficheiro:

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

1. Lê uma linha de dados do STDIN.
2. O carácter de linha nova `string.strip(line, "\n ")`que segue é removido usando .
3. Ao efazer o processamento do fluxo, uma única linha contém todos os valores com um caracteres de separador entre cada valor. Assim `string.split(line, "\t")` pode ser usado para dividir a entrada em cada separador, devolvendo apenas os campos.
4. Quando o processamento estiver concluído, a saída deve ser escrita para STDOUT como uma única linha, com um separador entre cada campo. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O `while` laço repete-se até não `line` ser lido.

A saída do script é uma concatenação dos valores de entrada para `devicemake` e, `devicemodel`e um hash do valor concatenado.

### <a name="upload-file-shell"></a>Upload ficheiro (concha)

Nos comandos abaixo, `sshuser` substitua-o pelo nome de utilizador real, se for diferente.  Substitua-a `mycluster` com o nome real do cluster.  Certifique-se de que o seu diretório de trabalho está onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros para o seu cluster HDInsight. Editar e inserir o comando abaixo:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para se ligar ao cluster.  Editar e inserir o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros python enviados anteriormente ao armazenamento do cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Use hive UDF (concha)

1. Para ligar à Hive, utilize o seguinte comando da sua sessão SSH aberta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Este comando começa o cliente Beeline.

2. Introduza a seguinte `0: jdbc:hive2://headnodehost:10001/>` consulta no pedido:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de entrar na última linha, o trabalho deve começar. Uma vez concluída a função, devolve a saída semelhante ao seguinte exemplo:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para sair de Beeline, insira o seguinte comando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Ficheiro de upload (PowerShell)

PowerShell também pode ser usado para executar remotamente consultas da Hive. Certifique-se de que `hiveudf.py` o seu diretório de trabalho está localizado.  Utilize o seguinte script PowerShell para executar uma `hiveudf.py` consulta da Hive que usa o script:

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
> Para obter mais informações sobre o upload de ficheiros, consulte os [dados de Upload para trabalhos de Apache Hadoop no documento HDInsight.](../hdinsight-upload-data.md)

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

A saída para o trabalho da **Colmeia** deve parecer semelhante ao seguinte exemplo:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Pig UDF

Um guião python pode ser usado como `GENERATE` uDF do Porco através da declaração. Podes executar o guião usando o Jython ou o C Python.

* Jython corre no JVM, e pode ser chamado de porco.
* C Python é um processo externo, por isso os dados do Pig no JVM são enviados para o script em execução num processo python. A saída do guião python é enviada de volta para Pig.

Para especificar o intérprete `register` Python, utilize ao fazer referência ao guião Python. Os seguintes exemplos registam `myfuncs`scripts com porco como:

* **Para usar Jython:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para utilizar C Python:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Ao usar Jython, o caminho para o ficheiro pig_jython pode ser um caminho local ou um caminho WASBS://. No entanto, ao utilizar o C Python, deve fazer referência a um ficheiro no sistema de ficheiros local do nó que está a usar para submeter a função de Porco.

Uma vez registado anteriormente, o Pig Latin para este exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Aqui está o que este exemplo faz:

1. A primeira linha carrega o `sample.log` `LOGS`ficheiro de dados da amostra, em . Também define cada registo `chararray`como .
2. A linha seguinte filtra quaisquer valores nulos, `LOG`armazenando o resultado da operação em .
3. Em seguida, iterates sobre `LOG` os `GENERATE` registos `create_structure` e usa para invocar o método contido `myfuncs`no script Python/Jython carregado como . `LINE`é usado para passar o registo atual para a função.
4. Finalmente, as saídas são despejadas para `DUMP` O DSTOUT utilizando o comando. Este comando apresenta os resultados após a operação estar concluída.

### <a name="create-file"></a>Criar ficheiro

No seu ambiente de desenvolvimento, `pigudf.py`crie um ficheiro de texto chamado . Utilize o seguinte código como conteúdo do ficheiro:

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

No exemplo do Pig `LINE` Latin, a entrada é definida como uma chararray porque não há um esquema consistente para a entrada. O script Python transforma os dados num esquema consistente para a saída.

1. A `@outputSchema` declaração define o formato dos dados que são devolvidos ao Porco. Neste caso, é um saco de **dados,** que é um tipo de dados de porco. O saco contém os seguintes campos, todos chararray (cordas):

   * data - a data em que a entrada de registo foi criada
   * tempo - o momento em que a entrada de registo foi criada
   * nome de classe - o nome de classe a entrada foi criado para
   * nível - o nível de registo
   * detalhe - detalhes verbosos para a entrada de registo

2. Em seguida, a função para a `def create_structure(input)` qual o Porco passa itens de linha.

3. Os dados `sample.log`de exemplo, na sua maioria em conformidade com a data, hora, nome de classe, nível e esquema de detalhe. No entanto, contém algumas `*java.lang.Exception*`linhas que começam com . Estas linhas devem ser modificadas para combinar com o esquema. A `if` declaração verifica-as e depois massaja os `*java.lang.Exception*` dados de entrada para mover a corda até ao fim, alinhando os dados com o esquema de saída esperado.

4. Em seguida, o `split` comando é usado para dividir os dados nos primeiros quatro caracteres espaciais. A saída é `date`atribuída `time` `classname`a, , e `level` `detail`.

5. Finalmente, os valores são devolvidos ao Porco.

Quando os dados são devolvidos ao Pig, tem um `@outputSchema` esquema consistente, tal como definido no comunicado.

### <a name="upload-file-shell"></a>Upload ficheiro (concha)

Nos comandos abaixo, `sshuser` substitua-o pelo nome de utilizador real, se for diferente.  Substitua-a `mycluster` com o nome real do cluster.  Certifique-se de que o seu diretório de trabalho está onde o ficheiro está localizado.

1. Utilize `scp` para copiar os ficheiros para o seu cluster HDInsight. Editar e inserir o comando abaixo:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilize o SSH para se ligar ao cluster.  Editar e inserir o comando abaixo:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, adicione os ficheiros python enviados anteriormente ao armazenamento do cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Use Pig UDF (concha)

1. Para ligar ao porco, utilize o seguinte comando da sua sessão SSH aberta:

    ```bash
    pig
    ```

2. Introduza as seguintes declarações no `grunt>` momento seguinte:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de entrar na seguinte linha, o trabalho deve começar. Uma vez concluída a função, devolve a saída semelhante aos seguintes dados:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilize `quit` para sair da concha Grunt e, em seguida, use o seguinte para editar o ficheiro pigudf.py no sistema de ficheiros local:

    ```bash
    nano pigudf.py
    ```

5. Uma vez no editor, não comente `#` a seguinte linha removendo o personagem do início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Esta linha modifica o guião python para trabalhar com C Python em vez de Jython. Uma vez feita a alteração, utilize **ctrl+X** para sair do editor. Selecione **Y**e, em seguida, **introduza** para guardar as alterações.

6. Use `pig` o comando para reinicie a concha. Uma vez que `grunt>` esteja na solicitação, use o seguinte para executar o script Python usando o intérprete C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Uma vez concluído este trabalho, deve ver a mesma saída que quando já executou o guião usando Jython.

### <a name="upload-file-powershell"></a>Ficheiro de upload (PowerShell)

PowerShell também pode ser usado para executar remotamente consultas da Hive. Certifique-se de que `pigudf.py` o seu diretório de trabalho está localizado.  Utilize o seguinte script PowerShell para executar uma `pigudf.py` consulta da Hive que usa o script:

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

### <a name="use-pig-udf-powershell"></a>Use Pig UDF (PowerShell)

> [!NOTE]  
> Ao submeter remotamente um trabalho utilizando powerShell, não é possível utilizar C Python como intérprete.

A PowerShell também pode ser usada para gerir trabalhos em Pig Latin. Para executar um trabalho em `pigudf.py` latim pig que usa o script, use o seguinte script PowerShell:

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

A produção para o trabalho de **porco** deve parecer semelhante aos seguintes dados:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Resolução de problemas

### <a name="errors-when-running-jobs"></a>Erros ao executar empregos

Ao executar o trabalho de colmeia, pode encontrar um erro semelhante ao seguinte texto:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Este problema pode ser causado pelos terminações de linha no ficheiro Python. Muitos editores do Windows não usam o CRLF como o fim da linha, mas as aplicações linux geralmente esperam LF.

Pode utilizar as seguintes declarações powerShell para remover os caracteres CR antes de enviar o ficheiro para HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts PowerShell

Ambos os scripts de exemplo PowerShell usados para executar os exemplos contêm uma linha comentada que exibe saída de erro para o trabalho. Se não estiver a ver a saída esperada para o trabalho, não comente a seguinte linha e veja se a informação de erro indica um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também estão registadas no seu armazenamento HDInsight.

| Para este trabalho... | Olhe para estes arquivos no recipiente de bolhas |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

Se necessitar de carregar módulos Python que não são fornecidos por padrão, consulte [como implementar um módulo para o Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para outras formas de usar o Porco, a Colmeia e aprender a usar o MapReduce, consulte os seguintes documentos:

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hdinsight-use-mapreduce.md)
