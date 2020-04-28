---
title: Depurar trabalhos do Apache Spark em execução no Azure HDInsight
description: Use yARN UI, Spark UI e Spark History servidor para rastrear e depurar empregos em funcionamento em um cluster de faíscas em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 75f8c1f77a372d3b9b9e9a5582927f24b99fe9ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190219"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar trabalhos do Apache Spark em execução no Azure HDInsight

Neste artigo, aprende-se a rastrear e depurar empregos da Apache Spark em clusters HDInsight. Depuração usando o Apache Hadoop YARN UI, Spark UI e o Spark History Server. Inicia-se um trabalho spark utilizando um caderno disponível com o cluster Spark, **Machine learning: Análise preditiva dos dados de inspeção alimentar utilizando mlLib**. Utilize os seguintes passos para rastrear uma aplicação que submeteu usando qualquer outra abordagem, por exemplo, **a apresentação de faíscas**.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Devia ter começado a executar o caderno, **[Machine learning: Análise preditiva dos dados da inspeção alimentar utilizando o MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este caderno, siga o link.  

## <a name="track-an-application-in-the-yarn-ui"></a>Acompanhe uma aplicação na YARN UI

1. Lançar a YARN UI. Selecione **fios** sob **os dashboards cluster**.

    ![Portal Azure lança YARN UI](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Em alternativa, também pode lançar a UI YARN a partir da Ambari UI. Para lançar o Ambari UI, selecione **casa Ambari** sob **dashboards cluster**. A partir da Ambari UI, navegue até **ao YARN** > **Quick Links** > o gestor de recursos ativo > Gestor de Recursos **UI.**

2. Como iniciou o trabalho de Spark usando cadernos Jupyter, a aplicação tem o nome **remotesparkmagics** (o nome para todas as aplicações iniciadas a partir dos cadernos). Selecione o ID da aplicação com o nome da aplicação para obter mais informações sobre o trabalho. Esta ação lança a visão da aplicação.

    ![Servidor de histórico de faíscas Encontre o ID da aplicação Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    Para as aplicações que são lançadas a partir dos cadernos Jupyter, o estado está sempre **a CORRER** até sair do caderno.

3. Do ponto de vista da aplicação, pode perfurar mais para descobrir os recipientes associados à aplicação e aos registos (stdout/stderr). Também pode lançar o Spark UI clicando na ligação correspondente ao URL de **Rastreio,** como mostrado abaixo.

    ![Registos de contentores de descarregamento de servidor de histórico de faíscas](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Acompanhe uma aplicação na Spark UI

Na Spark UI, você pode perfurar os trabalhos de Faísca que são gerados pela aplicação que você começou anteriormente.

1. Para lançar o Spark UI, a partir da vista da aplicação, selecione o link com o URL de **Rastreio**, como mostrado na captura do ecrã acima. Você pode ver todos os trabalhos spark que são lançados pela aplicação em execução no caderno Jupyter.

    ![Guia de empregos de servidor de histórico de faíscas](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selecione o separador **Executores** para ver informações de processamento e armazenamento para cada executor. Também pode recuperar a pilha de chamadas selecionando o link **de despejo de fios.**

    ![Separador de executores de servidor de histórico de faíscas](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selecione o separador **Palcos** para ver os estágios associados à aplicação.

    ![Separador de estágios de servidor de histórico de faíscas](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Ver estágios de faísca")

    Cada etapa pode ter múltiplas tarefas para as quais pode ver estatísticas de execução, como mostrado abaixo.

    ![Detalhes do separador de estágios do servidor de histórico de faíscas](./media/apache-spark-job-debugging/view-spark-stages-details.png "Ver detalhes dos estágios de Spark")

4. A partir da página de detalhes do palco, pode lançar a Visualização DAG. Expanda o link **de visualização DAG** no topo da página, como mostrado abaixo.

    ![Ver Spark stages VISUALIZAção DAG](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    O DAG ou o Direct Aclyic Graph representam as diferentes fases da aplicação. Cada caixa azul no gráfico representa uma operação Spark invocada a partir da aplicação.

5. A partir da página de detalhes do palco, também pode lançar a visão da linha do tempo da aplicação. Expanda a ligação timeline do **evento** no topo da página, como mostrado abaixo.

    ![Ver a cronologia do evento de estágios de faísca](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Esta imagem exibe os eventos Spark sob a forma de uma linha do tempo. A visão da linha do tempo está disponível a três níveis, entre empregos, dentro de um emprego e dentro de uma fase. A imagem acima captura a vista do tempo para um determinado estágio.

   > [!TIP]  
   > Se selecionar a caixa de verificação de **zooming Ativa,** pode deslocar-se para a esquerda e para a direita através da vista da linha do tempo.

6. Outros separadores na UI spark fornecem informações úteis sobre a instância Spark também.

   * Separador de armazenamento - Se a sua aplicação criar um RDD, pode encontrar informações no separador Armazenamento.
   * Separador ambiente - Este separador fornece informações úteis sobre a sua instância Spark, tais como:
     * Versão Scala
     * Diretório de registo de eventos associado ao cluster
     * Número de núcleos de executor para a aplicação

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Encontre informações sobre trabalhos concluídos usando o Spark History Server

Uma vez concluído o trabalho, a informação sobre o trabalho é persistiu no Spark History Server.

1. Para lançar o Spark History Server, a partir da página **Overview,** selecione **o servidor de histórico spark** em **dashboards cluster**.

    ![Portal Azure lança servidor histórico Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Lançar Spark History Server1")

   > [!TIP]  
   > Em alternativa, também pode lançar o Spark History Server UI a partir do Ambari UI. Para lançar o Ambari UI, a partir da lâmina overview, **selecione casa Ambari** sob **dashboards cluster**. Do Ambari UI, navegue até **Spark2** > **Quick Links** > **Spark2 History Server UI**.

2. Vê todas as candidaturas completas listadas. Selecione um ID de aplicação para perfurar uma aplicação para obter mais informações.

    ![O servidor de histórico de faíscas completou as aplicações](./media/apache-spark-job-debugging/view-completed-applications.png "Lançar Spark History Server2")

## <a name="see-also"></a>Consulte também

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Debug Apache Spark Jobs usando o servidor de histórico de faíscas estendido](apache-azure-spark-history-server.md)
* [Aplicações de Debug Apache Spark com Kit de Ferramentas Azure para IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
