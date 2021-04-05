---
title: 'Tutorial: HDInsight Apache Storm to Storage - Azure/Data Lake'
description: Tutorial - Aprenda a usar a Tempestade Apache para escrever ao armazenamento compatível com HDFS para Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932556"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Escreva para Apache Hadoop HDFS da Tempestade Apache em Azure HDInsight

Este tutorial demonstra como usar a Tempestade Apache para escrever dados para o armazenamento compatível com HDFS usado pela Apache Storm em HDInsight. O HDInsight pode usar tanto o Armazenamento Azure como o Armazenamento do Lago de Dados Azure como armazenamento compatível com HDFS. A tempestade fornece um componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que escreve dados para o HDFS. Este documento fornece informações sobre a escrita para qualquer tipo de armazenamento a partir do HdfsBolt.

O exemplo de topologia utilizada neste documento baseia-se em componentes que estão incluídos na Tempestade em HDInsight. Pode exigir modificação para trabalhar com o Azure Data Lake Storage quando usado com outros aglomerados de tempestade Apache.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure o cluster com ação de script
> * Construir e embalar a topologia
> * Implementar e executar a topologia
> * Ver dados de saída
> * Parar a topologia

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI será `wasbs://` .  Consulte também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)

### <a name="example-configuration"></a>Configuração de exemplo

O YAML seguinte é um excerto do `resources/writetohdfs.yaml` ficheiro incluído no exemplo. Este ficheiro define a topologia da tempestade usando a estrutura [do Fluxo](https://storm.apache.org/releases/current/flux.html) para a Tempestade Apache.

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

* `syncPolicy`: Define quando os ficheiros são sincronizados/lavados no sistema de ficheiros. Neste exemplo, a cada 1000 tuples.
* `fileNameFormat`: Define o padrão de nome do caminho e do ficheiro para utilizar ao escrever ficheiros. Neste exemplo, o caminho é fornecido em tempo de execução usando um filtro, e a extensão do ficheiro é `.txt` .
* `recordFormat`: Define o formato interno dos ficheiros escritos. Neste exemplo, os campos são delimitados pelo `|` personagem.
* `rotationPolicy`: Define quando rodar ficheiros. Neste exemplo, não é efetuada qualquer rotação.
* `hdfs-bolt`: Utiliza os componentes anteriores como parâmetros de configuração para a `HdfsBolt` classe.

Para obter mais informações sobre o quadro do Fluxo, consulte [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

## <a name="configure-the-cluster"></a>Configurar o cluster

Por padrão, a Tempestade em HDInsight não inclui os componentes que `HdfsBolt` utiliza para comunicar com o Azure Storage ou Data Lake Storage no caminho de classe da Tempestade. Utilize a seguinte ação de script para adicionar estes componentes ao `extlib` diretório de Storm no seu cluster:

| Propriedade | Valor |
|---|---|
|Tipo de script |- Personalizado|
|URI de guião de bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipo de nó(s) |Nimbus, Supervisor|
|Parâmetros |Nenhum|

Para obter informações sobre a utilização deste script com o seu cluster, consulte os [clusters Personale HDInsight utilizando](./../hdinsight-hadoop-customize-cluster-linux.md) o documento de ações de script.

## <a name="build-and-package-the-topology"></a>Construir e embalar a topologia

1. Faça o download do projeto exemplo do seu ambiente de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) desenvolvimento.

2. A partir de uma essão de comando, terminal ou shell, altere os diretórios para a raiz do projeto descarregado. Para construir e embalar a topologia, utilize o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Uma vez concluída a construção e a embalagem, há um novo diretório chamado `target` , que contém um ficheiro chamado `StormToHdfs-1.0-SNAPSHOT.jar` . Este ficheiro contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implementar e executar a topologia

1. Utilize o seguinte comando para copiar a topologia para o cluster HDInsight. `CLUSTERNAME`Substitua-o pelo nome do cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Uma vez concluído o upload, utilize o seguinte para ligar ao cluster HDInsight utilizando SSH. `CLUSTERNAME`Substitua-o pelo nome do cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez ligado, utilize o seguinte comando para criar um ficheiro chamado `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Utilize o seguinte texto como o conteúdo do `dev.properties` ficheiro. Reveja conforme necessário com base no seu [esquema URI.](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para guardar o ficheiro, utilize __ctrl + X,__ em __seguida, Y__, e finalmente __Insira__. Os valores deste ficheiro definem o URL de armazenamento e o nome do diretório a que os dados estão escritos.

1. Utilize o seguinte comando para iniciar a topologia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Este comando inicia a topologia usando a estrutura do Fluxo submetendo-a ao nó Nimbus do cluster. A topologia é definida pelo `writetohdfs.yaml` ficheiro incluído no frasco. O `dev.properties` ficheiro é passado como um filtro, e os valores contidos no ficheiro são lidos pela topologia.

## <a name="view-output-data"></a>Ver dados de saída

Para visualizar os dados, utilize o seguinte comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

É apresentada uma lista dos ficheiros criados por esta topologia. A lista a seguir é um exemplo dos dados devolvidos pelos comandos anteriores:

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

As topologias da tempestade funcionam até pararem, ou o aglomerado é apagado. Para parar a topologia, utilize o seguinte comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar a Apache Storm para escrever dados para o armazenamento compatível com HDFS usado pela Apache Storm em HDInsight.

> [!div class="nextstepaction"]
> Descubra [outros exemplos de Apache Storm para HDInsight](apache-storm-example-topology.md)