---
title: Como monitorizar a disponibilidade de clusters com Apache Ambari em Azure HDInsight
description: Saiba como usar o Apache Ambari para monitorizar a saúde e disponibilidade do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 34f752d4c3d50d5f680e317b2724b1e631537f32
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933198"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Como monitorizar a disponibilidade de clusters com Apache Ambari em Azure HDInsight

Os clusters HDInsight incluem Apache Ambari, que fornece informações de saúde num ápice e alertas predefinidos.

Este artigo mostra como usar Ambari para monitorizar o seu cluster e percorre alguns exemplos para configurar um alerta Ambari, monitorizar a taxa de disponibilidade de nó, e criar um alerta do Azure Monitor que dispara quando um batimento cardíaco não é recebido de um ou mais nós em cinco horas.

## <a name="dashboard"></a>Dashboard

O dashboard Ambari pode ser acedido selecionando o link **doméstico Ambari** na secção **de dashboards cluster** da visão geral hdInsight no portal Azure, como mostrado abaixo. Em alternativa, pode ser acedido navegando num `https://CLUSTERNAME.azurehdinsight.net` navegador onde clustername é o nome do seu cluster.

![Vista do portal de recursos HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Em seguida, será solicitado um nome de utilizador de login de cluster e senha. Introduza as credenciais que escolheu quando criou o cluster.

Em seguida, você será levado para o dashboard Ambari, que contém widgets que mostram um punhado de métricas para lhe dar uma visão geral rápida da saúde do seu cluster HDInsight. Estes widgets mostram métricas como o número de DataNodes vivos (nós operários) e JournalNodes (nóiro do zooman), o tempo de uptime NameNodes (nosdes da cabeça), bem como métricas específicas para certos tipos de clusters, como o tempo de uptime yarn ResourceManager para clusters Spark e Hadoop.

![Apache Ambari usa ecrã do painel de instrumentos](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>Anfitriões – ver o estado do nó individual

Também pode ver informações de estado para nós individuais. Selecione o **separador Anfitriões** para ver uma lista de todos os nós no seu cluster e consulte informações básicas sobre cada nó. A verificação verde à esquerda de cada nome do nó indica que todos os componentes estão no nó. Se um componente estiver em baixo num nó, verá um triângulo de alerta vermelho em vez da verificação verde.

![HDInsight Apache Ambari acolhe vista](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Em seguida, pode selecionar o **nome** de um nó para visualizar métricas de anfitrião mais detalhadas para esse nó em particular. Esta vista mostra o estado/disponibilidade de cada componente individual.

![Apache Ambari acolhe vista única do nó](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Alertas de Ambari

Ambari também oferece vários alertas configuráveis que podem fornecer notificação de certos eventos. Quando os alertas são acionados, são mostrados no canto superior esquerdo de Ambari num distintivo vermelho contendo o número de alertas. A seleção deste crachá mostra uma lista de alertas atuais.

![Apache Ambari atual alerta conta](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Para ver uma lista de definições de alerta e seus estados, selecione o separador **Alertas,** como mostrado abaixo.

![Ambari alerta ver definições](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferece muitos alertas predefinidos relacionados com a disponibilidade, incluindo:

| Nome do Alerta                        | Descrição   |
|---|---|
| Resumo da saúde dataNode           | Este alerta de nível de serviço é desencadeado se houver DataNodes pouco saudáveis|
| NomeNode Alta Disponibilidade Saúde | Este alerta de nível de serviço é acionado se o Nome Activonode ou o Nome de StandbyNode não estiverem em execução.|
| Por cento DiárioNodes disponíveis    | Este alerta é desencadeado se o número de Diárionodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo JournalNode. |
| Por cento DataNodes Disponíveis       | Este alerta é desencadeado se o número de DataNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo DataNode.|


Para ver os detalhes para um alerta ou modificar critérios, selecione o **nome** do alerta. Tome **o Resumo da Saúde dataNode** como um exemplo. Pode ver uma descrição do alerta, bem como os critérios específicos que desencadearão um alerta de "aviso" ou "crítico" e o intervalo de verificação dos critérios. Para editar a configuração, selecione o botão **Editar** no canto superior direito da caixa Configuração.

![Configuração de alerta Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, pode editar a descrição e, mais importante, o intervalo de verificação e os limiares para alerta ou alertas críticos.

![Configurações de alerta Ambari editam vista](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, pode fazer com que 2 DataNodes não saudáveis desencadeiem um alerta crítico e 1 DataNode pouco saudável apenas desencadeie um aviso. **Selecione Guardar** quando terminar a edição.

## <a name="email-notifications"></a>Notificações por e-mail

Também pode configurar opcionalmente notificações de e-mail para alertas Ambari. Para isso, quando estiver no separador **Alertas,** clique no botão **Ações** no canto superior esquerdo e, em seguida, **Gerencie as Notificações.**

![Ambari gere ação de notificações](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Será aberto um diálogo para a gestão das notificações de alerta. Selecione a **+** parte inferior do diálogo e preencha os campos necessários para fornecer a Ambari detalhes do servidor de e-mail a partir dos quais enviar e-mails.

> [!TIP]
> A configuração de notificações por e-mail Ambari pode ser uma boa maneira de receber alertas num só local quando se gerem muitos clusters HDInsight.

## <a name="next-steps"></a>Próximos passos

- [Disponibilidade e fiabilidade dos clusters Apache Hadoop em HDInsight](./hdinsight-business-continuity.md)
- [Disponibilidade do cluster - Registos do Azure Monitor](./cluster-availability-monitor-logs.md)
- [Utilize registos do Monitor Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Notificações por e-mail do Apache Ambari](apache-ambari-email.md)
