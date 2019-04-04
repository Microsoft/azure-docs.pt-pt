---
title: Previsão na guia técnico de energia de procura
description: Um guia técnico para o modelo de solução com o Microsoft Cortana Intelligence para previsão de energia de demanda.
services: machine-learning
author: garyericson
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/16/2016
ms.author: garye
ms.openlocfilehash: 6b80e73dec7d0e03823a8aa2867ee91bfb68f560
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893644"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guia técnico para o modelo de solução do Cortana Intelligence para previsão de energia de demanda
## **<a name="overview"></a>Descrição geral**
Modelos de soluções foram concebidos para acelerar o processo de criação de uma demonstração de E2E com base no Cortana Intelligence Suite. Um modelo implementado Aprovisiona a sua subscrição com o componente necessário do Cortana Intelligence e criar relações entre. Ele também realiza o seeding do pipeline de dados com dados de exemplo ser gerados a partir de um aplicativo de simulação de dados. Transferir o simulador de dados da ligação fornecida e instalá-lo no seu computador local, consulte o ficheiro Readme. txt. para obter instruções sobre como utilizar o simulador. Dados gerados a partir do simulador hydrates o pipeline de dados e começar a gerar a predição de aprendizagem automática, que pode ser visualizada no dashboard do Power BI.

O modelo de solução pode ser encontrado [aqui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

O processo de implantação o orienta através de vários passos para configurar as credenciais da sua solução. Certifique-se de que estas credenciais, tais como o nome da solução, nome de utilizador e palavra-passe que fornecer durante a implementação de registos.

O objetivo deste documento é explicar a arquitetura de referência e os diferentes componentes aprovisionados na sua subscrição como parte deste modelo de solução. O documento também fala sobre como substituir os dados de exemplo, com dados reais sozinho para poder ver insights/predições do que ganhou dados. Além disso, a fala documento sobre as partes do modelo de solução que precisaria ser modificado para personalizar a solução com os seus dados. No final, são fornecidas instruções sobre como criar o dashboard do Power BI para este modelo de solução.

## **<a name="details"></a>Detalhes**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitetura explicada
Quando a solução é implementada, vários serviços do Azure no Cortana Analytics Suite são ativados (ou seja, Hub de eventos, Stream Analytics, HDInsight, fábrica de dados, Machine Learning, *etc.*). O diagrama de arquitetura mostra como a previsão de procura para o modelo de solução de energia é construído de ponto-a-ponto, num alto nível. Pode investigar esses serviços ao clicar nas mesmas no diagrama do modelo de solução criado com a implementação da solução. As secções seguintes descrevem cada parte.

## **<a name="data-source-and-ingestion"></a>Origem de dados e de ingestão**
### <a name="synthetic-data-source"></a>Origem de dados sintéticos
Para este modelo, a origem de dados utilizada é gerada a partir de um aplicativo de desktop que baixar e executar localmente após a implementação com êxito. Localizar as instruções para transferir e instalar esta aplicação na barra de propriedades, ao selecionar o primeiro nó chamado simulador de dados de previsão de energia no diagrama de modelo de solução. Esta aplicação feeds a [Hub de eventos do Azure](#azure-event-hub) serviço com pontos de dados ou eventos que são utilizados no restante fluxo da solução.

O aplicativo de geração de evento preenche o Hub de eventos do Azure apenas enquanto ele está em execução no seu computador.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é o destinatário da entrada fornecida pela origem de dados sintéticos descrito.

## **<a name="data-preparation-and-analysis"></a>Preparação de dados e análise**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço é utilizado para fornecer uma análise em tempo real no fluxo de entrada do quase a [Hub de eventos do Azure](#azure-event-hub) de serviço e publicar os resultados num [doPowerBI](https://powerbi.microsoft.com)dashboard, bem como arquivar todos os eventos de entrada não processados para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) serviço para processamento posterior pelo [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço.

### <a name="hdinsight-custom-aggregation"></a>Agregação do HDInsight personalizadas
O serviço do Azure HDInsight é utilizado para executar [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orquestrados pelo Azure Data Factory) para fornecer agregações sobre os eventos não processados que foram arquivados usando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) serviço é utilizado (orquestrado pelo Azure Data Factory) para fazer a previsão consumo futuro de energia de uma determinada região com base nas entradas recebidas.

## **<a name="data-publishing"></a>Publicação de dados**
### <a name="azure-sql-database-service"></a>Serviço de base de dados SQL do Azure
O [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) serviço é utilizado para armazenar (geridas pelo Azure Data Factory) as previsões recebidas pelo serviço Azure Machine Learning que é consumido no [Power BI](https://powerbi.microsoft.com) dashboard.

## **<a name="data-consumption"></a>Consumo de dados**
### <a name="power-bi"></a>Power BI
O [Power BI](https://powerbi.microsoft.com) serviço é utilizado para mostrar um dashboard que contém as agregações fornecidas pela [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço, bem como a pedido prever resultados armazenados no [SQL do Azure Base de dados](https://azure.microsoft.com/services/sql-database/) que foram produzidos utilizando o [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) serviço. Para obter instruções sobre como criar o dashboard do Power BI para este modelo de solução, consulte a secção seguinte.

## **<a name="how-to-bring-in-your-own-data"></a>Como colocar em seus próprios dados**
Esta secção descreve como colocar os seus dados para o Azure e quais áreas exigiria alterações para os dados que traz para esta arquitetura.

Não é provável que qualquer conjunto de dados que colocar corresponderia o conjunto de dados utilizado para este modelo de solução. Compreender os seus dados e os requisitos são crucial na maneira como modifica esse modelo funcione com os seus dados. Se for novo serviço do Azure Machine Learning, pode obter uma introdução à mesma, utilizando o exemplo na [como criar a sua primeira experiência](machine-learning/studio/create-experiment.md).

As secções seguintes abordam as seções do modelo que requer modificações quando for introduzido um novo conjunto de dados.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é genérico, serviço, de modo a que os dados poderão ser lançados para o hub no formato JSON ou CSV. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que compreenda os dados que sejam fornecidos a ele.

Este documento não descreve como ingerir os seus dados, mas um pode enviar facilmente eventos ou dados para um Hub de eventos do Azure, utilizando o [API do Hub de eventos](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço é utilizado para fornecer uma análise em tempo real quase lendo de fluxos de dados e a saída de qualquer número de origens de dados.

Para previsão de procura para o modelo de solução de energia, a consulta do Azure Stream Analytics consiste em duas subconsultas, cada um consumo de eventos do serviço do Hub de eventos do Azure como entradas e ter saídas de duas localizações diferentes. Essas saídas são compostos por um conjunto de dados do Power BI e um local de armazenamento do Azure.

O [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) consulta pode ser encontrada ao:

* Iniciar sessão para o [portal do Azure](https://portal.azure.com/)
* Localizar as tarefas do stream analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) que foram gerados quando a solução foi implementada. Uma é para enviar dados para o armazenamento de BLOBs (por exemplo, mytest1streaming432822asablob) e o outro é para enviar dados por push para o Power BI (por exemplo, mytest1streaming432822asapbi).
* Selecionar

  * ***ENTRADAS*** ver a consulta de entrada
  * ***CONSULTA*** para ver a consulta em si
  * ***PRODUZ*** para ver as diferentes saídas

Informações sobre a construção de consulta do Azure Stream Analytics podem ser encontradas no [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, a tarefa do Azure Stream Analytics que produz o conjunto de dados, com quase informações de análise em tempo real sobre o fluxo de dados de entrada a um dashboard do Power BI é fornecida como parte deste modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, estas consultas precisaria ser alterado com base no seu formato de dados.

A outra tarefa do Azure Stream Analytics produz todos [Hub de eventos](https://azure.microsoft.com/services/event-hubs/) eventos para [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e não requer, pelo que nenhuma alteração, independentemente de seu formato de dados uma vez que as informações de evento completo são transmitidas para o armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço organiza o movimento e processamento de dados. Na previsão de procura para o modelo de solução de energia do data factory é constituído por 12 [pipelines](data-factory/concepts-pipelines-activities.md) que mover e processar os dados utilizando várias tecnologias.

  Pode acessar sua fábrica de dados, abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Verá a fábrica de dados no portal do Azure. Se vir erros em seus conjuntos de dados, pode ignorar os conforme forem devido a fábrica de dados a ser implementada antes do gerador de dados foi iniciado. Esses erros não impedem a fábrica de dados a funcionar.

Esta secção descreve as informações necessárias [pipelines](data-factory/concepts-pipelines-activities.md) e [atividades](data-factory/concepts-pipelines-activities.md) contidos no [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Imagem que se segue é a vista de diagrama da solução:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Cinco dos pipelines nesta fábrica de conter [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são utilizados para particionar e agregar os dados. Quando observado, os scripts estão localizados no [armazenamento do Azure](https://azure.microsoft.com/services/storage/) conta criada durante a configuração. É de sua localização: demandforecasting\\\\script\\\\hive\\ \\ (ou https://[Your solução name].blob.core.windows.net/demandforecasting).

Semelhante para o [do Azure Stream Analytics](#azure-stream-analytics-1) consultas, o [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts têm um conhecimento implícito sobre o formato de dados de entrada, estas consultas precisaria ser alterado com base no seu formato de dados e o ["feature Engineering"](machine-learning/team-data-science-process/create-features.md) requisitos.

#### *<a name="aggregatedemanddatato1hrpipeline"></a>AggregateDemandDataTo1HrPipeline*
Isso [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade usando um [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) que executa um [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) criar scripts para dados agregado de procura de transmissão em fluxo contínuo em cada 10 segundos no nível de subestação até à hora nível de região e colocar em [armazenamento do Azure](https://azure.microsoft.com/services/storage/) por meio da tarefa do Azure Stream Analytics.

O [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***AggregateDemandRegion1Hr.hql***

#### *<a name="loadhistorydemanddatapipeline"></a>LoadHistoryDemandDataPipeline*
Isso [pipeline](data-factory/concepts-pipelines-activities.md) contém duas atividades:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade usando um [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) que executa um script de Hive para agregar os dados de pedido por hora do histórico no nível de subestação até à hora nível de região e colocar no armazenamento do Azure durante o Stream do Azure Tarefa de análise
* [Cópia](/previous-versions/azure/dn835035(v=azure.100)) atividade que move os dados agregados de blob de armazenamento do Azure para a base de dados de SQL do Azure que foi aprovisionada como parte da instalação do modelo de solução.

O [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa é ***AggregateDemandHistoryRegion.hql***.

#### *<a name="mlscoringregionxpipeline"></a>MLScoringRegionXPipeline*
Estes [pipelines](data-factory/concepts-pipelines-activities.md) conter várias atividades e cujo resultado final é com a pontuação predições a partir da experimentação do Azure Machine Learning associadas com este modelo de solução. Eles são quase idênticos, exceto a cada um deles lida apenas com a região diferente, o que está sendo feito por diferente RegionID passado o pipeline do ADF e o script de ramo de registo para cada região.  
As atividades contidas neste pipeline são:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade usando um [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) que executa um script de Hive para efetuar agregações e "feature Engineering" necessário para a experimentação do Azure Machine Learning. Os scripts de Hive para esta tarefa são respectivos ***PrepareMLInputRegionX.hql***.
* [Cópia](/previous-versions/azure/dn835035(v=azure.100)) atividade que move os resultados a partir de [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade para um único blob de armazenamento do Azure que pode ser acedidos pela [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) atividade.
* [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) atividade que chama a experimentação do Azure Machine Learning, o que resulta nos resultados de ser colocados num único blob de armazenamento do Azure.

#### *<a name="copyscoredresultregionxpipeline"></a>CopyScoredResultRegionXPipeline*
Isso [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [cópia](/previous-versions/azure/dn835035(v=azure.100)) atividade que move os resultados da experimentação do Azure Machine Learning de respetivos ***MLScoringRegionXPipeline***para a base de dados de SQL do Azure que foi aprovisionada como parte da instalação do modelo de solução.

#### *<a name="copyaggdemandpipeline"></a>CopyAggDemandPipeline*
Isso [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [cópia](/previous-versions/azure/dn835035(v=azure.100)) atividade que move os dados agregados de pedido em curso do ***LoadHistoryDemandDataPipeline*** ao SQL do Azure Base de dados fosse aprovisionada como parte da instalação do modelo de solução.

#### *<a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>CopyTopologyDataPipeline CopyRegionDataPipeline, CopySubstationDataPipeline,*
Isso [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [cópia](/previous-versions/azure/dn835035(v=azure.100)) atividade que move os dados de referência de região/subestação/Topologygeo que são carregados para o blob de armazenamento do Azure como parte do modelo de solução instalação para a base de dados de SQL do Azure que foi aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentar utilizado para este modelo de solução fornece a previsão de procura da região. A experimentação é específica para o conjunto de dados consumido e, portanto, requer modificação ou substituição específica para os dados que seja colocados no.

## **<a name="monitor-progress"></a>Monitorize o progresso**
Assim que o gerador de dados é iniciado, o pipeline começa a obter alimentado e os diferentes componentes da sua solução de começar a iniciar na seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas, pode monitorizar o pipeline.

1. Verifique os dados do armazenamento de Blobs do Azure.

    Uma das tarefas do Stream Analytics escreve os dados de entrada não processados para armazenamento de Blobs. Se clicar em **armazenamento de Blobs do Azure** componente da sua solução a partir do ecrã implementado com êxito a solução e, em seguida, clique em **aberto** no painel à direita, ele leva-o para o [Azure Portal](https://portal.azure.com). Uma vez, clique em **Blobs**. No painel seguinte, é apresentada uma lista de contentores. Clique em **"energysadata"**. No painel seguinte, consulte a **"demandongoing"** pasta. Dentro da pasta de rawdata, verá que as pastas com nomes como data = 2016-01-28 etc. Se vir estas pastas, ele indica que os dados brutos são com êxito a ser gerado no seu computador e armazenados no armazenamento de Blobs. Deverá ver os ficheiros que devem ter tamanhos finitos em MB nessas pastas.
2. Verifique os dados da base de dados do Azure SQL.

    A última etapa do pipeline é escrever dados (por exemplo, predições do machine learning) na base de dados SQL. Poderá ter de esperar o máximo de duas horas para os dados a aparecer na base de dados SQL. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através de [portal do Azure](https://portal.azure.com/). No painel esquerdo, localize a bases de dados SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) e clique no mesmo. Em seguida, localize a base de dados (ou seja, demo123456db) e clique no mesmo. Na página seguinte, em **"Ligar à base de dados"** secção, clique em **"Consultas de execução. o Transact-SQL na base de dados SQL"**.

    Aqui, pode clicar em nova consulta e a consulta para o número de linhas (por exemplo, "selecione count(*) de DemandRealHourly)" à medida que aumenta a sua base de dados, deve aumentar o número de linhas na tabela.)
3. Verifique os dados a partir do dashboard do Power BI.

    Pode configurar o dashboard do Power BI o hot path para monitorizar os dados de entrada não processados. Siga as instruções na secção "Dashboard do Power BI".

## **<a name="power-bi-dashboard"></a>Dashboard do Power BI**
### <a name="overview"></a>Descrição geral
Esta secção descreve como configurar o dashboard do Power BI para visualizar os seus dados em tempo real do n (caminho instantâneo) do Azure stream analytics, bem como prever resultados a partir do Azure machine learning (caminho típico).

### <a name="setup-hot-path-dashboard"></a>Dashboard de caminho instantâneo da configuração
Os seguintes passos guiá-lo como visualizar a saída de dados em tempo real das tarefas do Stream Analytics que foram gerados no momento da implantação da solução. R [Power BI online](https://www.powerbi.com/) conta é necessária para executar os seguintes passos. Se não tiver uma conta, pode [criá-lo](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI do Azure Stream Analytics (ASA).

   * Tem de seguir as instruções em [Azure Stream Analytics e o Power BI: Um dashboard de análise em tempo real para visibilidade em tempo real de dados de transmissão em fluxo](stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída da tarefa do Azure Stream Analytics como o dashboard do Power BI.
   * Localize a tarefa do stream analytics no seu [portal do Azure](https://portal.azure.com). O nome da tarefa deve ser: YourSolutionName + "streamingjob" + número aleatório + "asapbi" (ou seja, demostreamingjob123456asapbi).
   * Adicione uma saída do Power BI para a tarefa ASA. Definir o **Alias de saída** como **'PBIoutput'**. Definir sua **nome do conjunto de dados** e **nomes de tabelas** como **'EnergyStreamData'**. Depois de adicionar a saída, clique em **"Start"** na parte inferior da página para iniciar a tarefa de Stream Analytics. Obterá uma mensagem de confirmação (por exemplo, "Iniciar tarefa de stream analytics myteststreamingjob12345asablob foi concluída com êxito").
2. Inicie sessão no [online do Power BI](https://www.powerbi.com)

   * No painel esquerdo, secção de conjuntos de dados na minha área de trabalho deve ser capaz de ver um novo conjunto de dados que mostra no painel esquerdo do Power BI. Esses são os dados de transmissão em fluxo que enviou a partir do Azure Stream Analytics no passo anterior.
   * Certifique-se de que o ***visualizações*** painel é aberto e é apresentado no lado direito da tela.
3. Crie o mosaico "A pedido por Timestamp":

   * Clique em conjunto de dados **'EnergyStreamData'** no painel esquerdo secção de conjuntos de dados.
   * Clique em **"Gráfico de linhas"** ícone ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Clique em "EnergyStreamData" na **campos** painel.
   * Clique em **"Timestamp"** e certifique-se de que mostra em "Eixo". Clique em **"Load"** e certifique-se de que mostra em "Values".
   * Clique em **guardar** nas partes superior e o nome do relatório como "EnergyStreamDataReport". O relatório com o nome "EnergyStreamDataReport" é apresentado na secção de relatórios no painel do navegador da esquerda.
   * Clique em **"Afixar Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ícone no canto superior direito neste gráfico de linhas, uma janela de "Afixar ao Dashboard" pode aparecer à sua escolha de um dashboard. Selecione "EnergyStreamDataReport", em seguida, clique em "Pin".
   * Paire o rato sobre este mosaico no dashboard, clique em "Editar" ícone no canto superior direito para alterar seu título como "A pedido por Timestamp"
4. Crie outros mosaicos de dashboard com base em conjuntos de dados apropriados. A vista do final dashboard: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Dashboard de caminho típico de configuração
No pipeline de dados do caminho típico, o objetivo essencial é obter a previsão de procura de cada região. Power BI liga a uma base de dados SQL do Azure como a respetiva origem de dados, onde são armazenados os resultados da predição.

> [!NOTE]
> 1) demora algumas horas para recolher os resultados da previsão suficiente para o dashboard. Recomendamos que inicia este processo 2 a 3 horas depois de lunch o gerador de dados. 2) neste passo, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Obter as credenciais da base de dados.

   Precisa **nome do servidor, nome de base de dados, nome de utilizador e palavra-passe de base de dados** antes de passar aos passos seguintes. Aqui estão as etapas para orientá-lo como encontrá-los.

   * Uma vez **"Do Azure SQL Database"** no seu modelo de solução diagrama fica verde, clique no mesmo e, em seguida, clique em **"Aberto"**. São direcionados para o portal do Azure e sua página de informações da base de dados é aberta também.
   * Na página, pode encontrar uma seção de "Base de dados". Ele lista horizontalmente a base de dados que criou. O nome da base de dados deve ser **"O nome da solução + Random Number + 'db'"** (por exemplo, "mytest12345db").
   * Clique em sua base de dados, no pop de novo o painel, pode encontrar o nome do servidor de base de dados na parte superior. O nome do servidor de base de dados deve ser `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (por exemplo, "mytest12345.database.windows.net,1433").
   * A base de dados **nome de utilizador** e **palavra-passe** são o mesmo que o nome de utilizador e palavra-passe gravado anteriormente durante a implementação da solução.
2. Atualizar a origem de dados de ficheiro do Power BI de caminho típico

   * Certifique-se de que instalou a versão mais recente do [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * Na **"DemandForecastingDataGeneratorv1.0"** pasta que transferiu, faça duplo clique o **'Power BI Template\DemandForecastPowerBI.pbix'** ficheiro. As visualizações iniciais são baseadas em dados fictícios. **Nota:** Se vir uma mensagem de erro, certifique-se de que instalou a versão mais recente do Power BI Desktop.

     Depois de abri-lo, na parte superior do ficheiro, clique em **'Editar consultas'**. No pop de fora da janela, faça duplo clique **"Origem"** no painel direito.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * No pop de fora da janela, substitua **"Server"** e **"Database"** com seus próprios nomes de servidor e base de dados e, em seguida, clique **"OK"**. Nome do servidor, certifique-se de que especifique a porta 1433 (**YourSolutionName.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem no ecrã.
   * No pop seguinte fora da janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **"Database"**, preencha suas **"Nomedeutilizador"** e **"Password"** (este é o nome de utilizador e palavra-passe que introduziu quando implementou a solução e criou um Azure pela primeira vez Base de dados SQL). Na ***selecione o nível para aplicar estas definições para***, marque a opção de nível de base de dados. Em seguida, clique em **"Ligar"**.
   * Assim que estiver orientada para a página anterior, feche a janela. Um pops de mensagem de saída - clique em **aplicar**. Por último, clique a **guardar** botão para guardar as alterações. O ficheiro do Power BI agora estabeleceu ligação ao servidor. Se as visualizações estiverem vazias, certifique-se de que desmarcar as seleções nas visualizações para visualizar todos os dados ao clicar no ícone de borracha no canto superior direito de legendas. Utilize o botão de atualização para refletir novos dados nas visualizações. Inicialmente, apenas verá os dados de seed nas suas visualizações conforme a fábrica de dados é agendada para atualização em três horas. Depois de 3 horas, verá novas predições refletidas nas suas visualizações, quando atualizar os dados.
3. (Opcional) Publique o dashboard do caminho típico para [Power BI online](https://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta Power BI (ou conta do Office 365).

   * Clique em **"Publicar"** e alguns segundos mais tarde é apresentada uma janela exibindo "Publicação para o Power BI sucesso!". com uma marca de verificação verde. Clique na ligação seguinte "Demoprediction.pbix aberto no Power BI". Para obter instruções detalhadas, consulte [publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo dashboard: clique no **+** inscrever-se junto a **Dashboards** secção no painel da esquerda. Introduza o nome "Demonstração de previsão de procura" para este dashboard novo.
   * Depois de abrir o relatório, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) para afixar a todas as visualizações ao dashboard. Para obter instruções detalhadas, consulte [afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página de dashboard e ajuste o tamanho e localização das visualizações e editar os respetivos títulos. Para obter instruções detalhadas sobre como editar os mosaicos, consulte [editar um mosaico--redimensionar, mover, mudar o nome, afixar, eliminar, Adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um dashboard de exemplo com algumas visualizações de caminho típico afixado ao mesmo.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcional) Agendar a atualização da origem de dados.

   * Para agendar a atualização dos dados, passe o mouse sobre o **EnergyBPI Final** conjunto de dados, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) e, em seguida, escolha **agendar atualização**.
     **Nota:** Se vir ajustamos um aviso, clique em **editar credenciais** e certificar-se de que as credenciais da sua base de dados são iguais aos descritos no passo 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expanda a **agendar atualização** secção. Ative o "manter os dados atualizados".
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, veja [de atualização de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## **<a name="how-to-delete-your-solution"></a>Como eliminar a sua solução**
Certifique-se de que pare o gerador de dados quando não estiver a utilizar a solução, como executar o gerador de dados incorre em custos de superiores. Elimine a solução se não estiver a utilizá-lo. A eliminar a sua solução elimina todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da sua solução no painel esquerdo do modelo de solução e clique em eliminar.

## **<a name="cost-estimation-tools"></a>Ferramentas de estimativa de custos**
As seguintes duas ferramentas estão disponíveis para o ajudar a compreender melhor os custos totais envolvidos na execução de previsão de procura para o modelo de solução de energia na sua subscrição:

* [Microsoft Azure Estimator a ferramenta Cost (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimador de custos do Microsoft Azure (ambiente de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)

## **<a name="acknowledgements"></a>Confirmações**
Este artigo é de autoria de cientista de dados Yijing Chen e engenheiro de software Qiu Min na Microsoft.
