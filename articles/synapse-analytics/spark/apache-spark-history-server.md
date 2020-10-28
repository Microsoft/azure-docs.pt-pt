---
title: Use o servidor histórico spark estendido para depurar apps
description: Utilize o servidor de histórico spark estendido para depurar e diagnosticar aplicações Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 94c30cad1e09a01686a9042a6271a152d0f433fd
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738543"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Utilize o servidor de histórico Apache Spark estendido para depurar e diagnosticar aplicações Apache Spark

Este artigo fornece orientações sobre como usar o servidor de histórico Apache Spark estendido para depurar e diagnosticar aplicações Spark concluídas e executando.

A extensão inclui um separador de dados, separador gráfico e separador de diagnóstico. Utilize o separador **Dados** para verificar os dados de entrada e saída da função Spark. O **separador Gráfico** mostra-lhe o fluxo de dados e a repetição do gráfico de trabalho. O separador **de Diagnóstico** mostra-lhe  **distorcer dados,** **distorcer o tempo** e a análise de **utilização do executor** .

## <a name="access-the-apache-spark-history-server"></a>Aceda ao servidor histórico Apache Spark

O servidor de histórico Apache Spark é a interface do utilizador web para aplicações Spark concluídas e executando. Pode abrir a interface web do servidor de histórico Apache Spark a partir do Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Abra o ui web Do Spark History Server a partir do nó de aplicações de faíscas Apache

