---
title: Migrar dados do Log Analytics para Azure HDInsight
description: Conheça as alterações à integração do Azure Monitor e as melhores práticas para a utilização das novas tabelas.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741809"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Guia de migração do Log Analytics para clusters Azure HDInsight

 A azure HDInsight é um serviço de cluster gerido e pronto para a empresa. Este serviço executa estruturas de análise de código aberto como Apache Spark, Hadoop, HBase e Kafka em Azure. O Azure HDInsight integrou-se com outros serviços Azure para permitir aos clientes gerir melhor as suas aplicações de big data analytics.

O Log Analytics fornece uma ferramenta no portal Azure para editar e executar consultas de registo. As consultas provêm de dados recolhidos pelos Registos do Monitor Azure e analisam interativamente os seus resultados. Os clientes podem utilizar consultas de Log Analytics para recuperar registos que correspondam a critérios específicos. Também podem usar consultas para identificar tendências, analisar padrões e fornecer insights sobre os seus dados.

A azure HDInsight permitiu a integração com o Log Analytics em 2017. Os clientes HDInsight adotaram rapidamente esta funcionalidade para monitorizar os seus clusters HDInsight e consultar os registos nos clusters. Embora a adoção desta funcionalidade tenha aumentado, os clientes forneceram feedback sobre a integração:

- Os clientes não podem decidir quais os registos a armazenar, e armazenar todos os registos pode tornar-se caro.
- Os registos de esquemas hdinsight atuais não estão a seguir convenções consistentes de nomeação e algumas tabelas são repetitivas.
- Os clientes querem um dashboard fora da caixa para monitorizar facilmente o KPI dos seus clusters HDInsight.
- Os clientes devem saltar para o Log Analytics para executar consultas simples.

## <a name="solution-overview"></a>Descrição geral da solução

Considerando o feedback do cliente, a equipa Azure HDInsight investiu na integração com o Azure Monitor. Esta integração permite:

- Um novo conjunto de tabelas no espaço de trabalho log analytics dos clientes. As novas tabelas são entregues através de um novo pipeline Log Analytics.
- Maior fiabilidade
- Entrega de registo mais rápido
- Agrupamentos de tabelas baseados em recursos e consultas padrão

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Benefícios da nova integração do Azure Monitor

Este documento descreve as alterações à integração do Azure Monitor e fornece as melhores práticas para a utilização das novas tabelas.

**Esquemas redesenhados**: O esquema de formatação para a nova integração do Azure Monitor é mais bem organizado e fácil de entender. Há dois terços menos esquemas para eliminar o máximo de ambiguidade possível nos esquemas do legado.

**Registo Seletivo (libertação em breve)**: Existem registos e métricas disponíveis através do Log Analytics. Para ajudá-lo a economizar nos custos de monitorização, vamos lançar uma nova funcionalidade de registo seletivo. Utilize esta função para ligar e desligar diferentes registos e fontes métricas. Com esta funcionalidade, só terá de pagar pelo que utiliza.

**Logs cluster portal integração**: O **painel de logs** é novo no portal HDInsight Cluster. Qualquer pessoa com acesso ao cluster pode ir a este painel para consultar qualquer mesa para a quais o recurso de cluster envia registos. Os utilizadores já não precisam de acesso ao espaço de trabalho do Log Analytics para ver os registos de um recurso específico do cluster.

**Insights cluster portal integração**: O painel **insights** também é novo no portal HDInsight Cluster. Depois de permitir a nova integração do Azure Monitor, pode selecionar o painel **insights** e um painel de registos e métricas fora de caixa específico do tipo do cluster irá automaticamente povoar para si. Estes dashboards foram renovados a partir das nossas soluções Azure anteriores. Dão-te uma ideia profunda do desempenho e da saúde do teu grupo.

**Insights em escala**: Pode utilizar o novo livro **de insights à escala** no portal **Azure Monitor** para monitorizar a saúde e o desempenho dos seus clusters em diferentes subscrições.

## <a name="customer-scenarios"></a>Cenários de clientes

