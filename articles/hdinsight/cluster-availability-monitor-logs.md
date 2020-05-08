---
title: Como monitorizar a disponibilidade do cluster com registos do Monitor Azure no HDInsight
description: Aprenda a utilizar os registos do Monitor Azure para monitorizar a saúde e disponibilidade do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 5527a27ab94c350a345bb916206057069c3d16da
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82723171"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Como monitorizar a disponibilidade do cluster com registos do Monitor Azure no HDInsight

Os clusters HDInsight incluem integração de registos do Monitor Azure, que fornece métricas e registos que se questionáveis, bem como alertas configuráveis. Este artigo mostra como usar o Monitor Azure para monitorizar o seu cluster.

## <a name="azure-monitor-logs-integration"></a>Integração de registos do Monitor Azure

Os registos do Monitor Azure permitem que os dados gerados por vários recursos, como os clusters HDInsight, sejam recolhidos e agregados num só local para alcançar uma experiência de monitorização unificada.

Como pré-requisito, vai precisar de um Espaço de Trabalho de Log Analytics para armazenar os dados recolhidos. Se ainda não criou uma, pode seguir as instruções aqui: [Criar um espaço](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)de trabalho de Log Analytics .

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Ativar a integração de registos do HDInsight Azure Monitor

A partir da página de recursos de cluster HDInsight no portal, **selecione Azure Monitor**. Em seguida, **selecione ativar** e selecione o seu espaço de trabalho Log Analytics a partir do drop-down.

![Suíte de Gestão de Operações HDInsight](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

## <a name="query-metrics-and-logs-tables"></a>Métricas de consulta e tabelas de troncos

Assim que a integração do registo do Monitor Azure estiver ativada (isto pode demorar alguns minutos), navegue para o seu recurso **Log Analytics Workspace** e selecione **Registos**.

![Log Analytics registos do espaço de trabalho](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Os registos listam uma série de consultas de amostra, tais como:

| Nome de consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Cartografe o número de computadores que enviam registos, a cada hora                     |
| Lista de batimentos cardíacos                 | Enumerar todos os batimentos cardíacos do computador da última hora                           |
| Último batimento cardíaco de cada computador | Mostre o último batimento cardíaco enviado por cada computador                             |
| Computadores indisponíveis           | Lista ruma todos os computadores conhecidos que não enviaram um batimento cardíaco nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Como exemplo, execute a consulta da amostra de taxa de **disponibilidade** selecionando **Executar** nessa consulta, como mostrado na imagem acima. Isto mostrará a taxa de disponibilidade de cada nó no seu cluster em percentagem. Se tiver ativado vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho log Analytics, verá a taxa de disponibilidade para todos os nós desses clusters apresentados.

![Log Analytics workspace regista consulta de amostra de 'taxa de disponibilidade'](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> A taxa de disponibilidade é medida durante um período de 24 horas, pelo que o seu cluster terá de funcionar durante pelo menos 24 horas antes de ver taxas de disponibilidade precisas.

Pode fixar esta tabela num dashboard partilhado clicando em **Pin** no canto superior direito. Se não tiver nenhum dashboard partilhado, pode ver como criar um aqui: [Criar e partilhar dashboards no portal Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Alertas do Monitor Azure

Também pode configurar alertas do Monitor Azure que irão desencadear quando o valor de uma métrica ou os resultados de uma consulta satisfaçam determinadas condições. Como exemplo, vamos criar um alerta para enviar um e-mail quando um ou mais nós não enviam um batimento cardíaco em 5 horas (ou seja, presume-se que não esteja disponível).

A partir de **Registos,** execute a consulta de amostra de **computadores indisponíveis** selecionando **Executar** nessa consulta, como mostrado abaixo.

![Log Analytics workspace regista amostra de 'computadores indisponíveis'](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, esta consulta deve devolver zero resultados por enquanto. Clique em **Nova regra** de alerta para começar a configurar o seu alerta para esta consulta.

![Log Analytics workspace nova regra de alerta](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para o qual criar a regra (o espaço de trabalho log Analytics neste caso), a *condição* para desencadear o alerta, e os *grupos* de ação que determinam o que vai acontecer quando o alerta for desencadeado.
Clique no título de **condição**, como mostrado abaixo, para terminar de configurar a lógica do sinal.

![Alerta de portal criar condição de regra](media/cluster-availability-monitor-logs/portal-condition-title.png)

Isto abrirá a lógica do **sinal configure.**

Desloque a secção lógica de **alerta** da seguinte forma:

*Com base em: Número de resultados, Condição: Maior do que, Limiar: 0.*

Uma vez que esta consulta só devolve nós indisponíveis como resultados, se o número de resultados for sempre maior do que 0, o alerta deve disparar.

No **Avaliado com base na** secção, desloque o **período** e **a frequência** com base na frequência com que pretende verificar os nós indisponíveis.

Para efeitos deste alerta, pretende certificar-se de **Period=Frequency.** Mais informações sobre período, frequência e outros parâmetros de alerta podem ser encontrados [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Selecione **Feito** quando terminar de configurar a lógica do sinal.

![Regra de alerta configura lógica de sinal](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Se ainda não tiver um grupo de ação existente, clique em **Criar Novo** na secção **Grupos de Ação.**

![Regra de alerta cria novo grupo de ação](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Isto abrirá **o grupo de ação Add**. Escolha um nome de **grupo de Ação**, Nome **curto,** **Subscrição**e **Grupo de Recursos.** Na secção **Ações,** escolha um Nome de **Ação** e selecione **Email/SMS/Push/Voice** como o Tipo de **Ação.**

> [!NOTE]
> Existem várias outras ações que um alerta pode desencadear além de um E-mail/SMS/Push/Voice, como uma Função Azure, LogicApp, Webhook, ITSM e Automation Runbook. [Saiba mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Isto abrirá **o email/SMS/Push/Voice**. Escolha um **Nome** para o destinatário, **verifique** a caixa de **e-mail** e digite um endereço de e-mail para o qual deseja o alerta enviado. Selecione **OK** em **Email/SMS/Push/Voice**e, em seguida, em **Adicionar grupo de ação** para terminar de configurar o seu grupo de ação.

![Regra de alerta cria grupo de ação adicionar](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Depois destas lâminas fecharem, deve ver o seu grupo de ação listado na secção **Grupos de Ação.** Por fim, complete a secção **de Detalhes** de Alerta digitando um Nome e **Descrição** da Regra de **Alerta** e escolhendo uma **Gravidade**. Clique em **Criar regra** de alerta para terminar.

![Portal cria fim de regra de alerta](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **a Severity** é uma ferramenta poderosa que pode ser usada ao criar vários alertas. Por exemplo, pode criar um alerta para levantar um aviso (Sev 1) se um único nó de cabeça descer e outro alerta que eleva o Critical (Sev 0) no caso improvável de ambos os nós descerem.

Quando a condição para este alerta for satisfeita, o alerta disparará e receberá um e-mail com os detalhes do alerta como este:

![Exemplo de e-mail de alerta Azure Monitor](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Também pode ver todos os alertas que dispararam, agrupados pela gravidade, indo para **Alertas** no seu Espaço de **Trabalho log Analytics**.

![Alertas de espaço de trabalho de Log Analytics](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

A seleção de um agrupamento de gravidade (isto **é, Sev 1,** como acima destacado) mostrará registos de todos os alertas dessa gravidade que dispararam como abaixo:

![Log Analytics workspace sev um alerta](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passos seguintes

* [Disponibilidade do cluster - Apache Ambari](./hdinsight-cluster-availability.md)
* [Utilize os registos do Monitor Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)