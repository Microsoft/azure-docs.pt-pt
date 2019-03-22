---
title: Guia para manutenção preditiva para aeronáutica - Team Data Science Process
description: Um guia técnico para o modelo de solução com o Microsoft Cortana Intelligence para manutenção preditiva no espaço aéreo, utilitários e transportes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: e2f0f1e7ac8f510c4ff5be7933c55278fef74694
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885441"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico para o modelo de solução do Cortana Intelligence para manutenção preditiva no espaço aéreo

> [!Important]
> Este artigo foi preterido. A discussão sobre a manutenção preditiva no espaço aéreo é ainda relevantes, mas para obter informações atuais, veja [descrição geral da solução para profissionais de negócios](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Modelos de soluções foram concebidos para acelerar o processo de criação de uma demonstração de E2E com base no Cortana Intelligence Suite. Um modelo implementado Aprovisiona a sua subscrição com componentes do Cortana Intelligence necessários e, em seguida, cria as relações entre eles. Ele também realiza o seeding do pipeline de dados com dados de exemplo de um aplicativo de gerador de dados, que transfere e instala no seu computador local, depois de implementar o modelo de solução. Os dados do gerador de hydrates o pipeline de dados e começar a gerar as previsões de aprendizagem automática, que podem ser visualizados no dashboard do Power BI.

O processo de implantação o orienta através de vários passos para configurar as credenciais da sua solução. Certifique-se de que as credenciais, tais como o nome da solução, nome de utilizador e palavra-passe que fornece durante a implementação de registos. 


Os objetivos deste artigo são:
- Descrevem a arquitetura de referência e componentes aprovisionados na sua subscrição.
- Demonstre como substituir os dados de exemplo com os seus dados. 
- Mostre como modificar o modelo de solução.  

> [!TIP]
> Pode transferir e imprimir um [versão em PDF deste artigo](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Descrição geral
![Arquitetura de manutenção preditiva](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Ao implementar a solução, ativa a serviços do Azure no Cortana Analytics Suite (incluindo o Hub de eventos, o Stream Analytics, o HDInsight, o Data Factory e o Machine Learning). O diagrama de arquitetura mostra como a manutenção preditiva para aeronáutica modelo de solução é construída. Pode investigar estes serviços no portal do Azure ao clicar nas mesmas no diagrama de modelo de solução criado com a implementação da solução (exceto para o HDInsight, o que é aprovisionada a pedido quando as atividades do pipeline relacionados são necessárias para executar e são eliminar posteriormente).
Transferir uma [obter a versão do diagrama](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As secções seguintes descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Origem de dados e de ingestão
### <a name="synthetic-data-source"></a>Origem de dados sintéticos
Para este modelo, a origem de dados utilizada é gerada a partir de um aplicativo de desktop que baixar e executar localmente após a implementação com êxito.

Para localizar as instruções para transferir e instalar esta aplicação, selecione o nó primeiro, o gerador de dados de manutenção preditiva, no diagrama de modelo de solução. Pode encontrar as instruções na barra de propriedades. Esta aplicação feeds a [Hub de eventos do Azure](#azure-event-hub) serviço com pontos de dados ou eventos, utilizados no restante fluxo da solução. Esta origem de dados é derivada de dados publicamente disponíveis do [repositório de dados de NASA](https://c3.nasa.gov/dashlink/resources/139/) utilizando o [conjunto de dados de simulação de degradação de motor de Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de evento preenche o Hub de eventos do Azure apenas enquanto ele está em execução no seu computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é o destinatário da entrada fornecida pela origem de dados sintético.

## <a name="data-preparation-and-analysis"></a>Preparação de dados e análise  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Uso [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análise em tempo real no fluxo de entrada do quase a [Hub de eventos do Azure](#azure-event-hub) serviço. Em seguida, publicar os resultados um [Power BI](https://powerbi.microsoft.com) dashboard, bem como arquivar eventos de entrada de todos os não processados para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) serviço para processamento posterior pelo [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)serviço.

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada do HDInsight
Execute [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orquestrados pelo Azure Data Factory) com o HDInsight para fornecer agregações sobre os eventos não processados arquivados usando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Fazer previsões sobre a vida útil remanescente (RUL) de um motor de aeronave através dos dados recebidos com [serviço do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (orquestrado pelo Azure Data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Uso [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo serviço do Azure Machine Learning, que, em seguida, são consumidos no [Power BI](https://powerbi.microsoft.com) dashboard.

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Uso [Power BI](https://powerbi.microsoft.com) para mostrar um dashboard que contém as agregações e os alertas fornecidas pelo [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), bem como de previsões de RUL armazenados no [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como colocar em seus próprios dados
Esta secção descreve como colocar os seus dados para o Azure e que áreas precisam de fazer alterações para os dados que traz para esta arquitetura.

É improvável que o conjunto de dados corresponde ao conjunto de dados utilizado pelas [conjunto de dados de simulação de degradação de motor de Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) utilizado para este modelo de solução. Compreender os seus dados e os requisitos são crucial na maneira como modifica esse modelo funcione com os seus dados. 

As secções seguintes abordam as partes do modelo de que necessitam de modificações quando for introduzido um novo conjunto de dados.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
Hub de eventos do Azure é genérico; dados poderão ser lançados para o hub no formato JSON ou CSV. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que compreenda os dados que sejam fornecidos a ele.

Este documento não descreve como ingerir os seus dados, mas pode enviar facilmente eventos ou dados para um Hub de eventos do Azure com as APIs do Hub de eventos.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Utilize o serviço do Azure Stream Analytics para fornecer uma análise em tempo real quase lendo de fluxos de dados e a saída de qualquer número de origens de dados.

A manutenção preditiva para aeronáutica modelo de solução, a consulta do Azure Stream Analytics é composta por quatro subconsultas, cada consulta consumir eventos do serviço do Hub de eventos do Azure, com saídas para quatro localizações distintas. Essas saídas consistem em três conjuntos de dados do Power BI e um local de armazenamento do Azure.

A consulta do Azure Stream Analytics pode ser encontrada ao:

* Ligar ao portal do Azure
* Localização de tarefas do Stream Analytics ![ícone de Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) que foram gerados quando a solução foi implementada (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção preditiva)
* Selecionar
  
  * ***ENTRADAS*** ver a consulta de entrada
  * ***CONSULTA*** para ver a consulta em si
  * ***PRODUZ*** para ver as diferentes saídas

Informações sobre a construção de consulta do Azure Stream Analytics podem ser encontradas no [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, as consultas de saída três conjuntos de dados com perto de informações de análise em tempo real sobre o fluxo de dados de entrada para um dashboard do Power BI fornecido como parte deste modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, estas consultas tem de ser alteradas com base no seu formato de dados.

A consulta na segunda tarefa do Stream Analytics **maintenancesa02asablob** simplesmente produz todos [Hub de eventos](https://azure.microsoft.com/services/event-hubs/) eventos [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, por conseguinte, não requer nenhuma alteração independentemente de seu formato de dados como o evento completo, informações são transmitidas para o armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço organiza o movimento e processamento de dados. Na manutenção preditiva para aeronáutica modelo de solução, o data factory é constituído por três [pipelines](../../data-factory/concepts-pipelines-activities.md) que mover e processar os dados utilizando várias tecnologias.  Aceda a fábrica de dados, abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Erros em seus conjuntos de dados são devido a fábrica de dados que está a ser implementados antes do gerador de dados foi iniciado. Esses erros podem ser ignorados e não impedem a fábrica de dados a funcionar

![Erros de conjunto de dados de fábrica de dados](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta secção descreve as informações necessárias [pipelines e atividades](../../data-factory/concepts-pipelines-activities.md) contidas no [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Eis uma vista de diagrama da solução.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos pipelines nesta fábrica de contenham [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts utilizados para particionar e agregar os dados. Quando observado, os scripts estão localizados no [armazenamento do Azure](https://azure.microsoft.com/services/storage/) conta criada durante a configuração. É de sua localização: maintenancesascript\\\\script\\\\hive\\ \\ (ou https://[Your solução name].blob.core.windows.net/maintenancesascript).

Semelhante à [do Azure Stream Analytics](#azure-stream-analytics-1) consultas, o [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts têm um conhecimento implícito sobre o formato de dados de entrada e tem de ser alterados com base no seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Isso [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) atividade usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) coloca o script para particionar os dados [armazenamento do Azure](https://azure.microsoft.com/services/storage/) durante a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tarefa.

O [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Isso [pipeline](../../data-factory/concepts-pipelines-activities.md) contém várias atividades, cujo resultado final é as previsões com a pontuação da [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação associada com este modelo de solução.

Atividades incluídas são:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) atividade usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa uma [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para efetuar agregações e "feature Engineering" necessário para o [máquina do Azure Aprendizagem](https://azure.microsoft.com/services/machine-learning/) experimentar.
  O [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***PrepareMLInput.hql***.
* [Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados a partir do [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) atividade para um único [armazenamento do Azure](https://azure.microsoft.com/services/storage/) blob acedido pela [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) atividade.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) chamadas de atividade a [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação, com resultados colocar num único [armazenamento do Azure](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Isso [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - um [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados do [Azure Machine Learning](#azure-machine-learning) experimentação do  ***MLScoringPipeline*** para o [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentar utilizado para este modelo de solução fornece a útil vida remanescente (RUL) de um motor de aeronave. A experimentação é específica para o conjunto de dados consumido e requer modificação ou substituição específica para os dados colocados.

Para obter informações sobre a criação da experimentação do Azure Machine Learning, consulte [manutenção preditiva: Passo 1 de 3, preparação de dados e de engenharia de funcionalidades](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitorize o progresso
Assim que o gerador de dados é iniciado, o pipeline começa a dehydrate e os diferentes componentes da sua solução iniciar iniciar no seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas de monitorizar o pipeline.

1. Uma das tarefas do Stream Analytics escreve os dados de entrada não processados para armazenamento de Blobs. Se clicar no componente de armazenamento de BLOBs da sua solução a partir do ecrã, com êxito implantado a solução e, em seguida, clique em abrir no painel à direita, ele leva-o para o [portal do Azure](https://portal.azure.com/). Uma vez, clique em Blobs. No painel seguinte, é apresentada uma lista de contentores. Clique em **maintenancesadata**. No próximo painel é o **rawdata** pasta. Dentro da pasta de rawdata são pastas com nomes como hora = 17 e hora = 18. A presença dessas pastas indica os dados não processados estão a ser gerado no seu computador e armazenados no armazenamento de Blobs. Deverá ver ficheiros csv com tamanhos finitos em MB nessas pastas.
2. A última etapa do pipeline é escrever dados (por exemplo predições do machine learning) na base de dados SQL. Poderá ter de esperar o máximo de três horas para os dados a aparecer na base de dados SQL. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através da [portal do Azure](https://portal.azure.com/). No painel esquerdo, localize bases de dados SQL ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) e clique no mesmo. Em seguida, localize a base de dados **pmaintenancedb** e clique no mesmo. Na página seguinte na parte inferior, clique em GERIR
   
    ![Gerir o ícone](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Aqui, pode clicar em nova consulta e a consulta para o número de linhas (por exemplo selecione count(*) de PMResult). À medida que aumenta a sua base de dados, deve aumentar o número de linhas na tabela.

## <a name="power-bi-dashboard"></a>Dashboard do Power BI

Configure um dashboard do Power BI para visualizar os seus dados do Azure Stream Analytics (caminho instantâneo) e os resultados de predição de batch do Azure machine learning (caminho típico).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o dashboard do caminho típico
O pipeline de dados do caminho típico, o objetivo é obter a RUL preditiva (vida útil restante) de cada motor de aeronave após a conclusão de um voo (ciclo). O resultado de predição é atualizado em três horas para prever os motores das aeronaves que tem concluído um voo durante as últimas 3 horas.

Power BI liga a uma base de dados SQL do Azure como a respetiva origem de dados, onde são armazenados os resultados da predição. Nota: 1) sobre a implementação da sua solução, uma predição aparecerá na base de dados dentro de 3 horas.
O ficheiro pbix que acompanha o download de gerador contém alguns dados de semente, para que pode criar o dashboard do Power BI imediatamente. 2) neste passo, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Os seguintes passos guiá-lo sobre como ligar o ficheiro pbix para a base de dados do SQL que era lançado no momento da implantação da solução que contém os dados (por exemplo, resultados de predição) para visualização.

1. Obter as credenciais da base de dados.
   
   Precisará **nome do servidor, nome de base de dados, nome de utilizador e palavra-passe de base de dados** antes de passar aos passos seguintes. Aqui estão as etapas para orientá-lo como encontrá-los.
   
   * Uma vez **'Do Azure SQL Database'** no seu modelo de solução diagrama fica verde, clique no mesmo e, em seguida, clique em **"Abrir"**.
   * Verá uma novo separador/janela do browser que apresenta a página do portal do Azure. Clique em **grupos de recursos** no painel esquerdo.
   * Selecione a subscrição está a utilizar para implementar a solução e, em seguida, selecione **' YourSolutionName\_ResourceGroup "**.
   * No pop de novo o painel, clique nas ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ícone para acessar seu banco de dados. O nome da sua base de dados é junto a este ícone (por exemplo, **'pmaintenancedb'**) e o **nome do servidor de base de dados** está listado sob a propriedade de nome de servidor e deve ser semelhante à  **YourSolutionName.database.windows.net**.
   * A base de dados **nome de utilizador** e **palavra-passe** são o mesmo que o nome de utilizador e palavra-passe gravado anteriormente durante a implementação da solução.
2. Atualize a origem de dados do ficheiro de relatório do caminho típico com o Power BI Desktop.
   
   * Na pasta onde transferiu e descompactei o ficheiro de gerador, faça duplo clique o **PowerBI\\PredictiveMaintenanceAerospace.pbix** ficheiro. Se vir quaisquer mensagens de aviso quando abrir o arquivo, ignorá-las. Na parte superior do ficheiro, clique em **'Editar consultas'**.
     
     ![Editar consultas](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique em ![ícone de definições de consulta](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) junto a **"Source"** sob **passos aplicados** à direita **definições de consulta** painel. Ignore todas as mensagens de aviso que aparecem.
   * No pop de fora da janela, substitua **"Server"** e **'Database'** com seus próprios nomes de servidor e base de dados e, em seguida, clique **'OK'**. Nome do servidor, certifique-se de que especifique a porta 1433 (**YourSolutionName.database.windows.net, 1433**). Deixe o campo de base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.
   * No pop seguinte fora da janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **'Database'**, preencha suas **'Username'** e **'Password'** (este é o nome de utilizador e palavra-passe que introduziu quando implementou a solução pela primeira vez e criado do Azure Base de dados SQL). Na ***selecione o nível para aplicar estas definições para***, marque a opção de nível de base de dados. Em seguida, clique em **"Ligar"**.
   * Clique na segunda tabela **PMResult** , em seguida, clique em ![ícone de navegação](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) junto a **"Source"** sob **passos aplicados** à direita **Definições de consulta** painel e atualizar os nomes de servidor e base de dados, tal como os passos acima e clique em OK.
   * Assim que estiver orientada para a página anterior, feche a janela. É apresentada uma mensagem - clique **aplicar**. Por último, clique a **guardar** botão para guardar as alterações. O ficheiro do Power BI agora estabeleceu ligação ao servidor. Se as visualizações estiverem vazias, certifique-se de que desmarcar as seleções nas visualizações para visualizar todos os dados ao clicar no ícone de borracha no canto superior direito de legendas. Utilize o botão de atualização para refletir novos dados nas visualizações. Inicialmente, apenas verá os dados de seed nas suas visualizações conforme a fábrica de dados é agendada para atualização em três horas. Depois de 3 horas, verá novas predições refletidas nas suas visualizações, quando atualizar os dados.
3. (Opcional) Publique o dashboard do caminho típico para [Power BI online](https://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta Power BI (ou conta do Office 365).
   
   * Clique em **'Publish'** e alguns segundos mais tarde é apresentada uma janela exibindo "Publicação para o Power BI sucesso!". com uma marca de verificação verde. Clique na ligação abaixo de "Open PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, consulte [publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo dashboard: clique no **+** inscrever-se junto a **Dashboards** secção no painel da esquerda. Introduza o nome "Demonstração de manutenção preditiva" para este dashboard novo.
   * Depois de abrir o relatório, clique em ![ícone PIN](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) para afixar a todas as visualizações ao dashboard. Para obter instruções detalhadas, consulte [afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página de dashboard e ajuste o tamanho e localização das visualizações e editar os respetivos títulos. Para obter instruções detalhadas sobre como editar os mosaicos, consulte [editar um mosaico--redimensionar, mover, mudar o nome, afixar, eliminar, Adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um dashboard de exemplo com algumas visualizações de caminho típico afixado ao mesmo.  Consoante o tempo que executa seu gerador de dados, os seus números nas visualizações podem ser diferentes.
     <br/>
     ![Exibição final](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o mouse sobre o **PredictiveMaintenanceAerospace** conjunto de dados, clique em ![ícone de reticências](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) e, em seguida, escolha **agendar atualização**.
     <br/>
     **Nota:** Se vir ajustamos um aviso, clique em **editar credenciais** e certificar-se de que as credenciais da sua base de dados são iguais aos descritos no passo 1.
     <br/>
     ![Agendar Atualização](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expanda a **agendar atualização** secção. Ative o "manter os dados atualizados".
     <br/>
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, veja [de atualização de dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Dashboard de caminho instantâneo da configuração
Os seguintes passos guiá-lo como visualizar a saída de dados de tarefas do Stream Analytics que foram gerados no momento da implantação da solução. R [Power BI online](https://www.powerbi.com/) conta é necessária para executar os seguintes passos. Se não tiver uma conta, pode [criá-lo](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI do Azure Stream Analytics (ASA).
   
   * Tem de seguir as instruções em [Azure Stream Analytics e o Power BI: Dashboard de análise de visibilidade em tempo real de dados de transmissão em fluxo](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída da tarefa do Azure Stream Analytics como o dashboard do Power BI.
   * A consulta do ASA tem três saídas que são **aircraftmonitor**, **aircraftalert**, e **flightsbyhour**. Pode ver a consulta ao clicar no separador de consulta. Correspondente a cada uma destas tabelas, terá de adicionar uma saída para ASA. Ao adicionar o primeiro resultado (**aircraftmonitor**) certifique-se a **Alias de saída**, **nome do conjunto de dados** e **nome da tabela** são o mesmo (**aircraftmonitor**). Repita os passos para adicionar as saídas de **aircraftalert**, e **flightsbyhour**. Depois de ter adicionado todas as três tabelas de saída e a tarefa ASA foi iniciada, deverá receber uma mensagem de confirmação ("a iniciar o Stream Analytics tarefa maintenancesa02asapbi foi concluída com êxito").
2. Inicie sessão no [online do Power BI](https://www.powerbi.com)
   
   * No painel esquerdo secção de conjuntos de dados na minha área de trabalho, o ***conjunto de dados*** nomes **aircraftmonitor**, **aircraftalert**, e **flightsbyhour** deve aparecer. Esses são os dados de transmissão em fluxo que enviou a partir do Azure Stream Analytics no passo anterior. O conjunto de dados **flightsbyhour** podem não aparecer ao mesmo tempo em que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás dele. No entanto, deve ser apresentado depois de uma hora.
   * Certifique-se de que o ***visualizações*** painel é aberto e é apresentado no lado direito da tela.
3. Depois de ter os dados que flui para o Power BI, pode começar a visualizar os dados de transmissão em fluxo. Abaixo está um dashboard de exemplo com algumas visualizações o hot path afixado ao mesmo. Pode criar outros mosaicos de dashboard com base em conjuntos de dados apropriados. Consoante o tempo que executa seu gerador de dados, os seus números nas visualizações podem ser diferentes.

    ![Vista do Dashboard](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Eis alguns passos para criar um dos mosaicos acima – "visão frota de Sensor 11 vs. Limiar 48.26" mosaico:
   
   * Clique em conjunto de dados **aircraftmonitor** no painel esquerdo secção de conjuntos de dados.
   * Clique nas **gráfico de linhas** ícone.
   * Clique em **processados** no **campos** painel, para que mostre em "Eixo" no **visualizações** painel.
   * Clique em "s11" e "s11\_alerta", para que os dois são apresentados em "Values". Clique na seta pequena junto a **s11** e **s11\_alerta**, altere o "Soma" para "Média".
   * Clique em **guardar** nas partes superior e o nome do relatório "aircraftmonitor." Mostra o relatório com o nome "aircraftmonitor" a **relatórios** secção a **navegador** painel à esquerda.
   * Clique nas **afixar Visual** ícone no canto superior direito neste gráfico de linhas. Uma janela de "Afixar ao Dashboard" pode aparecer à sua escolha de um dashboard. Selecione "Demonstração de manutenção preditiva", em seguida, clique em "Pin".
   * Paire o rato sobre este mosaico no dashboard, clique no ícone de "Editar" no canto superior direito para alterar o título para o "modo de exibição frota de Sensor 11 vs. Limiar 48.26" e o subtítulo para"Média de frota ao longo do tempo."

## <a name="delete-your-solution"></a>Eliminar a solução
Certifique-se de que pare o gerador de dados quando não estiver a utilizar a solução, como executar o gerador de dados implicará custos mais elevados. Elimine a solução se não estiver a utilizá-lo. A eliminar a sua solução elimina todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique em seu nome de solução no painel do lado esquerdo do modelo de solução e, em seguida, clique em **eliminar**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custos
As seguintes duas ferramentas estão disponíveis para o ajudar a compreender melhor os custos totais envolvidos em execução a manutenção preditiva para aeronáutica de solução de modelo na sua subscrição:

* [Microsoft Azure Estimator a ferramenta Cost (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimador de custos do Microsoft Azure (ambiente de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)

