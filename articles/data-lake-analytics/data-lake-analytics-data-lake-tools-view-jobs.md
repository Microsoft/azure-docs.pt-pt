---
title: Utilize o Browser de trabalho e a vista de tarefas para tarefas de Azure Data Lake Analytics
description: Este artigo descreve como utilizar o Browser de trabalho e a vista de tarefas para tarefas de Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60616235"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Utilizar o Browser de trabalho e a vista de tarefas para o Azure Data Lake Analytics
O serviço Azure Data Lake Analytics arquiva trabalhos submetidos num arquivo de consultas. Neste artigo, saiba como utilizar o Browser de trabalho e a vista de tarefas no Azure Data Lake Tools para Visual Studio para encontrar as informações de histórico da tarefa. 

Por predefinição, o serviço Data Lake Analytics arquiva as tarefas durante 30 dias. O período de expiração pode ser configurado no portal do Azure ao configurar a política de expiração personalizado. Não será capaz de acessar as informações de tarefa após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Ver [Data Lake Tools para pré-requisitos do Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Abra o Browser de trabalho
O Browser de trabalho por meio de acesso **Explorador de servidores > Azure > do Data Lake Analytics > tarefas** no Visual Studio.  Utilizar o Browser de trabalho, pode acessar o arquivo de consultas de uma conta do Data Lake Analytics. O Browser de trabalho apresenta Store de consulta à esquerda, mostrando as informações de trabalho básico e detalhadas de vista de tarefas no direito mostrando informações da tarefa.

## <a name="job-view"></a>Vista de tarefas
Vista de tarefas mostra as informações detalhadas de uma tarefa. Para abrir uma tarefa, pode clicar duas vezes uma tarefa no Browser de trabalho ou abri-lo no menu do Data Lake ao clicar em vista de tarefas. Deverá ver uma caixa de diálogo preenchida com o URL da tarefa.

![Data Lake Tools Browser de trabalho do Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Vista de tarefas contém:

* Resumo da Tarefa
  
    Atualize a vista de tarefa para ver as informações mais recentes de tarefas em execução.
  
  * Estado da tarefa (gráfico):
    
      Estado da tarefa descreve as fases de tarefa:
    
      ![Estado de fases da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Preparar: Carregar o script para a nuvem, compilar e otimizar o script com o serviço de compilação.
    * Em fila: São colocados em fila de tarefas quando estão a aguardar recursos suficientes, ou as tarefas excederem as máximas tarefas simultâneas por limitação de conta. A definição de prioridade determina a seqüência de tarefas em fila - quanto menor o número, maior será a prioridade.
    * A executar: A tarefa, na verdade, está em execução na sua conta do Data Lake Analytics.
    * A finalizar: A tarefa está a concluir (por exemplo, a finalizar o arquivo).
      
      A tarefa pode falhar em todas as fases. Por exemplo, erros de compilação na fase de preparando, erros de tempo limite na fase de em fila e erros de execução na fase de execução, etc.
  * Informações básicas
    
      Mostram as informações de trabalho básico na parte inferior do painel de resumo da tarefa.
    
      ![Estado de fases da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado da tarefa: Com êxito ou falhadas. A tarefa poderá falhar em todas as fases.
    * Duração total: Tempo de relógio (duração) entre a hora de envio e horário de término.
    * Tempo de computação total: A soma de cada tempo de execução de vértice, pode considerá-lo como o tempo que a tarefa é executada em apenas um vértice. Consulte vértices Total para obter mais informações sobre o vértice.
    * Hora de envio/início/fim: A hora quando o serviço Data Lake Analytics recebe a tarefa. o envio/começa a executar o tarefa/termina o trabalho com êxito ou não.
    * Compilação/colocados em fila/em execução: Tempo de relógio gasto durante a fase de preparando/em fila/em execução.
    * Conta: A conta de Data Lake Analytics utilizada para executar a tarefa.
    * Autor: O utilizador que enviou a tarefa, pode ser a conta de uma pessoa real ou uma conta de sistema.
    * Prioridade: A prioridade da tarefa. Menor o número, maior será a prioridade. Afeta apenas a sequência de tarefas na fila. Definir uma prioridade mais alta não tomar o lugar de tarefas em execução.
    * Paralelismo: O número máximo de pedido simultâneo Azure Lake Analytics de unidades de dados (ADLAUs), também conhecido como vértices. Atualmente, um vértice é igual a uma VM com dois núcleos virtuais e seis GB de RAM, embora isso poderia ser atualizado no futuro do Data Lake Analytics de atualizações.
    * À esquerda em bytes: Bytes que precisam ser processadas até que a tarefa é concluída.
    * Bytes lidos/escritos: Bytes lidos/escritos tem sido desde o trabalho que iniciou a execução.
    * Vértices total: A tarefa é dividida em várias partes do trabalho, cada trabalho é chamado um vértice. Este valor descreve quantas partes do trabalho consiste na tarefa. Pode considerar um vértice como uma unidade de processo básico, também conhecido como Azure Data Lake Analytics unidade (ADLAU), e vértices podem ser executados em paralelismo. 
    * Concluído/execução/falha: Contagem de vértices concluída/execução/falha. Vértices podem falhar devido a ambas as falhas de código e de sistema do utilizador, mas as repetições de sistema de falha de vértices automaticamente algumas vezes. Se o vértice ainda é falha depois de repetir, todo o trabalho irá falhar.
* Gráfico da tarefa
  
    Um script de U-SQL representa a lógica de transformar dados de entrada para dados de saída. O script é compilado e otimizado para um plano de execução física na fase de preparando. Gráfico da tarefa é mostrar o plano de execução físico.  O diagrama seguinte ilustra o processo:
  
    ![Estado de fases da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Uma tarefa é dividida em várias partes do trabalho. Cada trabalho é chamado de um vértice. Os vértices são agrupados como Super vértice (também conhecido como fase) e visualizados como gráfico da tarefa. As fase de verde placards no gráfico da tarefa mostram as fases.
  
    Cada vértice numa fase está a fazer o mesmo tipo de trabalho com diferentes partes dos mesmos dados. Por exemplo, se tiver um ficheiro com dados TB e existirem centenas de vértices lendo nele, cada um deles está lendo um segmento. Esses vértices são agrupadas na mesma fase e fazendo mesmo trabalhar em partes diferentes do mesmo ficheiro de entrada.
  
  * <a name="state-information"></a>Informações de fase
    
      Num estágio particular, alguns números são mostrados no placard.
    
      ![Fase de gráfico da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Extração de SV1: O nome de uma fase, chamado por um número e o método de operação.
    * 84 vértices: A contagem total de vértices neste estágio. A figura indica quantas partes do trabalho é dividido neste estágio.
    * s 12.90/vértice: O tempo de execução de vértice média neste estágio. Esta figura é calculada pela soma (cada tempo de execução do vértice) / (total de contagem de vértice). O que significa se foi atribuir todos os vértices executados em paralelismo, o estágio de inteiro é concluído na 12.90 s. Isso também quer dizer se todo o trabalho neste estágio é feito em série, o custo seria #vertices * Méd do tempo.
    * 850,895 linhas escritas: Contagem total de linhas escrita neste estágio.
    * R/W: Quantidade de dados de leitura/Written neste estágio em bytes.
    * Cores: Cores são utilizadas na fase para indicar o estado de vértice diferentes.
      
      * Verde indica que o vértice é concluído com êxito.
      * Cor de laranja indica que o vértice será repetido. O vértice de nova tentativa falhou, mas é repetido com êxito e automaticamente pelo sistema e o estágio geral é concluído com êxito. Se o vértice repetida, mas ainda falha, a cor torna-se vermelho e todo o trabalho falhou.
      * Vermelho indica falha, que significa que um determinado vértice tive foi repetida algumas vezes pelo sistema, mas ainda falha. Este cenário faz com que todo o trabalho efetuar a ativação.
      * Azul significa que um determinado vértice está em execução.
      * Branco indica o vértice está à espera. O vértice pode estar esperando ser agendada uma vez que um ADLAU torna-se disponível, ou podem estar esperando para entrada, uma vez que os respetivos dados de entrada podem não estar preparados.
      
      Pode encontrar mais detalhes para o estágio ao pairar o cursor do mouse por um Estado:
      
      ![Detalhes de fase de gráfico da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices: Descreve os detalhes de vértices, por exemplo, o número de vértices no total, o número de vértices forem concluídas, são elas falhou ou ainda em execução/em espera, etc.
  * Dados lidos em vários/intra pod: Ficheiros e dados são armazenados em vários pods no sistema de ficheiros distribuído. O valor aqui descreve a quantidade de dados foi lido no pod mesmo ou cruzada pod.
  * Tempo de computação total: A soma de cada tempo de execução de vértice no estágio, pode considerá-lo como o tempo que demoraria se tudo funcionar na fase é executado em apenas um vértice.
  * Dados e escritos/lidos de linhas: Indica o quanto dados linhas tem sido lidos/escritos ou tem de ser lidos.
  * Vértice ler falhas: Descreve o número de vértices são falhou ao dados de leitura.
  * Elimina o vértice duplicado: Se um vértice é executado muito lenta, o sistema pode agendar a várias vértices para executar o mesmo trabalho. Vértices reductant serão eliminadas depois de um dos vértices concluída com êxito. Rejeições duplicadas do vértice regista o número de vértices são eliminadas como duplicações na fase.
  * Revogações de vértice: O vértice foi concluída com êxito, mas executar mais tarde, obtenha novamente devido a algumas razões. Por exemplo, se vértice downstream perder dados de entrada intermediários, é-lhe perguntado do vértice a montante para voltar a executar.
  * Execuções da agenda de vértice: O tempo total que os vértices tiverem sido agendados.
  * Leitura de dados de vértice de média/mín/máx: O mínimo/médio/máximo de cada vértice ler os dados.
  * Duração: A hora de relógio de parede demora de uma fase, precisará carregar perfil para ver este valor.
  * Reprodução de Tarefa
    
      Executa tarefas de data Lake Analytics e arquiva os vértices com informações de tarefas, tais como quando os vértices são iniciados, parados, falha e como são repetidas, etc. Todas as informações é automaticamente registado no arquivo de consultas e armazenadas no seu perfil de trabalho. Pode transferir o perfil de trabalho por meio de "Perfil de carga" na vista de tarefas e pode ver a reprodução de tarefa depois de transferir o perfil de trabalho.
    
      Reprodução de tarefa é uma visualização de resumo do que aconteceu no cluster. Ajuda-o a ver o progresso de execução da tarefa e detetar visualmente anomalias de desempenho e congestionamentos de muito pouco tempo (menos do que normalmente 30s).
  * Apresentação do mapa de calor de tarefa 
    
      Mapa térmico de tarefa podem ser selecionado através da lista pendente de apresentar no gráfico da tarefa. 
    
      ![Apresentação do mapa de área dinâmica para dados do gráfico de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Mostra o mapa gráfico e/s, o tempo e o débito de uma tarefa, por meio do qual pode encontrar em que o trabalho passa a maior parte do tempo, ou se a sua tarefa é uma tarefa de limite de e/s e assim por diante.
    
      ![Exemplo de mapa do Azure Data Lake Analytics tarefa gráfico heap](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Curso: A execução de tarefa de progresso, consulte informações nas informações de fase.
    * Dados lidos/escritos: O mapa gráfico do total de dados lidos/escritos em cada fase.
    * Tempo de computação: O mapa térmico de SUM (cada tempo de execução do vértice), pode considerar isso como a forma como há muito tempo que demoraria se todo o trabalho na fase é executado com apenas 1 vértice.
    * Tempo de execução média por nó: O mapa térmico de SUM (cada tempo de execução do vértice) / (número de vértice). O que significa que se foi atribuir todos os vértices executados em paralelismo, o estágio de inteiro será efetuado neste período de tempo.
    * Débito de entrada/saída: O mapa térmico de débito de entrada/saída de cada estágio, pode confirmar se o seu trabalho é que uma e/s vinculado a tarefa através deste.
* Operações de metadados
  
    Pode realizar algumas operações de metadados no seu script de U-SQL, tais como criar uma base de dados, remover uma tabela, etc. Estas operações são mostradas na operação de metadados após a compilação. Pode encontrar as asserções, criar entidades, largar entidades aqui.
  
    ![Operações de metadados de vista de tarefas do Data Lake Analytics do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Histórico de estado
  
    O histórico de estado também é visualizado no resumo da tarefa, mas pode obter mais detalhes aqui. Pode encontrar as informações detalhadas, como quando a tarefa é preparada, colocados em fila, execução de introdução, terminou. Também é possível encontrar o número de vezes que a tarefa tiver sido compilada (o CcsAttempts: 1), quando é a tarefa despachada para o cluster, na verdade, (o detalhe: Expedição de tarefa ao cluster), etc.
  
    ![Histórico de estado de vista de tarefas do Data Lake Analytics do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnóstico
  
    A ferramenta diagnostica automaticamente a execução da tarefa. Irá receber alertas quando existirem alguns erros ou problemas de desempenho nas suas tarefas. Tenha em atenção que terá de transferir o perfil para obter informações completas aqui. 
  
    ![Diagnóstico de vista de tarefas do Data Lake Analytics do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Avisos: Um alerta é exibido aqui com aviso do compilador. Pode clicar em "x problema (s)" link para ter obter mais detalhes, uma vez que será apresentado o alerta.
  * Vértice executar demasiado longo: Se qualquer vértice ficar sem tempo (Digamos 5 horas), irão encontrar problemas aqui.
  * Utilização de recursos: Se já tiver alocado paralelismo mais ou insuficiente do necessário, irão encontrar problemas aqui. Também pode clicar em utilização de recursos para ver mais detalhes e efetuar cenários para encontrar uma alocação de recursos melhor (para obter mais detalhes, consulte este guia).
  * Verificação da memória: Se qualquer vértice utilizar mais de 5 GB de memória, irão encontrar problemas aqui. Execução da tarefa pode obter terminada pelo sistema, se utiliza mais memória do que a limitação do sistema.

## <a name="job-detail"></a>Detalhes da tarefa
Detalhes da tarefa mostra as informações detalhadas do trabalho, incluindo Script, recursos e vista de execução do vértice.

![Detalhes da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O script de U-SQL da tarefa é armazenado no arquivo de consultas. Pode ver o script de U-SQL original e envie novamente se necessário.
* Recursos
  
    Pode encontrar as saídas de compilação de tarefa armazenadas no arquivo de consultas através de recursos. Por exemplo, pode encontrar "algebra.xml", que é utilizado para mostrar o gráfico da tarefa, os assemblies que registou, etc. aqui.
* Vista de execução do vértice
  
    Ela mostra vértices detalhes da execução. O perfil de trabalho arquiva todos os registo de execução de vértice, como o total de dados lidos/escritos, tempo de execução, estado, etc. Através desta vista, pode obter mais detalhes sobre como foi executada uma tarefa. Para obter mais informações, consulte [utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Próximos Passos
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução de vértice, consulte o artigo [utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

