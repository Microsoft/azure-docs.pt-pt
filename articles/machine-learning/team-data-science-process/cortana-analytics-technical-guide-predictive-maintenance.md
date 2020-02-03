---
title: Guia para manutenção preditiva para aeronáutica - Team Data Science Process
description: Um guia técnico para o modelo de solução com o Microsoft Cortana Intelligence para manutenção preditiva no espaço aéreo, utilitários e transportes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9871e1402336f5ad282c12f959d45fda85512a84
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721851"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico para o modelo de solução do Cortana Intelligence para manutenção preditiva no espaço aéreo

> [!Important]
> Este artigo foi preterido. A discussão sobre a Manutenção Preditiva em Aeroespacial ainda é relevante, mas para informação atual, consulte a Visão Geral da [Solução para o Público Empresarial.](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)


Modelos de soluções foram concebidos para acelerar o processo de criação de uma demonstração de E2E com base no Cortana Intelligence Suite. Um modelo implementado Aprovisiona a sua subscrição com componentes do Cortana Intelligence necessários e, em seguida, cria as relações entre eles. Ele também realiza o seeding do pipeline de dados com dados de exemplo de um aplicativo de gerador de dados, que transfere e instala no seu computador local, depois de implementar o modelo de solução. Os dados do gerador de hydrates o pipeline de dados e começar a gerar as previsões de aprendizagem automática, que podem ser visualizados no dashboard do Power BI.

O processo de implantação o orienta através de vários passos para configurar as credenciais da sua solução. Certifique-se de que as credenciais, tais como o nome da solução, nome de utilizador e palavra-passe que fornece durante a implementação de registos. 


Os objetivos deste artigo são:
- Descrevem a arquitetura de referência e componentes aprovisionados na sua subscrição.
- Demonstre como substituir os dados de exemplo com os seus dados. 
- Mostre como modificar o modelo de solução.  

> [!TIP]
> Pode descarregar e imprimir uma [versão PDF deste artigo.](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)
> 
> 

