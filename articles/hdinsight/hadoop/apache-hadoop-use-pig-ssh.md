---
title: Utilizar o Apache Pig com o SSH num cluster do HDInsight - Azure
description: Saiba como ligar a um cluster do Apache Hadoop baseado em Linux com o SSH e, em seguida, utilize o comando de Pig para executar as instruções em Pig Latin interativamente ou como uma tarefa do batch.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 503449e36b2b11e5663449dd732fdaf785417570
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110755"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Executar tarefas do Apache Pig num cluster baseado em Linux com o comando de Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como executar interativamente tarefas do Apache Pig a partir de uma ligação SSH ao seu cluster do HDInsight. A linguagem de programação de Pig Latin permite-lhe descrever transformações são aplicadas a dados de entrada para produzir a saída desejada.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight baseado em Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a id="ssh"></a>Ligar com SSH

Utilize o SSH para ligar ao cluster do HDInsight. O exemplo seguinte liga a um cluster com o nome **myhdinsight** como a conta com o nome **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Utilize o comando de Apache Pig

1. Assim que estiver ligado, inicie a interface de linha de comandos (CLI) do Pig com o seguinte comando:

    ```bash
    pig
    ```

    Após alguns momentos, a linha de comandos muda para`grunt>`.

2. Introduza a seguinte instrução:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Este comando carrega o conteúdo do ficheiro Sample log para registos. Pode ver o conteúdo do arquivo usando a instrução seguinte:

    ```piglatin
    DUMP LOGS;
    ```

3. Em seguida, transforme os dados através da aplicação de uma expressão regular para extrair apenas o nível de registo de cada registo, utilizando a seguinte instrução:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Pode usar **DESPEJAR** para ver os dados após a transformação. Neste caso, utilize `DUMP LEVELS;`.

4. Continue a aplicar transformações utilizando as instruções na tabela a seguir:

    | Instrução de PIg Latin | O que faz a instrução |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Remove as linhas que contêm um valor nulo para o nível de registo e armazena os resultados em `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Agrupa as linhas por nível de registo e armazena os resultados em `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Cria um conjunto de dados que contém cada registo único valor de nível e o número de vezes que ele ocorre. O conjunto de dados é armazenado em `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Ordena os níveis de registo por contagem (descendente) e armazena em `RESULT`. |

    > [!TIP]  
    > Utilize `DUMP` para ver o resultado da transformação após cada passo.

5. Também pode guardar os resultados de uma transformação, utilizando o `STORE` instrução. Por exemplo, a instrução a seguir salva a `RESULT` para o `/example/data/pigout` diretório no armazenamento padrão para o seu cluster:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > Os dados são armazenados no diretório especificado nos arquivos chamados `part-nnnnn`. Se já existir no diretório, receberá um erro.

6. Para sair da linha de comandos da assistente, introduza a seguinte instrução:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>PIg Latin arquivos em lote

Também pode utilizar o comando de Pig para executar o Pig Latin contidos num ficheiro.

1. Depois de sair de linha de comandos da assistente, utilize o seguinte comando para criar o ficheiro com o nome `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Escreva ou cole as seguintes linhas:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Quando terminar, utilize __Ctrl__ + __X__, __Y__e, em seguida __Enter__ para guardar o ficheiro.

3. Utilize o seguinte comando para executar o `pigbatch.pig` ficheiro através do comando de Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Quando concluir a tarefa de lote, verá a seguinte saída:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Pig no HDInsight, consulte o documento seguinte:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter mais informações sobre outras formas de trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
