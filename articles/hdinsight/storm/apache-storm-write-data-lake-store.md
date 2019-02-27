---
title: O Apache Storm escrever no armazenamento do armazenamento/Data Lake - Azure HDInsight
description: Saiba como utilizar o Apache Storm para escrever para o armazenamento compatível com HDFS para HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 34056b3a562d3110569e05c5c50ae4d9dae2375a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868141"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>Escrever para Apache Hadoop HDFS do Apache Storm no HDInsight

Aprenda a usar [Apache Storm](https://storm.apache.org/) para escrever dados para o armazenamento compatível com HDFS utilizado pelo Apache Storm no HDInsight. HDInsight pode utilizar o armazenamento do Azure e o armazenamento do Azure Data Lake como armazenamento compatível com HDFS. O Storm fornece um [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) componente que escreve dados no HDFS. Este documento fornece informações sobre como escrever para ambos os tipos de armazenamento do HdfsBolt. 

> [!IMPORTANT]  
> A topologia de exemplo utilizada neste documento baseia-se nos componentes que estão incluídas com o Storm no HDInsight. Ele pode exigir modificações para funcionar com o armazenamento do Azure Data Lake quando utilizado com outros clusters do Apache Storm.

## <a name="get-the-code"></a>Obter o código

O projeto que contém esta topologia está disponível como uma transferência a partir [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Para compilar esse projeto, terá a seguinte configuração para o seu ambiente de desenvolvimento:

* [Java JDK 1.8](https://aka.ms/azure-jdks) ou superior. O HDInsight 3.5 ou superior requer o Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

As variáveis de ambiente que se seguem podem ser definidas quando instala o Java e o JDK na sua estação de trabalho de desenvolvimento. No entanto, deve certificar-se de que existem e que contêm os valores corretos para o seu sistema.

* `JAVA_HOME` - deve apontar para o diretório onde o JDK está instalado.
* `PATH` - deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório onde o Maven está instalado.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Como utilizar o HdfsBolt com o HDInsight

> [!IMPORTANT]  
> Antes de utilizar o HdfsBolt com o Storm no HDInsight, primeiro tem de utilizar uma ação de script para copiar ficheiros jar necessária para o `extpath` para o Storm. Para obter mais informações, consulte a configurar a secção de cluster.

O HdfsBolt usa o esquema de ficheiro por si para compreender como escrever no HDFS. Com o HDInsight, utilize um dos seguintes esquemas:

* `wasb://`: Utilizado com uma conta de armazenamento do Azure.
* `abfs://`: Utilizado com uma geração 2 Lake armazenamento de dados do Azure.
* `adl://`: Utilizado com o Azure Data Lake Storage Gen1.

A tabela seguinte fornece exemplos de como utilizar o esquema de ficheiro para diferentes cenários:

| Esquema | Notas |
| ----- | ----- |
| `wasb:///` | A conta de armazenamento predefinida é um contentor de BLOBs numa conta de armazenamento do Azure |
| `abfs:///` | A conta de armazenamento predefinida é um diretório numa conta de geração 2 de armazenamento do Azure Data Lake |
| `adl:///` | A conta de armazenamento predefinida é um diretório na geração 1 de armazenamento do Azure Data Lake. Durante a criação do cluster, especifique o diretório no armazenamento do Data Lake que é a raiz do HDFS do cluster. Por exemplo, o `/clusters/myclustername/` diretório. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Uma conta de armazenamento do Azure (adicionais) de não-padrão associada ao cluster. |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | Uma conta de armazenamento do Azure (adicionais) de não-padrão associada ao cluster. |
| `adl://STORENAME/` | A raiz do armazenamento do Data Lake utilizados pelo cluster. Esse esquema permite-lhe aceder aos dados localizados fora do diretório que contém o sistema de ficheiros do cluster. |

Para obter mais informações, consulte a [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) referência em Apache.org.

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

Por predefinição, o Storm no HDInsight não inclui os componentes que HdfsBolt utiliza para comunicar com o armazenamento do Azure ou o armazenamento do Data Lake no caminho da classe do Storm. Utilize a ação de script seguinte para adicionar esses componentes para o `extlib` diretório para o Storm no seu cluster:

* URI do script: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Nós para aplicar a: Nimbus, Supervisor
* Parâmetros: Nenhuma

Para obter informações sobre como utilizar este script com o seu cluster, consulte a [HDInsight personalizar clusters com ações de script](./../hdinsight-hadoop-customize-cluster-linux.md) documento.

## <a name="build-and-package-the-topology"></a>Criar e empacotar a topologia

1. Transfira o projeto de exemplo em [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) ao seu ambiente de desenvolvimento.

2. A partir de uma linha de comandos, terminal ou shell sessão, diretórios de alteração para a raiz do projeto transferido. Para criar e empacotar a topologia, utilize o seguinte comando:
   
        mvn compile package
   
    Uma vez concluída a compilação e empacotamento, há um novo diretório com o nome `target`, que contém um ficheiro denominado `StormToHdfs-1.0-SNAPSHOT.jar`. Este ficheiro contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implementar e executar a topologia

1. Utilize o seguinte comando para copiar a topologia para o cluster do HDInsight. Substitua **utilizador** com o nome de utilizador SSH que utilizou quando criou o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criou o utilizador SSH para o cluster. Se utilizou uma chave pública em vez de uma palavra-passe, poderá ter de utilizar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
   
   > [!NOTE]  
   > Para obter mais informações sobre como utilizar `scp` com o HDInsight, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Após a conclusão do carregamento, utilize o seguinte para ligar ao cluster HDInsight através de SSH. Substitua **utilizador** com o nome de utilizador SSH que utilizou quando criou o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criou o utilizador SSH para o cluster. Se utilizou uma chave pública em vez de uma palavra-passe, poderá ter de utilizar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
   
   Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Assim que estiver ligado, utilize o seguinte comando para criar um arquivo chamado `dev.properties`:

        nano dev.properties

4. Utilize o seguinte texto como conteúdo do `dev.properties` ficheiro:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > Este exemplo assume que o cluster utiliza uma conta de armazenamento do Azure como armazenamento predefinido. Se o cluster utiliza a geração 2 de armazenamento do Azure Data Lake, utilize `hdfs.url: abfs:///` em vez disso. Se o cluster utiliza Gen1 de armazenamento do Azure Data Lake, utilize `hdfs.url: adl:///` em vez disso.
    
    Para guardar o ficheiro, utilize __Ctrl + X__, em seguida, __Y__e finalmente __Enter__. Os valores existentes neste ficheiro de definir o URL de armazenamento do Data Lake e o nome do diretório que dados são gravados.

3. Utilize o seguinte comando para iniciar a topologia:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Este comando inicia a topologia usando a estrutura de fluxo, enviando-a para o nó Nimbus do cluster. A topologia é definida pelo `writetohdfs.yaml` ficheiro incluído no jar. O `dev.properties` arquivo é transmitido como um filtro e os valores contidos no ficheiro são lidas pela topologia.

## <a name="view-output-data"></a>Ver dados de saída

Para ver os dados, utilize o seguinte comando:

    hdfs dfs -ls /stormdata/

É apresentada uma lista dos ficheiros criados por esta topologia.

A lista seguinte é um exemplo dos dados retornados por comandos anteriores:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Parar a topologia

Topologias do Storm executar até serem parados ou o cluster é eliminado. Para parar a topologia, utilize o seguinte comando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Elimina o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Apache Storm para escrever no armazenamento do Azure e o armazenamento do Azure Data Lake, detetar outros [exemplos de Apache Storm para HDInsight](apache-storm-example-topology.md).

## <a name="see-also"></a>Consulte também
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