As secções seguintes descrevem como os clientes podem usar a nova integração do Azure Monitor em diferentes cenários. A nova secção [de integração do Azure Monitor](#activate-a-new-azure-monitor-integration) descreve como ativar e utilizar a nova integração do Azure Monitor. A [migração do Azure Monitor Classic para a nova secção de Integração do Monitor Azure](#migrate-to-the-new-azure-monitor-integration) inclui informações adicionais para os utilizadores que dependem da antiga integração do Azure Monitor.

> [!NOTE]
> Apenas os clusters criados no final de setembro de 2020 e depois são elegíveis para a nova integração do Azure Monitoring.

## <a name="activate-a-new-azure-monitor-integration"></a>Ativar uma nova integração do Azure Monitor 

> [!NOTE]
> Tem de ter um espaço de trabalho log Analytics criado numa subscrição a que tem acesso antes de permitir a nova integração. Para obter mais informações sobre como criar um espaço de trabalho log analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/learn/quick-create-workspace.md)

Ative a nova integração indo para a página do portal do seu cluster e deslocando o menu à esquerda até chegar à secção **de Monitorização.** Na secção **de Monitorização,** selecione **a integração do Monitor**. Em seguida, **selecione Enable** e pode escolher o espaço de trabalho Log Analytics para o qual deseja que os seus registos sejam enviados. **Selecione Save** uma vez que tenha escolhido o seu espaço de trabalho. 

### <a name="access-the-new-tables"></a>Aceda às novas tabelas

Há duas maneiras de aceder às novas mesas. 

### <a name="approach-1"></a>Abordagem 1:
A primeira forma de aceder às novas tabelas é através do espaço de trabalho Log Analytics. 

1. Vá ao espaço de trabalho Log Analytics que selecionou quando ativou a integração. 
2. Desloque-se no menu do lado esquerdo do ecrã e selecione **Logs**. Um editor de consulta de Logs aparecerá com uma lista de todas as tabelas no espaço de trabalho. 
3. Se as tabelas forem agrupadas pela **Solution,** as novas tabelas HDI estão na secção **Gestão de Registos.** 
4. Se agrupar as tabelas por **Tipo de Recurso,** as tabelas estão sob a secção **hdInsight Clusters,** como mostra a imagem abaixo. 

> [!NOTE]
> Este processo descreve como os registos foram acedidos na antiga integração. Isto requer que o utilizador tenha acesso ao espaço de trabalho.

### <a name="approach-2"></a>Abordagem 2:

A segunda forma de aceder às novas tabelas é através do acesso ao portal Cluster.
 
1. Navegue na página do portal do cluster e desloque o menu do lado esquerdo até ver a secção **de Monitorização.** Nesta secção, você verá o painel **de logs.** 
2. Aparece o **Selecionador de Registos** e um editor de consulta de registos. O editor contém todos os registos que estão associados ao recurso cluster. Enviou os registos para o espaço de trabalho Do Log Analytics quando permitiu a integração. Estes registos fornecem acesso baseado em recursos (RBAC). Com o RBAC, os utilizadores que tenham acesso ao cluster mas não ao espaço de trabalho podem ver os registos que estão associados ao cluster.

Para comparação, as imagens que se seguem mostram a visão do espaço de trabalho de integração do legado e a nova visão do espaço de trabalho de integração:

**Vista de espaço de trabalho de integração de legado**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Screenshot que mostra a visão do espaço de trabalho de integração do legado." border="false":::

**Nova visão do espaço de trabalho de integração**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Screenshot que mostra a nova vista do espaço de trabalho de integração." border="false":::

### <a name="use-the-new-tables"></a>Use as novas mesas

Estas integrações podem ajudá-lo a usar as novas tabelas:

#### <a name="default-queries-to-use-with-new-tables"></a>Consultas padrão para usar com novas tabelas

No seu editor de consulta de Logs, desa um alterne para **consultas** acima da lista de tabelas. Certifique-se de que agrupa as consultas por **Tipo de Recurso** e que não existe um conjunto de filtros para um tipo de recurso diferente dos **Clusters HDInsight**. A imagem a seguir mostra como os resultados são agrupados pelo **Tipo de Recurso** e filtrados para **clusters HDInsight**. Basta selecionar um e aparece no editor de consulta de Logs. Não se esqueça de ler os comentários incluídos nas consultas, pois alguns exigem que introduza algumas informações, como o seu nome de cluster, para que a consulta seja executada com sucesso.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Screenshot que mostra o tipo de recurso agrupado por resultados de consulta padrão." border="true":::


#### <a name="create-your-own-queries"></a>Crie as suas próprias consultas

Pode inserir as suas próprias consultas no editor de consultas logs. As consultas usadas nas mesas antigas não serão válidas nas novas mesas, uma vez que muitas das novas mesas têm esquemas novos e refinados. As consultas padrão são ótimas referências para moldar consultas nas novas tabelas.

