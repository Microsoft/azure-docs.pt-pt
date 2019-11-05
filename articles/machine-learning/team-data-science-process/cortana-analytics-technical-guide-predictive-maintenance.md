---
title: Guia de manutenção preditiva para o processo de ciência de dados da equipe aeroespacial
description: Um guia técnico para o modelo de solução com a Microsoft Cortana Intelligence para manutenção preditiva no setor aeroespacial, utilitários e transporte.
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
ms.openlocfilehash: a73308274c9aedf6a85745c17c14637e2ef3d27d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492468"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico para o modelo de solução de Cortana Intelligence para manutenção preditiva no setor aeroespacial

> [!Important]
> Este artigo foi preterido. A discussão sobre a manutenção preditiva no setor aeroespacial ainda é relevante, mas para obter informações atuais, consulte [visão geral da solução para públicos comerciais](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Os modelos de solução foram projetados para acelerar o processo de criação de uma demonstração E2E sobre Cortana Intelligence Suite. Um modelo implantado provisiona sua assinatura com os componentes de Cortana Intelligence necessários e, em seguida, cria as relações entre eles. Ele também propaga o pipeline de dados com dados de exemplo de um aplicativo gerador de dados, que você baixa e instala em seu computador local depois de implantar o modelo de solução. Os dados do gerador hidratamm o pipeline de dados e começam a gerar previsões de aprendizado de máquina, que podem ser visualizadas no painel de Power BI.

O processo de implantação orienta você por várias etapas para configurar suas credenciais de solução. Certifique-se de registrar as credenciais, como nome da solução, nome de usuário e senha que você fornece durante a implantação. 


Os objetivos deste artigo são:
- Descreva a arquitetura de referência e os componentes provisionados em sua assinatura.
- Demonstre como substituir os dados de exemplo pelos seus próprios dados. 
- Mostre como modificar o modelo de solução.  

> [!TIP]
> Você pode baixar e imprimir uma [versão em PDF deste artigo](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Descrição geral
![Arquitetura de manutenção preditiva](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Quando você implanta a solução, ela ativa os serviços do Azure no Cortana Analytics Suite (incluindo o Hub de eventos, Stream Analytics, HDInsight, Data Factory e Machine Learning). O diagrama de arquitetura mostra como a manutenção preditiva para o modelo de solução aeroespacial é construída. Você pode investigar esses serviços na portal do Azure clicando neles no diagrama de modelo de solução criado com a implantação da solução (exceto para o HDInsight, que é provisionada sob demanda quando as atividades de pipeline relacionadas são necessárias para execução e são excluído posteriormente).
Baixe uma [versão em tamanho normal do diagrama](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As seções a seguir descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Fonte de dados e ingestão
### <a name="synthetic-data-source"></a>Fonte de dados sintéticas
Para esse modelo, a fonte de dados usada é gerada de um aplicativo de área de trabalho que você baixa e executa localmente após a implantação bem-sucedida.

Para encontrar as instruções para baixar e instalar esse aplicativo, selecione o primeiro nó, gerador de dados de manutenção preditiva, no diagrama de modelo de solução. As instruções são encontradas na barra de propriedades. Esse aplicativo alimenta o serviço [Hub de eventos do Azure](#azure-event-hub) com pontos de dados, ou eventos, usados no restante do fluxo da solução. Essa fonte de dados é derivada de dados disponíveis publicamente do [repositório de dados da NASA](https://c3.nasa.gov/dashlink/resources/139/) usando o [conjunto de dados de simulação de degradação do mecanismo turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de eventos popula o Hub de eventos do Azure somente enquanto ele está em execução no computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O serviço [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela fonte de dados sintética.

## <a name="data-preparation-and-analysis"></a>Preparação e análise de dados  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Use [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análise quase em tempo real no fluxo de entrada do serviço [Hub de eventos do Azure](#azure-event-hub) . Em seguida, você publica os resultados em um painel de [Power bi](https://powerbi.microsoft.com) , bem como arquiva todos os eventos de entrada brutos no serviço de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Azure data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada do HDInsight
Execute scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrados por Azure data Factory) usando o HDInsight para fornecer agregações nos eventos brutos arquivados usando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Faça previsões na RUL (vida útil restante) de um mecanismo de aeronave específico usando as entradas recebidas com o [serviço de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (orquestrado por Azure data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Use o [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo Azure Machine Learning, que são consumidas no painel [Power bi](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Use [Power bi](https://powerbi.microsoft.com) para mostrar um painel que contém agregações e alertas fornecidos pelo [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), bem como previsões de RUL armazenadas no [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidos usando [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como trazer seus próprios dados
Esta seção descreve como trazer seus próprios dados para o Azure e quais áreas exigem alterações para os dados que você traz para essa arquitetura.

É improvável que seu conjunto de dados corresponda ao DataSet usado pelo [turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) usado para esse modelo de solução. Entender seus dados e os requisitos é crucial para a maneira como você modifica esse modelo para trabalhar com seus próprios dados. 

As seções a seguir discutem as partes do modelo que exigem modificações quando um novo conjunto de uma é introduzido.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O Hub de eventos do Azure é genérico; os dados podem ser postados no Hub no formato CSV ou JSON. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que você compreenda os dados que estão inseridos nele.

Este documento não descreve como ingerir seus dados, mas você pode facilmente enviar eventos ou dados para um hub de eventos do Azure usando as APIs do hub de eventos.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Use o serviço de Azure Stream Analytics para fornecer análise quase em tempo real, lendo de fluxos de dados e gerando dados para qualquer número de fontes.

Para a manutenção preditiva do modelo de solução aeroespacial, a consulta de Azure Stream Analytics consiste em quatro subconsultas, cada uma consulta consumindo eventos do serviço Hub de eventos do Azure, com saídas para quatro locais distintos. Essas saídas consistem em três conjuntos de Power BI e um local de armazenamento do Azure.

A consulta Azure Stream Analytics pode ser encontrada por:

* Conectar-se ao portal do Azure
* Localizar os trabalhos de Stream Analytics ![ícone de Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) que foram gerados quando a solução foi implantada (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a manutenção preditiva soluções
* Selecione
  
  * ***Entradas*** para exibir a entrada da consulta
  * ***Consulta*** para exibir a própria consulta
  * ***Saídas*** para exibir as diferentes saídas

Informações sobre Azure Stream Analytics construção de consulta podem ser encontradas na [referência de consulta de Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) no msdn.

Nessa solução, as consultas geram três conjuntos de dados com informações de análise quase em tempo real sobre o fluxo de dado de entrada para um painel de Power BI fornecido como parte desse modelo de solução. Como há conhecimento implícito sobre o formato de dados de entrada, essas consultas devem ser alteradas com base no seu formato de dados.

A consulta no segundo Stream Analytics trabalho **maintenancesa02asablob** simplesmente gera todos os eventos do [Hub de eventos](https://azure.microsoft.com/services/event-hubs/) para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, portanto, não requer nenhuma alteração, independentemente do seu formato de dados, pois as informações completas do evento são transmitidas para repositório.

### <a name="azure-data-factory"></a>Azure Data Factory
O serviço de [Azure data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra a movimentação e o processamento de dados. No modelo manutenção preditiva para a solução aeroespacial, o data factory é composto de três [pipelines](../../data-factory/concepts-pipelines-activities.md) que movem e processam os dados usando várias tecnologias.  Acesse sua data factory abrindo o nó Data Factory na parte inferior do diagrama do modelo de solução criado com a implantação da solução. Os erros em seus conjuntos de dados são devido a data factory implantadas antes do início do gerador de data. Esses erros podem ser ignorados e não impedem o funcionamento do data factory

![Erros do Data Factory DataSet](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta seção aborda os [pipelines e atividades](../../data-factory/concepts-pipelines-activities.md) necessários contidos no [Azure data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Aqui está uma exibição de diagrama da solução.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos pipelines deste alocador contêm scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para particionar e agregar os dados. Quando observado, os scripts estão localizados na conta de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. Sua localização é: maintenancesascript\\\\script\\\\Hive\\\\ (ou https://[nome da solução]. blob. Core. Windows. net/maintenancesascript).

Semelhante a consultas [Azure Stream Analytics](#azure-stream-analytics-1) , os scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm conhecimento implícito sobre o formato de dados de entrada e devem ser alterados com base no seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para particionar os dados colocados no [armazenamento do Azure](https://azure.microsoft.com/services/storage/) durante o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) trabalho.

O script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém várias atividades cujo resultado final são as previsões pontuadas do experimento [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associado a esse modelo de solução.

As atividades incluídas são:

* Atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para executar agregações e engenharia de recursos necessários para o experimento de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) .
  O script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***PrepareMLInput. HQL***.
* Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) para um único blob de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .
* A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) chama o experimento de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) , com resultados colocados em um único blob de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados do [Azure Machine Learning](#azure-machine-learning) experimento do ***MLScoringPipeline*** para o [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) provisionado como parte da solução instalação do modelo.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O experimento [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a RUL (vida útil restante) de um mecanismo de aeronave. O experimento é específico do conjunto de dados consumido e requer modificação ou substituição específica para os dados trazidos.

Para obter informações sobre como o experimento de Azure Machine Learning foi criado, consulte [manutenção preditiva: etapa 1 de 3, preparação de dados e engenharia de recursos](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitorar o progresso
Depois que o gerador de dados é iniciado, o pipeline começa a esvaziar e os diferentes componentes da solução iniciam o iniciar em ação seguindo os comandos emitidos pelo data factory. Há duas maneiras de monitorar o pipeline.

1. Um dos trabalhos de Stream Analytics grava os dados de entrada brutos no armazenamento de BLOBs. Se você clicar no componente de armazenamento de BLOBs da sua solução na tela em que implantou a solução com êxito e, em seguida, clicar em abrir no painel à direita, ele levará você para a [portal do Azure](https://portal.azure.com/). Uma vez lá, clique em BLOBs. No painel seguinte, você verá uma lista de contêineres. Clique em **maintenancesadata**. No painel seguinte está a pasta **RAWDATA** . Dentro da pasta RAWDATA estão pastas com nomes como hora = 17 e hora = 18. A presença dessas pastas indica que os dados brutos estão sendo gerados em seu computador e armazenados no armazenamento de BLOBs. Você deve ver arquivos CSV com tamanhos finitos em MB nessas pastas.
2. A última etapa do pipeline é gravar dados (por exemplo, previsões do Machine Learning) no banco de dado SQL. Talvez seja necessário aguardar um máximo de três horas para que os dados apareçam no SQL Database. Uma maneira de monitorar a quantidade de dados disponível no banco de dado SQL é por meio do [portal do Azure](https://portal.azure.com/). No painel esquerdo, localize bancos de dados SQL ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) e clique nele. Em seguida, localize o banco de dados **pmaintenancedb** e clique nele. Na página seguinte na parte inferior, clique em gerenciar
   
    ![Ícone gerenciar](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Aqui, você pode clicar em Nova consulta e consultar o número de linhas (por exemplo, Count de seleção (*) de PMResult). À medida que seu banco de dados cresce, o número de linhas na tabela deve aumentar.

## <a name="power-bi-dashboard"></a>Dashboard do Power BI

Configure um painel de Power BI para visualizar os dados de Azure Stream Analytics (Hot Path) e os resultados de previsão de lote do Azure Machine Learning (caminho frio).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o painel de caminho frio
No pipeline de dados de caminho frio, o objetivo é obter o RUL de previsão (vida útil restante) de cada mecanismo de aeronave após a conclusão de um vôo (ciclo). O resultado da previsão é atualizado a cada três horas para prever os mecanismos de aeronave que concluíram um vôo nas últimas 3 horas.

O Power BI se conecta a um banco de dados SQL do Azure como sua fonte, onde os resultados da previsão são armazenados. Observação: 1) na implantação da solução, uma previsão aparecerá no banco de dados dentro de 3 horas.
O arquivo pbix que acompanha o download do gerador contém alguns dados de semente para que você possa criar o painel de Power BI imediatamente. 2) nesta etapa, o pré-requisito é baixar e instalar o software gratuito [Power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

As etapas a seguir guiam você sobre como conectar o arquivo pbix ao banco de dados SQL que foi girado no momento da implantação da solução que contém dados (por exemplo, resultados de previsão) para visualização.

1. Obtenha as credenciais do banco de dados.
   
   Você precisará **do nome do servidor de banco de dados, nome do banco de dados, nome de usuário e senha** antes de passar para as próximas etapas. Aqui estão as etapas para orientá-lo a encontrá-los.
   
   * Depois que o **' banco de dados SQL do Azure '** estiver verde, clique nele e clique em **' abrir '** .
   * Você verá uma nova guia/janela do navegador que exibe a página portal do Azure. Clique em **' grupos de recursos '** no painel esquerdo.
   * Selecione a assinatura que você está usando para implantar a solução e, em seguida, selecione **' nomedasolução\_resourcegroup '** .
   * No novo painel pop-out, clique no ícone ![SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ícone para acessar seu banco de dados. O nome do banco de dados está ao lado desse ícone (por exemplo, **' pmaintenancedb '** ) e o **nome do servidor de banco de dados** é listado sob a propriedade nome do servidor e deve ser semelhante a **YourSolutionName.Database.Windows.net**.
   * O **nome de usuário** e a **senha** do banco de dados são os mesmos que o nome de usuário e a senha gravados anteriormente durante a implantação da solução.
2. Atualize a fonte de dados do arquivo de relatório de caminho frio com Power BI Desktop.
   
   * Na pasta em que você baixou e descompactou o arquivo do gerador, clique duas vezes no arquivo **PowerBI\\PredictiveMaintenanceAerospace. pbix** . Se você vir qualquer mensagem de aviso ao abrir o arquivo, ignore-o. Na parte superior do arquivo, clique em **' editar consultas '** .
     
     ![Editar consultas](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Você verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique ![ícone configurações de consulta](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) ao lado de **"origem"** em **"etapas aplicadas"** no painel **"configurações de consulta"** à direita. Ignore as mensagens de aviso que aparecem.
   * Na janela pop-out, substitua **' Server '** e **' database '** pelos seus próprios nomes de servidor e de banco de dados e clique em **' OK '** . Para nome do servidor, certifique-se de especificar a porta 1433 (**YourSolutionName.Database.Windows.net, 1433**). Deixe o campo de banco de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem na tela.
   * Na próxima janela pop-out, você verá duas opções no painel esquerdo (**Windows** e banco de **dados**). Clique em **' banco de dados '** , preencha seu **' nome de usuário '** e **' senha '** (esse é o nome de usuário e a senha que você inseriu quando implantou a solução pela primeira vez e criou um banco de dados SQL do Azure). Em ***selecionar a qual nível aplicar essas configurações***, verifique a opção nível de banco de dados. Em seguida, clique em **' conectar '** .
   * Clique na segunda tabela **PMResult** e clique ![ícone de navegação](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) ao lado de **"origem"** em **"etapas aplicadas"** no painel **"configurações de consulta"** à direita e atualize os nomes de servidor e banco de dados como nas etapas acima e clique em OK.
   * Depois que você for guiado de volta para a página anterior, feche a janela. Uma mensagem é exibida-clique em **aplicar**. Por fim, clique no botão **salvar** para salvar as alterações. Seu arquivo de Power BI agora estabeleceu conexão com o servidor. Se suas visualizações estiverem vazias, certifique-se de desmarcar as seleções nas visualizações para Visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão atualizar para refletir novos dados nas visualizações. Inicialmente, você vê apenas os dados semente em suas visualizações, pois a data factory está agendada para ser atualizada a cada 3 horas. Após 3 horas, você verá novas previsões refletidas em suas visualizações ao atualizar os dados.
3. Adicional Publique o painel de caminho frio para [Power bi online](https://www.powerbi.com/). Observe que essa etapa precisa de uma conta de Power BI (ou conta do Office 365).
   
   * Clique em **' publicar '** e alguns segundos depois que uma janela for exibida, exibindo "publicação em Power bi êxito!" com uma marca de seleção verde. Clique no link abaixo de "abrir PredictiveMaintenanceAerospace. pbix no Power BI". Para encontrar instruções detalhadas, consulte [publicar de Power bi desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo painel: clique no sinal de **+** ao lado da seção **painéis** no painel esquerdo. Digite o nome "demonstração de manutenção preditiva" para esse novo painel.
   * Depois de abrir o relatório, clique ![ícone de pino](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) para fixar todas as visualizações em seu painel. Para encontrar instruções detalhadas, confira [fixar um bloco em um painel de Power bi de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página painel e ajuste o tamanho e o local de suas visualizações e edite seus títulos. Para encontrar instruções detalhadas sobre como editar seus blocos, consulte [Editar um bloco – redimensionar, mover, renomear, fixar, excluir, Adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um painel de exemplo com algumas visualizações de caminho frio fixadas a ela.  Dependendo de quanto tempo você executa o gerador de dados, seus números nas visualizações podem ser diferentes.
     <br/>
     ![exibição final](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o mouse sobre o conjunto de **PredictiveMaintenanceAerospace** , clique ![ícone de reticências](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) e, em seguida, escolha **agendar atualização**.
     <br/>
     **Observação:** Se você vir um aviso mensagem, clique em **Editar credenciais** e verifique se suas credenciais de banco de dados são as mesmas descritas na etapa 1.
     <br/>
     ![agendar atualização](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expanda a seção **agendar atualização** . Ative "manter seus dados atualizados".
     <br/>
   * Agende a atualização com base em suas necessidades. Para obter mais informações, consulte [atualização de dados em Power bi](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Configurar painel do Hot Path
As etapas a seguir guiam como visualizar a saída de dados de Stream Analytics trabalhos que foram gerados no momento da implantação da solução. Uma conta do [Power bi online](https://www.powerbi.com/) é necessária para executar as etapas a seguir. Se você não tiver uma conta, poderá [criar uma](https://powerbi.microsoft.com/pricing).

1. Adicione Power BI saída no Azure Stream Analytics (ASA).
   
   * Você deve seguir as instruções em [Azure Stream Analytics & Power bi: um painel de análise para visibilidade em tempo real de streaming de dados](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída de seu trabalho de Azure Stream Analytics como seu painel de Power bi.
   * A consulta ASA tem três saídas que são **aircraftmonitor**, **aircraftalert**e **flightsbyhour**. Você pode exibir a consulta clicando na guia consulta. correspondente a cada uma dessas tabelas, você precisa adicionar uma saída ao ASA. Quando você adiciona a primeira saída (**aircraftmonitor**), verifique se o alias de saída, o **nome do conjunto** de **resultados**e o **nome da tabela** são os mesmos (**aircraftmonitor**). Repita as etapas para adicionar saídas para **aircraftalert**e **flightsbyhour**. Depois de adicionar todas as três tabelas de saída e iniciar o trabalho do ASA, você deverá obter uma mensagem de confirmação ("Iniciando Stream Analytics trabalho maintenancesa02asapbi com êxito").
2. Faça logon no [Power bi online](https://www.powerbi.com)
   
   * Na seção conjuntos de banco de valores do painel esquerdo em meu espaço de trabalho, os nomes de ***conjunto*** de **aircraftmonitor**, **aircraftalert**e **flightsbyhour** devem aparecer. Esses são os dados de streaming que você enviou de Azure Stream Analytics na etapa anterior. O conjunto de **flightsbyhour** pode não aparecer ao mesmo tempo que os outros dois conjuntos de data devido à natureza da consulta SQL por trás dele. No entanto, ele deve aparecer após uma hora.
   * Verifique se o painel ***visualizações*** está aberto e é mostrado no lado direito da tela.
3. Depois que os dados estiverem fluindo para o Power BI, você poderá começar a visualizar os dados de streaming. Veja abaixo um painel de exemplo com algumas visualizações de caminho quente fixadas a ele. Você pode criar outros blocos de painel com base em conjuntos de valores apropriados. Dependendo de quanto tempo você executa o gerador de dados, seus números nas visualizações podem ser diferentes.

    ![Vista do Dashboard](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Aqui estão algumas etapas para criar um dos blocos acima – o bloco "exibição da frota do sensor 11 vs. limite 48,26":
   
   * Clique em conjunto de **aircraftmonitor** na seção conjuntos de banco de valores do painel esquerdo.
   * Clique no ícone de **gráfico de linhas** .
   * Clique em **processado** no painel **campos** para que ele seja exibido em "eixo" no painel **visualizações** .
   * Clique em "S11" e em "S11\_alerta" para que ambos apareçam em "valores". Clique na seta pequena ao lado de **S11** e **S11\_alerta**, altere "Sum" para "Average".
   * Clique em **salvar** na parte superior e nomeie o relatório como "aircraftmonitor". O relatório chamado "aircraftmonitor" é mostrado na seção **relatórios** no painel **navegador** à esquerda.
   * Clique no ícone **fixar Visual** no canto superior direito deste gráfico de linhas. Uma janela "fixar no painel" pode aparecer para que você escolha um painel. Selecione "demonstração de manutenção preditiva" e clique em "fixar".
   * Passe o mouse sobre este bloco no painel, clique no ícone "Editar" no canto superior direito para alterar seu título para "exibição de frota do sensor 11 versus o limite 48,26" e o subtítulo para "média entre a frota ao longo do tempo".

## <a name="delete-your-solution"></a>Excluir sua solução
Certifique-se de interromper o gerador de dados quando não estiver usando ativamente a solução, pois a execução do gerador de dados incorrerá em custos mais altos. Exclua a solução se você não a estiver usando. Excluir sua solução exclui todos os componentes provisionados em sua assinatura quando você implantou a solução. Para excluir a solução, clique no nome da solução no painel esquerdo do modelo de solução e clique em **excluir**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custo
As duas ferramentas a seguir estão disponíveis para ajudá-lo a entender melhor os custos totais envolvidos na execução da manutenção preditiva para o modelo de solução aeroespacial em sua assinatura:

* [Ferramenta de estimador de custo Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimador de custo Microsoft Azure (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

