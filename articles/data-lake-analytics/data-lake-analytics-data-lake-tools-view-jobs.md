---
title: Use o navegador de trabalho & vista de emprego - Azure Data Lake Analytics
description: Este artigo descreve como usar o Job Browser e Job View para trabalhos Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/02/2017
ms.openlocfilehash: a1e9a9df4c2ec57dfeec8cf5ddd5348228b9cc3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018567"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Utilizar o Browser de Tarefas e o Visualizador de Tarefas do Azure Data Lake Analytics
Os arquivos do serviço Azure Data Lake Analytics submeteram empregos numa loja de consultas. Neste artigo, você aprende a usar Job Browser e Job View em Azure Data Lake Tools para o Visual Studio para encontrar as informações históricas do trabalho. 

Por padrão, o serviço Data Lake Analytics arquiva os trabalhos por 30 dias. O período de validade pode ser configurado a partir do portal Azure, configurando a política de expiração personalizada. Não poderá aceder à informação do trabalho após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Consulte [as ferramentas do Data Lake para pré-requisitos do Estúdio Visual](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Abra o Navegador de Emprego
Aceda ao Navegador de Emprego via **Server Explorer>Azure>Data Lake Analytics>Jobs** in Visual Studio.  Utilizando o Browser job, pode aceder à loja de consultas de uma conta Data Lake Analytics. Job Browser exibe a Loja de Consultas à esquerda, mostrando informações básicas de trabalho, e Uma Visão de Trabalho à direita mostrando informações detalhadas sobre o trabalho.

## <a name="job-view"></a>Vista de emprego
Job View mostra a informação detalhada de um trabalho. Para abrir um trabalho, pode clicar duas vezes num trabalho no Browser de Trabalho ou abri-lo a partir do menu Data Lake clicando em Job View. Devia ver um diálogo preenchido com a URL de trabalho.

![Data Lake Tools Visual Studio Job Browser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A Visão de Trabalho contém:

* Resumo da Tarefa
  
    Refresque a Visão de Emprego para ver as informações mais recentes sobre a gestão de empregos.
  
  * Estatuto do Trabalho (gráfico):
    
      O Estatuto do Trabalho descreve as fases de trabalho:
    
      ![Screenshot que mostra as fases de trabalho do Azure Data Lake Analytics.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Preparação: Faça o upload do seu script para a nuvem, compilando e otimizando o script utilizando o serviço de compilação.
    * Fila: Os postos de trabalho são postos em fila quando estão à espera de recursos suficientes, ou os postos de trabalho excedem os empregos máximos simultâneos por limitação de conta. A definição de prioridade determina a sequência de trabalhos em fila - quanto menor o número, maior a prioridade.
    * Execução: O trabalho está realmente a decorrer na sua conta Data Lake Analytics.
    * Finalização: O trabalho está a concluir (por exemplo, a finalizar o ficheiro).
      
      O trabalho pode falhar em todas as fases. Por exemplo, erros de compilação na fase de preparação, erros de tempo limite na fase de Queued e erros de execução na fase de funcionamento, etc.
  * Informações Básicas
    
      As informações básicas sobre o trabalho mostram na parte inferior do painel de Resumo de Emprego.
    
      ![Screenshot que mostra o Resumo de Trabalho com descrições em caixas de texto.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado do trabalho: Bem sucedido ou falhado. O trabalho pode falhar em todas as fases.
    * Duração total: Tempo do relógio de parede (duração) entre o tempo de envio e o tempo de fim.
    * Tempo total de cálculo: A soma de cada tempo de execução do vértice, pode considerá-lo como o momento em que o trabalho é executado em apenas um vértice. Consulte a Total Vértices para encontrar mais informações sobre o vértice.
    * Enviar/Iniciar/Terminar: A hora em que o serviço Data Lake Analytics recebe submissão de emprego/começa a executar o trabalho/termina o trabalho com ou sem sucesso.
    * Compilação/Fila/Funcionamento: Tempo de relógio de parede gasto durante a fase de preparação/queção/funcionamento.
    * Conta: A conta Data Lake Analytics usada para gerir o trabalho.
    * Autor: O utilizador que submeteu o trabalho, pode ser uma conta de uma pessoa real ou uma conta do sistema.
    * Prioridade: A prioridade do trabalho. Quanto menor for o número, maior é a prioridade. Só afeta a sequência dos trabalhos na fila. A definição de uma prioridade mais elevada não impede a gestão de postos de trabalho.
    * Paralelismo: O número máximo solicitado de Unidades de Análise simultâneas do Azure Data Lake Analytics (ADLAUs), também conhecidas como vértices. Atualmente, um vértice é igual a um VM com dois núcleos virtuais e seis GB de RAM, embora isso possa ser atualizado em futuras atualizações do Data Lake Analytics.
    * Bytes Left: Bytes que precisam de ser processados até que o trabalho termine.
    * Bytes leia/escreve: Bytes que foram lidos/escritos desde que o trabalho começou a funcionar.
    * Vértices totais: O trabalho é dividido em muitas peças de trabalho, cada obra é chamada de vértice. Este valor descreve quantas peças de trabalho o trabalho consiste. Pode considerar um vértice como uma unidade de processo básico, também conhecida como Azure Data Lake Analytics Unit (ADLAU), e os vértices podem ser executados em paralelo. 
    * Concluído/Running/Failed: A contagem de vértices concluídos/em execução/falhados. Os vértices podem falhar devido tanto ao código do utilizador como às falhas do sistema, mas as retrações do sistema falharam automaticamente algumas vezes. Se o vértice ainda for falhado depois de voltar a tentar, todo o trabalho falhará.
* Gráfico de trabalho
  
    Um script U-SQL representa a lógica de transformar dados de entrada em dados de saída. O guião é compilado e otimizado para um plano de execução física na fase de Preparação. O Gráfico de Trabalho é mostrar o plano de execução física.  O seguinte diagrama ilustra o processo:
  
    ![Azure Data Lake Analytics estado de trabalho fases de trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Um trabalho é dividido em muitas peças de trabalho. Cada obra é chamada de Vertex. Os vértices são agrupados como Super Vertex (também conhecido como palco), e visualizados como Job Graph. Os cartazes verdes do palco no gráfico de trabalho mostram os palcos.
  
    Cada vértice numa fase está a fazer o mesmo tipo de trabalho com diferentes peças dos mesmos dados. Por exemplo, se tiver um ficheiro com um dado de Tuberculose, e houver centenas de vértices a ler dele, cada um deles está a ler um pedaço. Esses vértices são agrupados na mesma fase e fazem o mesmo trabalho em diferentes peças do mesmo ficheiro de entrada.
  
  * <a name="state-information"></a>Informação de palco
    
      Numa determinada fase, alguns números são mostrados no cartaz.
    
      ![Fase de gráfico de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Extração SV1: O nome de um palco, nomeado por um número e pelo método de funcionamento.
    * 84 vértices: A contagem total de vértices nesta fase. O número indica quantas peças de trabalho estão divididas nesta fase.
    * 12,90 s/vértice: O tempo médio de execução do vértice para esta fase. Este valor é calculado por SUM (cada tempo de execução do vértice) / (contagem total de Vértex). O que significa que se puder atribuir todos os vértices executados em paralelismo, todo o estágio está concluído em 12,90 s. Também significa que se todo o trabalho nesta fase for feito em série, o custo seria #vertices * tempo AVG.
    * 850.895 linhas escritas: Contagem total de linhas escritas nesta fase.
    * R/W: Quantidade de dados lidos/Escritos nesta fase em bytes.
    * Cores: As cores são usadas no palco para indicar o estado do vértice diferente.
      
      * Verde indica que o vértice é bem sucedido.
      * Laranja indica que o vértice é novamente experimentado. O vértice novamente experimentado foi falhado, mas é novamente experimentado e com sucesso pelo sistema, e a fase geral é concluída com sucesso. Se o vértice voltou a tentar, mas mesmo assim falhou, a cor fica vermelha e todo o trabalho falhou.
      * O vermelho indica que falhou, o que significa que um certo vértice foi novamente julgado pelo sistema, mas mesmo assim falhou. Este cenário faz com que todo o trabalho falhe.
      * Azul significa que um certo vértice está a funcionar.
      * O branco indica que o vértice está à espera. O vértice pode estar à espera de ser programado uma vez que um ADLAU esteja disponível, ou pode estar à espera de entrada, uma vez que os seus dados de entrada podem não estar prontos.
      
      Você pode encontrar mais detalhes para o palco, pairando sobre o seu cursor de rato por um estado:
      
      ![Detalhes do palco do gráfico de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices: Descreve os detalhes dos vértices, por exemplo, quantos vértices no total, quantos vértices foram concluídos, são eles falhados ou ainda em execução/espera, etc.
  * Os dados lêem o cross/intra pod: Os ficheiros e os dados são armazenados em várias cápsulas no sistema de ficheiros distribuídos. O valor aqui descreve quantos dados foram lidos na mesma cápsula ou cápsula cruzada.
  * Tempo total de cálculo: A soma de cada tempo de execução do vértice no palco, pode considerá-lo como o tempo que levaria se todo o trabalho no palco fosse executado em apenas um vértice.
  * Dados e linhas escritas/lidas: Indica quantos dados ou linhas foram lidos/escritos, ou precisam de ser lidos.
  * Falhas de leitura do Vertex: Descreve quantos vértices são falhados enquanto lêem dados.
  * Vertex duplica devoluções: Se um vértice for demasiado lento, o sistema pode agendar vários vértices para executar a mesma peça de trabalho. Os vértices redutores serão descartados assim que um dos vértices completar com sucesso. Vertex duplicado descarta o número de vértices que são descartados como duplicações no palco.
  * Revogações do Vertex: O vértice foi bem sucedido, mas será reeleito mais tarde devido a algumas razões. Por exemplo, se o vértice a jusante perder dados de entrada intermédios, pedirá ao vértice a montante para se reexame.
  * Execuções de horários vertex: O tempo total que os vértices foram programados.
  * Os dados min/average/Max Vertex dizem: O mínimo/médio/máximo de cada dado de leitura de vértice.
  * Duração: O tempo do relógio de parede que um palco leva, é necessário carregar o perfil para ver este valor.
  * Reprodução de Tarefa
    
      Data Lake Analytics gere empregos e arquiva os vértices que executam informações dos trabalhos, como quando os vértices são iniciados, parados, falhados e como são novamente julgados, etc. Todas as informações são automaticamente registadas na loja de consultas e armazenadas no seu Perfil de Trabalho. Pode baixar o Perfil de Trabalho através de "Perfil de Carga" em Visualização de Emprego e pode ver a Reprodução de Emprego depois de descarregar o Perfil de Trabalho.
    
      Job Playback é uma visualização epítome do que aconteceu no cluster. Ajuda-o a ver o progresso da execução do trabalho e a detetar visualmente anomalias de desempenho e estrangulamentos num curto espaço de tempo (menos de 30 anos normalmente).
  * Display do mapa de calor do trabalho 
    
      O Mapa de Calor do Trabalho pode ser selecionado através do dropdown do Display no Gráfico de Trabalho. 
    
      ![Azure Data Lake Analytics job gráfico gráfico mapa de mapa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Mostra o mapa de calor de I/O, tempo e produção de um trabalho, através do qual você pode encontrar onde o trabalho passa a maior parte do tempo, ou se o seu trabalho é um trabalho de fronteira de E/S, e assim por diante.
    
      ![Exemplo de mapa de gráfico de azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Progresso: O progresso da execução do trabalho, ver Informação em informação de fase.
    * Leitura/escrita de dados: Mapa de calor do total de dados lidos/escritos em cada fase.
    * Tempo de cálculo: O mapa de calor de SUM (cada tempo de execução do vértice), pode considerar isto como o tempo que levaria se todo o trabalho no palco fosse executado com apenas 1 vértice.
    * Tempo médio de execução por nó: Mapa de calor de SUM (cada tempo de execução do vértice) / (Número de Vértex). O que significa que se atribuísse todos os vértices executados em paralelismo, todo o estágio será feito neste período de tempo.
    * Entrada/saída: O mapa de calor da entrada/saída de cada fase, pode confirmar se o seu trabalho é um trabalho ligado à E/S através deste.
* Operações de Metadados
  
    Pode executar algumas operações de metadados no seu script U-SQL, como criar uma base de dados, deixar cair uma tabela, etc. Estas operações são apresentadas na Operação Metadados após a compilação. Pode encontrar afirmações, criar entidades, deixar cair entidades aqui.
  
    ![Azure Data Lake Analytics Job Ver metadados](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* História do Estado
  
    A História do Estado também está visualizada no Job Summary, mas pode obter mais detalhes aqui. Pode encontrar a informação detalhada, como quando o trabalho é preparado, em fila, começou a correr, terminou. Também pode descobrir quantas vezes o trabalho foi compilado (o CcsAttempts: 1), quando é o trabalho enviado para o cluster na verdade (o Detalhe: Despacho de trabalho para cluster), etc.
  
    ![Azure Data Lake Analytics Job View História do estado](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnóstico
  
    A ferramenta diagnostica a execução de trabalho automaticamente. Receberá alertas quando houver alguns erros ou problemas de desempenho no seu trabalho. Por favor, note que precisa de baixar o Profile para obter informações completas aqui. 
  
    ![Diagnósticos de job view do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Avisos: Um alerta aparece aqui com aviso de compilador. Pode clicar no link "x issue(s)" para ter mais detalhes assim que o alerta aparecer.
  * O vertex é demasiado longo: Se algum vértice se esgotar no tempo (digamos 5 horas), os problemas serão encontrados aqui.
  * Utilização de recursos: Se alocar mais ou menos o parallelismo do que o necessário, as questões serão encontradas aqui. Também pode clicar na utilização de Recursos para ver mais detalhes e realizar cenários para encontrar uma melhor alocação de recursos (para mais detalhes, consulte este guia).
  * Verificação de memória: Se algum vértice utilizar mais de 5 GB de memória, os problemas serão encontrados aqui. A execução de emprego pode ser morta pelo sistema se usar mais memória do que limitação do sistema.

## <a name="job-detail"></a>Detalhe de trabalho
O Detalhe de Trabalho mostra as informações detalhadas do trabalho, incluindo script, recursos e vista de execução de vértice.

![Detalhes de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O script U-SQL do trabalho está armazenado na loja de consultas. Pode ver o script original do U-SQL e reencando-o se necessário.
* Recursos
  
    Pode encontrar as saídas de compilação de trabalho armazenadas na loja de consultas através de Recursos. Por exemplo, pode encontrar "algebra.xml" que é usado para mostrar o Gráfico de Trabalho, as assembleias que registou, etc. aqui.
* Vista de execução de vértice
  
    Mostra detalhes da execução dos vértices. O Perfil de Trabalho arquiva todos os registos de execução de vértice, tais como a leitura total de dados/escritos, tempo de execução, estado, etc. Através desta vista, você pode obter mais detalhes sobre como um trabalho funciona. Para obter mais informações, consulte [a vista de execução do vertex em ferramentas do lago de dados para estúdio visual.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução do vértice, consulte [a vista de execução do vertex em ferramentas do lago de dados para estúdio visual](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