#### <a name="insights"></a>Informações

Insights são dashboards de visualização específicos do cluster feitos usando [livros de trabalho Azure](../azure-monitor/platform/workbooks-overview.md). Estes dashboards dão-lhe gráficos detalhados e visualizações de como o seu cluster está a funcionar. Os dashboards têm secções para cada tipo de cluster, YARN, métricas do sistema e registos de componentes. Pode aceder ao painel de instrumentos do seu cluster visitando a página do seu cluster no portal, deslocando-se até à secção **de Monitorização** e selecionando o painel **insights.** O painel de instrumentos carrega automaticamente se tiver ativado a nova integração. Deixe alguns segundos para que os gráficos carreguem à medida que consultam os registos.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Screenshot que mostra o painel de visualização.":::

#### <a name="custom-azure-workbooks"></a>Livros de Azure personalizados

Pode criar os seus próprios livros Azure com gráficos e visualizações personalizadas. Na página do portal do seu cluster, desloque-se até à secção **de Monitorização** e selecione o painel **de livros** de trabalho no menu à esquerda. Pode começar a usar um modelo em branco ou utilizar um dos modelos na secção **HdInsight Clusters.** Há um modelo para cada tipo de cluster. Os modelos são úteis se pretender guardar personalizações específicas que o HDInsight Insights padrão não fornece. Sinta-se à vontade para enviar pedidos de novas funcionalidades no HDInsight Insights se sentir que lhes falta alguma coisa.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Livros à escala para novas integrações do Azure Monitor

Use o nosso novo livro à escala para obter uma experiência de monitorização multi-cluster para os seus clusters. O nosso livro em escala mostra-lhe qual dos seus clusters tem o gasoduto de monitorização ativado. O livro também lhe dá uma maneira simples de verificar a saúde de vários aglomerados ao mesmo tempo. Para ver este livro:

1. Vá à página do **Monitor Azure** a partir da página inicial do portal Azure
2. Uma vez na página **Azure Monitor,** selecione **Insights Hub** na secção **Insights.**
3. Selecione **HDInsight Clusters** na secção **Analytics.**

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Screenshot que mostra o livro à escala." border="false":::

#### <a name="alerts"></a>Alertas

Pode adicionar alertas personalizados aos seus clusters e espaços de trabalho no editor de consulta log. Aceda ao editor de consulta de Logs selecionando o painel **de Logs** do seu cluster ou portal do espaço de trabalho. Executar uma consulta e, em seguida, selecione **Nova Regra de Alerta** como mostrado na imagem seguinte. Para mais informações, leia sobre [configurar alertas.](../azure-monitor/platform/alerts-log.md)

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Screenshot que mostra o novo alerta de regra." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migrar para a nova Integração do Monitor Azure

Se estiver a utilizar a integração clássica do Azure Monitor, tem de fazer alguns ajustes nos novos formatos de mesa depois de mudar para a nova integração do Azure Monitor.

