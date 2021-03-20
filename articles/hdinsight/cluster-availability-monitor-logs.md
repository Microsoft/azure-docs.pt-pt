---
title: Como monitorizar a disponibilidade do cluster com registos do Azure Monitor em HDInsight
description: Saiba como utilizar os registos do Azure Monitor para monitorizar a saúde e disponibilidade do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 3bc5c659d9871cb8f1d49d2a3bfde2ce03faea86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571898"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Como monitorizar a disponibilidade do cluster com registos do Azure Monitor em HDInsight

Os clusters HDInsight incluem a integração de registos do Azure Monitor, que fornece métricas e troncos questionáveis, bem como alertas configuráveis. Este artigo mostra como usar o Azure Monitor para monitorizar o seu cluster.

## <a name="azure-monitor-logs-integration"></a>Integração de registos do Monitor Azure

Os registos do Azure Monitor permitem que os dados gerados por múltiplos recursos, tais como clusters HDInsight, sejam recolhidos e agregados num só local para obter uma experiência de monitorização unificada.

Como pré-requisito, você precisará de um log analytics workspace para armazenar os dados recolhidos. Se ainda não criou um, pode seguir as instruções aqui: [Criar um espaço de trabalho log analytics](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Permitir a integração de registos do HDInsight Azure Monitor

A partir da página de recursos do cluster HDInsight no portal, selecione **Azure Monitor**. Em seguida, selecione **ativar** e selecione o seu espaço de trabalho Log Analytics a partir do drop-down.

![Suíte de Gestão de Operações HDInsight](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Por predefinição, isto instala o agente OMS em todos os nós de cluster, exceto nos nós de borda. Como nenhum agente OMS é instalado nos nós de borda do cluster, não existe telemetria nos nós de borda presentes no Log Analytics por padrão.

## <a name="query-metrics-and-logs-tables"></a>Tabelas de métricas e troncos de consulta

Uma vez ativada a integração do registo do Monitor Azure (isto pode demorar alguns minutos), navegue para o seu recurso **log Analytics Workspace** e selecione **Logs**.

![Log Analytics registos de espaço de trabalho](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Os registos listam uma série de consultas de amostra, tais como:

| Nome de consulta                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Mapear o número de computadores que enviam registos, a cada hora                     |
| Liste batimentos cardíacos                 | Listar todos os batimentos cardíacos do computador da última hora                           |
| Última pulsação de cada computador | Mostre o último batimento cardíaco enviado por cada computador                             |
| Computadores indisponíveis           | Listar todos os computadores conhecidos que não enviaram um batimento cardíaco nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Como exemplo, executar a consulta da amostra **de taxa de disponibilidade** selecionando **Run** nessa consulta, como mostrado na imagem acima. Isto mostrará a taxa de disponibilidade de cada nó no seu cluster em percentagem. Se tiver ativado vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho Log Analytics, verá a taxa de disponibilidade para todos os nós (excluindo nós de borda) nesses clusters apresentados.

![Log Analytics logs workspace 'taxa de disponibilidade' consulta de amostra](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> A taxa de disponibilidade é medida durante um período de 24 horas, pelo que o seu cluster terá de funcionar durante pelo menos 24 horas antes de ver as taxas de disponibilidade precisas.

Pode fixar esta tabela num dashboard partilhado clicando em **Pin** no canto superior direito. Se não tiver painéis partilhados, pode ver como criar um aqui: [Criar e partilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Alertas do Monitor Azure

Também pode configurar alertas do Azure Monitor que irão desencadear quando o valor de uma métrica ou os resultados de uma consulta satisfaçam determinadas condições. Como exemplo, vamos criar um alerta para enviar um e-mail quando um ou mais nós não enviam um batimento cardíaco em 5 horas (ou seja, presume-se que não está disponível).

A partir de **Logs**, executar a consulta de amostra **de computadores Indisponíveis** selecionando **Run** nessa consulta, como mostrado abaixo.

![Log Analytics regista amostra de 'computadores indisponíveis'](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, esta consulta deve devolver zero resultados por enquanto. Clique **em Nova regra de alerta** para começar a configurar o seu alerta para esta consulta.

![Log Analytics nova regra de alerta do espaço de trabalho](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para o qual criar a regra (o espaço de trabalho Log Analytics neste caso), a *condição* para desencadear o alerta, e os *grupos de ação* que determinam o que vai acontecer quando o alerta é ativado.
Clique no título da **condição**, como mostrado abaixo, para terminar a configuração da lógica do sinal.

![Alerta de portal cria condição de regra](media/cluster-availability-monitor-logs/portal-condition-title.png)

Isto abrirá **a lógica do sinal de configuração.**

Desaver a secção **lógica alerta** da seguinte forma:

*Com base em: Número de resultados, Condição: Maior do que, Limiar: 0.*

Uma vez que esta consulta só devolve nós indisponíveis como resultados, se o número de resultados for sempre superior a 0, o alerta deverá disparar.

Na secção **Avaliada com base na** secção, desa estaver o **período** e a **frequência** com base na frequência com que pretende verificar se existem nós indisponíveis.

Para efeitos deste alerta, deverá certificar-se de **period=frequência.** Mais informações sobre o período, frequência e outros parâmetros de alerta podem ser encontrados [aqui.](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition)

Selecione **Feito** quando terminar de configurar a lógica do sinal.

![Regra de alerta configura lógica de sinal](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Se ainda não tiver um grupo de ação existente, clique em **Criar Novo** na secção **Grupos de Ação.**

![Regra de alerta cria novo grupo de ação](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Isto abrirá **o Grupo de Ação Add**. Escolha um **nome de grupo action,** **nome curto,** **subscrição** e **grupo de recursos.** Na secção **Ações,** escolha um **Nome de Ação** e selecione **e-mail/SMS/Push/Voice** como o **Tipo de Ação.**

> [!NOTE]
> Existem várias outras ações que um alerta pode desencadear além de um Email/SMS/Push/Voice, como uma Função Azure, LogicApp, Webhook, ITSM e Automation Runbook. [Saiba mais.](../azure-monitor/alerts/action-groups.md#action-specific-information)

Isto abrirá **o Email/SMS/Push/Voice**. Escolha um **Nome** para o destinatário, **verifique** a caixa **de e-mail** e escreva um endereço de e-mail para o qual deseja que o alerta seja enviado. Selecione **OK** em  **Email/SMS/Push/Voice,** em seguida, em **Adicionar grupo de ação** para terminar a configuração do seu grupo de ação.

![Regra de alerta cria adicionar grupo de ação](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Depois de estas lâminas fecharem, deverá ver o seu grupo de ação listado na secção **Grupos de Ação.** Por fim, preencha a secção **Detalhes de Alerta** digitando um Nome e **Descrição** **da Regra de Alerta** e escolhendo uma **Severidade**. Clique **em Criar Regra de Alerta** para terminar.

![Portal cria acabamento de regra de alerta](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **Severidade** é uma ferramenta poderosa que pode ser usada ao criar vários alertas. Por exemplo, pode criar um alerta para levantar um Aviso (Sev 1) se um único nó de cabeça cair e outro alerta que eleva a Critical (Sev 0) no caso improvável de ambos os nós da cabeça descerem.

Quando a condição para este alerta for cumprida, o alerta disparará e receberá um e-mail com os detalhes de alerta como este:

![Exemplo de e-mail de alerta do Azure Monitor](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Também pode ver todos os alertas que tenham disparado, agrupados por gravidade, indo para **Alertas** no seu **Log Analytics Workspace**.

![Alertas de espaço de trabalho Log Analytics](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Selecionando em um agrupamento de severidade (ou **seja, Sev 1,** como acima salientado) mostrará registos para todos os alertas dessa gravidade que dispararam como abaixo:

![Log Analytics workspace sev um alerta](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passos seguintes

* [Disponibilidade do cluster - Apache Ambari](./hdinsight-cluster-availability.md)
* [Utilize registos do Monitor Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)
