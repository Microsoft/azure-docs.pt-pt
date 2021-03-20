---
title: Guia de manutenção preditiva para aeroespacial - Processo de Ciência de Dados de Equipa
description: Um guia técnico para o Modelo de Solução para manutenção preditiva em aeroespacial, utilitários e transportes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321986"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico do Modelo de Solução para manutenção preditiva em aeroespacial

> [!Important]
> Este artigo foi precotado. A discussão sobre a Manutenção Preditiva em Aeroespacial ainda é relevante, mas para informação atual, consulte a [Solução Geral para Audiências empresariais.](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)


Os modelos de solução são projetados para acelerar o processo de construção de uma demonstração E2E. Um modelo implantado prevê a sua subscrição com os componentes necessários e, em seguida, constrói as relações entre eles. Também semeia o pipeline de dados com dados de amostra de uma aplicação de gerador de dados, que descarrega e instala na sua máquina local depois de implementar o modelo de solução. Os dados do gerador hidratam o pipeline de dados e começam a gerar previsões de aprendizagem automática, que podem ser visualizadas no painel Power BI.

O processo de implantação guia-o através de vários passos para configurar as suas credenciais de solução. Certifique-se de que regista as credenciais como o nome de solução, o nome de utilizador e a palavra-passe que fornece durante a implementação. 


Os objetivos deste artigo são:
- Descreva a arquitetura de referência e os componentes previstos na sua subscrição.
- Demonstre como substituir os dados da amostra pelos seus próprios dados. 
- Mostrar como modificar o modelo de solução.  

