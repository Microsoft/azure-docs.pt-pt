---
title: Instalar e utilizar Giraph no HDInsight (Hadoop) - Azure
description: Saiba como instalar o Giraph nos clusters do HDInsight baseado em Linux utilizando as ações de Script. Ações de script permitem-lhe personalizar o cluster durante a criação, alterar a configuração de cluster ou instalação de serviços e utilitários.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: a2f964915efda6ce83439c3c1970de58b0467456
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201726"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar o Apache Giraph nos clusters do Hadoop do HDInsight e utilizar Giraph para processar gráficos em grande escala

Saiba como instalar o Apache Giraph num HDInsight cluster. A funcionalidade de ação de script do HDInsight permite-lhe personalizar o seu cluster ao executar um script de bash. Scripts podem ser usados para personalizar os clusters durante e após a criação do cluster.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="whatis"></a>O que é o Giraph

[O Apache Giraph](https://giraph.apache.org/) permite-lhe efetuar o processamento através do Hadoop de gráficos e pode ser utilizado com o Azure HDInsight. Gráficos de modelam relações entre objetos. Por exemplo, as ligações entre routers numa rede de grandes dimensões, como a Internet ou as relações entre as pessoas nas redes sociais. Processamento de gráficos permite-lhe de ponderar sobre as relações entre objetos num gráfico, tais como:

* Identificar amigos potenciais com base nas suas relações atuais.

* Identificar a rota mais curta entre dois computadores numa rede.

* Calcular a classificação de página de páginas da Web.

> [!WARNING]  
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas - Support da Microsoft ajuda a isolar e resolver problemas relacionados com esses componentes.
>
> Componentes personalizados, por exemplo, o Giraph, recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Microsoft Support poderá conseguir resolver o problema. Caso contrário, tem de consultar Comunidades de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Por exemplo, há muitos sites de Comunidade que podem ser utilizados, como: [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Também projetos Apache tem sites de projeto no [ https://apache.org ](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>O que faz o script

Este script executa as seguintes ações:

* Instala o Giraph para `/usr/hdp/current/giraph`

* Copia o `giraph-examples.jar` ficheiro para o armazenamento de predefinido (WASB) para o seu cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar o Giraph usando as ações de Script

Um script de exemplo para instalar o Giraph num cluster do HDInsight está disponível na seguinte localização:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta secção fornece instruções sobre como utilizar o script de exemplo ao criar o cluster com o portal do Azure.

> [!NOTE]  
> Uma ação de script pode ser aplicada usando qualquer um dos seguintes métodos:
> * Azure PowerShell
> * A CLI clássica do Azure
> * O HDInsight .NET SDK
> * Modelos do Azure Resource Manager
> 
> Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Começar a criar um cluster, utilizando os passos em [clusters do HDInsight baseado em Linux criar](hdinsight-hadoop-create-linux-clusters-portal.md), mas não concluir a criação.

2. Na **configuração opcional** secção, selecione **ações de Script**e forneça as seguintes informações:

   * **NOME**: Introduza um nome amigável para a ação de script.

   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Verifique esta entrada.

   * **FUNÇÃO DE TRABALHO**: Deixe esta entrada desmarcada.

   * **ZOOKEEPER**: Deixe esta entrada desmarcada.

   * **PARÂMETROS**: Deixe este campo em branco.

3. Na parte inferior a **ações de Script**, utilize o **selecione** botão para guardar a configuração. Por último, utilize o **selecionar** na parte inferior do **configuração opcional** secção para guardar as informações de configuração opcional.

4. Continuar a criar o cluster, conforme descrito em [clusters do HDInsight baseado em Linux criar](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Como posso utilizar Giraph no HDInsight?

Depois do cluster ter sido criado, utilize os seguintes passos para executar o exemplo de SimpleShortestPathsComputation incluído com o Giraph. Este exemplo utiliza o básico [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) implementação para localizar o caminho mais curto entre objetos num gráfico.

1. Ligar ao cluster do HDInsight com o SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para criar um ficheiro denominado **tiny_graph.txt**:

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

    Estes dados descreve uma relação entre objetos num gráfico direcionado, utilizando o formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um `source_id` objeto e um ou mais `dest_id` objetos. O `edge_value` pode ser considerada como a força ou a distância da ligação entre `source_id` e `dest\_id`.

    Desenhado, e utilizar o valor (ou o peso) como a distância entre objetos, os dados como podem ser o diagrama seguinte:

    ![tiny_graph.txt desenhado como círculos com linhas de variados distância entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Para guardar o ficheiro, utilize **Ctrl + X**, em seguida, **Y**e finalmente **Enter** para aceitar o nome de ficheiro.

4. Utilize o seguinte para armazenar os dados para o armazenamento primário para o seu cluster do HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Execute o exemplo de SimpleShortestPathsComputation com o seguinte comando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Os parâmetros utilizados com este comando são descritos na tabela a seguir:

   | Parâmetro | O que faz |
   | --- | --- |
   | `jar` |O ficheiro jar que contém os exemplos. |
   | `org.apache.giraph.GiraphRunner` |A classe usada para iniciar os exemplos. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |O exemplo que é utilizado. Neste exemplo, computa o caminho mais curto entre 1 de ID e todos os outros IDs no gráfico. |
   | `-ca mapred.job.tracker` |O nó principal para o cluster. |
   | `-vif` |O formato de entrada a utilizar para os dados de entrada. |
   | `-vip` |O ficheiro de dados de entrada. |
   | `-vof` |O formato de saída. Neste exemplo, a ID e o valor como texto simples. |
   | `-op` |A localização de saída. |
   | `-w 2` |O número de funções de trabalho para utilizar. Neste exemplo, 2. |

    Para obter mais informações sobre esses e outros parâmetros utilizados com o Giraph exemplos, consulte a [guia de início rápido Giraph](https://giraph.apache.org/quick_start.html).

6. Assim que a tarefa estiver concluída, os resultados são armazenados no **/example/out/shotestpaths** diretório. Os nomes de ficheiro de saída de começar por **parte-m -** e terminar com um número a indicar o primeiro, o segundo, o ficheiro etc. Utilize o seguinte comando para ver o resultado:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A saída será semelhante ao seguinte texto:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exemplo estiver codificado para começar a 1 de ID de objeto e localizar o caminho mais curto para outros objetos. O resultado está no formato `destination_id` e `distance`. O `distance` é o valor (ou o peso) das margens deslocadas entre 1 de ID de objeto e o ID de destino.

    Visualização de tais dados, pode verificar os resultados ao viajando os caminhos mais curto entre 1 de ID e todos os outros objetos. O caminho mais curto entre ID 1 e 4 de ID é 5. Este valor é a distância total entre <span style="color:orange">ID de 1 e 3</span>e, em seguida <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como círculos com caminhos mais desenhados entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Passos Seguintes

* [Instalar e utilizar Hue em clusters do HDInsight](hdinsight-hadoop-hue-linux.md).
