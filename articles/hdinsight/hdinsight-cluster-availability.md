---
title: 'Monitorização: Apache Ambari & Registos Azure Monitor - Azure HDInsight'
description: Aprenda a usar os registos ambari e azure monitor para monitorizar a saúde e disponibilidade do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060184"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Como monitorizar a disponibilidade do cluster com os registos Apache Ambari e Azure Monitor

Os clusters HDInsight incluem tanto a Apache Ambari, que fornece informações de saúde num relance e alertas predefinidos, bem como a integração de registos do Azure Monitor, que fornece métricas e registos que se questionam, bem como alertas configuráveis.

Este doc mostra como usar estas ferramentas para monitorizar o seu cluster e percorre alguns exemplos para configurar um alerta Ambari, monitorizar a taxa de disponibilidade do nó, e criar um alerta do Monitor Azure que dispara quando um batimento cardíaco não foi recebido de um ou mais nós em cinco horas.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

O painel ambari pode ser acedido selecionando o link **doméstico Ambari** na secção de **dashboards cluster** da visão geral do HDInsight no portal Azure, como mostrado abaixo. Em alternativa, pode ser acedido `https://CLUSTERNAME.azurehdinsight.net` navegando num browser onde clusterNAME é o nome do seu cluster.

![Vista do portal de recursos HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Em seguida, será solicitado um nome de utilizador de login de cluster e senha. Introduza as credenciais que escolheu quando criou o cluster.

Em seguida, será levado para o painel ambari, que contém widgets que mostram um punhado de métricas para lhe dar uma visão geral rápida da saúde do seu cluster HDInsight. Estes widgets mostram métricas como o número de DataNodes ao vivo (nós operários) e JournalNodes (nó do zookeeper), NameNodes (nóóis de cabeça) uptime, bem como métricas específicas para certos tipos de cluster, como o tempo de uptime do YARN ResourceManager para os clusters Spark e Hadoop.

![Apache Ambari usa ecrã de painel de instrumentos](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Anfitriões – veja o estado do nó individual

Também pode ver informações sobre o estado para os nódosos individuais. Selecione o separador **Anfitriões** para ver uma lista de todos os nós do seu cluster e consulte informações básicas sobre cada nó. A verificação verde à esquerda de cada nome do nó indica que todos os componentes estão no nó. Se um componente estiver em baixo num nó, verá um triângulo de alerta vermelho em vez da verificação verde.

![HDInsight Apache Ambari acolhe vista](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Em seguida, pode selecionar o **nome** de um nó para ver métricas de hospedeiro mais detalhadas para esse nó em particular. Esta visão mostra o estado/disponibilidade de cada componente individual.

![Apache Ambari acolhe vista de nó único](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertas de Ambari

Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando os alertas são disparados, são mostrados no canto superior esquerdo de Ambari num crachá vermelho contendo o número de alertas. A seleção deste crachá mostra uma lista de alertas atuais.

![Contagem de alertas atuais de Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Para ver uma lista de definições de alerta e seus estados, selecione o separador **Alertas,** como mostrado abaixo.

![Ambari alerta a visão das definições](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferece muitos alertas predefinidos relacionados com a disponibilidade, incluindo:

| Nome do Alerta                        | Descrição   |
|---|---|
| Resumo da Saúde dataNode           | Este alerta de nível de serviço é desencadeado se não houver DataNodes saudáveis|
| NomeNode Alta Disponibilidade Saúde | Este alerta de nível de serviço é acionado se o Nome Ativo ou o Nome de Standby Node não estiverem em funcionamento.|
| Por cento de revistas disponíveis    | Este alerta é desencadeado se o número de JournalNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados dos controlos do processo JournalNode. |
| Por cento dataNodes disponíveis       | Este alerta é desencadeado se o número de DataNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados dos controlos do processo DataNode.|

Uma lista completa de alertas de Ambari que ajudam a monitorizar a disponibilidade de um cluster pode ser encontrada [aqui,](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)

Para ver detalhes para um alerta ou modificar critérios, selecione o **nome** do alerta. Tome o resumo da **saúde dataNode** como um exemplo. Pode ver uma descrição do alerta, bem como os critérios específicos que desencadearão um alerta "de advertência" ou "crítico" e o intervalo de verificação dos critérios. Para editar a configuração, selecione o botão **Editar** no canto superior direito da caixa de configuração.

![Configuração de alerta Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, pode editar a descrição e, mais importante, o intervalo de verificação e os limiares para alertaou alertas críticos.

![Ambari alerta configurações editar vista](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, pode fazer com que 2 DataNodes não saudáveis desencadeiem um alerta crítico e 1 DataNode pouco saudável apenas desencadeie um aviso. Selecione **Guardar** quando terminar a edição.

### <a name="email-notifications"></a>Notificações por e-mail

Também pode configurar opcionalmente notificações de e-mail para alertas Ambari. Para isso, quando estiver no separador **Alertas,** clique no botão **Ações** na parte superior esquerda e, em seguida, **gerir notificações.**

![Ambari gere ação de notificações](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Será aberto um diálogo para a gestão de notificações de alerta. Selecione a **+** parte inferior do diálogo e preencha os campos necessários para fornecer à Ambari detalhes do servidor de e-mail a partir dos quais enviar e-mails.

> [!TIP]
> Configurar notificações de e-mail ambari pode ser uma boa maneira de receber alertas num só local ao gerir muitos clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integração de registos do Monitor Azure

Os registos do Monitor Azure permitem que os dados gerados por vários recursos, como os clusters HDInsight, sejam recolhidos e agregados num só local para alcançar uma experiência de monitorização unificada.

Como pré-requisito, vai precisar de um Espaço de Trabalho de Log Analytics para armazenar os dados recolhidos. Se ainda não criou uma, pode seguir as instruções aqui: [Criar um espaço](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)de trabalho de Log Analytics .

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Ativar a integração de registos do HDInsight Azure Monitor

A partir da página de recursos de cluster HDInsight no portal, **selecione Azure Monitor**. Em seguida, **selecione ativar** e selecione o seu espaço de trabalho Log Analytics a partir do drop-down.

![Suíte de Gestão de Operações HDInsight](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Métricas de consulta e tabelas de troncos

Assim que a integração do registo do Monitor Azure estiver ativada (isto pode demorar alguns minutos), navegue para o seu recurso **Log Analytics Workspace** e selecione **Registos**.

![Log Analytics registos do espaço de trabalho](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Os registos listam uma série de consultas de amostra, tais como:

| Nome de consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Cartografe o número de computadores que enviam registos, a cada hora                     |
| Lista de batimentos cardíacos                 | Enumerar todos os batimentos cardíacos do computador da última hora                           |
| Último batimento cardíaco de cada computador | Mostre o último batimento cardíaco enviado por cada computador                             |
| Computadores indisponíveis           | Lista ruma todos os computadores conhecidos que não enviaram um batimento cardíaco nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Como exemplo, execute a consulta da amostra de taxa de **disponibilidade** selecionando **Executar** nessa consulta, como mostrado na imagem acima. Isto mostrará a taxa de disponibilidade de cada nó no seu cluster em percentagem. Se tiver ativado vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho log Analytics, verá a taxa de disponibilidade para todos os nós desses clusters apresentados.

![Log Analytics workspace regista consulta de amostra de 'taxa de disponibilidade'](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> A taxa de disponibilidade é medida durante um período de 24 horas, pelo que o seu cluster terá de funcionar durante pelo menos 24 horas antes de ver taxas de disponibilidade precisas.

Pode fixar esta tabela num dashboard partilhado clicando em **Pin** no canto superior direito. Se não tiver nenhum dashboard partilhado, pode ver como criar um aqui: [Criar e partilhar dashboards no portal Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertas do Monitor Azure

Também pode configurar alertas do Monitor Azure que irão desencadear quando o valor de uma métrica ou os resultados de uma consulta satisfaçam determinadas condições. Como exemplo, vamos criar um alerta para enviar um e-mail quando um ou mais nós não enviam um batimento cardíaco em 5 horas (ou seja, presume-se que não esteja disponível).

A partir de **Registos,** execute a consulta de amostra de **computadores indisponíveis** selecionando **Executar** nessa consulta, como mostrado abaixo.

![Log Analytics workspace regista amostra de 'computadores indisponíveis'](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, esta consulta deve devolver zero resultados por enquanto. Clique em **Nova regra** de alerta para começar a configurar o seu alerta para esta consulta.

![Log Analytics workspace nova regra de alerta](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para o qual criar a regra (o espaço de trabalho log Analytics neste caso), a *condição* para desencadear o alerta, e os *grupos* de ação que determinam o que vai acontecer quando o alerta for desencadeado.
Clique no título de **condição**, como mostrado abaixo, para terminar de configurar a lógica do sinal.

![Alerta de portal criar condição de regra](media/hdinsight-cluster-availability/portal-condition-title.png)

Isto abrirá a lógica do **sinal configure.**

Desloque a secção lógica de **alerta** da seguinte forma:

*Com base em: Número de resultados, Condição: Maior do que, Limiar: 0.*

Uma vez que esta consulta só devolve nós indisponíveis como resultados, se o número de resultados for sempre maior do que 0, o alerta deve disparar.

No **Avaliado com base na** secção, desloque o **período** e **a frequência** com base na frequência com que pretende verificar os nós indisponíveis.

Para efeitos deste alerta, pretende certificar-se de **Period=Frequency.** Mais informações sobre período, frequência e outros parâmetros de alerta podem ser encontrados [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Selecione **Feito** quando terminar de configurar a lógica do sinal.

![Regra de alerta configura lógica de sinal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se ainda não tiver um grupo de ação existente, clique em **Criar Novo** na secção **Grupos de Ação.**

![Regra de alerta cria novo grupo de ação](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Isto abrirá **o grupo de ação Add**. Escolha um nome de **grupo de Ação**, Nome **curto,** **Subscrição**e **Grupo de Recursos.** Na secção **Ações,** escolha um Nome de **Ação** e selecione **Email/SMS/Push/Voice** como o Tipo de **Ação.**

> [!NOTE]
> Existem várias outras ações que um alerta pode desencadear além de um E-mail/SMS/Push/Voice, como uma Função Azure, LogicApp, Webhook, ITSM e Automation Runbook. [Saiba mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Isto abrirá **o email/SMS/Push/Voice**. Escolha um **Nome** para o destinatário, **verifique** a caixa de **e-mail** e digite um endereço de e-mail para o qual deseja o alerta enviado. Selecione **OK** em **Email/SMS/Push/Voice**e, em seguida, em **Adicionar grupo de ação** para terminar de configurar o seu grupo de ação.

![Regra de alerta cria grupo de ação adicionar](media/hdinsight-cluster-availability/portal-add-action-group.png)

Depois destas lâminas fecharem, deve ver o seu grupo de ação listado na secção **Grupos de Ação.** Por fim, complete a secção **de Detalhes** de Alerta digitando um Nome e **Descrição** da Regra de **Alerta** e escolhendo uma **Gravidade**. Clique em **Criar regra** de alerta para terminar.

![Portal cria fim de regra de alerta](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **a Severity** é uma ferramenta poderosa que pode ser usada ao criar vários alertas. Por exemplo, pode criar um alerta para levantar um aviso (Sev 1) se um único nó de cabeça descer e outro alerta que eleva o Critical (Sev 0) no caso improvável de ambos os nós descerem.

Quando a condição para este alerta for satisfeita, o alerta disparará e receberá um e-mail com os detalhes do alerta como este:

![Exemplo de e-mail de alerta Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Também pode ver todos os alertas que dispararam, agrupados pela gravidade, indo para **Alertas** no seu Espaço de **Trabalho log Analytics**.

![Alertas de espaço de trabalho de Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

A seleção de um agrupamento de gravidade (isto **é, Sev 1,** como acima destacado) mostrará registos de todos os alertas dessa gravidade que dispararam como abaixo:

![Log Analytics workspace sev um alerta](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passos seguintes

- [Disponibilidade e fiabilidade dos clusters Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