## <a name="overview"></a>Descrição geral
![Arquitetura de manutenção preditiva](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Ao implementar a solução, ativa os serviços Azure, incluindo Event Hub, Stream Analytics, HDInsight, Data Factory e Machine Learning. O diagrama de arquitetura mostra como se constrói a Manutenção Preditiva para o Modelo de Solução Aeroespacial. Pode investigar estes serviços no portal Azure clicando neles no diagrama do modelo de solução criado com a implementação da solução (exceto para HDInsight, que é a pedido quando as atividades relacionadas do pipeline são necessárias para executar e são eliminadas posteriormente).
Faça o download de uma [versão em tamanho real do diagrama.](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)

As seguintes secções descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Fonte de dados e ingestão
### <a name="synthetic-data-source"></a>Fonte de dados sintéticos
Para este modelo, a fonte de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho descarregada que executou localmente após uma implementação bem sucedida.

Para encontrar as instruções para descarregar e instalar esta aplicação, selecione o primeiro nó, Gerador de Dados de Manutenção Preditiva, no diagrama do modelo de solução. As instruções encontram-se na barra Properties. Esta aplicação alimenta o serviço [Azure Event Hub](#azure-event-hub) com pontos de dados, ou eventos, utilizados no resto do fluxo de solução. Esta fonte de dados é derivada de dados disponíveis publicamente a partir do [repositório](https://c3.nasa.gov/dashlink/resources/139/) de dados da NASA utilizando o Conjunto de [Dados de Simulação de Degradação do Motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

A aplicação de geração de eventos só povoa o Azure Event Hub enquanto está a ser executada no seu computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O serviço [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de Dados Sintético.

## <a name="data-preparation-and-analysis"></a>Preparação e análise de dados  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Utilize [o Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análises quase em tempo real no fluxo de entrada do serviço [Azure Event Hub.](#azure-event-hub) Em seguida, publica resultados num dashboard [Power BI,](https://powerbi.microsoft.com) bem como arquiva todos os eventos brutos de entrada no serviço [de Armazenamento Azure](https://azure.microsoft.com/services/storage/) para posterior processamento pelo serviço [Azure Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada HDInsight
Executar scripts [de Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) (orquestrados pela Azure Data Factory) usando HDInsight para fornecer agregações sobre os eventos brutos arquivados usando o recurso Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Faça previsões sobre a vida útil restante (RUL) de um determinado motor de aeronaves utilizando as entradas recebidas com o [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (orquestrado pela Azure Data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Utilize [a Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo Azure Machine Learning, que são depois consumidas no painel Power [BI.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Utilize [o Power BI](https://powerbi.microsoft.com) para mostrar um dashboard que contém agregações e alertas fornecidos pela [Azure Stream Analytics,](https://azure.microsoft.com/services/stream-analytics/)bem como previsões RUL armazenadas na [Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidas com [o Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como trazer os seus próprios dados
Esta secção descreve como trazer os seus próprios dados para o Azure, e que áreas requerem alterações para os dados que traz para esta arquitetura.

É improvável que o seu conjunto de dados corresponda ao conjunto de dados utilizado pelo Conjunto de Dados de [Simulação de Degradação](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) do Motor Turbofan utilizado para este modelo de solução. Compreender os seus dados e os requisitos são cruciais na forma como modifica este modelo para trabalhar com os seus próprios dados. 

As secções seguintes discutem as partes do modelo que requerem modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O Azure Event Hub é genérico; os dados podem ser publicados no hub em formato CSV ou JSON. Nenhum processamento especial ocorre no Azure Event Hub, mas é importante que compreenda os dados que são alimentados nele.

Este documento não descreve como ingerir os seus dados, mas pode facilmente enviar eventos ou dados para um Hub de Eventos Azure utilizando as APIs do Event Hub.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Utilize o recurso Azure Stream Analytics para fornecer análises quase em tempo real, lendo a partir de fluxos de dados e fornecendo dados para qualquer número de fontes.

Para a manutenção preditiva para o modelo de solução aeroespacial, a consulta Azure Stream Analytics é composta por quatro sub-consultas, cada consulta consumindo eventos do serviço Azure Event Hub, com saídas para quatro locais distintos. Estas saídas consistem em três conjuntos de dados Power BI e um local de armazenamento Azure.

A consulta Azure Stream Analytics pode ser encontrada por:

* Ligue-se ao portal Azure
* Localizar o stream Analytics empregos ![ Stream Analytics ícone que foram ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) gerados quando a solução foi implementada *(por exemplo,* **manutenção02asapbi** e **manutençãosa02asablob** para a solução de manutenção preditiva)
* Seleção
  
  * ***ENTRADAS*** para ver a entrada de consulta
  * ***CONSULTA*** PARA ver a consulta em si
  * ***SAÍDAS*** para ver as diferentes saídas

Informações sobre a construção de consultas Azure Stream Analytics podem ser encontradas na [Referência de Consulta de Análise de Fluxo](/stream-analytics-query/stream-analytics-query-language-reference) em MSDN.

Nesta solução, as consultas deslomem três conjuntos de dados com informações de análise quase em tempo real sobre o fluxo de dados de entrada para um painel power BI fornecido como parte deste modelo de solução. Como existe um conhecimento implícito sobre o formato de dados recebidos, estas consultas devem ser alteradas com base no seu formato de dados.

A consulta no segundo stream Analytics **manutenção de trabalhosa02asablob** simplesmente produz todos os eventos [do Event Hub](https://azure.microsoft.com/services/event-hubs/) para [Azure Storage](https://azure.microsoft.com/services/storage/) e, portanto, não requer qualquer alteração independentemente do seu formato de dados, uma vez que toda a informação do evento é transmitida para armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra o movimento e o processamento de dados. No Modelo de Manutenção Preditiva para Soluções Aeroespaciais, a fábrica de dados é composta por três [oleodutos](../../data-factory/concepts-pipelines-activities.md) que movem e processam os dados utilizando várias tecnologias.  Aceda à sua fábrica de dados abrindo o nó data Factory na parte inferior do diagrama do modelo de solução criado com a implementação da solução. Os erros nos seus conjuntos de dados devem-se à implantação da fábrica de dados antes do início do gerador de dados. Esses erros podem ser ignorados e não impedem o funcionamento da sua fábrica de dados.

![Erros de data-conjunto de dados da Data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Esta secção discute os [oleodutos e atividades necessários contidos](../../data-factory/concepts-pipelines-activities.md) na [Fábrica de Dados Azure.](https://azure.microsoft.com/documentation/services/data-factory/) Aqui está uma visão do diagrama da solução.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dois dos oleodutos desta fábrica contêm scripts [de Colmeia](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) usados para dividir e agregar os dados. Quando indicado, os scripts estão localizados na conta [de Armazenamento Azure](https://azure.microsoft.com/services/storage/) criada durante a configuração. A sua localização é: colmeia de scripts de manutenção \\ \\ \\ \\ \\ \\ (ou https://[Nome da solução].blob.core.windows.net/maintenancesascript).

Semelhantes às consultas [Azure Stream Analytics,](#azure-stream-analytics-1) os scripts [da Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) têm conhecimento implícito sobre o formato de dados de entrada e devem ser alterados com base no seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AgregaçãoFlightInfoPipeline*
Este [oleoduto](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) que executa um script [hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) para dividir os dados colocados no [Azure Storage](https://azure.microsoft.com/services/storage/) durante o trabalho [Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/)

O script [da Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) para esta tarefa de partição é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Este [oleoduto](../../data-factory/concepts-pipelines-activities.md) contém várias atividades cujo resultado final são as previsões pontuadas da experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associadas a este modelo de solução.

As atividades incluídas são:

* [Atividade HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) que executa um script [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) para realizar agregações e engenharia de recursos necessários para a experiência [Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/)
  O script [da Colmeia](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) para esta tarefa de partição é ***PrepareMLInput.hql***.
* [Copiar](/previous-versions/azure/dn835035(v=azure.100)) a atividade que move os resultados da atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) para uma única bolha [de armazenamento Azure](https://azure.microsoft.com/services/storage/) acedida pela atividade [AzureMLBatchScoring.](/previous-versions/azure/dn894009(v=azure.100))
* A atividade [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) chama a experiência [Azure Machine Learning,](https://azure.microsoft.com/services/machine-learning/) com resultados colocados numa única bolha [de armazenamento Azure.](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Este [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - uma atividade [copy](/previous-versions/azure/dn835035(v=azure.100)) que move os resultados da experiência [Azure Machine Learning](#azure-machine-learning) da ***MLScoringPipeline*** para a [Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) a provisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A experiência [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) utilizada para este modelo de solução fornece a Vida Útil Restante (RUL) de um motor de avião. A experiência é específica do conjunto de dados consumido e requer modificação ou substituição específica dos dados trazidos.

## <a name="monitor-progress"></a>Monitorizar o Progresso
Uma vez lançado o Gerador de Dados, o gasoduto começa a desidratar-se e os diferentes componentes da sua solução começam a entrar em ação seguindo os comandos emitidos pela fábrica de dados. Há duas maneiras de monitorizar o oleoduto.

* Um dos trabalhos do Stream Analytics escreve os dados brutos de entrada para o armazenamento de bolhas. Se clicar no componente Blob Storage da sua solução a partir do ecrã implementou com sucesso a solução e, em seguida, clicar em Abrir no painel direito, leva-o ao [portal Azure](https://portal.azure.com/). Uma vez lá, clique em Blobs. No painel seguinte, vê-se uma lista de contentores. Clique em **manutenção deadata.** No painel seguinte encontra-se a pasta **de dados brutos.** Dentro da pasta de dados brutos encontram-se pastas com nomes como hora=17 e hora=18. A presença destas pastas indica que os dados brutos estão a ser gerados no seu computador e armazenados no armazenamento de bolhas. Deve ver ficheiros csv com tamanhos finitos em MB nessas pastas.
* O último passo do pipeline é escrever dados (por exemplo, previsões de aprendizagem automática) na Base de Dados SQL. Poderá ter de esperar um máximo de três horas para que os dados apareçam na Base de Dados SQL. Uma forma de monitorizar a quantidade de dados disponíveis na sua Base de Dados SQL é através do [portal Azure.](https://portal.azure.com/) No painel esquerdo, localize as BASES de DADOS SQL :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: e clique nele. Em seguida, localize a sua base de dados **pmaintenancedb** e clique nela. Na página seguinte na parte inferior, clique em MANAGE.
   
    ![Gerir ícone](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Aqui, pode clicar em Nova Consulta e consultar o número de linhas (por exemplo, selecionar a contagem(*) do PMResult). À medida que a sua base de dados cresce, o número de filas na tabela deve aumentar.

## <a name="power-bi-dashboard"></a>Dashboard do Power BI

Crie um painel Power BI para visualizar os dados do Azure Stream Analytics (caminho quente) e os resultados da previsão do lote resultam da aprendizagem automática Azure (caminho frio).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o painel de caminhos frios
No gasoduto de dados da via fria, o objetivo é obter o RUL preditivo (vida útil restante) de cada motor de avião uma vez que termine um voo (ciclo). O resultado da previsão é atualizado a cada 3 horas para prever os motores dos aviões que terminaram um voo nas últimas 3 horas.

O Power BI liga-se a uma Base de Dados Azure SQL como fonte de dados, onde os resultados da previsão são armazenados. 

Nota: 
1.    Ao implementar a sua solução, uma previsão aparecerá na base de dados dentro de 3 horas. O ficheiro pbix que veio com o download do Gerador contém alguns dados de sementes para que possa criar o painel Power BI imediatamente. 
2.    Neste passo, o pré-requisito é descarregar e instalar o software gratuito [Power BI desktop](/power-bi/fundamentals/desktop-get-the-desktop).

Os passos a seguir guiam-no sobre como ligar o ficheiro pbix à Base de Dados SQL que foi girada no momento da implementação da solução contendo dados (por exemplo, resultados de previsão) para visualização.

1. Pegue as credenciais da base de dados.
   
   Você precisará do nome do **servidor de base de dados, nome da base de dados, nome do utilizador e palavra-passe** antes de passar para os próximos passos. Aqui estão os passos para guiá-lo como encontrá-los.
   
   * Uma vez que **'Azure SQL Database'** no seu diagrama de modelo de solução fique verde, clique nele e clique em **'Open'**.
   * Verá um novo separador/janela do navegador que exibe a página do portal Azure. Clique em **"Grupos de recursos"** no painel esquerdo.
   * Selecione a subscrição que está a utilizar para implementar a solução e, em seguida, selecione **'YourSolutionName \_ ResourceGroup'.**
   * No novo painel pop out, clique no  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: ícone para aceder à sua base de dados. O nome da sua base de dados está ao lado deste ícone (por exemplo, **'pmaintenancedb'**), e o nome do **servidor de base de dados** está listado na propriedade do nome do Servidor e deve ser semelhante a **YourSolutionName.database.windows.net**.
   * O **nome de utilizador** e **a palavra-passe** da sua base de dados são os mesmos que o nome de utilizador e a palavra-passe anteriormente registados durante a implementação da solução.
2. Atualize a fonte de dados do ficheiro de relatório de caminhos frios com o Power BI Desktop.
   
   * Na pasta onde descarregou e desapertou o ficheiro Gerador, clique duas vezes no ficheiro **PowerBI \\ PredictiveMaintenanceAerospace.pbix.** Se vir alguma mensagem de aviso quando abrir o ficheiro, ignore-as. Na parte superior do ficheiro, clique em **'Editar Consultas'.**
     
     ![Editar Consultas](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Você verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique no ícone de definições de ![ consulta ao lado de ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) **'Fonte'** em **'PASSOS APLICADOS'** no painel **'Definições de consulta'** à direita. Ignore as mensagens de aviso apresentadas.
   * Na janela pop out, substitua **'Server'** e **'Database'** pelos nomes do seu próprio servidor e base de dados e, em seguida, clique em **'OK'.** Para o nome do servidor, certifique-se de especificar a porta 1433 **(YourSolutionName.database.windows.net, 1433**). Deixe o campo base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.
   * Na próxima janela pop out, você verá duas opções no painel esquerdo **(Windows** and **Database).** Clique em **'Base de Dados',** preencha o seu **'Nome de Utilizador'** e **'Password'** (o nome de utilizador e a palavra-passe que introduziu quando implementou a solução pela primeira vez e criou uma Base de Dados Azure SQL). Em ***Selecione qual o nível para aplicar estas definições,*** verifique a opção de nível de base de dados. Em seguida, clique em **'Connect'.**
   * Clique na segunda tabela **PMResult,** em seguida, clique no ![ ícone de Navegação ](./media/predictive-maintenance-technical-guide/icon-navigation.png) ao lado de **'Fonte'** em **'PASSOS APLICADOS'** no painel **'Definições de consulta'** à direita e atualize os nomes do servidor e da base de dados como nos passos acima e clique em OK.
   * Uma vez que seja guiado de volta para a página anterior, feche a janela. Uma mensagem aparece - clique em **Aplicar**. Por último, clique no botão **Guardar** para guardar as alterações. O seu ficheiro Power BI estabeleceu agora a ligação ao servidor. Se as suas visualizações estiverem vazias, certifique-se de que limpa as seleções das visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das lendas. Utilize o botão de atualização para refletir novos dados sobre as visualizações. Inicialmente, só se vê os dados de sementes nas suas visualizações, uma vez que a fábrica de dados está programada para atualizar a cada 3 horas. Após 3 horas, verá novas previsões refletidas nas suas visualizações quando atualizar os dados.
3. (Opcional) Publique o painel de instrumentos de caminho frio para [o Power BI online.](https://www.powerbi.com/) Este passo necessita de uma conta Power BI (ou uma conta de trabalho ou escola).
   
   * Clique em **'Publicar'** e poucos segundos depois aparece uma janela que mostra "Publishing to Power BI Success!" com uma marca de verificação verde. Clique no link abaixo "Open PredictiveMaintenanceAerospace.pbix in Power BI". Para obter instruções detalhadas, consulte [a publicação do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo painel de instrumentos: clique no **+** sinal ao lado da secção **Dashboards** no painel esquerdo. Introduza o nome "Demonstração de Manutenção Preditiva" para este novo painel de instrumentos.
   * Assim que abrir o relatório, clique no ![ ícone PIN para fixar todas as ](./media/predictive-maintenance-technical-guide/icon-pin.png) visualizações no seu painel de instrumentos. Para obter instruções detalhadas, consulte [um azulejo de um painel power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá à página do painel de instrumentos e ajuste o tamanho e localização das suas visualizações e edite os seus títulos. Para encontrar instruções detalhadas sobre como editar os seus azulejos, consulte [Editar um azulejo -- redimensionar, mover, mudar, renomear, pin, excluir, adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um painel de exemplo com algumas visualizações de caminhos frios presos a ele.  Dependendo do tempo de funcionação do seu gerador de dados, os seus números nas visualizações podem ser diferentes.
     <br/>
     ![Vista final](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o rato sobre o conjunto de dados **PredictiveMaintenanceAerospace,** clique no ![ ícone Ellipsis ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) e, em seguida, escolha **Agenda Refresh**.
     <br/>
     > [!NOTE]
     > Se vir uma mensagem de aviso, clique em **Editar Credenciais** e certifique-se de que as credenciais de base de dados são as mesmas descritas no passo 1.
     <br/>
     ![Agendar atualização](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Expandir a secção **'Atualizar' agendamento.** Ligue "mantenha os seus dados atualizados".
     <br/>
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte [atualização de dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Painel de instrumentos de caminho quente de configuração
Os seguintes passos guiam-no como visualizar a produção de dados a partir de trabalhos stream Analytics que foram gerados no momento da implementação da solução. É necessária uma conta [power BI on-line](https://www.powerbi.com/) para executar os seguintes passos. Se não tiver uma conta, pode [criar uma.](https://powerbi.microsoft.com/pricing)

1. Adicione a saída power BI em Azure Stream Analytics (ASA).
   
   * Deve seguir as instruções no [Azure Stream Analytics & Power BI: Um dashboard de análise para visibilidade em tempo real dos dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho Azure Stream Analytics como o seu painel Power BI.
   * A consulta da ASA tem três saídas que são **o planetário,** **o aircraftalert,** e **os voos por hora.** Pode ver a consulta clicando no separador de consulta. Correspondendo a cada uma destas tabelas, é necessário adicionar uma saída à ASA. Quando adicionar a primeira saída **(aircraftmonitor)** certifique-se de que o **Alias de Saída,** o Nome do Conjunto de **Dados** e o Nome da **Tabela** são os mesmos **(porta-aviões).** Repita os passos para adicionar saídas para **aviões,** e **voos por hora.** Depois de ter adicionado as três tabelas de saída e iniciado o trabalho asa, deverá receber uma mensagem de confirmação ("Starting Stream Analytics job maintenancesa02asapbi conseguiu").
2. Faça login no [Power BI online](https://www.powerbi.com)
   
   * Na secção datasets do painel esquerdo no Meu Espaço de Trabalho, deve aparecer o ***DATASET** _ nomes _*aircraftmonitor**, **aircraftalert**, e **flightsbyhour.** Estes são os dados de streaming que empurrou do Azure Stream Analytics no passo anterior. O **dataset voos por hora** pode não aparecer ao mesmo tempo que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás. No entanto, deve aparecer depois de uma hora.
   * Certifique-se de que o ***painel visualizações*** está aberto e é mostrado no lado direito do ecrã.
3. Assim que tiver os dados a fluir para o Power BI, pode começar a visualizar os dados de streaming. Abaixo está um painel de exemplo com algumas visualizações de caminhos quentes fixadas nele. Pode criar outros azulejos do painel com base em conjuntos de dados apropriados. Dependendo do tempo de funcionação do seu gerador de dados, os seus números nas visualizações podem ser diferentes.

    ![Vista do Dashboard](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Aqui estão alguns passos para criar um dos azulejos acima – o azulejo "Fleet View of Sensor 11 vs. Threshold 48.26":
   
   * Clique no **painel de dados do** dataset na secção datasets do painel esquerdo.
   * Clique no ícone **'Gráfico de Linha'.**
   * Clique no **painel** de **campos** processado para que apareça no painel "Eixo" no painel **visualizações.**
   * Clique em "s11" e "s11 \_ alert" para que ambos apareçam em "Valores". Clique na seta pequena ao lado do alerta **s11** e **\_ s11,** altere "Soma" para "Média".
   * Clique em **SAVE** no topo e nomeie o relatório "aircraftmonitor". O relatório denominado "aircraftmonitor" é apresentado na secção **Relatórios** no painel **do Navegador** à esquerda.
   * Clique no ícone **Pin Visual** no canto superior direito desta tabela de linha. Uma janela "Pin to Dashboard" pode aparecer para que escolha um dashboard. Selecione "Demo de Manutenção Preditiva" e, em seguida, clique em "Pin".
   * Passe o rato sobre este azulejo no painel de instrumentos, clique no ícone "editar" no canto superior direito para alterar o seu título para "Fleet View of Sensor 11 vs. Threshold 48.26" e legendar "Média através da frota ao longo do tempo".

## <a name="delete-your-solution"></a>Elimine a sua solução
Certifique-se de que para o gerador de dados quando não utilizar ativamente a solução como funcionamento do gerador de dados irá incorrer em custos mais elevados. Elimine a solução se não estiver a usá-la. A eliminação da sua solução elimina todos os componentes previstos na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da solução no painel esquerdo do modelo de solução e, em seguida, clique em **Eliminar**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custos
As duas ferramentas que se seguem estão disponíveis para ajudá-lo a entender melhor os custos totais envolvidos na execução do Modelo de Manutenção Preditiva para Solução Aeroespacial na sua subscrição:

* [Ferramenta de estimativa de custos da Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de estimativa de custos do Microsoft Azure (ambiente de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)