## <a name="overview"></a>Descrição geral
![Arquitetura de manutenção preditiva](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Ao implementar a solução, ativa a serviços do Azure no Cortana Analytics Suite (incluindo o Hub de eventos, o Stream Analytics, o HDInsight, o Data Factory e o Machine Learning). O diagrama de arquitetura mostra como a manutenção preditiva para aeronáutica modelo de solução é construída. Pode investigar estes serviços no portal do Azure ao clicar nas mesmas no diagrama de modelo de solução criado com a implementação da solução (exceto para o HDInsight, o que é aprovisionada a pedido quando as atividades do pipeline relacionados são necessárias para executar e são eliminar posteriormente).
Descarregue uma [versão em tamanho real do diagrama](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As secções seguintes descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Origem de dados e de ingestão
### <a name="synthetic-data-source"></a>Origem de dados sintéticos
Para este modelo, a fonte de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho descarregada que executa localmente após uma implementação bem sucedida.

Para localizar as instruções para transferir e instalar esta aplicação, selecione o nó primeiro, o gerador de dados de manutenção preditiva, no diagrama de modelo de solução. Pode encontrar as instruções na barra de propriedades. Esta aplicação alimenta o serviço [Azure Event Hub](#azure-event-hub) com pontos de dados, ou eventos, utilizados no resto do fluxo de solução. Esta fonte de dados é derivada de dados disponíveis publicamente do [repositório](https://c3.nasa.gov/dashlink/resources/139/) de dados da NASA utilizando o Conjunto de Dados de Simulação de [Degradação do Motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de evento preenche o Hub de eventos do Azure apenas enquanto ele está em execução no seu computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O serviço [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de Dados Sintéticos.

## <a name="data-preparation-and-analysis"></a>Preparação de dados e análise  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Utilize o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análises quase em tempo real no fluxo de entrada do serviço [Azure Event Hub.](#azure-event-hub) Em seguida, publica os resultados num dashboard [Power BI,](https://powerbi.microsoft.com) bem como arquiva todos os eventos de entrada cruas no serviço [de Armazenamento Azure](https://azure.microsoft.com/services/storage/) para posterior processamento pelo serviço [Azure Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada do HDInsight
Executar scripts [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado por Azure Data Factory) usando hDInsight para fornecer agregados sobre os eventos brutos arquivados usando o recurso Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Faça previsões sobre a vida útil restante (RUL) de um determinado motor de avião utilizando as inputs recebidas com o [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (orquestrado pela Azure Data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Utilize a Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo Azure Machine Learning, que são depois consumidas no painel power [BI.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Use [o Power BI](https://powerbi.microsoft.com) para mostrar um dashboard que contém agregações e alertas fornecidos pela [Azure Stream Analytics,](https://azure.microsoft.com/services/stream-analytics/)bem como previsões rul armazenadas na Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando [O Machine Learning Azure](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como colocar em seus próprios dados
Esta secção descreve como colocar os seus dados para o Azure e que áreas precisam de fazer alterações para os dados que traz para esta arquitetura.

É pouco provável que o seu conjunto de dados corresponda ao conjunto de dados utilizado pelo Conjunto de Dados de Simulação de Degradação do [Motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) utilizado para este modelo de solução. Compreender os seus dados e os requisitos são crucial na maneira como modifica esse modelo funcione com os seus dados. 

As secções seguintes abordam as partes do modelo de que necessitam de modificações quando for introduzido um novo conjunto de dados.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
Hub de eventos do Azure é genérico; dados poderão ser lançados para o hub no formato JSON ou CSV. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que compreenda os dados que sejam fornecidos a ele.

Este documento não descreve como ingerir os seus dados, mas pode enviar facilmente eventos ou dados para um Hub de eventos do Azure com as APIs do Hub de eventos.

### <a name="azure-stream-analytics-1"></a>Análise de Fluxo Azure
Utilize o recurso Azure Stream Analytics para fornecer análises quase em tempo real, lendo a partir de fluxos de dados e divulgando dados para qualquer número de fontes.

A manutenção preditiva para aeronáutica modelo de solução, a consulta do Azure Stream Analytics é composta por quatro subconsultas, cada consulta consumir eventos do serviço do Hub de eventos do Azure, com saídas para quatro localizações distintas. Essas saídas consistem em três conjuntos de dados do Power BI e um local de armazenamento do Azure.

A consulta do Azure Stream Analytics pode ser encontrada ao:

* Ligar ao portal do Azure
* Localizar os trabalhos do Stream Analytics ![ícone stream analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) que foram gerados quando a solução foi implementada (*por exemplo,* **manutençãosa02asapbi** e **manutençãoa02asablob** para a solução de manutenção preditiva)
* Selecionar
  
  * ***INPUTS*** para ver a entrada de consulta
  * ***CONSULTA*** para ver a consulta em si
  * ***SAÍDAS*** para ver as diferentes saídas

Informações sobre a construção de consulta sonorizadora Azure Stream Analytics podem ser encontradas na Referência de [Consulta de Análise de Fluxo](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) no MSDN.

Nesta solução, as consultas de saída três conjuntos de dados com perto de informações de análise em tempo real sobre o fluxo de dados de entrada para um dashboard do Power BI fornecido como parte deste modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, estas consultas tem de ser alteradas com base no seu formato de dados.

A consulta no segundo Stream Analytics manutenção de **trabalhosa02asablob** simplesmente produz todos os eventos [do Event Hub](https://azure.microsoft.com/services/event-hubs/) para o Armazenamento [Azure](https://azure.microsoft.com/services/storage/) e, portanto, não requer qualquer alteração, independentemente do seu formato de dados, uma vez que a informação completa do evento é transmitida para armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra o movimento e o processamento de dados. No Modelo de Manutenção Preditiva para Solução Aeroespacial, a fábrica de dados é composta por três [oleodutos](../../data-factory/concepts-pipelines-activities.md) que movem e processam os dados utilizando várias tecnologias.  Aceda a fábrica de dados, abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Erros em seus conjuntos de dados são devido a fábrica de dados que está a ser implementados antes do gerador de dados foi iniciado. Esses erros podem ser ignorados e não impedem a fábrica de dados a funcionar

![Erros de conjunto de dados de fábrica de dados](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta secção discute os [gasodutos e atividades necessários contidos](../../data-factory/concepts-pipelines-activities.md) na Fábrica de [Dados Azure.](https://azure.microsoft.com/documentation/services/data-factory/) Eis uma vista de diagrama da solução.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos oleodutos desta fábrica contêm scripts [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para dividir e agregar os dados. Quando notado, os scripts estão localizados na conta [De armazenamento Azure](https://azure.microsoft.com/services/storage/) criada durante a configuração. A sua localização é: manutenção\\\\guião\\\\colmeia\\\\ (ou https://[O seu nome de solução].blob.core.windows.net/maintenancesascript).

Semelhante suplicou as consultas [do Azure Stream Analytics,](#azure-stream-analytics-1) os scripts [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm conhecimento implícito sobre o formato de dados que está a chegar e devem ser alterados com base no seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Este [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para dividir os dados colocados no [Armazenamento Azure](https://azure.microsoft.com/services/storage/) durante o trabalho de [Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/)

O script [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa de partição é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MlScoringPipeline*
Este [oleoduto](../../data-factory/concepts-pipelines-activities.md) contém várias atividades cujo resultado final são as previsões pontuadas da experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associada a este modelo de solução.

Atividades incluídas são:

* A atividade [do HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [da Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar agregações e engenharia de recursos necessária para a experiência [de Aprendizagem automática azure.](https://azure.microsoft.com/services/machine-learning/)
  O script [da Colmeia](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa de partição é ***PrepareMLInput.hql***.
* [Copiar](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados da atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) para uma única bolha de [armazenamento Azure](https://azure.microsoft.com/services/storage/) acedida pela atividade [AzureMLBatchScoring.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) chama a experiência [Azure Machine Learning,](https://azure.microsoft.com/services/machine-learning/) com resultados colocados numa única bolha de [armazenamento Azure.](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Este [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [de Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da experiência [Azure Machine Learning](#azure-machine-learning) do ***MLScoringPipeline*** para a Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) utilizada para este modelo de solução fornece a vida útil remanescente (RUL) de um motor de avião. A experimentação é específica para o conjunto de dados consumido e requer modificação ou substituição específica para os dados colocados.

Para obter informações sobre como foi criada a experiência Azure Machine Learning, consulte Manutenção [Preditiva: Passo 1 de 3, preparação de dados e engenharia](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)de recursos.

## <a name="monitor-progress"></a>Monitorize o progresso
Assim que o gerador de dados é iniciado, o pipeline começa a dehydrate e os diferentes componentes da sua solução iniciar iniciar no seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas de monitorizar o pipeline.

* Uma das tarefas do Stream Analytics escreve os dados de entrada não processados para armazenamento de Blobs. Se clicar no componente de armazenamento blob da sua solução a partir do ecrã, implementou com sucesso a solução e, em seguida, clique em Open no painel certo, leva-o ao [portal Azure](https://portal.azure.com/). Uma vez, clique em Blobs. No painel seguinte, é apresentada uma lista de contentores. Clique em dados de **manutenção.** No painel seguinte está a pasta **rawdata.** Dentro da pasta de rawdata são pastas com nomes como hora = 17 e hora = 18. A presença dessas pastas indica os dados não processados estão a ser gerado no seu computador e armazenados no armazenamento de Blobs. Deverá ver ficheiros csv com tamanhos finitos em MB nessas pastas.
* A última etapa do pipeline é escrever dados (por exemplo predições do machine learning) na base de dados SQL. Poderá ter de esperar o máximo de três horas para os dados a aparecer na base de dados SQL. Uma forma de monitorizar a quantidade de dados disponíveis na sua Base de Dados SQL é através do [portal Azure](https://portal.azure.com/). No painel esquerdo, localize as bases de dados SQL ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) e clique nele. Em seguida, localize o **seu pmaintenancedb** da sua base de dados e clique nele. Na página seguinte na parte inferior, clique em GERIR
   
    ![Gerir o ícone](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Aqui, pode clicar em nova consulta e a consulta para o número de linhas (por exemplo selecione count(*) de PMResult). À medida que aumenta a sua base de dados, deve aumentar o número de linhas na tabela.

## <a name="power-bi-dashboard"></a>Dashboard do Power BI

Configure um dashboard do Power BI para visualizar os seus dados do Azure Stream Analytics (caminho instantâneo) e os resultados de predição de batch do Azure machine learning (caminho típico).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o dashboard do caminho típico
O pipeline de dados do caminho típico, o objetivo é obter a RUL preditiva (vida útil restante) de cada motor de aeronave após a conclusão de um voo (ciclo). O resultado de predição é atualizado em três horas para prever os motores das aeronaves que tem concluído um voo durante as últimas 3 horas.

O Power BI liga-se a uma Base de Dados SQL Azure como fonte de dados, onde os resultados da previsão são armazenados. 

Nota: 
1.    Ao implementar a sua solução, uma previsão aparecerá na base de dados dentro de 3 horas. O ficheiro pbix que acompanha o download de gerador contém alguns dados de semente, para que pode criar o dashboard do Power BI imediatamente. 
2.    Neste passo, o pré-requisito é descarregar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Os seguintes passos guiá-lo sobre como ligar o ficheiro pbix para a base de dados do SQL que era lançado no momento da implantação da solução que contém os dados (por exemplo, resultados de predição) para visualização.

1. Obter as credenciais da base de dados.
   
   Necessitará do nome do servidor de base de **dados, nome da base de dados, nome do utilizador e palavra-passe** antes de passar para os próximos passos. Aqui estão as etapas para orientá-lo como encontrá-los.
   
   * Uma vez que **'Azure SQL Database'** no seu modelo de modelo de solução fique verde, clique nele e clique em **'Abrir'** .
   * Verá um novo separador/janela do navegador que exibe a página do portal Azure. Clique em **"Grupos** de recursos" no painel esquerdo.
   * Selecione a subscrição que está a utilizar para implementar a solução e, em seguida, selecione **'YourSolutionName\_ResourceGroup'.**
   * No novo painel pop out, clique no ícone ![SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) para aceder à sua base de dados. O nome da sua base de dados está ao lado deste ícone (por exemplo, **'pmaintenancedb'),** e o nome do servidor de base de **dados** está listado na propriedade do nome Server e deve ser semelhante ao **YourSolutionName.database.windows.net**.
   * O **nome** de utilizador e a **palavra-passe** da base de dados são os mesmos que o nome de utilizador e a palavra-passe previamente registados durante a implementação da solução.
2. Atualize a origem de dados do ficheiro de relatório do caminho típico com o Power BI Desktop.
   
   * Na pasta onde descarregou e desfechou o ficheiro Generator, clique duas vezes no ficheiro **PowerBI\\PredictiveMaintenanceAerospace.pbix.** Se vir quaisquer mensagens de aviso quando abrir o arquivo, ignorá-las. Na parte superior do ficheiro, clique em **'Editar Consultas'.**
     
     ![Editar consultas](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Verá duas mesas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique ![ícone de definições de consulta](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) ao lado de **'Source'** em **'PASSOS APLICADOS'** no painel **'Definições** de consulta' direito. Ignore todas as mensagens de aviso que aparecem.
   * Na janela pop out, substitua **'Server'** e **'Database'** pelos nomes do seu próprio servidor e base de dados e, em seguida, clique em **'OK'** . Para o nome do servidor, certifique-se de especificar a porta 1433 **(YourSolutionName.database.windows.net, 1433**). Deixe o campo base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.
   * Na próxima janela, verá duas opções no painel esquerdo **(Windows** e **Database).** Clique em **'Database'** ( preencha o seu nome de **utilizador'** e **'Password'** (o nome de utilizador e a palavra-passe que introduziu quando implementou a solução pela primeira vez e criou uma Base de Dados Azure SQL). ***Selecione em que nível aplicar estas definições***, verifique a opção de nível de base de dados. Em seguida, clique em **'Ligar'** .
   * Clique na segunda tabela **PMResult** e, em seguida, clique em ![ícone de navegação](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) ao lado de **'Source'** em **'PASSOS APLICADOS'** no painel **'Definições** de Consulta' direito, e atualize os nomes do servidor e da base de dados como nos passos acima e clique em OK.
   * Assim que estiver orientada para a página anterior, feche a janela. Uma mensagem exibe - clique **Em Aplicar**. Por último, clique no botão **Guardar** para guardar as alterações. O ficheiro do Power BI agora estabeleceu ligação ao servidor. Se as visualizações estiverem vazias, certifique-se de que desmarcar as seleções nas visualizações para visualizar todos os dados ao clicar no ícone de borracha no canto superior direito de legendas. Utilize o botão de atualização para refletir novos dados nas visualizações. Inicialmente, apenas verá os dados de seed nas suas visualizações conforme a fábrica de dados é agendada para atualização em três horas. Depois de 3 horas, verá novas predições refletidas nas suas visualizações, quando atualizar os dados.
3. (Opcional) Publique online o painel de instrumentos de via fria para [Power BI](https://www.powerbi.com/). Este passo precisa de uma conta Power BI (ou conta office 365).
   
   * Clique em **'Publicar'** e poucos segundos depois aparece uma janela exibindo "Publishing to Power BI Success!" com uma marca de verificação verde. Clique na ligação abaixo de "Open PredictiveMaintenanceAerospace.pbix no Power BI". Para encontrar instruções detalhadas, consulte [Publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo dashboard: clique no sinal **de+** ao lado da secção **Dashboards** no painel esquerdo. Introduza o nome "Demonstração de manutenção preditiva" para este dashboard novo.
   * Assim que abrir o relatório, clique ![ícone PIN](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) para fixar todas as visualizações no seu painel de instrumentos. Para encontrar instruções detalhadas, consulte [pin um azulejo para um painel power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página de dashboard e ajuste o tamanho e localização das visualizações e editar os respetivos títulos. Para encontrar instruções detalhadas sobre como editar os seus azulejos, consulte [Editar um azulejo -- redimensionar, mover, mudar, mudar o nome, pin, eliminar, adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um dashboard de exemplo com algumas visualizações de caminho típico afixado ao mesmo.  Consoante o tempo que executa seu gerador de dados, os seus números nas visualizações podem ser diferentes.
     <br/>
     ![visão final](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o rato sobre o conjunto de dados **PredictiveMaintenanceAerospace,** clique ![ícone de Elipsis](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) e, em seguida, escolha **'Schedule Refresh '** .
     <br/>
     **Nota:** Se vir uma massagem de aviso, clique em **Editar Credenciais** e certifique-se de que as suas credenciais de base de dados são as mesmas descritas no passo 1.
     <br/>
     ![Horário atualizar](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expandir a secção **'Atualização de** Horários'. Ative o "manter os dados atualizados".
     <br/>
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte [data refresh in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Dashboard de caminho instantâneo da configuração
Os seguintes passos guiá-lo como visualizar a saída de dados de tarefas do Stream Analytics que foram gerados no momento da implantação da solução. É necessária uma conta [online Power BI](https://www.powerbi.com/) para realizar os seguintes passos. Se não tiver uma conta, pode [criar uma.](https://powerbi.microsoft.com/pricing)

1. Adicione a saída do Power BI do Azure Stream Analytics (ASA).
   
   * Deve seguir as instruções no [Azure Stream Analytics & Power BI: Um painel de análise para visibilidade em tempo real dos dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho de Análise de Fluxo saque a Azure como painel de instrumentos Power BI.
   * A consulta da ASA tem três saídas que são de vigilância de **aeronaves,** **alerta**de aeronaves e **voos por hora.** Pode ver a consulta clicando no separador de consulta. Correspondente a cada uma destas tabelas, precisa de adicionar uma saída à ASA. Quando adicionar a primeira saída **(monitor**de aeronaves) certifique-se de que o nome de **saída,** **o nome** do conjunto de dados e o nome da **tabela** são os mesmos **(monitor de aeronaves).** Repita os passos para adicionar saídas para alerta de **aeronaves**e **voos por hora.** Depois de ter adicionado todas as três tabelas de saída e a tarefa ASA foi iniciada, deverá receber uma mensagem de confirmação ("a iniciar o Stream Analytics tarefa maintenancesa02asapbi foi concluída com êxito").
2. Inicie sessão [no Power BI online](https://www.powerbi.com)
   
   * Na secção datasets do painel esquerdo no Meu Espaço de Trabalho, o ***DATASET*** dá nomes de **aeronaves monitorado,** alerta de **aeronaves,** e **voos de hora de viagem** devem aparecer. Esses são os dados de transmissão em fluxo que enviou a partir do Azure Stream Analytics no passo anterior. Os **voos por hora** podem não aparecer ao mesmo tempo que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás. No entanto, deve ser apresentado depois de uma hora.
   * Certifique-se de que o painel de ***visualizações*** está aberto e é mostrado no lado direito do ecrã.
3. Depois de ter os dados que flui para o Power BI, pode começar a visualizar os dados de transmissão em fluxo. Abaixo está um dashboard de exemplo com algumas visualizações o hot path afixado ao mesmo. Pode criar outros mosaicos de dashboard com base em conjuntos de dados apropriados. Consoante o tempo que executa seu gerador de dados, os seus números nas visualizações podem ser diferentes.

    ![Vista do Dashboard](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Aqui ficam alguns passos para criar um dos azulejos acima – o azulejo "Fleet View of Sensor 11 vs. Threshold 48.26":
   
   * Clique no monitor de **aeronaves** dataset na secção datasets do painel esquerdo.
   * Clique no ícone **do Gráfico de Linha.**
   * Clique no painel **"Campos"** **para** que mostre em "Eixo" no painel de **Visualizações.**
   * Clique em "s11" e "s11\_alerta" para que ambos apareçam em "Valores". Clique na pequena seta junto ao **alerta s11** e **s11\_** , mude "Soma" para "Média".
   * Clique em **SAVE** na parte superior e nomeie o relatório "aircraftmonitor". O relatório denominado "monitor de aeronaves" é mostrado na secção **relatórios** no painel **do Navegador** à esquerda.
   * Clique no ícone **Pin Visual** no canto superior direito desta tabela de linhas. Uma janela de "Afixar ao Dashboard" pode aparecer à sua escolha de um dashboard. Selecione "Demonstração de manutenção preditiva", em seguida, clique em "Pin".
   * Passe o rato sobre este azulejo no painel de instrumentos, clique no ícone "editar" no canto superior direito para alterar o seu título para "Fleet View of Sensor 11 vs. Threshold 48.26" e legenda para "Average across fleet over time".

## <a name="delete-your-solution"></a>Eliminar a solução
Certifique-se de que pare o gerador de dados quando não estiver a utilizar a solução, como executar o gerador de dados implicará custos mais elevados. Elimine a solução se não estiver a utilizá-lo. A eliminar a sua solução elimina todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da solução no painel esquerdo do modelo de solução e, em seguida, clique em **Eliminar**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custos
As seguintes duas ferramentas estão disponíveis para o ajudar a compreender melhor os custos totais envolvidos em execução a manutenção preditiva para aeronáutica de solução de modelo na sua subscrição:

* [Ferramenta de estimativa de custos do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimativa de custos do Microsoft Azure (ambiente de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)

