---
title: Como monitorizar a disponibilidade do cluster com a Apache Ambari no Azure HDInsight
description: Aprenda a usar Apache Ambari para monitorizar a saúde e disponibilidade do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 4b26128b794a6a667edc578f56ad0bc9fb8303a7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691142"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Como monitorizar a disponibilidade do cluster com a Apache Ambari no Azure HDInsight

Os clusters HDInsight incluem Apache Ambari, que fornece informações de saúde num relance e alertas predefinidos.

Este artigo mostra como usar Ambari para monitorizar o seu cluster e percorre alguns exemplos para configurar um alerta Ambari, monitorizar a taxa de disponibilidade do nó, e criar um alerta do Monitor Azure de que dispara quando um batimento cardíaco não é recebido de um ou mais nós em cinco horas.

## <a name="dashboard"></a>Dashboard

O painel ambari pode ser acedido selecionando o link **doméstico Ambari** na secção de **dashboards cluster** da visão geral do HDInsight no portal Azure, como mostrado abaixo. Em alternativa, pode ser acedido `https://CLUSTERNAME.azurehdinsight.net` navegando num browser onde clusterNAME é o nome do seu cluster.

![Vista do portal de recursos HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Em seguida, será solicitado um nome de utilizador de login de cluster e senha. Introduza as credenciais que escolheu quando criou o cluster.

Em seguida, será levado para o painel ambari, que contém widgets que mostram um punhado de métricas para lhe dar uma visão geral rápida da saúde do seu cluster HDInsight. Estes widgets mostram métricas como o número de DataNodes ao vivo (nós operários) e JournalNodes (nó do zookeeper), NameNodes (nóóis de cabeça) uptime, bem como métricas específicas para certos tipos de cluster, como o tempo de uptime do YARN ResourceManager para os clusters Spark e Hadoop.

![Apache Ambari usa ecrã de painel de instrumentos](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>Anfitriões – veja o estado do nó individual

Também pode ver informações sobre o estado para os nódosos individuais. Selecione o separador **Anfitriões** para ver uma lista de todos os nós do seu cluster e consulte informações básicas sobre cada nó. A verificação verde à esquerda de cada nome do nó indica que todos os componentes estão no nó. Se um componente estiver em baixo num nó, verá um triângulo de alerta vermelho em vez da verificação verde.

![HDInsight Apache Ambari acolhe vista](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Em seguida, pode selecionar o **nome** de um nó para ver métricas de hospedeiro mais detalhadas para esse nó em particular. Esta visão mostra o estado/disponibilidade de cada componente individual.

![Apache Ambari acolhe vista de nó único](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Alertas de Ambari

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

## <a name="email-notifications"></a>Notificações por e-mail

Também pode configurar opcionalmente notificações de e-mail para alertas Ambari. Para isso, quando estiver no separador **Alertas,** clique no botão **Ações** na parte superior esquerda e, em seguida, **gerir notificações.**

![Ambari gere ação de notificações](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Será aberto um diálogo para a gestão de notificações de alerta. Selecione a **+** parte inferior do diálogo e preencha os campos necessários para fornecer à Ambari detalhes do servidor de e-mail a partir dos quais enviar e-mails.

> [!TIP]
> Configurar notificações de e-mail ambari pode ser uma boa maneira de receber alertas num só local ao gerir muitos clusters HDInsight.

## <a name="next-steps"></a>Passos seguintes

- [Disponibilidade e fiabilidade dos clusters Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
- [Disponibilidade do cluster - Registos do Monitor Azure](./cluster-availability-monitor-logs.md)
- [Utilize os registos do Monitor Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Notificações por e-mail do Apache Ambari](apache-ambari-email.md)
