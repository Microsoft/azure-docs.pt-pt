---
title: Tutorial – utilizar o Apache Storm para escrever no armazenamento do armazenamento/Data Lake - Azure HDInsight
description: Tutorial – Saiba como utilizar o Apache Storm para escrever para o armazenamento compatível com HDFS para Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428349"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Escrever para Apache Hadoop HDFS do Apache Storm no HDInsight do Azure

Este tutorial demonstra como utilizar o Apache Storm para escrever dados para o armazenamento compatível com HDFS utilizado pelo Apache Storm no HDInsight. HDInsight pode utilizar o armazenamento do Azure e o armazenamento do Azure Data Lake como armazenamento compatível com HDFS. O Storm fornece um [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) componente que escreve dados no HDFS. Este documento fornece informações sobre como escrever para ambos os tipos de armazenamento do HdfsBolt.

A topologia de exemplo utilizada neste documento baseia-se nos componentes que estão incluídas com o Storm no HDInsight. Ele pode exigir modificações para funcionar com o armazenamento do Azure Data Lake quando utilizado com outros clusters do Apache Storm.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o cluster com a ação de script
> * Criar e empacotar a topologia
> * Implementar e executar a topologia
> * Ver dados de saída
> * Parar a topologia

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de desenvolvimento Java (JDK) versão 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) , de acordo com o Apache.  A maven é um projeto de criar o sistema de projetos de Java.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para seu armazenamento primário de clusters. Isso seria `wasb://` armazenamento do Azure, `abfs://` para a geração 2 de armazenamento do Azure Data Lake ou `adl://` para geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento do Azure ou de geração 2 de armazenamento do Data Lake, o URI seria `wasbs://` ou `abfss://`, respetivamente, também, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Configuração de exemplo

O YAML seguinte é um extrato do `resources/writetohdfs.yaml` ficheiro incluído no exemplo. Esse arquivo define a topologia de Storm através da [fluxo](https://storm.apache.org/releases/1.1.2/flux.html) framework para o Apache Storm.

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

* `syncPolicy`: Define quando os ficheiros estão sincronizado/liberadas para o sistema de ficheiros. Neste exemplo, todas as cadeias de identificação de 1000.
* `fileNameFormat`: Define o padrão de nome de ficheiro e caminho ser usada para criar arquivos. Neste exemplo, o caminho é fornecido no tempo de execução usando um filtro e a extensão de ficheiro é `.txt`.
* `recordFormat`: Define o formato interno dos ficheiros escritos. Neste exemplo, os campos são delimitados pelo `|` caráter.
* `rotationPolicy`: Define quando deve rodar os ficheiros. Neste exemplo, é executada sem rotação.
* `hdfs-bolt`: Utiliza os componentes anteriores como parâmetros de configuração para o `HdfsBolt` classe.

Para obter mais informações sobre a estrutura de fluxo, consulte [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Configurar o cluster

Por predefinição, o Storm no HDInsight não inclui os componentes que `HdfsBolt` utiliza para comunicar com o armazenamento do Azure ou o armazenamento do Data Lake no caminho da classe do Storm. Utilize a ação de script seguinte para adicionar esses componentes para o `extlib` diretório para o Storm no seu cluster:

| Propriedade | Value |
|---|---|
|Tipo de script |-Personalizado|
|URI do script de bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|O tipo ou tipos de nó |Nimbus, Supervisor|
|Parâmetros |Nenhuma|

Para obter informações sobre como utilizar este script com o seu cluster, consulte a [HDInsight personalizar clusters com ações de script](./../hdinsight-hadoop-customize-cluster-linux.md) documento.

## <a name="build-and-package-the-topology"></a>Criar e empacotar a topologia

1. Transfira o projeto de exemplo em [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) ao seu ambiente de desenvolvimento.

2. A partir de uma linha de comandos, terminal ou shell sessão, diretórios de alteração para a raiz do projeto transferido. Para criar e empacotar a topologia, utilize o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Uma vez concluída a compilação e empacotamento, há um novo diretório com o nome `target`, que contém um ficheiro denominado `StormToHdfs-1.0-SNAPSHOT.jar`. Este ficheiro contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implementar e executar a topologia

1. Utilize o seguinte comando para copiar a topologia para o cluster do HDInsight. Substitua `CLUSTERNAME` com o nome do cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Após a conclusão do carregamento, utilize o seguinte para ligar ao cluster HDInsight através de SSH. Substitua `CLUSTERNAME` com o nome do cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Assim que estiver ligado, utilize o seguinte comando para criar um arquivo chamado `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Utilize o seguinte texto como conteúdo do `dev.properties` ficheiro. Correção conforme necessário com base no seu [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para guardar o ficheiro, utilize __Ctrl + X__, em seguida, __Y__e finalmente __Enter__. Os valores existentes neste ficheiro de definir o URL de armazenamento e o nome do diretório que dados são gravados.

1. Utilize o seguinte comando para iniciar a topologia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Este comando inicia a topologia usando a estrutura de fluxo, enviando-a para o nó Nimbus do cluster. A topologia é definida pelo `writetohdfs.yaml` ficheiro incluído no jar. O `dev.properties` arquivo é transmitido como um filtro e os valores contidos no ficheiro são lidas pela topologia.

## <a name="view-output-data"></a>Ver dados de saída

Para ver os dados, utilize o seguinte comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

É apresentada uma lista dos ficheiros criados por esta topologia. A lista seguinte é um exemplo dos dados retornados por comandos anteriores:

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

Topologias do Storm executar até serem parados ou o cluster é eliminado. Para parar a topologia, utilize o seguinte comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o Apache Storm para escrever dados para o armazenamento compatível com HDFS utilizado pelo Apache Storm no HDInsight.

> [!div class="nextstepaction"]
> Descubra outras [exemplos do Apache Storm para HDInsight](apache-storm-example-topology.md)