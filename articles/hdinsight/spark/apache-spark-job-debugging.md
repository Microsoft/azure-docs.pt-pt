---
title: Depurar trabalhos do Apache Spark em execução no Azure HDInsight
description: Use o servidor YARN UI, Spark UI e Spark History para rastrear e depurar empregos em execução num cluster spark em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 0dd250f0a8f67d7e370b8ff453e9cff4d88b7896
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866102"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar trabalhos do Apache Spark em execução no Azure HDInsight

Neste artigo, você aprende a rastrear e depurar os empregos de Apache Spark correndo em clusters HDInsight. Debug usando o Apache Hadoop YARN UI, Spark UI e o Spark History Server. Inicia-se um trabalho de Spark utilizando um caderno disponível com o cluster Spark, **Machine learning: Análise preditiva em dados de inspeção alimentar utilizando MLLib**. Utilize os seguintes passos para acompanhar uma aplicação que submeteu usando qualquer outra abordagem, por exemplo, **apresentação de faíscas**.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Devia ter começado a executar o caderno, **[Machine learning: Análise preditiva sobre dados de inspeção alimentar utilizando mLLib](apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este caderno, siga o link.  

## <a name="track-an-application-in-the-yarn-ui"></a>Acompanhe uma aplicação na UI YARN

1. Lançar a UI YARN. Selecione **Fios** sob **os dashboards cluster**.

    :::image type="content" source="./media/apache-spark-job-debugging/launch-apache-yarn-ui.png" alt-text="Portal Azure lança YARN UI" border="true":::

   > [!TIP]  
   > Em alternativa, também pode lançar o UI YARN a partir da UI Ambari. Para lançar o Ambari UI, selecione **Ambari casa** sob **painéis de cluster.** A partir da UI Ambari, navegue até **YARN**  >  **Quick Links** > o gestor de recursos ativo > Gestor de Recursos **UI**.

2. Como iniciou o trabalho de Spark usando Os Cadernos Jupyter, a aplicação tem o nome **de remotesparkmagics** (o nome para todas as aplicações partiu dos cadernos). Selecione o ID da aplicação contra o nome da aplicação para obter mais informações sobre o trabalho. Esta ação lança a visão da aplicação.

    :::image type="content" source="./media/apache-spark-job-debugging/find-application-id1.png" alt-text="Servidor histórico de faíscas Encontrar ID da aplicação Spark" border="true":::

    Para estas aplicações que são lançadas a partir dos Cadernos Jupyter, o estado está sempre **A CORRER** até sair do caderno.

3. A partir da vista da aplicação, pode aprofundar-se mais para descobrir os recipientes associados à aplicação e os registos (stdout/stderr). Também pode lançar o Spark UI clicando na ligação correspondente ao **URL de rastreio,** como mostrado abaixo.

    :::image type="content" source="./media/apache-spark-job-debugging/download-container-logs.png" alt-text="Servidor de histórico de faíscas descarrega registos de contentores" border="true":::

## <a name="track-an-application-in-the-spark-ui"></a>Acompanhe uma aplicação no Spark UI

Na Spark UI, você pode aprofundar os trabalhos de Faísca que são gerados pela aplicação que você começou mais cedo.

1. Para lançar o Spark UI, a partir da vista de aplicação, selecione o link contra o **URL de rastreio,** como mostrado na captura do ecrã acima. Você pode ver todos os trabalhos spark que são lançados pela aplicação em execução no Caderno Jupyter.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-jobs.png" alt-text="Separador de empregos de servidor de histórico de faíscas" border="true":::

2. Selecione o **separador Executors** para ver as informações de processamento e armazenamento para cada executor. Também pode recuperar a pilha de chamadas selecionando o link **de despejo de fio.**

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-executors.png" alt-text="Separador de executores de servidor de histórico de faíscas" border="true":::

3. Selecione o **separador Estágios** para ver as fases associadas à aplicação.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-stages.png " alt-text="Separador de estágios de servidor de histórico de faíscas" border="true":::

    Cada fase pode ter múltiplas tarefas para as quais pode ver estatísticas de execução, como mostrado abaixo.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-details.png " alt-text="Spark histórico estágios detalhes do separador" border="true":::

4. A partir da página de detalhes do palco, você pode lançar VISUALIZAção DAG. Expanda o link **de visualização DAG** no topo da página, como mostrado abaixo.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png" alt-text="Ver estágios de faísca visualização DAG" border="true":::

    O DAG ou o Direct Aclyic Graph representam as diferentes fases da aplicação. Cada caixa azul no gráfico representa uma operação Spark invocada a partir da aplicação.

5. A partir da página de detalhes de palco, também pode lançar a visão da linha do tempo da aplicação. Expanda a ligação timeline do **evento** no topo da página, como mostrado abaixo.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png" alt-text="Ver linha do tempo do evento spark stages" border="true":::

    Esta imagem exibe os eventos Spark sob a forma de uma linha do tempo. A visão da linha do tempo está disponível a três níveis, através de empregos, dentro de um trabalho, e dentro de uma fase. A imagem acima captura a visão da linha do tempo para um determinado estágio.

   > [!TIP]  
   > Se selecionar a caixa **de verificação de zoom de ativação,** pode deslocar-se para a esquerda e para a direita através da vista da linha do tempo.

6. Outros separadores no Spark UI fornecem informações úteis sobre o exemplo de Spark também.

   * Separador de armazenamento - Se a sua aplicação criar um RDD, pode encontrar informações no separador Armazenamento.
   * Separador ambiente - Este separador fornece informações úteis sobre o seu exemplo de Spark, como:
     * Versão Scala
     * Diretório de registo de eventos associado ao cluster
     * Número de núcleos de executor para a aplicação

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Encontre informações sobre trabalhos concluídos utilizando o Spark History Server

Uma vez concluído o trabalho, a informação sobre o trabalho é persistiu no Spark History Server.

1. Para lançar o Spark History Server, a partir da página **'Vista Geral',** selecione **o servidor histórico spark** sob os **dashboards do Cluster**.

    :::image type="content" source="./media/apache-spark-job-debugging/launch-spark-history-server.png " alt-text="Portal Azure lança servidor histórico spark" border="true":::

   > [!TIP]  
   > Em alternativa, também pode lançar o Spark History Server UI a partir da UI Ambari. Para lançar o UI Ambari, a partir da lâmina overview, selecione **Ambari casa** sob **os dashboards cluster**. A partir do Ambari UI, navegue até **Spark2**  >  **Quick Links**  >  **Spark2 History Server UI**.

2. Vê todas as candidaturas completas listadas. Selecione um ID de aplicação para aprofundar uma aplicação para mais informações.

    :::image type="content" source="./media/apache-spark-job-debugging/view-completed-applications.png " alt-text="Servidor de histórico de faíscas completou aplicações" border="true":::

## <a name="see-also"></a>Ver também

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Debug Apache Spark Jobs usando servidor de histórico de faíscas estendido](apache-azure-spark-history-server.md)
* [Aplicações Debug Apache Spark com Azure Toolkit para IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
