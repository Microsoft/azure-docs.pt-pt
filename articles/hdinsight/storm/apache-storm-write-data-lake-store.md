---
title: Tutorial-Apache Storm gravações no armazenamento/Data Lake Storage-Azure HDInsight
description: Tutorial-saiba como usar Apache Storm para gravar no armazenamento compatível com HDFS para o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: b6114a764d0834b7bcfe4b95d34fae6a03a8a40e
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181031"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Gravar em Apache Hadoop HDFS de Apache Storm no Azure HDInsight

Este tutorial demonstra como usar Apache Storm para gravar dados no armazenamento compatível com HDFS usado pelo Apache Storm no HDInsight. O HDInsight pode usar o armazenamento do Azure e Azure Data Lake Storage como armazenamento compatível com HDFS. O Storm fornece um componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que grava dados no HDFS. Este documento fornece informações sobre como gravar em um dos tipos de armazenamento do HdfsBolt.

A topologia de exemplo usada neste documento depende dos componentes incluídos no Storm no HDInsight. Ele pode exigir modificação para trabalhar com Azure Data Lake Storage quando usado com outros clusters de Apache Storm.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o cluster com a ação de script
> * Compilar e empacotar a topologia
> * Implantar e executar a topologia
> * Exibir dados de saída
> * Parar a topologia

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks)

* O [Apache Maven](https://maven.apache.org/download.cgi) foi [instalado](https://maven.apache.org/install.html) corretamente de acordo com o Apache.  O Maven é um sistema de compilação de projeto para projetos Java.

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário de clusters. Isso seria `wasb://` para o armazenamento do Azure `abfs://` , por Azure data Lake Storage Gen2 `adl://` ou para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, `wasbs://`o URI será.  Consulte também a [transferência segura](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Configuração de exemplo

O YAML a seguir é um trecho do `resources/writetohdfs.yaml` arquivo incluído no exemplo. Esse arquivo define a topologia Storm usando a estrutura de [fluxo](https://storm.apache.org/releases/current/flux.html) para Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Este YAML define os seguintes itens:

* `syncPolicy`: Define quando os arquivos são sincronizados/liberados para o sistema de arquivos. Neste exemplo, a cada 1000 tuplas.
* `fileNameFormat`: Define o caminho e o padrão de nome de arquivo a ser usado ao gravar arquivos. Neste exemplo, o caminho é fornecido em tempo de execução usando um filtro e a extensão de arquivo `.txt`é.
* `recordFormat`: Define o formato interno dos arquivos gravados. Neste exemplo, os `|` campos são delimitados pelo caractere.
* `rotationPolicy`: Define quando girar arquivos. Neste exemplo, nenhuma rotação é executada.
* `hdfs-bolt`: Usa os componentes anteriores como parâmetros de configuração para `HdfsBolt` a classe.

Para obter mais informações sobre a estrutura de fluxo [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html), consulte.

## <a name="configure-the-cluster"></a>Configurar o cluster

Por padrão, o Storm no HDInsight não inclui os componentes que `HdfsBolt` o usa para se comunicar com o armazenamento do Azure ou data Lake Storage no classpath do Storm. Use a seguinte ação de script para adicionar esses componentes ao `extlib` diretório do Storm no cluster:

| Propriedade | Value |
|---|---|
|Tipo de script |-Personalizado|
|URI do script de bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipo (s) de nó |Nimbus, supervisor|
|Parâmetros |Nenhum|

Para obter informações sobre como usar esse script com o cluster, consulte o documento [Personalizar clusters HDInsight usando ações de script](./../hdinsight-hadoop-customize-cluster-linux.md) .

## <a name="build-and-package-the-topology"></a>Compilar e empacotar a topologia

1. Baixe o projeto de exemplo [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do em seu ambiente de desenvolvimento.

2. Em um prompt de comando, terminal ou sessão de Shell, altere os diretórios para a raiz do projeto baixado. Para compilar e empacotar a topologia, use o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Depois que a compilação e o empacotamento forem concluídos, haverá um `target`novo diretório chamado, que contém `StormToHdfs-1.0-SNAPSHOT.jar`um arquivo chamado. Esse arquivo contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implantar e executar a topologia

1. Use o comando a seguir para copiar a topologia para o cluster HDInsight. Substituir `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Quando o carregamento for concluído, use o seguinte para se conectar ao cluster HDInsight usando SSH. Substituir `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez conectado, use o seguinte comando para criar um arquivo `dev.properties`chamado:

    ```bash
    nano dev.properties
    ```

1. Use o texto a seguir como o conteúdo do `dev.properties` arquivo. Revisar conforme necessário com base no seu [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para salvar o arquivo, use __Ctrl + X__, em seguida, __Y__e, por fim, __Enter__. Os valores nesse arquivo definem a URL de armazenamento e o nome do diretório no qual os dados são gravados.

1. Use o seguinte comando para iniciar a topologia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Esse comando inicia a topologia usando a estrutura de fluxo enviando-a para o nó Nimbus do cluster. A topologia é definida pelo `writetohdfs.yaml` arquivo incluído no jar. O `dev.properties` arquivo é passado como um filtro e os valores contidos no arquivo são lidos pela topologia.

## <a name="view-output-data"></a>Exibir dados de saída

Para exibir os dados, use o seguinte comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

É exibida uma lista dos arquivos criados por essa topologia. A lista a seguir é um exemplo dos dados retornados pelos comandos anteriores:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Parar a topologia

As topologias do Storm são executadas até serem interrompidas ou o cluster é excluído. Para interromper a topologia, use o seguinte comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar Apache Storm para gravar dados no armazenamento compatível com HDFS usado pelo Apache Storm no HDInsight.

> [!div class="nextstepaction"]
> Descubra outros [exemplos de Apache Storm para o HDInsight](apache-storm-example-topology.md)