1. Abra [a Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Selecione **Monitor** e, em seguida, selecione **Aplicações de faíscas Apache** .

    ![Selecione o monitor e, em seguida, selecione a aplicação de faísca.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecione uma aplicação e, em seguida, abra **a consulta de Log** selecionando-a.

    ![Abra a janela de consulta de log.](./media/apache-spark-history-server/open-application-window.png)

4. Selecione **o servidor de histórico Spark,** em seguida, o Web UI do Spark History Server aparecerá.

    ![Abra o servidor de histórico de faíscas.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Abra o UI web do Spark History Server a partir do nó de dados

1. A partir do seu caderno Azure Synapse Studio, selecione o **servidor de histórico Spark** a partir da célula de saída de execução de trabalho ou do painel de estado na parte inferior do documento do caderno. Selecione **Detalhes da sessão** .

   ![Lançar servidor histórico spark 1](./media/apache-spark-history-server/launch-history-server2.png "Lançar servidor histórico de spark")

2. Selecione **o servidor de histórico Spark** a partir do painel de despersar.

   ![Lançar servidor histórico spark 2](./media/apache-spark-history-server/launch-history-server.png "Lançar servidor histórico de spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Explore o separador Dados no servidor histórico Spark

Selecione o Job ID para o trabalho que deseja ver. Em seguida, selecione **Dados** no menu da ferramenta para obter a visualização de dados. Esta secção mostra-lhe como fazer várias tarefas no separador Dados.

* Verifique as **Entradas,** **Saídas** e **Operações de Tabela** selecionando os separados.

    ![Dados para separadores de aplicações Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copie todas as linhas selecionando **Copy** .

    ![Dados para cópia de aplicação Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Guarde todos os dados como ficheiro CSV selecionando **csv** .

    ![Dados para salvar aplicações spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Pesse inserindo palavras-chave no campo **Pesquisar** . Os resultados da pesquisa são apresentados imediatamente.

    ![Dados para pesquisa de aplicações Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecione o cabeçalho da coluna para classificar a tabela, selecione o sinal de mais para expandir uma linha para mostrar mais detalhes, ou selecione o sinal de menos para colapsar uma linha.

    ![Dados para tabela de aplicações Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Descarregue um único ficheiro selecionando **Download Parcial** . O ficheiro selecionado é descarregado para local. Se o ficheiro já não existir, aparece um novo separador com uma mensagem de erro.

    ![Datas para linha de descarregamento de aplicações Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Para copiar um percurso completo ou um caminho relativo, selecione as opções **Copy Full Path** ou Copy Relative **Path** que se expandem a partir do menu suspenso. Para ficheiros de armazenamento de data lake azure, **open in Azure Storage Explorer** lança Azure Storage Explorer e localiza a pasta quando você está assinado.

    ![Datas para Rota da Cópia da Aplicação Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecione os números de página abaixo da tabela para navegar páginas quando há demasiadas linhas para exibir numa página.

    ![Dados para página de aplicação Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Passe no ponto de interrogação ao lado **de Data** para mostrar a ponta da ferramenta ou selecione o ponto de interrogação para obter mais informações.

    ![Dados para a aplicação Spark mais informações](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Envie feedback com problemas selecionando **Fornecer-nos feedback** .

    ![O gráfico de faíscas fornece-nos feedback novamente](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Separador de gráficos no servidor histórico apache Spark

Selecione o Job ID para o trabalho que deseja ver. Em seguida, selecione **Gráfico** no menu de ferramentas para obter a visualização do gráfico de trabalho.

### <a name="overview"></a>Descrição geral

Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Por defeito, o gráfico mostra todos os trabalhos. Pode filtrar esta vista por **Job ID** .

![ID de trabalho de aplicação de faísca e gráfico de trabalho](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Apresentar

Por predefinição, o **visor Progress** é selecionado. Pode verificar o fluxo de dados selecionando **Ler** ou **Escrito** na lista de entrega do **Visor.**

![Aplicação de faísca e exibição de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-display.png)

O nó gráfico exibe as cores mostradas na lenda do mapa de calor.

![Aplicação de faísca e mapa de calor gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Reproduzir

Para reproduzir o trabalho, selecione **Reprodução** . Pode selecionar **Parar** a qualquer momento para parar. As cores da tarefa mostram diferentes status ao reproduzir:

|Color|Significado|
|-|-|
|Green|Conseguiu: O trabalho foi concluído com sucesso.|
|Laranja|Novamente julgado: Casos de tarefas que falharam mas não afetam o resultado final do trabalho. Estas tarefas duplicaram ou redobrou casos que podem ter sucesso mais tarde.|
|Blue|A correr: A tarefa está a decorrer.|
|Branco|Esperando ou ignorado: A tarefa está à espera de correr, ou o palco saltou.|
|Red|Falhou: a tarefa falhou.|

A imagem a seguir mostra cores verdes, laranja e azul.

![Aplicação de faísca e amostra de cor de gráfico de trabalho, correndo](./media/apache-spark-history-server/sparkui-graph-color-running.png)

A imagem a seguir mostra cores de estado verde e branco.

![Aplicação de faísca e amostra de cor de gráfico de trabalho, saltar](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

A imagem a seguir mostra cores de estado vermelho e verde.

![Aplicação de faísca e amostra de cor de gráfico de trabalho, falhou](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> É permitida a reprodução de cada trabalho. Para trabalhos incompletos, a reprodução não é apoiada.

### <a name="zoom"></a>Zoom

Utilize o seu deslocal para fazer zoom dentro e fora no gráfico de trabalho, ou selecione **Zoom para se adaptar** ao ecrã.

![Aplicação de faísca e zoom de gráfico de trabalho para caber](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Descrições

Passe no nó gráfico para ver a ponta da ferramenta quando houver tarefas falhadas e selecione um palco para abrir a sua página de palco.

![Aplicação de faísca e ponta de ferramenta de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

No separador gráfico de trabalho, as fases têm uma ponta de ferramenta e um pequeno ícone apresentados se tiverem tarefas que satisfaçam as seguintes condições:

|Condição|Descrição|
|-|-|
|Distorção de dados|tamanho de leitura de dados > tamanho médio de leitura de dados de todas as tarefas dentro desta fase * 2 e tamanho de leitura de dados > 10 MB|
|Distorção do tempo|tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 e tempo de execução > 2 minutos|
   
![Ícone de distorção de design de faísca e gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrição do nó gráfico

O nó gráfico de trabalho apresenta as seguintes informações de cada fase:

  * A identificação.
  * Nome ou descrição.
  * Número total de tarefas.
  * Os dados dizem: a soma do tamanho da entrada e baralhar o tamanho da leitura.
  * Os dados escrevem: a soma do tamanho da saída e baralhar o tamanho.
  * Tempo de execução: o tempo entre a hora de início da primeira tentativa e o tempo de conclusão da última tentativa.
  * Contagem de linha: a soma dos registos de entradas, registos de saída, baralhar discos de leitura e baralhar discos de escrita.
  * O progresso.

    > [!NOTE]  
    > Por predefinição, o nó gráfico de trabalho apresenta informações da última tentativa de cada etapa (exceto o tempo de execução do estágio). No entanto, durante a reprodução, o nó gráfico mostra informações de cada tentativa.
    >  
    > O tamanho dos dados de ler e escrever é de 1MB = 1000 KB = 1000 * 1000 Bytes.

### <a name="provide-feedback"></a>Enviar comentários

Envie feedback com problemas selecionando **Fornecer-nos feedback** .

![Aplicação de faísca e feedback de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Explore o separador Diagnóstico no servidor histórico Apache Spark

Para aceder ao separador Diagnóstico, selecione uma identificação de trabalho. Em seguida, selecione **Diagnóstico** no menu da ferramenta para obter a visão de Diagnóstico de Trabalho. O separador de diagnóstico inclui **Data Skew,** **Time Skew** e **Análise de Utilização do Executor** .

Verifique o distorcer de **dados,** **o distorcer do tempo** e a análise de **utilização do executor** selecionando os separadores respectivamente.

![Guia de dados de diagnóstico SparkUI novamente](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorcer dados

Quando selecionar o separador **Distorcer dados,** as tarefas distorcidas correspondentes são apresentadas com base nos parâmetros especificados.

* **Especificar Parâmetros** - A primeira secção apresenta os parâmetros, que são utilizados para detetar o distorcer de dados. A regra padrão é: A Leitura de Dados de Tarefa é superior a três vezes a média de dados de tarefas lidas, e os dados de tarefa lidos são mais de 10 MB. Se quiser definir a sua própria regra para tarefas distorcidas, pode escolher os seus parâmetros. As secções **Skewed Stage** e **Skew Char** são renovadas em conformidade.

* **Fase distorcida** - A segunda secção apresenta estágios, que têm tarefas distorcidas que satisfazem os critérios acima especificados. Se houver mais do que uma tarefa distorcida numa fase, a tabela de palco distorcida apenas apresenta a tarefa mais distorcida (por exemplo, os maiores dados para distorcer dados).

    ![guia de dados de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Skew Chart** – Quando uma linha na tabela de estágios de skew é selecionada, o gráfico de distorção apresenta mais detalhes de distribuição de tarefas com base na leitura de dados e tempo de execução. As tarefas distorcidas são marcadas a vermelho e as tarefas normais são marcadas em azul. O gráfico apresenta até 100 tarefas de amostra e os detalhes da tarefa são apresentados no painel inferior direito.

    ![gráfico sparkui skew para a fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorcer do tempo

O **separador Time Skew** apresenta tarefas distorcidas com base no tempo de execução da tarefa.

* **Especificar Parâmetros** - A primeira secção apresenta os parâmetros, que são utilizados para detetar o distorcer do tempo. Os critérios padrão para detetar o distorção do tempo são: o tempo de execução da tarefa é superior a três vezes o tempo médio de execução e o tempo médio de execução da tarefa é superior a 30 segundos. Pode alterar os parâmetros com base nas suas necessidades. O **Skewed Stage** and **Skew Chart** exibe as fases e tarefas correspondentes, tal como o **separador Data Skew** acima.

* Selecione **O distorce de tempo** , em seguida, o resultado filtrado é apresentado na secção fase de **skewed** de acordo com os parâmetros definidos na secção **Especificar Parâmetros** . Selecione um item na secção **Skewed Stage,** em seguida, o gráfico correspondente é redigido na secção3, e os detalhes da tarefa são apresentados no painel inferior direito.

    ![sparkui tempo distorcer](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de Utilização do Executor

O Gráfico de Utilização do Executor visualiza a atribuição e o estado de funcionamento do executor de trabalho spark.  

1. Selecione **a Análise de Utilização do Executor** , em seguida, são redigidas quatro curvas sobre a utilização do executor, incluindo **executores atribuídos,** **executores executantes,** **executores inativos** e **instâncias de executor max** . Para os executores atribuídos, cada evento "Executor adicionado" ou "Executor removido" aumenta ou diminui os executores atribuídos. Pode consultar "Event Timeline" no separador "Jobs" para obter mais comparação.

   ![guia de executores de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os projetos.

    ![sparkui diagnostica gráfico selecionado](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemas conhecidos

Os dados de entrada/saída que utilizam conjuntos de dados distribuídos resilientes (RDDs) não aparecem no separador de dados.

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