Para permitir a nova integração do Azure Monitor, siga os passos descritos na secção de integração do [Monitor Azure.](#activate-a-new-azure-monitor-integration)

### <a name="run-queries-in-log-analytics"></a>Executar consultas em Log Analytics

Uma vez que o novo formato de mesa é diferente do anterior, as suas consultas precisam de ser reformuladas para que possa utilizar as nossas novas tabelas. Assim que ativar a nova integração do Azure Monitor, pode navegar nas tabelas e esquemas para identificar os campos utilizados nas suas consultas antigas.

Fornecemos uma [mesa de mapeamento](#appendix-table-mapping) entre a mesa antiga e a nova mesa para ajudá-lo a encontrar rapidamente os novos campos que precisa de usar para migrar os seus dashboards e consultas.

**Consultas padrão**: Criámos consultas padrão que mostram como usar as novas tabelas para situações comuns. As consultas padrão também mostram que informações estão disponíveis em cada tabela. Pode aceder às consultas predefinitivas seguindo as instruções nas [consultas Predefinidos para utilizar com a](#default-queries-to-use-with-new-tables) secção de novas tabelas neste artigo.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Atualizar dashboards para clusters HDInsight

Se construiu vários dashboards para monitorizar os seus clusters HDInsight, tem de ajustar a consulta atrás da mesa assim que ativar a nova integração do Azure Monitor. O nome da mesa ou o nome de campo podem mudar na nova integração, mas toda a informação que tem na antiga integração está incluída.

Consulte a [tabela de mapeamento](#appendix-table-mapping) entre a tabela/esquema antigo e a nova tabela/esquema para atualizar a consulta por trás dos painéis de instrumentos.

#### <a name="out-of-box-dashboards"></a>Painéis fora da caixa 

Também melhorámos os painéis fora da caixa, tanto ao nível do cluster. Há um botão no topo direito de cada gráfico que permite ver a consulta subjacente que produz a informação. O gráfico é uma ótima maneira de se familiarizar com a forma como as novas tabelas podem ser consultadas eficazmente. Pode aceder aos dashboards fora de caixa seguindo as instruções que encontrará nos [livros de insights](#insights) e em escala para novas secções de [integração do Azure Monitor.](#at-scale-workbooks-for-new-azure-monitor-integrations)

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Utilize um painel de monitorização hdInsight à escala

Se estiver a utilizar o painel de monitorização fora da caixa para clusters HDInsight como o HDInsight Spark Monitoring e o HDInsight Interactive Monitoring, estamos a trabalhar para lhe fornecer as mesmas capacidades no portal Azure Monitor.

Verá que há uma opção de clusters HDInsight no Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Screenshot que mostra a opção HDInsight no Azure Monitor." border="false":::

O Centro de Insights do portal Azure Monitor fornece-lhe a capacidade de monitorizar vários clusters HDInsight num só local. Organizamos os clusters com base no tipo de carga de trabalho, para que veja tipos como Spark, HBase e Hive. Em vez de ir a vários dashboards, agora pode monitorizar todos os seus clusters HDInsight nesta vista.

> [!NOTE]
> Para obter mais informações, consulte os [livros de insights](#insights) e em escala para novas secções de [integração do Azure Monitor](#at-scale-workbooks-for-new-azure-monitor-integrations) neste artigo.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Permitir ambas as integrações para acelerar a migração

Você pode ativar tanto as integrações clássicas como as novas integrações do Azure Monitor ao mesmo tempo num cluster que é elegível para ambas as integrações migrarem rapidamente para a nova integração do Azure Monitor. A nova integração está disponível para todos os clusters criados a partir de meados de setembro de 2020.

Desta forma, pode facilmente fazer uma comparação lado a lado para as consultas que está a usar.

### <a name="enabling-the-classic-integration"></a>Possibilitando a integração clássica

Se estiver a utilizar um cluster criado depois de meados de setembro de 2020, verá a nova experiência do portal no portal do seu cluster. Para ativar o novo oleoduto, pode seguir os passos descritos na secção Ativar uma nova secção de [integração do Azure Monitor.](#activate-a-new-azure-monitor-integration) Para ativar a integração clássica neste cluster, aceda à página do portal do seu cluster. Selecione o painel de **integração do Monitor** na secção de **monitorização** do menu no lado esquerdo da página do portal do cluster. Selecione **Configure Azure Monitor para integração de clusters HDInsight (clássico)**. Um contexto lateral aparece com um toggle que pode usar para ativar e desativar a integração clássica do Azure Monitoring. 

> [!NOTE]
> Não verá nenhum registo ou métrica da integração clássica através dos registos e insights do seu portal Cluster. Apenas os novos registos de integrações e métricas estarão presentes nesses locais.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Screenshot que mostra a ligação para aceder à integração clássica." border="false":::

A criação de novos clusters com a integração clássica do Azure Monitor não está disponível depois de 1 de julho de 2021.

## <a name="release-and-support-timeline"></a>Linha do tempo de lançamento e suporte

- A integração de Monitores Clássicos estará indisponível a partir de 15 de outubro de 2021. Não pode permitir a integração clássica do Azure Monitoring após essa data.
- As integrações de monitorização clássicas do Azure continuarão a funcionar. Haverá um apoio limitado para a integração clássica do Azure Monitoring. 
  - As questões serão investigadas assim que os clientes apresentarem o bilhete de apoio.
  - Se a solução requer mudança de imagem, os clientes devem passar para a nova integração.
  - Não vamos corrigir os clássicos clusters de integração do Azure Monitoring exceto questões de segurança críticas.

## <a name="appendix-table-mapping"></a>Apêndice: Mapeamento de mesa

Os gráficos seguintes mostram os mapeamentos de mesa da integração clássica de monitorização do Azure para o nosso novo. A coluna **Workload** descreve a carga de trabalho com que cada tabela está associada. A nova fila da **Mesa** mostra o nome da nova tabela. A linha **Descrição** descreve o tipo de registos/métricas que estarão disponíveis nesta tabela. A fila **Old Table** é uma lista de todas as tabelas da integração clássica do Azure Monitor cujos dados estarão agora presentes na tabela listada na nova fila **da Tabela.**

> [!NOTE]
> Algumas mesas são novas e não baseadas em mesas velhas.

## <a name="general-workload-tables"></a>Tabelas gerais de carga de trabalho

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Descrição**: Esta tabela contém métricas do sistema recolhidas em Ambari. As métricas agora vêm de cada nó no cluster (exceto os nós de borda) em vez de apenas os dois headnodes. Cada métrica é agora uma coluna e cada métrica é reportada uma vez por registo.</li><li>**Tabela antiga**: métricas \_ cpu \_ nice \_ cl, metrics \_ cpu system \_ \_ cl, metrics \_ cpu user \_ \_ cl, metrics \_ memory cache \_ \_ CL, \_ metrics memory swap \_ \_ CL, metrics \_ memory total \_ \_ CLmetrics \_ memory buffer \_ \_ CL, metrics \_ load \_ 1min \_ CL, metrics \_ load \_ cpu \_ CL, metrics \_ load \_ nodes \_ CL, metrics \_ load \_ procs CL, metrics load procs \_ \_ CL, metrics network \_ in \_ CL, metrics \_ network out \_ \_ CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Descrição**: Esta tabela contém alertas de cluster Ambari de cada nó no cluster (exceto para nós de borda). Cada alerta é um recorde nesta tabela.</li><li>**Tabela antiga**: cluster de métricas \_ alerta \_ \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Descrição**: Esta tabela contém registos da Auditoria Ambari e dos Registos Auth.</li><li>**Tabela antiga**: log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos do registo de auditoria ranger para clusters ESP.</li><li>**Tabela antiga**: \_ registos de auditoria ranger \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Descrição**: Esta tabela contém as informações de auditoria dos nós gateway. É o mesmo formato da tabela na coluna Old Tables. **Ainda está localizado na secção Registos Personalizados.**</li><li>**Tabela antiga**: log \_ gateway Audit \_ \_ CL</li></ul>|

## <a name="spark-workload"></a>Carga de trabalho de faísca

> [!NOTE]
> As tabelas relacionadas com a aplicação Spark foram substituídas por 11 novas tabelas Spark (a começar pelo HDInsightSpark*) que darão mais informações aprofundadas sobre as suas cargas de trabalho spark.


| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos relacionados com a Spark e o seu componente relacionado: Livy e Jupyter.</li><li>**Tabela antiga**: log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkexecutors \_ CL, log \_ sparkappsdrives CL, log sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationSEvents | <ul><li>**Descrição**: Esta tabela contém informações de eventos para aplicações spark, incluindo o tempo de submissão e conclusão, ID da aplicação e appName. É útil para acompanhar quando as aplicações começaram e completaram. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Descrição**: Esta tabela contém informações de eventos relacionadas com o Gestor de Blocos da Spark. Inclui informações como o uso da memória do executor.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Descrição**: Esta tabela contém informações de eventos relacionadas com o Ambiente que uma aplicação executa incluindo, Modo de Implantação de Faíscas, Master, e informações sobre o Executor.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Descrição**: Esta tabela contém informações do evento sobre a utilização do Executor de Faíscas por uma Aplicação.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Descrição**: Esta tabela contém informações do evento que não se encaixam em nenhuma outra tabela Spark. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Descrição**: Esta tabela contém informações sobre o Spark Jobs, incluindo os seus tempos de início e fim, resultados e fases associadas.</li></ul>|
| HDInsightSparkSqlExecutionS | <ul><li>**Descrição**: Esta tabela contém informações sobre o evento sobre as consultas Spark SQL, incluindo as suas informações de plano e descrição e horários de início e fim.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Descrição**: Esta tabela contém informações sobre eventos para as fases de faíscas, incluindo os seus tempos de início e conclusão, estado de falha e informações detalhadas sobre a execução.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Descrição**: Esta tabela contém métricas de desempenho para fases e tarefas.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Descrição**: Esta tabela contém informações de eventos para tarefas de faísca, incluindo tempo de início e conclusão, fases associadas, estado de execução e tipo de tarefa.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Descrição**: Esta tabela contém informações do evento para cadernos Jupyter.</li></ul>|

## <a name="hadoopyarn-workload"></a>Carga de trabalho hadoop/YARN

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Descrição**: Esta tabela contém métricas JMX das estruturas Hadoop e YARN. Contém todas as mesmas métricas JMX que as antigas tabelas de Registos Personalizados, além de mais métricas que considerámos importantes. Adicionámos métricas do Servidor de Linha do Tempo, do Gestor de Nó e do Servidor de Histórico de Emprego. Contém uma métrica por registo.</li><li>**Tabela antiga**: métricas \_ recursos gestor \_ clustermetrics \_ CL, metrics \_ resourcemanager \_ jvm \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ CL, metrics \_ resourcemanager \_ base \_ \_ joblauncher \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos gerados a partir dos quadros hadoop e YARN.</li><li>**Tabela antiga**: log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Carga de trabalho da Colmeia/LLAP 

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Descrição**: Esta tabela contém métricas JMX das estruturas da Colmeia e LLAP. Contém todas as mesmas métricas JMX que as antigas tabelas de Registos Personalizados. Contém uma métrica por registo.</li><li>**Tabela antiga**: llap \_ metrics \_ hiveserver2 \_ CL, llap \_ metrics \_ hs2 \_ metrics \_ subsystemllap \_ metrics \_ jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info \_ CL, llap \_ metrics buddy \_ \_ allocator info \_ \_ CL, llap \_ metrics \_ deamon \_ jvm \_ CL, llap \_ metrics \_ io \_ CL, llap \_ metrics \_ \_ executor metrics \_ CL, llap \_ \_ metrics metrics \_ \_ cl, llap metrics CL, llap metrics cl, llap \_ metrics \_ \_ caches CL, llap metrics</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Descrição**: Esta tabela contém registos gerados a partir de Hive, LLAP, e seus componentes relacionados: WebHCat e Zeppelin.</li><li>**Tabela antiga**: log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log zeppelin zeppelin \_ \_ \_ CL</li></ul>|


## <a name="kafka-workload"></a>Carga de trabalho kafka

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Descrição**: Esta tabela contém métricas JMX de Kafka. Contém todas as mesmas métricas JMX que as antigas tabelas de Registos Personalizados, além de mais métricas que considerámos importantes. Contém uma métrica por registo.</li><li>**Tabela antiga**: métricas \_ kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos gerados a partir dos Corretores Kafka.</li><li>**Tabela antiga**: log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>Carga de trabalho HBase

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Descrição**: Esta tabela contém métricas JMX da HBase. Contém todas as mesmas métricas JMX das tabelas listadas na coluna Old Schema. Em contraste com as tabelas antigas, cada linha contém uma métrica.</li><li>**Tabela antiga**: métricas \_ regionserver \_ CL, métricas \_ regionserver \_ wal \_ CL, metrics \_ regionserver \_ iPC \_ CL, metrics \_ regionserver \_ os \_ CL, metrics \_ regionserver \_ replication \_ \_ CL, metrics restserver \_ CL, metrics \_ restserver \_ jvm \_ CL, metrics \_ hmaster \_ assignmentmanager \_ CL, metrics \_ hmaster \_ ipc \_ CL, metrics \_ hmaser \_ os \_ CL, metrics \_ hmaster \_ balancer \_ CL, metrics \_ hmaster \_ jvm \_ CL, metrics \_ \_ hmaster \_ \_ \_</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Descrição**: Esta tabela contém registos da HBase e dos seus componentes relacionados: Phoenix e HDFS.</li><li>**Tabela antiga**: \_ log regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log garbage collector \_ \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Carga de trabalho de tempestade

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Descrição**: Esta tabela contém as mesmas métricas JMX que as tabelas na secção Tabelas Antigas. As suas linhas contêm uma métrica por registo.</li><li>**Tabela antiga**: métricas \_ stormnimbus \_ CL, métricas \_ stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Descrição**: Esta tabela contém métricas de nível topologia da Tempestade. Tem a mesma forma que a tabela listada na secção Old Tables.</li><li>**Tabela antiga**: métricas \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos gerados a partir da Tempestade.</li><li>**Tabela antiga**: \_ log supervisor \_ CL, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Carga de trabalho de Oozie

| Nova Tabela | Detalhes |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Descrição**: Esta tabela contém todos os registos gerados a partir da estrutura Oozie.</li><li>**Tabela antiga**: Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Passos seguintes
[Registos do Monitor de Consulta Azure para monitorizar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
