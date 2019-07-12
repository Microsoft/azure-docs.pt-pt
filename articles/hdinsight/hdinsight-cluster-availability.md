---
title: Como monitorizar a disponibilidade do cluster com os registos do Ambari e o Azure Monitor
description: Saiba como utilizar Ambari e registos do Azure Monitor para monitorizar o estado de funcionamento do cluster e a disponibilidade.
keywords: monitorização, ambari, monitor, do log analytics, alerta, disponibilidade, estado de funcionamento
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 195999ba685828042fc958e8aed7e67bad694657
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786567"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Como monitorizar a disponibilidade do cluster com os registos do Ambari e o Azure Monitor

Os clusters do HDInsight incluem tanto Apache Ambari, que fornece informações de estado de funcionamento no rapidamente e de alertas predefinidas, bem como integração de registos do Azure Monitor, que fornece consultáveis métricas e registos, bem como alertas configuráveis.

Este documento mostra como usar essas ferramentas para monitorizar o seu cluster e apresenta alguns exemplos para configurar um alerta de Ambari, a tarifa de disponibilidade do nó de monitorização e a criação de um alerta de Monitor do Azure que é acionado quando não foi recebido um heartbeat de um ou mais nós em cinco horas.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

O dashboard de Ambari, pode ser acedido ao clicar o **Ambari doméstica** ligação na **dashboards de clusters** secção do HDInsight painel Descrição geral no portal do Azure, conforme mostrado abaixo. Em alternativa, pode ser acedido ao introduzir o URL seguinte num browser [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Vista de portal de recursos do HDInsight](media/hdinsight-cluster-availability/portal-overview.png)

Em seguida, será solicitado um nome de utilizador de início de sessão de cluster e a palavra-passe. Introduza as credenciais que escolheu quando criou o cluster.

Será encaminhado para o dashboard de Ambari, que contém os widgets que mostram algumas das métricas para lhe dar uma rápida visão geral do Estado de funcionamento do seu cluster de HDInsight. Estes widgets mostram métricas como o número de direto DataNodes (nós de trabalho) e JournalNodes (nó do zookeeper), tempo de atividade NameNodes (nós principais), como bem métricas específicas para determinados tipos de cluster, como o tempo de atividade do YARN Resource Manager para clusters do Spark e Hadoop.

