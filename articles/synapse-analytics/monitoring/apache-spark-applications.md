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
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387358"
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

- Um espaço de trabalho do Estúdio Synapse. Para obter instruções, consulte Criar um espaço de [trabalho do Estúdio Synapse](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Uma piscina Apache Spark.

## <a name="monitor-running-apache-spark-application"></a>Monitor que executa a aplicação Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações Apache Spark que estão em execução, selecione a aplicação Apache Spark e veja os detalhes. Se a aplicação Apache Spark ainda estiver em funcionamento, pode monitorizar o progresso.

  ![selecionar trabalho de execução](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Verifique as **tarefas preenchidas,** **estado**e **duração total**.

2. Cancele o pedido de Faísca Apache.

3. Atualizar consulta de registo.

4. Veja o gráfico.

5. Consulte a informação **do Resumo.**

6. Verifique os **Registos.** A informação de registo está vazia durante a execução.

    ![ver trabalho de execução](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Ver aplicação concluída do Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações concluídas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

  ![selecionar trabalho concluído](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Verifique as **tarefas preenchidas,** **estado**e **duração total**.

2. Atualizar consulta de registo.

3. Abra o link do servidor histórico apache Spark clicando no **servidor histórico de Spark**.

4. Consulte a informação **do Resumo** clicando no ícone no gráfico.

5. Verifique os **Registos.** Pode selecionar diferentes tipos de registos da lista de drop-down e pode descarregar a informação de registo clicando em **registos de descarregamento**.

6. Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Por defeito, o gráfico mostra todos os trabalhos. Pode filtrar esta vista por **Job ID**.

7. Por predefinição, o **visor Progress** é selecionado. Pode verificar o fluxo de dados selecionando **Ler** ou **Escrito** na lista de entrega do **Visor.**

8. Para reproduzir o trabalho, selecione **Reprodução**. Pode selecionar **Parar** a qualquer momento para parar.

9. Utilize o seu deslocal para fazer zoom dentro e fora no gráfico de trabalho, ou selecione **Zoom para se adaptar** ao ecrã.

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
    
11. Clique no gráfico e os detalhes do palco serão apresentados.

   ![detalhes para o estágio](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Ver pedido cancelado da Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações canceladas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

 ![selecionar trabalho cancelado](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Verifique as **tarefas preenchidas,** **estado**e **duração total**.

2. Refresque a consulta de log.

3. Abra o link do servidor de histórico Apache clicando no **servidor histórico de Spark**.

4. Veja o gráfico.

5. Consulte a informação **do Resumo.**

6. Verifique os **Registos.** Pode selecionar diferentes tipos de registos da lista de drop-down e pode descarregar a informação de registo clicando em **registos de descarregamento**.

   ![ver trabalho cancelado](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debug falhou pedido de Apache Spark

Abrir **monitor**, em seguida, selecione **as aplicações Apache Spark**. Para ver os detalhes sobre as aplicações falhadas do Apache Spark, selecione a aplicação Apache Spark e veja os detalhes.

![selecionar trabalho falhado](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Verifique as **tarefas preenchidas,** **estado**e **duração total**.

2. Atualizar consulta de registo.

3. Abra o link do servidor histórico apache Spark clicando no **servidor histórico de Spark**.

4. Veja o gráfico.

5. Consulte a informação **do Resumo.**

6. Verifique a informação de erro.

   ![informação de trabalho falhado](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o gasoduto de monitorização, consulte o gasoduto Monitor funciona usando o artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md)  
