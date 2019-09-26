---
title: Usar o navegador de trabalhos & exibição de trabalho-Azure Data Lake Analytics
description: Este artigo descreve como usar o navegador de trabalho e a exibição de trabalho para trabalhos de Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309939"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Usar o navegador de trabalhos e a exibição de trabalho para Azure Data Lake Analytics
O serviço de Azure Data Lake Analytics arquiva trabalhos enviados em um repositório de consultas. Neste artigo, você aprenderá a usar o navegador de trabalhos e o modo de exibição de trabalho no Ferramentas do Azure Data Lake para Visual Studio para localizar as informações históricas do trabalho. 

Por padrão, o serviço de Data Lake Analytics arquiva os trabalhos por 30 dias. O período de validade pode ser configurado no portal do Azure Configurando a política de expiração personalizada. Você não poderá acessar as informações do trabalho após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Consulte [Data Lake Tools for Visual Studio Prerequisites](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Abrir o navegador de trabalhos
Acesse o navegador de trabalhos por meio do **Gerenciador de Servidores > Azure > data Lake Analytics trabalhos do >** no Visual Studio.  Usando o navegador de trabalhos, você pode acessar o repositório de consultas de uma conta de Data Lake Analytics. O navegador de trabalhos exibe Repositório de Consultas à esquerda, mostrando as informações básicas do trabalho e a exibição do trabalho à direita mostrando informações detalhadas do trabalho.

## <a name="job-view"></a>Exibição de trabalho
A exibição de trabalho mostra as informações detalhadas de um trabalho. Para abrir um trabalho, você pode clicar duas vezes em um trabalho no navegador de trabalhos ou abri-lo no menu Data Lake clicando em exibição de trabalho. Você deverá ver uma caixa de diálogo populada com a URL do trabalho.

![Navegador de trabalhos de Data Lake ferramentas do Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A exibição do trabalho contém:

* Resumo de Tarefa
  
    Atualize a exibição de trabalho para ver as informações mais recentes de trabalhos em execução.
  
  * Status do trabalho (grafo):
    
      O status do trabalho descreve as fases do trabalho:
    
      ![Status de Azure Data Lake Analytics de fases do trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Prepar Carregue o script na nuvem, compilando e otimizando o script usando o serviço de compilação.
    * Em fila Os trabalhos são enfileirados quando estão aguardando recursos suficientes ou os trabalhos excedem a limitação máxima de trabalhos simultâneos por conta. A configuração de prioridade determina a sequência de trabalhos em fila-quanto menor o número, maior a prioridade.
    * Executado O trabalho está realmente em execução na sua conta de Data Lake Analytics.
    * Finalizando O trabalho está sendo concluído (por exemplo, finalizando o arquivo).
      
      O trabalho pode falhar em todas as fases. Por exemplo, erros de compilação na fase de preparação, erros de tempo limite na fase de fila e erros de execução na fase de execução, etc.
  * Informação Básica
    
      As informações básicas do trabalho são mostradas na parte inferior do painel Resumo do trabalho.
    
      ![Status de Azure Data Lake Analytics de fases do trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado do trabalho: Com êxito ou falha. O trabalho pode falhar em todas as fases.
    * Duração total: Tempo de relógio de parede (duração) entre o envio de hora e a hora de término.
    * Tempo total de computação: A soma de cada tempo de execução de vértice, você pode considerá-la como a hora em que o trabalho é executado em apenas um vértice. Consulte total de vértices para obter mais informações sobre o vértice.
    * Hora de envio/início/término: A hora em que o serviço de Data Lake Analytics recebe o envio do trabalho/começa a executar o trabalho/encerra o trabalho com êxito ou não.
    * Compilação/em fila/em execução: Tempo gasto durante a fase de preparação/na fila/em execução.
    * Considerar A conta de Data Lake Analytics usada para executar o trabalho.
    * Autorização O usuário que enviou o trabalho pode ser uma conta da pessoa real ou uma conta do sistema.
    * Prioridade: A prioridade do trabalho. Quanto menor o número, maior a prioridade. Ele afeta apenas a sequência dos trabalhos na fila. Definir uma prioridade mais alta não antecipa a execução de trabalhos.
    * Paralelismo O número máximo solicitado de ADLAUs (unidades de Azure Data Lake Analytics simultâneas), também conhecido como vértices. Atualmente, um vértice é igual a uma VM com dois núcleos virtuais e seis GB de RAM, embora isso possa ser atualizado em futuras atualizações de Data Lake Analytics.
    * Bytes restantes: Bytes que precisam ser processados até que o trabalho seja concluído.
    * Bytes lidos/gravados: Bytes que foram lidos/gravados desde que o trabalho começou a ser executado.
    * Total de vértices: O trabalho é dividido em muitas partes de trabalho, cada parte do trabalho é chamada de vértice. Esse valor descreve de quantas partes de trabalho o trabalho consiste. Você pode considerar um vértice como uma unidade de processo básica, também conhecida como ADLAU (unidade de Azure Data Lake Analytics) e os vértices podem ser executados em paralelismo. 
    * Concluído/em execução/com falha: A contagem de vértices concluídos/em execução/com falha. Os vértices podem falhar devido a falhas do sistema e do código do usuário, mas o sistema repete os vértices com falha automaticamente algumas vezes. Se o vértice ainda falhar após a repetição, todo o trabalho falhará.
* Grafo de trabalho
  
    Um script U-SQL representa a lógica da transformação de dados de entrada para dados de saída. O script é compilado e otimizado para um plano de execução física na fase de preparação. O grafo do trabalho é mostrar o plano de execução física.  O diagrama a seguir ilustra o processo:
  
    ![Status de Azure Data Lake Analytics de fases do trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Um trabalho é dividido em muitas partes de trabalho. Cada parte do trabalho é chamada de vértice. Os vértices são agrupados como um super vértice (também conhecido como estágio) e visualizados como grafo de trabalho. O estágio verde letreiros no grafo do trabalho mostra os estágios.
  
    Cada vértice em um estágio está fazendo o mesmo tipo de trabalho com partes diferentes dos mesmos dados. Por exemplo, se você tiver um arquivo com um TB de dados, e houver centenas de vértices lendo a partir dele, cada um deles estará lendo uma parte. Esses vértices são agrupados no mesmo estágio e funcionam em diferentes partes do mesmo arquivo de entrada.
  
  * <a name="state-information"></a>Informações do estágio
    
      Em um estágio específico, alguns números são mostrados no letreiro.
    
      ![Azure Data Lake Analytics estágio do grafo de trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Extração de SV1: O nome de um estágio, nomeado por um número e o método de operação.
    * 84 vértices: A contagem total de vértices neste estágio. A figura indica quantas partes de trabalho são divididas neste estágio.
    * 12,90 s/vértice: O tempo médio de execução do vértice para este estágio. Esta figura é calculada por SUM (cada tempo de execução de vértice)/(contagem total de vértices). Ou seja, se você puder atribuir todos os vértices executados em paralelismo, o estágio inteiro será concluído em 12,90 s. Isso também significa que se todo o trabalho nesse estágio for feito em série, o custo será #vertices * tempo médio.
    * 850.895 linhas gravadas: Contagem total de linhas gravadas neste estágio.
    * R/W: Quantidade de dados lidos/gravados neste estágio em bytes.
    * Cores As cores são usadas no estágio para indicar um status de vértice diferente.
      
      * Verde indica que o vértice foi bem-sucedido.
      * Laranja indica que o vértice é repetido. O vértice repetido falhou, mas é repetido automaticamente e com êxito pelo sistema, e o estágio geral é concluído com êxito. Se o vértice for repetido, mas ainda falhar, a cor ficará vermelha e todo o trabalho falhará.
      * O vermelho indica falha, o que significa que um determinado vértice foi repetido algumas vezes pelo sistema, mas ainda falhou. Esse cenário faz com que todo o trabalho falhe.
      * Azul significa que um determinado vértice está em execução.
      * Branco indica que o vértice está aguardando. O vértice pode estar aguardando para ser agendado quando um ADLAU se tornar disponível ou pode estar aguardando a entrada, já que seus dados de entrada podem não estar prontos.
      
      Você pode encontrar mais detalhes para o estágio focalizando o cursor do mouse em um Estado:
      
      ![Detalhes de estágio do grafo de trabalho Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices Descreve os detalhes dos vértices, por exemplo, quantos vértices no total, quantos vértices foram concluídos, se eles falharam ou continuam em execução/aguardando, etc.
  * Dados lidos Cross/intra Pod: Os arquivos e os dados são armazenados em vários pods no sistema de arquivos distribuído. O valor aqui descreve a quantidade de dados lidos no mesmo Pod ou entre Pod.
  * Tempo total de computação: A soma de cada tempo de execução de vértice no estágio, você pode considerá-lo como o tempo que levaria se todo o trabalho no estágio fosse executado em apenas um vértice.
  * Dados e linhas gravados/lidos: Indica a quantidade de dados ou as linhas que foram lidas/gravadas ou precisam ser lidas.
  * Falhas de leitura de vértice: Descreve o número de vértices com falha durante a leitura de dados.
  * Descartes duplicados de vértice: Se um vértice for executado muito devagar, o sistema poderá agendar vários vértices para executar a mesma parte do trabalho. Os vértices redutos serão descartados quando um dos vértices for concluído com êxito. Os descartes de vértice duplicados registram o número de vértices descartados como duplicatas no estágio.
  * Revogações de vértice: O vértice foi bem-sucedido, mas será executado novamente mais tarde devido a alguns motivos. Por exemplo, se o vértice downstream perder dados de entrada intermediários, ele solicitará que o vértice upstream seja executado novamente.
  * Execuções de agenda de vértice: O tempo total em que os vértices foram agendados.
  * Leitura de dados de vértice mín./média/máx.: O mínimo/médio/máximo de cada vértice de leitura de dados.
  * Permanência O tempo que um estágio leva para o relógio, você precisa carregar o perfil para ver esse valor.
  * Reprodução de Tarefa
    
      Data Lake Analytics executa trabalhos e arquiva as informações de execução dos vértices dos trabalhos, como quando os vértices são iniciados, interrompidos, com falha e como são repetidos etc. Todas as informações são registradas automaticamente no repositório de consultas e armazenadas em seu perfil de trabalho. Você pode baixar o perfil de trabalho por meio de "perfil de carga" na exibição de trabalho e pode exibir a reprodução do trabalho depois de baixar o perfil de trabalho.
    
      A reprodução do trabalho é uma visualização representativa do que aconteceu no cluster. Ele ajuda você a assistir ao progresso da execução do trabalho e a detectar visualmente anomalias de desempenho e afunilamentos em um tempo muito curto (menos de 30s geralmente).
  * Exibição do mapa de calor do trabalho 
    
      O mapa de calor do trabalho pode ser selecionado por meio da lista suspensa Exibir no grafo do trabalho. 
    
      ![Exibição do mapa de heap do grafo de trabalho Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Ele mostra o mapa de calor de e/s, tempo e taxa de transferência de um trabalho, por meio do qual você pode descobrir onde o trabalho passa a maior parte do tempo, ou se seu trabalho é um trabalho de limite de e/s e assim por diante.
    
      ![Exemplo de mapa de heap de grafo de trabalho Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Andamento O andamento da execução do trabalho, consulte informações em informações do estágio.
    * Dados lidos/gravados: O mapa de calor do total de dados lidos/gravados em cada estágio.
    * Tempo de computação: O mapa de calor da soma (cada tempo de execução de vértice), você pode considerar isso como quanto tempo levaria se todo o trabalho no estágio fosse executado com apenas um vértice.
    * Tempo médio de execução por nó: O mapa de calor da soma (cada tempo de execução de vértice)/(número de vértice). Ou seja, se você puder atribuir todos os vértices executados em paralelismo, todo o estágio será feito nesse período.
    * Taxa de transferência de entrada/saída: O mapa de calor da taxa de transferência de entrada/saída de cada estágio você pode confirmar se o trabalho é um trabalho vinculado de e/s por meio dele.
* Operações de metadados
  
    Você pode executar algumas operações de metadados em seu script U-SQL, como criar um banco de dados, remover uma tabela etc. Essas operações são mostradas na operação de metadados após a compilação. Você pode encontrar asserções, criar entidades e descartar entidades aqui.
  
    ![Azure Data Lake Analytics trabalho exibir operações de metadados](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Histórico de Estado
  
    O histórico de estado também é visualizado no resumo do trabalho, mas você pode obter mais detalhes aqui. Você pode encontrar informações detalhadas, como quando o trabalho é preparado, enfileirado, iniciado em execução, encerrado. Além disso, você pode encontrar quantas vezes o trabalho foi compilado (o CcsAttempts: 1), quando o trabalho é expedido para o cluster de fato (os detalhes: Expedindo o trabalho para o cluster), etc.
  
    ![Histórico do estado de exibição de Azure Data Lake Analytics trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnóstico
  
    A ferramenta diagnostica a execução do trabalho automaticamente. Você receberá alertas quando houver alguns erros ou problemas de desempenho em seus trabalhos. Observe que você precisa baixar o perfil para obter informações completas aqui. 
  
    ![Diagnóstico de exibição de Azure Data Lake Analytics trabalho](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * :| Um alerta aparece aqui com o aviso do compilador. Você pode clicar no link "x problema (s)" para obter mais detalhes depois que o alerta for exibido.
  * Execução de vértice muito longa: Se algum vértice for executado sem tempo (digamos, 5 horas), os problemas serão encontrados aqui.
  * Uso de recursos: Se você tiver alocado mais ou não um paralelismo suficiente do que o necessário, os problemas serão encontrados aqui. Além disso, você pode clicar em uso de recursos para ver mais detalhes e executar cenários hipotéticos para encontrar uma alocação de recursos melhor (para obter mais detalhes, consulte este guia).
  * Verificação de memória: Se qualquer vértice usar mais de 5 GB de memória, os problemas serão encontrados aqui. A execução do trabalho pode ser eliminada pelo sistema se ele usar mais memória do que a limitação do sistema.

## <a name="job-detail"></a>Detalhes do trabalho
O detalhe do trabalho mostra as informações detalhadas do trabalho, incluindo script, recursos e exibição de execução de vértice.

![Detalhes do trabalho de Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O script U-SQL do trabalho é armazenado no repositório de consultas. Você pode exibir o script U-SQL original e enviá-lo novamente, se necessário.
* Recursos
  
    Você pode encontrar as saídas de compilação de trabalho armazenadas no repositório de consultas por meio de recursos. Por exemplo, você pode encontrar "Algebra. xml", que é usado para mostrar o grafo do trabalho, os assemblies que você registrou, etc. aqui.
* Exibição de execução de vértice
  
    Ele mostra os detalhes de execução dos vértices. O perfil de trabalho arquiva cada log de execução de vértice, como total de leitura/gravação de dados, tempo de execução, estado, etc. Por meio dessa exibição, você pode obter mais detalhes sobre como um trabalho foi executado. Para obter mais informações, consulte [usar o modo de exibição de execução de vértice em ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Próximos Passos
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para usar a exibição de execução de vértice, consulte [usar o modo de exibição de execução de vértice em ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

