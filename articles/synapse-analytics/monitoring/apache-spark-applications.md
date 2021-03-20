---
title: Monitor Apache Spark aplicações usando o Synapse Studio
description: Utilize o Synapse Studio para monitorizar as suas aplicações Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584535"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Use o Synapse Studio para monitorizar as suas aplicações Apache Spark

Com a Azure Synapse Analytics, você pode usar Apache Spark para executar cadernos, empregos e outros tipos de aplicações nas suas piscinas Apache Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas aplicações Apache Spark, permitindo-lhe estar atento ao estado mais recente, problemas e progressos.

Este tutorial abrange as seguintes tarefas:

* Monitor que executa a aplicação de faíscas Apache
* Ver aplicação apache spark concluída
* Ver Pedido de Faísca Apache cancelado
* Debug falhou aplicação apache spark

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:

- Um espaço de trabalho do Estúdio Synapse. Para obter instruções, consulte Criar um espaço de [trabalho do Estúdio Synapse](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Uma piscina Apache Spark.

## <a name="view-apache-spark-applications"></a>Ver aplicações Apache Spark 
Pode ver todas as aplicações Apache Spark a partir de   ->  **aplicações** Monitor Apache Spark .
   ![aplicações de faísca apache](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Ver aplicação concluída do Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações concluídas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

  ![selecionar trabalho concluído](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Verifique as **tarefas preenchidas,** **estado** e **duração total**.

2. Atualizar consulta de registo.

3. Abra o link do servidor histórico apache Spark clicando no **servidor histórico de Spark**.

4. Consulte a informação **do Resumo.**

5. Verifique os **Registos.** Pode selecionar diferentes tipos de registos da lista de drop-down e pode descarregar a informação de registo clicando **em registos de descarregamento** e verificar a caixa de verificação de **erros e avisos** do Filtro para filtrar os erros e avisos de que necessita.

6. Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Por defeito, o gráfico mostra todos os trabalhos. Pode filtrar esta vista por **Job ID**.

7. Por predefinição, o **visor Progress** é selecionado. Pode verificar o fluxo de dados selecionando a Duração Escrita de **Leitura de Progresso** na lista /  /  /  de desistências do **Visor.**

8. Para reproduzir o trabalho, clique no botão **Reprodução.** Pode clicar no botão **Stop** a qualquer momento para parar.

9. Utilize o rolo do rato ou a barra de deslocação para ampliar e ampliar o gráfico de trabalho, também pode selecionar **Zoom to Fit** para que encaixe no ecrã.

10. O nó gráfico de trabalho apresenta as seguintes informações de cada fase:

    * A identificação.

    * Nome ou descrição.

    * Número total de tarefas.

    * Os dados dizem: a soma do tamanho da entrada e baralhar o tamanho da leitura.

    * Os dados escrevem: a soma do tamanho da saída e baralhar o tamanho.

    * Tempo de execução: o tempo entre a hora de início da primeira tentativa e o tempo de conclusão da última tentativa.

    * Contagem de linha: a soma dos registos de entradas, registos de saída, baralhar discos de leitura e baralhar discos de escrita.

    * O progresso.

     ![ver trabalho concluído](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Clique em **Ver detalhes** no gráfico e os detalhes do palco serão apresentados.

    ![detalhes para o estágio](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Monitor que executa a aplicação Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações Apache Spark que estão em execução, selecione a aplicação Apache Spark e veja os detalhes. Se a aplicação Apache Spark ainda estiver em funcionamento, pode monitorizar o progresso.

   ![selecionar trabalho de execução](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Verifique as **tarefas preenchidas,** **estado** e **duração total**.

2. **Cancele** o pedido de Faísca Apache.

3. **Refrescar** Consulta de registo.

4. Clique no botão **Spark UI** para ir à página Spark Job.

5. Veja o gráfico. Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Consulte o passo 6,7,8,9,10 de [Vista concluído pedido Apache Spark](#view-completed-apache-spark-application).

6. Consulte a informação **do Resumo.**

7. Verifique os diagnósticos no **separador Diagnóstico.**

8. Verifique os **Registos** neste separador. Pode selecionar diferentes tipos de registos da lista de drop-down e pode descarregar a informação de registo clicando **em registos de descarregamento** e verificar a caixa de verificação de **erros e avisos** do Filtro para filtrar os erros e avisos de que necessita.

    ![ver trabalho de execução](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Ver pedido cancelado da Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações canceladas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

 ![selecionar trabalho cancelado](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Verifique as **tarefas preenchidas,** **estado** e **duração total**.

2. Refresque a consulta de log.

3. Abra o link do servidor de histórico Apache clicando no **servidor histórico de Spark**.

4. Veja o gráfico. Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Consulte o passo 6,7,8,9,10 de [Vista concluído pedido Apache Spark](#view-completed-apache-spark-application).

5. Consulte a informação **do Resumo.**

6. Verifique os **Registos.** Pode selecionar diferentes tipos de registos da lista de drop-down e pode descarregar a informação de registo clicando em registos **de descarregamento** e verificar a caixa de verificação de **erros e avisos** do Filtro para filtrar os erros e avisos de que necessita.

7. Clique em **Ver detalhes** no gráfico e os detalhes do palco serão apresentados.

   ![ver trabalho cancelado](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debug falhou pedido de Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações falhadas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

![selecionar trabalho falhado](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Verifique as **tarefas preenchidas,** **estado** e **duração total**.

2. Atualizar consulta de registo.

3. Abra o link do servidor histórico apache Spark clicando no **servidor histórico de Spark**.

4. Veja o gráfico. Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Consulte o Passo 6,7,8,9,10 de [Vista concluído pedido Apache Spark](#view-completed-apache-spark-application)

5. Consulte a informação **do Resumo.**

6. Verifique a informação de erro.

   ![informação de trabalho falhado](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Compare aplicações Apache Spark
Clique no botão **Compare aplicações** e escolha uma aplicação para comparar desempenho, os utilizadores podem intuitivamente ver a diferença entre as duas aplicações.

![comparar aplicações](./media/how-to-monitor-spark-applications/compare-applications.png)

1. Utilize o rato para pairar sobre uma aplicação e, em seguida, é apresentado o ícone **de aplicações Compare.**

2. Clique no ícone **de aplicações Compare** e a página de aplicações Compare aparecerá.

3. Clique no botão **Escolha o botão de aplicação** para abrir Escolha a página **de aplicação de comparação.**

4. Ao escolher a aplicação de comparação, os utilizadores precisam de introduzir o URL da aplicação ou escolher entre a lista recorrente. Em seguida, clique no botão **OK.** 

   ![escolher aplicação de comparação](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. O resultado da comparação será apresentado na página de aplicações de comparação.

   ![resultado de comparação](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o gasoduto de monitorização, consulte o gasoduto Monitor funciona usando o artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md)
