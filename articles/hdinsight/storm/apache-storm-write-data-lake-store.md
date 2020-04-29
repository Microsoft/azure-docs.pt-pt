---
title: 'Tutorial: HDInsight Apache Storm to Storage - Azure/Data Lake'
description: Tutorial - Aprenda a usar a Tempestade Apache para escrever para o armazenamento compatível com HDFS para Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73241207"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Escreva ao Apache Hadoop HDFS da Tempestade Apache no Azure HDInsight

Este tutorial demonstra como usar a Tempestade Apache para escrever dados para o armazenamento compatível com HDFS usado pela Apache Storm no HDInsight. O HDInsight pode utilizar tanto o Armazenamento Azure como o Armazenamento do Lago Azure Data como armazenamento compatível com HDFS. A tempestade fornece um componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que escreve dados ao HDFS. Este documento fornece informações sobre a escrita para qualquer tipo de armazenamento a partir do HdfsBolt.

O exemplo de topologia usada neste documento baseia-se em componentes que estão incluídos com storm on HDInsight. Pode exigir modificação para trabalhar com o Armazenamento de Lagos De Dados Azure quando utilizado com outros aglomerados de tempestades Apache.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure o cluster com ação de script
> * Construir e embalar a topologia
> * Implementar e executar a topologia
> * Ver dados de saída
> * Parar a topologia

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o `abfs://` Armazenamento Azure, para o `adl://` Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Armazenamento Azure, o URI seria .  Ver também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)

### <a name="example-configuration"></a>Configuração de exemplo

O Seguinte YAML é `resources/writetohdfs.yaml` um excerto do ficheiro incluído no exemplo. Este ficheiro define a topologia da tempestade usando a estrutura [de Fluxo](https://storm.apache.org/releases/current/flux.html) para a Tempestade Apache.

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

* `syncPolicy`: Define quando os ficheiros são sincronizados/lavados ao sistema de ficheiros. Neste exemplo, a cada 1000 tuples.
* `fileNameFormat`: Define o padrão de nome de caminho e ficheiro a utilizar ao escrever ficheiros. Neste exemplo, o caminho é fornecido no tempo de execução utilizando um filtro, e a extensão do ficheiro é `.txt`.
* `recordFormat`: Define o formato interno dos ficheiros escritos. Neste exemplo, os `|` campos são delimitados pelo personagem.
* `rotationPolicy`: Define quando rodar ficheiros. Neste exemplo, não é realizada qualquer rotação.
* `hdfs-bolt`: Utiliza os componentes anteriores como `HdfsBolt` parâmetros de configuração para a classe.

Para obter mais informações [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)sobre a estrutura do Fluxo, consulte .

## <a name="configure-the-cluster"></a>Configurar o cluster

Por padrão, a Tempestade no HDInsight `HdfsBolt` não inclui os componentes que usam para comunicar com o Armazenamento Azure ou armazenamento de data lake no caminho de classe storm. Utilize a seguinte ação de guião `extlib` para adicionar estes componentes ao diretório de Storm no seu cluster:

| Propriedade | Valor |
|---|---|
|Tipo de script |- Personalizado|
|Roteiro de bash URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipo de nó(s) |Nimbus, Supervisor|
|Parâmetros |Nenhuma|

Para obter informações sobre a utilização deste script com o seu cluster, consulte os [clusters Personalizados HDInsight utilizando](./../hdinsight-hadoop-customize-cluster-linux.md) o documento de ações de script.

## <a name="build-and-package-the-topology"></a>Construir e embalar a topologia

1. Descarregue o [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) projeto de exemplo do seu ambiente de desenvolvimento.

2. A partir de um pedido de comando, terminal ou sessão de concha, altere os diretórios para a raiz do projeto descarregado. Para construir e embalar a topologia, use o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Uma vez que a construção e embalagem `target`complete, há um `StormToHdfs-1.0-SNAPSHOT.jar`novo diretório chamado , que contém um ficheiro chamado . Este ficheiro contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implementar e executar a topologia

1. Utilize o seguinte comando para copiar a topologia para o cluster HDInsight. Substitua-o `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Uma vez que o upload esteja concluído, utilize o seguinte para se ligar ao cluster HDInsight utilizando o SSH. Substitua-o `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez ligado, utilize o seguinte `dev.properties`comando para criar um ficheiro chamado:

    ```bash
    nano dev.properties
    ```

1. Utilize o seguinte texto como `dev.properties` conteúdo do ficheiro. Reveja conforme necessário com base no seu [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para guardar o ficheiro, utilize __ctrl + X,__ em __seguida, Y__, e finalmente __Enter__. Os valores deste ficheiro definiram o URL de armazenamento e o nome de diretório a que os dados estão escritos.

1. Utilize o seguinte comando para iniciar a topologia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Este comando inicia a topologia utilizando a estrutura do Fluxo, submetendo-a ao nó Nimbus do cluster. A topologia é `writetohdfs.yaml` definida pelo ficheiro incluído no jarro. O `dev.properties` ficheiro é passado como filtro, e os valores contidos no ficheiro são lidos pela topologia.

## <a name="view-output-data"></a>Ver dados de saída

Para visualizar os dados, utilize o seguinte comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

É apresentada uma lista dos ficheiros criados por esta topologia. A lista que se segue é um exemplo dos dados devolvidos pelos comandos anteriores:

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

As topoologias da tempestade funcionam até pararem, ou o aglomerado é apagado. Para parar a topologia, utilize o seguinte comando:

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

Neste tutorial, aprendeu a usar a Tempestade Apache para escrever dados para o armazenamento compatível com HDFS usado pela Apache Storm no HDInsight.

> [!div class="nextstepaction"]
> Descubra outros exemplos da [Tempestade Apache para hDInsight](apache-storm-example-topology.md)