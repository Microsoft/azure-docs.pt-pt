---
title: Como monitorar a disponibilidade do cluster com os logs do Ambari e do Azure Monitor
description: Saiba como usar os logs do Ambari e do Azure Monitor para monitorar a integridade e a disponibilidade do cluster.
keywords: monitoramento, ambari, monitor, log Analytics, alerta, disponibilidade, integridade
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 153c28dc8a06968dc9dd3cfda021496672a094d5
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076557"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Como monitorar a disponibilidade do cluster com os logs do Ambari e do Azure Monitor

Os clusters HDInsight incluem o Apache Ambari, que fornece informações de integridade em um relance e alertas predefinidos, bem como a integração de logs de Azure Monitor, que fornece métricas e logs consultáveis, bem como alertas configuráveis.

Este documento mostra como usar essas ferramentas para monitorar o cluster e percorrer alguns exemplos de configuração de um alerta do Ambari, taxa de disponibilidade do nó de monitoramento e criação de um Azure Monitor alerta que é disparado quando uma pulsação não foi recebida de um ou mais nós em cinco horas.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

O painel do Ambari pode ser acessado clicando no link do **Ambari Home** na seção **painéis do cluster** da folha de visão geral do HDInsight em portal do Azure, conforme mostrado abaixo. Como alternativa, ele pode ser acessado digitando a seguinte URL em um navegador [\<https://\>ClusterName. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Exibição do portal de recursos do HDInsight](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Em seguida, você será solicitado a fornecer um nome de usuário e senha de logon de cluster. Insira as credenciais que você escolheu ao criar o cluster.

Em seguida, você será levado para o painel do Ambari, que contém widgets que mostram algumas métricas para fornecer uma visão geral rápida da integridade do seu cluster HDInsight. Esses widgets mostram métricas como o número de JournalNodes (nós de trabalho) em tempo real e o tempo de atividade (nó Zookeeper), a NameNodes (nós de cabeçalho), bem como métricas específicas para determinados tipos de cluster, como o tempo de atividade de ResourceManager de YARN para clusters Spark e Hadoop.

![Exibição do painel de uso do Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – Exibir status de nó individual

Você também pode exibir informações de status para nós individuais. Clique na guia **hosts** para exibir uma lista de todos os nós no cluster e ver informações básicas sobre cada nó. A marca de seleção verde à esquerda de cada nome de nó indica que todos os componentes estão ativos no nó. Se um componente estiver inoperante em um nó, você verá um triângulo de alerta vermelho em vez da marca de seleção verde.

![Exibição de hosts Apache Ambari do HDInsight](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Em seguida, você pode clicar no **nome** de um nó para exibir as métricas de host mais detalhadas para esse nó específico. Essa exibição mostra o status/disponibilidade de cada componente individual.

![O Apache Ambari hospeda exibição de nó único](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertas do Ambari

O Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando os alertas são disparados, eles são mostrados no canto superior esquerdo do Ambari em um emblema vermelho que contém o número de alertas. Clicar nessa notificação mostra uma lista de alertas atuais.

![Contagem de alertas atuais do Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Para exibir uma lista de definições de alerta e seus status, clique na guia **alertas** , conforme mostrado abaixo.

![Exibição de definições de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

O Ambari oferece muitos alertas predefinidos relacionados à disponibilidade, incluindo:

| Nome do Alerta                        | Descrição                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Resumo de integridade do datanode           | Esse alerta de nível de serviço é disparado se houver datanodes não íntegros                                                                                                                |
| Integridade de alta disponibilidade do NameNode | Esse alerta de nível de serviço será disparado se o NameNode ativo ou o NameNode em espera não estiverem em execução.                                                                              |
| Porcentagem de JournalNodes disponíveis    | Esse alerta será disparado se o número de JournalNodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do JournalNode. |
| Porcentagem de datanodes disponíveis       | Esse alerta será disparado se o número de datanodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados de verificações de processo de datanode.       |

Uma lista completa de alertas do Ambari que ajudam a monitorar a disponibilidade de um cluster pode ser encontrada [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Para exibir detalhes de um alerta ou modificar critérios, clique no **nome** do alerta. Use o **Resumo de integridade do datanode** como um exemplo. Você pode ver uma descrição do alerta, bem como os critérios específicos que irão disparar um alerta de ' aviso ' ou ' crítico ' e o intervalo de verificação para os critérios. Para editar a configuração, clique no botão **Editar** no canto superior direito da caixa configuração.

![Configuração de alerta do Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, você pode editar a descrição e, o mais importante, o intervalo de verificação e os limites para alertas de aviso ou críticos.

![Exibição de edição de configuração de alerta do Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, você pode fazer com que dois datanodes não íntegros disparem um alerta crítico e um datanode não íntegro só dispare um aviso. Clique em **salvar** quando terminar de editar.

### <a name="email-notifications"></a>Notificações de e-mail

Opcionalmente, você também pode configurar notificações por email para alertas do Ambari. Para fazer isso, na guia **alertas** , clique no botão **ações** no canto superior esquerdo e, em seguida, em **gerenciar notificações.**

![Ação de gerenciar notificações do Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Será aberta uma caixa de diálogo para gerenciar notificações de alerta. **+** Clique na parte inferior da caixa de diálogo e preencha os campos necessários para fornecer Ambari com detalhes do servidor de email do qual enviar emails.

> [!TIP]
> A configuração de notificações por email do Ambari pode ser uma boa maneira de receber alertas em um único lugar ao gerenciar vários clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integração de logs de Azure Monitor

Os logs de Azure Monitor permitem que os dados gerados por vários recursos, como clusters HDInsight, sejam coletados e agregados em um único local para alcançar uma experiência de monitoramento unificada.

Como pré-requisito, você precisará de um espaço de trabalho Log Analytics para armazenar os dados coletados. Se você ainda não criou uma, você pode seguir as instruções aqui: [Crie um espaço de trabalho log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Habilitar a integração de logs de Azure Monitor do HDInsight

Na página de recursos do cluster HDInsight no portal, clique na folha **Operations Management Suite** . Em seguida, clique em **habilitar** e selecione seu espaço de trabalho log Analytics na lista suspensa.

![Folha do HDInsight Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Consultar as tabelas de métricas e logs na folha logs

Quando Azure Monitor integração de log estiver habilitada (isso pode levar alguns minutos), navegue até o recurso de **espaço de trabalho do log Analytics** e clique na folha **logs**

![Folha de logs do espaço de trabalho Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

A folha **logs** lista várias consultas de exemplo, como:

| Nome da consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Gráfico o número de computadores que enviam logs, a cada hora                     |
| Listar pulsações                 | Listar todas as pulsações de computador da última hora                           |
| Última pulsação de cada computador | Mostrar a última pulsação enviada por cada computador                             |
| Computadores não disponíveis           | Listar todos os computadores conhecidos que não enviaram uma pulsação nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Por exemplo, execute a consulta de exemplo **taxa de disponibilidade** clicando em **executar** na consulta, conforme mostrado na captura de tela acima. Isso mostrará a taxa de disponibilidade de cada nó no cluster como uma porcentagem. Se você tiver habilitado vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho Log Analytics, verá a taxa de disponibilidade para todos os nós nesses clusters exibidos.

![Consulta de exemplo "taxa de disponibilidade" da folha de logs do espaço de trabalho Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> A taxa de disponibilidade é medida em um período de 24 horas, portanto, o cluster precisará ser executado por pelo menos 24 horas antes de você ver taxas de disponibilidade precisas.

Você pode fixar essa tabela em um painel compartilhado clicando em **fixar** no canto superior direito. Se você não tiver nenhum Dashboard compartilhado gravável, poderá ver como criar um aqui: [Crie e compartilhe painéis no portal do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertas de Azure Monitor

Você também pode configurar Azure Monitor alertas que serão disparados quando o valor de uma métrica ou os resultados de uma consulta atenderem a determinadas condições. Por exemplo, vamos criar um alerta para enviar um email quando um ou mais nós não enviar uma pulsação em 5 horas (ou seja, presume-se que não esteja disponível).

Na folha **logs** , execute a consulta de exemplo **computadores indisponíveis** clicando em **executar** na consulta, conforme mostrado abaixo.

![Log Analytics de logs do espaço de trabalho folha ' computadores não disponíveis '](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, essa consulta deverá retornar 0 resultados por enquanto. Clique em **nova regra de alerta** para começar a configurar o alerta para esta consulta.

![Nova regra de alerta do Log Analytics Workspace](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para o qual criar a regra (o log Analytics espaço de trabalho nesse caso), a *condição* para disparar o alerta e os *grupos de ações* que determinam o que acontecerá quando o alerta for ativou.

Clique no **título da condição**, conforme mostrado abaixo, para concluir a configuração da lógica de sinal.

![Alerta do portal criar condição de regra](media/hdinsight-cluster-availability/portal-condition-title.png)

Isso abrirá a folha **Configurar lógica de sinal** .

Defina a seção de **lógica de alerta** da seguinte maneira:

*Com base em: Número de resultados, condição: Maior que, limite: 0.*

Como essa consulta só retorna nós indisponíveis como resultados, se o número de resultados for maior que 0, o alerta deverá ser acionado.

Na seção **avaliado com base em** , defina o **período** e a **frequência** com base na frequência com que você deseja verificar se há nós indisponíveis.

Observe que, para fins deste alerta, você deseja garantir o **período = frequência.** Mais informações sobre o período, a frequência e outros parâmetros de alerta podem ser encontrados [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Clique em **concluído** quando tiver terminado de configurar a lógica de sinal.

![Regra de alerta configurar lógica de sinal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se você ainda não tiver um grupo de ações existente, clique em **criar novo** na seção **grupos de ação** .

![Regra de alerta criar novo grupo de ação](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Isso abrirá a folha **Adicionar grupo de ações** . Escolha um **nome de grupo de ação**, **nome curto**, **assinatura**e **grupo de recursos.** Na seção **ações** , escolha um **nome de ação** e selecione **email/SMS/Push/voz** como o **tipo de ação.**

> [!NOTE]
> Há várias outras ações que um alerta pode disparar além de um email/SMS/Push/voz, como um Azure functions, LogicApp, webhook, ITSM e runbook de automação. [Saiba Mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Isso abrirá a folha **email/SMS/Push/Voice** . Escolha um **nome** para o destinatário, **marque** a caixa **email** e digite um endereço de email para o qual você deseja que o alerta seja enviado. Clique em **OK** na folha **email/SMS/Push/voz** e, na folha **Adicionar grupo de ações** , para concluir a configuração do grupo de ações.

![Regra de alerta criar grupo de ação adicionar](media/hdinsight-cluster-availability/portal-add-action-group.png)

Depois que essas folhas forem fechadas, você deverá ver o grupo de ações listado na seção **grupos de ações** . Por fim, conclua a seção **detalhes do alerta** digitando um nome e uma **Descrição** da **regra de alerta** e escolhendo uma **severidade**.
Clique em **criar regra de alerta** para concluir.

![Término da regra de alerta criar Portal](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar a **gravidade** é uma ferramenta poderosa que pode ser usada durante a criação de vários alertas. Por exemplo, você pode criar um alerta para gerar um aviso (Sev 1) se um único nó de cabeçalho ficar inativo e outro alerta que gere crítico (Sev 0) no caso improvável de ambos os nós de cabeçalho ficarem inativos.

Quando a condição desse alerta for atendida, o alerta será acionado e você receberá um email com os detalhes do alerta como este:

![Exemplo de email de alerta Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Você também pode exibir todos os alertas que foram disparados, agrupados por gravidade, acessando a folha **alertas** em seu **espaço de trabalho log Analytics**.

![Log Analytics alertas de espaço de trabalho](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Clicar em um agrupamento de severidade (ou seja **, Sev 1,** como destacado acima) mostrará os registros de todos os alertas dessa severidade que foram disparados da seguinte maneira:

![Log Analytics espaço de trabalho Sev 1 alertas](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passos Seguintes
- [Disponibilidade e confiabilidade de clusters de Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