![Dashboard do Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Anfitriões – ver o estado do nó individual

Também pode ver informações de estado para nós individuais. Clique nas **anfitriões** separador para ver uma lista de todos os nós do cluster e ver informações básicas sobre cada nó. A marca de verificação verde à esquerda de cada nome de nó indica que todos os componentes estão operacionais no nó. Se um componente estiver desativado num nó, verá um triângulo alerta vermelho em vez da verificação a verde.

![Vista de anfitriões do Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

Em seguida, pode clicar no **nome** de um nó para ver mais métricas de anfitrião para esse nó específico. Esta vista mostra a Estado de disponibilidade de cada componente individual.

![Anfitriões de Ambari única vista do nó](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertas do Ambari

Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando são acionados alertas, eles são mostrados no canto superior esquerdo do Ambari num distintivo vermelho que contém o número de alertas. Clicar neste distintivo mostra uma lista de alertas atuais.

![Contagem de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Para ver uma lista de definições de alerta e os respetivos Estados, clique a **alertas** separador, conforme mostrado abaixo.

![Vista de definições de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferece muitos alertas predefinidas relacionados com a disponibilidade, incluindo:

| Nome do alerta                        | Descrição                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Resumo do Estado de funcionamento do DataNode           | Este alerta de nível de serviço é acionada se existirem DataNodes mau estado de funcionamento                                                                                                                |
| O estado de funcionamento do NameNode alta disponibilidade | Este alerta de nível de serviço é acionado se o Active Directory NameNode ou NameNode de reserva não está em execução.                                                                              |
| Percentagem JournalNodes disponíveis    | Este alerta é acionado se o número de baixo JournalNodes do cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo JournalNode. |
| Percentagem DataNodes disponíveis       | Este alerta é acionado se o número de baixo DataNodes do cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo DataNode.       |

Esse monitor ajuda a disponibilidade de um cluster pode ser encontrada de alertas de uma lista completa de Ambari [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Para ver os detalhes de um alerta ou alterar os critérios, clique nas **nome** do alerta. Tirar **resumo de estado de funcionamento de DataNode** como exemplo. Pode ver uma descrição do alerta, bem como os critérios específicos que irão acionar um alerta de "aviso" ou "crítico" e o intervalo de verificação para os critérios. Para editar a configuração, clique a **editar** botão no canto superior direito da caixa de configuração.

![Configuração de alerta do Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, pode editar a descrição e, mais importante, a verificação de intervalo e limiares para alertas de aviso ou críticas.

![Exibição de edição de configuração de alerta do Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, poderia fazer 2 DataNodes mau estado de funcionamento, acionar um alerta crítico e 1 acionar de apenas de DataNode um aviso de mau estado de funcionamento. Clique em **guardar** quando tiver terminado edição.

### <a name="email-notifications"></a>Notificações por e-mail

Opcionalmente, também pode configurar notificações por e-mail para alertas do Ambari. Para fazer isso, quando no **alertas** separador, clique nas **ações** botão no canto superior esquerdo, em seguida, **gerir notificações.**

![Ação de notificações de gerir do Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Será aberta uma caixa de diálogo para o gerenciamento de notificações de alerta. Clique nas **+** na parte inferior da caixa de diálogo e preencha os campos necessários para fornecer Ambari com o e-mail de detalhes do servidor do qual pretende enviar e-mails.

> [!TIP]
> Configuração de Ambari notificações de e-mail podem ser uma boa forma de receber alertas num único local, ao gerir vários clusters do HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integração de registos de Monitor do Azure

O Azure Monitor permite dados gerados por vários recursos, tais como clusters do HDInsight, a ser recolhido e agregados num único local para obter uma experiência unificada de monitorização de registos.

Como pré-requisito, terá uma área de trabalho do Log Analytics para armazenar os dados recolhidos. Se ainda não tenha criado uma, pode seguir as instruções aqui: [Criar uma área de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Ativar a integração de registos do HDInsight do Azure Monitor

Na página recursos de HDInsight cluster no portal, clique nas **Operations Management Suite** painel. Em seguida, clique em **ativar** e selecione a área de trabalho do Log Analytics a partir da lista pendente.

![Painel de HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Consultar métricas e registos de tabelas no painel de registos

Quando a integração de registos do Azure Monitor estiver ativada (pode demorar alguns minutos), navegue até à sua **área de trabalho do Log Analytics** recursos e clique no **registos** painel

![Painel de registos de área de trabalho do log Analytics](media/hdinsight-cluster-availability/portal-logs.png)

O **registos** painel lista um conjunto de consultas de exemplo, tais como:

| Nome da consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Hoje em dia a disponibilidade de computadores    | O número de computadores de envio de registos, cada hora de gráfico                     |
| Lista de heartbeats                 | Lista de todos os heartbeats do computador de última hora                           |
| Último heartbeat de cada computador | Mostrar o último heartbeat enviado por cada computador                             |
| Computadores não disponíveis           | Listar todos os computadores conhecidos não tiver enviado um heartbeat nas últimas 5 horas |
| Tarifa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador ligado                |

Por exemplo, execute o **tarifa de disponibilidade** consulta de exemplo ao clicar em **executar** nessa consulta, conforme mostrado na captura de ecrã acima. Isto irá mostrar a tarifa de disponibilidade de cada nó do cluster como uma percentagem. Se tiver ativado o vários clusters do HDInsight enviar métricas para a mesma área de trabalho do Log Analytics, verá a tarifa de disponibilidade para todos os nós nesses clusters apresentados.

![Consulta de exemplo de "tarifa de disponibilidade" registos painel Análise área de trabalho de registo](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Tarifa de disponibilidade é medida durante um período de 24 horas, para que o cluster tem de executar durante, pelo menos, 24 horas, só poderá ver os preços de disponibilidade exata.

Pode afixar esta tabela para um dashboard partilhado clicando **Pin** no canto superior direito. Se não tem quaisquer dashboards partilhados graváveis, verá como criar um aqui: [Criar e partilhar dashboards no portal do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertas de Monitor do Azure

Também pode configurar alertas do Azure Monitor que irão acionar quando o valor de uma métrica ou os resultados de uma consulta atendem a determinadas condições. Por exemplo, vamos criar um alerta para enviar um e-mail quando um ou mais nós ainda não enviaram um heartbeat nas cinco horas (ou seja, deve ser indisponível).

Do **Logs** painel, execute o **computadores não disponíveis** consulta de exemplo ao clicar em **executar** nessa consulta, conforme mostrado abaixo.

![Consulta de exemplo de "computadores não disponíveis" registos painel Análise área de trabalho de registo](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, esta consulta deve retornar 0 resultados por agora. Clique em **nova regra de alerta** para começar a configurar o alerta para esta consulta.

![Log Analytics área de trabalho nova regra de alerta](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Existem três componentes para um alerta: os *resource* para o qual pretende criar a regra (Log Analytics área de trabalho neste caso), o *condição* para acionar o alerta e o *grupos de ação*  que determinam o que acontece quando o alerta é acionado.

Clique nas **title de condição**, conforme mostrado abaixo, para concluir a configuração a lógica de sinal.

![Condição de regra de alerta](media/hdinsight-cluster-availability/portal-condition-title.png)

Esta ação abre o **lógica de sinal de configurar** painel.

Definir o **lógica de alerta** secção da seguinte forma:

*Com base em: Número de resultados, condição: Superior ao limiar: 0.*

Uma vez que esta consulta devolve apenas nós indisponíveis como resultados, se o número de resultados é cada vez maior que 0, o alerta deve ser acionados.

Na **Evaluated com base nos** secção, defina o **período** e **frequência** com base na frequência com que pretende procurar nós indisponíveis.

Tenha em atenção que com o objetivo deste alerta, deseja tornar-se de que **período = frequência.** Podem encontrar mais informações sobre o período, a frequência e outros parâmetros de alerta [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Clique em **feito** quando tiver terminado de configurar a lógica de sinal.

![Regra de alerta configurar lógica de sinal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se ainda não tiver um grupo de ação existente, clique em **criar novo** sob a **grupos de ação** secção.

![Novo grupo de ação de regra de alerta](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Esta ação abre o **grupo de ação de adicionar** painel. Escolher uma **nome do grupo de ação**, **nome abreviado**, **subscrição**, e **grupo de recursos.** Sob o **ações** secção, escolha um **nome da ação** e selecione **E-Mail/SMS/Push/voz** como o **tipo de ação.**

> [!NOTE]
> Existem várias outras ações que um alerta pode acionar além de um E-Mail/SMS/Push/voz, como uma função do Azure, LogicApp, Webhook, ITSM e o Runbook de automatização. [Saiba mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Esta ação abre o **E-Mail/SMS/Push/voz** painel. Escolha um **nome** para o destinatário **verificar** o **E-Mail** caixa e, escreva um endereço de e-mail para o qual pretende que o alerta seja enviado. Clique em **OK** no **E-Mail/SMS/Push/voz** painel, em seguida, no **adicionar grupo de ação** painel para concluir a configuração do seu grupo de ação.

![Grupo de ação de adicionar regra de alerta](media/hdinsight-cluster-availability/portal-add-action-group.png)

Depois de fechar estes painéis, deverá ver seu grupo de ação listado sob os **grupos de ação** secção. Por fim, conclua os **detalhes do alerta** secção, escrevendo um **nome da regra de alerta** e **Descrição** e escolhendo um **gravidade**.
Clique em **criar regra de alerta** para concluir.

![Criar regra de alerta concluir](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **gravidade** é uma ferramenta poderosa que pode ser utilizada durante a criação de vários alertas. Por exemplo, poderia criar um alerta para criar um aviso (gravidade 1) se um único nó principal ficar inativo e outro alerta que gera crítico (gravidade 0) no improvável evento que os dois nós principais ficam inativos.

Quando a condição para este alerta for cumprida, o alerta será acionado e receberá um e-mail com os detalhes do alerta como este:

![E-mail de alerta de Monitor do Azure](media/hdinsight-cluster-availability/alert-email.png)

Também pode ver todos os alertas que foram acionados, agrupados por gravidade, ao aceder a **alertas** painel no seu **área de trabalho do Log Analytics**.

![Alertas da área de trabalho de análise de registo](media/hdinsight-cluster-availability/portal-alerts.png)

Clicar num agrupamento de gravidade (ou seja, **gravidade 1,** conforme destacado acima) mostrará os registos para todos os alertas de gravidade do que foram acionados como abaixo:

![Alertas de gravidade 1 de área de trabalho de análise de registo](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Passos Seguintes
- [Disponibilidade e fiabilidade de clusters do Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
