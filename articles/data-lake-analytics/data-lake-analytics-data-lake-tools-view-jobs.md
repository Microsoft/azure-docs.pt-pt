---
title: Use Job Browser & Vista de Trabalho - Azure Data Lake Analytics
description: Este artigo descreve como usar o Job Browser e a Job View para trabalhos de Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71309939"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Utilizar o Browser de Tarefas e o Visualizador de Tarefas do Azure Data Lake Analytics
Os arquivos de serviços Azure Data Lake Analytics submeteram empregos numa loja de consultas. Neste artigo, você aprende a usar Job Browser e Job View em Ferramentas de Lago de Dados Azure para o Estúdio Visual para encontrar a informação histórica do trabalho. 

Por padrão, o serviço Data Lake Analytics arquiva os trabalhos durante 30 dias. O período de expiração pode ser configurado a partir do portal Azure configurando a política de expiração personalizada. Não poderá aceder à informação de trabalho após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Consulte [as ferramentas do Data Lake para os pré-requisitos](data-lake-analytics-data-lake-tools-get-started.md#prerequisites)do Estúdio Visual.

## <a name="open-the-job-browser"></a>Abra o Navegador de Emprego
Aceda ao Job Browser via **Server Explorer>Azure>Data Lake Analytics>Jobs** in Visual Studio.  Utilizando o Job Browser, pode aceder à loja de consultas de uma conta Data Lake Analytics. O Job Browser exibe a Consulta Store à esquerda, mostrando informações básicas sobre o trabalho, e a Vista de Emprego à direita, mostrando informações detalhadas sobre o trabalho.

## <a name="job-view"></a>Vista de emprego
A Vista de Emprego mostra a informação detalhada de um trabalho. Para abrir um trabalho, pode clicar duas vezes num trabalho no Job Browser ou abri-lo a partir do menu Data Lake clicando em Job View. Devia ver um diálogo preenchido com o URL de trabalho.

![Data Lake Tools Visual Studio Job Browser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A Vista de Emprego contém:

* Resumo da Tarefa
  
    Refresque a Visão de Emprego para ver as informações mais recentes sobre trabalhos em funcionamento.
  
  * Estado do Trabalho (gráfico):
    
      O Estatuto do Emprego descreve as fases de trabalho:
    
      ![Estado das fases de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Preparação: Faça upload do seu script para a nuvem, compilando e otimizando o script utilizando o serviço de compilação.
    * Fila: Os postos de trabalho são na fila quando estão à espera de recursos suficientes, ou os postos de trabalho excedem o número máximo de empregos simultâneos por limitação de conta. A definição prioritária determina a sequência de empregos em fila - quanto menor o número, maior a prioridade.
    * Execução: O trabalho está realmente a funcionar na sua conta Data Lake Analytics.
    * Finalização: O trabalho está a concluir (por exemplo, finalizar o ficheiro).
      
      O trabalho pode falhar em todas as fases. Por exemplo, erros de compilação na fase de preparação, erros de tempo na fase de fila e erros de execução na fase de execução, etc.
  * Informação Básica
    
      As informações básicas sobre o trabalho mostram na parte inferior do painel de resumo do trabalho.
    
      ![Estado das fases de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado do trabalho: Bem sucedido ou falhado. O trabalho pode falhar em todas as fases.
    * Duração total: Tempo de relógio de parede (duração) entre o tempo de apresentação e o tempo de fim.
    * Tempo total da Computação: A soma de cada tempo de execução de vértices, pode considerá-lo como o tempo em que o trabalho é executado em apenas um vértice. Consulte a Total Vertices para encontrar mais informações sobre o vértice.
    * Submeta/Início/Fim: O momento em que o serviço Data Lake Analytics recebe submissão de emprego/começa a executar o trabalho/termina o trabalho com sucesso ou não.
    * Compilação/Queued/Corrida: Tempo de relógio de parede gasto durante a fase de preparação/fila/execução.
    * Conta: A conta Data Lake Analytics usada para gerir o trabalho.
    * Autor: O utilizador que submeteu o trabalho, pode ser uma conta de pessoa real ou uma conta de sistema.
    * Prioridade: A prioridade do trabalho. Quanto menor o número, maior a prioridade. Só afeta a sequência dos trabalhos na fila. A definição de uma prioridade mais elevada não impede a realização de postos de trabalho.
    * Paralelismo: O número máximo solicitado de Unidades De Análise do Lago de Dados Azure (ADLAUs), também conhecido como vértices. Atualmente, um vértice é igual a um VM com dois núcleos virtuais e seis GB DE RAM, embora este possa ser atualizado em futuras atualizações do Data Lake Analytics.
    * Bytes Left: Bytes que precisam de ser processados até que o trabalho termine.
    * Bytes read/written: Bytes que foram lidos/escritos desde que o trabalho começou a funcionar.
    * Total de vértices: O trabalho é dividido em muitas peças de trabalho, cada peça de trabalho é chamada de vértice. Este valor descreve quantas peças de trabalho o trabalho consiste. Você pode considerar um vértice como uma unidade de processo básico, também conhecida como Azure Data Lake Analytics Unit (ADLAU), e os vértices podem ser executados em paraleloismo. 
    * Concluído/Execução/Falhado: A contagem de vértices concluídos/em execução/falhados. Os vértices podem falhar devido tanto ao código do utilizador como às falhas do sistema, mas o sistema retenta os vértices falhados automaticamente algumas vezes. Se o vértice continuar a falhar depois de tentar, todo o trabalho falhará.
* Gráfico de trabalho
  
    Um script U-SQL representa a lógica de transformar dados de entrada em dados de saída. O script é compilado e otimizado para um plano de execução física na fase de preparação. O Job Graph é para mostrar o plano de execução física.  O seguinte diagrama ilustra o processo:
  
    ![Estado das fases de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Um trabalho é dividido em muitos trabalhos. Cada peça de trabalho é chamada de Vertex. Os vértices são agrupados como Super Vertex (também conhecido como palco), e visualizados como Job Graph. Os cartazes verdes do gráfico mostram os palcos.
  
    Cada vértice numa fase está a fazer o mesmo tipo de trabalho com diferentes peças dos mesmos dados. Por exemplo, se tiver um ficheiro com um dado de Tuberculose, e houver centenas de vértices a ler a partir dele, cada um deles está a ler um pedaço. Esses vértices são agrupados no mesmo palco e fazem o mesmo trabalho em diferentes peças do mesmo ficheiro de entrada.
  
  * <a name="state-information"></a>Informação de palco
    
      Numa fase particular, alguns números são mostrados no cartaz.
    
      ![Estágio do gráfico do gráfico do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Extrato SV1: O nome de um palco, nomeado por um número e o método de operação.
    * 84 vértices: A contagem total de vértices nesta fase. A figura indica quantas peças de trabalho estão divididas nesta fase.
    * 12,90 s/vértice: O tempo médio de execução do vértice para esta fase. Este valor é calculado por SUM (cada tempo de execução de vértice) / (contagem total de Vertex). O que significa que se puder atribuir todos os vértices executados em paralelismo, todo o palco está concluído em 12,90 s. Também significa que se todo o trabalho nesta fase for feito em série, o custo será #vertices * tempo AVG.
    * 850.895 linhas escritas: Contagem total de filas escrita seleções nesta fase.
    * R/W: Quantidade de dados lidos/Escritos nesta fase em bytes.
    * Cores: As cores são usadas no palco para indicar diferente estado do vértice.
      
      * O verde indica que o vértice é bem sucedido.
      * Laranja indica que o vértice é novamente experimentado. O vértice reexperimentado foi falhado, mas é reexperimentado automaticamente e com sucesso pelo sistema, e a fase geral é concluída com sucesso. Se o vértice voltou a tentar, mas mesmo assim falhou, a cor fica vermelha e todo o trabalho falhou.
      * O vermelho indica que falhou, o que significa que um certo vértice tinha sido novamente julgado algumas vezes pelo sistema, mas mesmo assim falhou. Este cenário faz com que todo o trabalho falhe.
      * Azul significa que um certo vértice está a funcionar.
      * O branco indica que o vértice está à espera. O vértice pode estar à espera de ser agendado assim que um ADLAU estiver disponível, ou pode estar à espera de entrada, uma vez que os seus dados de entrada podem não estar prontos.
      
      Você pode encontrar mais detalhes para o palco pairando o cursor do rato por um estado:
      
      ![Detalhes do gráfico do gráfico do gráfico do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices: Descreve os detalhes dos vértices, por exemplo, quantos vértices no total, quantos vértices foram concluídos, são falhados ou ainda em execução/espera, etc.
  * Dados lêem cross/intra pod: Ficheiros e dados são armazenados em várias cápsulas no sistema de ficheiros distribuídos. O valor aqui descreve quantos dados foram lidos na mesma cápsula ou cross pod.
  * Tempo total da computação: A soma de cada tempo de execução de vértices no estágio, pode considerá-lo como o tempo que levaria se todo o trabalho no palco fosse executado em apenas um vértice.
  * Dados e linhas escritos/lidos: Indica quantos dados ou linhas foram lidos/escritos ou precisam de ser lidos.
  * Falhas de leitura do Vertex: Descreve quantos vértices são falhados durante a leitura dos dados.
  * Devoluções duplicadas de vertex: Se um vértice for demasiado lento, o sistema pode agendar vários vértices para executar o mesmo trabalho. Os vértices redutores serão descartados assim que um dos vértices estiver concluído com sucesso. O duplicado vertex elimina os registos do número de vértices que são descartados como duplicações no estágio.
  * Revogações de vertex: O vértice foi bem sucedido, mas ser reexecutado mais tarde por algumas razões. Por exemplo, se o vértice a jusante perder dados de entrada intermédios, pedirá ao vértice a montante que se regere.
  * Vertex agenda execuções: O tempo total que os vértices foram agendados.
  * Dados min/Average/Max Vertex lêem-se: O mínimo/médio/máximo de cada vertex lê dados.
  * Duração: O tempo de tempo de parede que um estágio demora, é necessário carregar o perfil para ver este valor.
  * Reprodução de Tarefa
    
      Data Lake Analytics gere empregos e arquiva os vértices que executam informações sobre os trabalhos, como quando os vértices são iniciados, parados, falhados e como são retentados, etc. Toda a informação é automaticamente registada na loja de consultas e armazenada no seu Perfil de Trabalho. Você pode baixar o Perfil de Trabalho através de "Load Profile" em Job View, e você pode ver a Reprodução de Trabalho depois de descarregar o Perfil de Trabalho.
    
      Job Playback é uma visualização epítome do que aconteceu no cluster. Ajuda-o a ver o progresso da execução do emprego e a detetar visualmente anomalias de desempenho e estrangulamentos num curto espaço de tempo (menos de 30 anos normalmente).
  * Exibição do mapa de calor de trabalho 
    
      O Mapa de Calor do Trabalho pode ser selecionado através da queda do display no Gráfico de Trabalho. 
    
      ![Azure Data Lake Analytics job graph map display](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Mostra o mapa de calor de um trabalho, no tempo e no mapa de calor de um trabalho, através do qual se pode encontrar onde o trabalho passa a maior parte do tempo, ou se o seu trabalho é um trabalho de fronteira, e assim por diante.
    
      ![Azure Data Lake Analytics trabalho gráfico gráfico gráfico exemplo de mapa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Progresso: O progresso da execução do emprego, consulte a informação em fase de informação.
    * Leitura de dados/escrito: O mapa de calor do total de dados lidos/escritos em cada fase.
    * Tempo de computação: O mapa de calor da SUM (cada tempo de execução de vértice), pode considerar isto como quanto tempo demoraria se todo o trabalho no palco fosse executado com apenas 1 vértice.
    * Tempo médio de execução por nó: O mapa de calor de SUM (cada tempo de execução de vértice) / (Número Vertex). O que significa que se puder atribuir todos os vértices executados em paralelismo, todo o palco será feito neste período de tempo.
    * Entrada/saída: O mapa de calor da entrada/saída de cada fase, pode confirmar se o seu trabalho é um trabalho ligado a I/O através disto.
* Operações de Metadados
  
    Pode efetuar algumas operações de metadados no seu script U-SQL, tais como criar uma base de dados, deixar cair uma tabela, etc. Estas operações são mostradas na Operação Metadados após compilação. Você pode encontrar afirmações, criar entidades, deixar cair entidades aqui.
  
    ![Operações de metadados de dados do Lago Azure Data Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* História do Estado
  
    A História do Estado também é visualizada em Resumo de Trabalho, mas você pode obter mais detalhes aqui. Você pode encontrar a informação detalhada, como quando o trabalho é preparado, em fila, começou a correr, terminou. Também pode descobrir quantas vezes o trabalho foi compilado (as CcsAttempts: 1), quando é o trabalho enviado para o cluster na verdade (o Detalhe: Envio de trabalho para cluster), etc.
  
    ![Azure Data Lake Analytics Job View história do estado](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnóstico
  
    A ferramenta diagnostica a execução do trabalho automaticamente. Receberá alertas quando houver alguns erros ou problemas de desempenho no seu trabalho. Por favor, note que precisa de baixar o Perfil para obter informações completas aqui. 
  
    ![Diagnósticos de visão de emprego do Lago de Dados Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Avisos: Um alerta aparece aqui com aviso de compilador. Pode clicar no link "x issue(s)" para ter mais detalhes assim que o alerta aparecer.
  * O vertex dura demasiado tempo: Se algum vértice ficar sem tempo (digamos 5 horas), os problemas serão encontrados aqui.
  * Utilização de recursos: Se alocou mais ou não o suficiente paralelismo do que o necessário, as questões serão encontradas aqui. Também pode clicar na utilização do Recurso para ver mais detalhes e realizar cenários de ese para encontrar uma melhor alocação de recursos (para mais detalhes, consulte este guia).
  * Verificação de memória: Se algum vértice utilizar mais de 5 GB de memória, os problemas serão encontrados aqui. A execução de emprego pode ser morta pelo sistema se usar mais memória do que limitação do sistema.

## <a name="job-detail"></a>Detalhe de trabalho
O Destacamento de Trabalho mostra as informações detalhadas do trabalho, incluindo script, recursos e vertex execution View.

![Detalhe de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O guião u-SQL do trabalho está guardado na loja de consultas. Pode ver o script U-SQL original e resubmetê-lo se necessário.
* Recursos
  
    Você pode encontrar as saídas de compilação de trabalho armazenadas na loja de consulta através de Recursos. Por exemplo, pode encontrar "álgebra.xml" que é usado para mostrar o Gráfico de Trabalho, os conjuntos que registou, etc. aqui.
* Vista de execução de Vertex
  
    Mostra detalhes de execução de vértices. O Perfil de Trabalho arquiva todos os registos de execução de vértices, tais como dados totais lidos/escritos, tempo de execução, estado, etc. Através desta vista, você pode obter mais detalhes sobre como um trabalho correu. Para mais informações, consulte [Use a vertex Execution View em Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução de vértices, consulte Use a vista de [execução vertex em ferramentas](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) de lago de dados para estúdio visual

