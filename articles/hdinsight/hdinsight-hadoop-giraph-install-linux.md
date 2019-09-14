---
title: Instalar e usar o O giraph no Azure HDInsight
description: Saiba como instalar o O giraph em clusters HDInsight usando ações de script. Você pode usar O giraph para fazer o processamento de grafo no Apache Hadoop na nuvem do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962036"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar o Apache O giraph em clusters Hadoop do HDInsight e usar o O giraph para processar grafos em grande escala

Saiba como instalar o Apache O giraph em um cluster HDInsight. O recurso de ação de script do HDInsight permite que você personalize seu cluster executando um script bash. Os scripts podem ser usados para personalizar clusters durante e após a criação do cluster.

## <a name="whatis"></a>O que é O giraph

O [Apache o giraph](https://giraph.apache.org/) permite executar o processamento de grafo usando o Hadoop e pode ser usado com o Azure HDInsight. Os gráficos modelam relações entre objetos. Por exemplo, as conexões entre roteadores em uma rede grande, como a Internet, ou relações entre pessoas em redes sociais. O processamento de grafo permite que você detenha um motivo sobre as relações entre objetos em um grafo, como:

* Identificação de possíveis amigos com base em suas relações atuais.

* Identificar a rota mais curta entre dois computadores em uma rede.

* Calculando a classificação de página de páginas da Web.

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados-Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados, como O giraph, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Suporte da Microsoft pode ser capaz de resolver o problema. Caso contrário, você deve consultar as comunidades de código-fonte aberto em que a profunda experiência para essa tecnologia é encontrada. Por exemplo, há muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache [https://apache.org](https://apache.org)têm sites de projeto em, por exemplo: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>O que o script faz

Esse script executa as seguintes ações:

* Instala o O giraph em`/usr/hdp/current/giraph`

* Copia o `giraph-examples.jar` arquivo para o armazenamento padrão (WASB) para o cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar o O giraph usando ações de script

Um script de exemplo para instalar o O giraph em um cluster HDInsight está disponível no seguinte local:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta seção fornece instruções sobre como usar o script de exemplo ao criar o cluster usando o portal do Azure.

> [!NOTE]  
> Uma ação de script pode ser aplicada usando qualquer um dos seguintes métodos:
> * Azure PowerShell
> * A CLI do Azure
> * O SDK do .NET do HDInsight
> * Modelos do Azure Resource Manager
> 
> Você também pode aplicar ações de script a clusters já em execução. Para obter mais informações, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Comece a criar um cluster usando as etapas em [Criar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md), mas não conclua a criação.

2. Na seção **configuração opcional** , selecione **ações de script**e forneça as seguintes informações:

   * **NOME**: Insira um nome amigável para a ação de script.

   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **CABEÇALHO**: Verifique esta entrada.

   * **TRABALHO**: Deixe essa entrada desmarcada.

   * **ZOOKEEPER**: Deixe essa entrada desmarcada.

   * **PARÂMETROS**: Deixe esse campo em branco.

3. Na parte inferior das **ações de script**, use o botão **selecionar** para salvar a configuração. Por fim, use o botão **selecionar** na parte inferior da seção **configuração opcional** para salvar as informações de configuração opcionais.

4. Continue criando o cluster conforme descrito em [Criar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Como fazer usar o O giraph no HDInsight?

Depois que o cluster tiver sido criado, use as etapas a seguir para executar o exemplo de SimpleShortestPathsComputation incluído com O giraph. Este exemplo usa a implementação básica de [pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) para localizar o caminho mais curto entre objetos em um grafo.

1. Ligar ao cluster do HDInsight com o SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para criar um arquivo chamado **tiny_graph. txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Utilize o seguinte texto como o conteúdo desse ficheiro:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Esses dados descrevem uma relação entre objetos em um grafo direcionado, usando o formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um `source_id` objeto e um ou mais `dest_id` objetos. O `edge_value` pode ser considerado como a força ou a distância da conexão entre `source_id` e `dest\_id`.

    Desenhado e usando o valor (ou peso) como a distância entre os objetos, os dados podem ser semelhantes ao diagrama a seguir:

    ![tiny_graph. txt desenhado como círculos com linhas de distância variável entre](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Para salvar o arquivo, use **Ctrl + X**, em seguida, **Y**e, por fim, **Enter** para aceitar o nome do arquivo.

4. Use o seguinte para armazenar os dados no armazenamento primário para o cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Execute o exemplo SimpleShortestPathsComputation usando o seguinte comando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Os parâmetros usados com esse comando são descritos na tabela a seguir:

   | Parâmetro | O que faz |
   | --- | --- |
   | `jar` |O arquivo JAR que contém os exemplos. |
   | `org.apache.giraph.GiraphRunner` |A classe usada para iniciar os exemplos. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |O exemplo usado. Neste exemplo, ele computa o caminho mais curto entre a ID 1 e todas as outras IDs no grafo. |
   | `-ca mapred.job.tracker` |O cabeçalho para o cluster. |
   | `-vif` |O formato de entrada a ser usado para os dados de entrada. |
   | `-vip` |O arquivo de dados de entrada. |
   | `-vof` |O formato de saída. Neste exemplo, ID e valor como texto sem formatação. |
   | `-op` |O local de saída. |
   | `-w 2` |O número de trabalhos a serem usados. Neste exemplo, 2. |

    Para obter mais informações sobre esses e outros parâmetros usados com exemplos de O giraph, consulte o guia de [início rápido do o giraph](https://giraph.apache.org/quick_start.html).

6. Depois que o trabalho for concluído, os resultados serão armazenados no diretório **/example/out/shortestpaths** . Os nomes de ficheiro de saída de começar por **parte-m -** e terminar com um número a indicar o primeiro, o segundo, o ficheiro etc. Use o seguinte comando para exibir a saída:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A saída é semelhante ao seguinte texto:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    O exemplo de SimpleShortestPathComputation é embutido em código para iniciar com a ID de objeto 1 e localizar o caminho mais curto para outros objetos. A saída está no formato de `destination_id` e. `distance` O `distance` é o valor (ou peso) das bordas viajadas entre a ID de objeto 1 e a ID de destino.

    Visualizando esses dados, você pode verificar os resultados viajando os caminhos mais curtos entre a ID 1 e todos os outros objetos. O caminho mais curto entre a ID 1 e a ID 4 é 5. Esse valor é a distância total entre a <span style="color:orange">ID 1 e 3</span>e, em seguida, a <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como círculos com caminhos mais curtos desenhados entre](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Passos seguintes

* [Instale e use o matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
