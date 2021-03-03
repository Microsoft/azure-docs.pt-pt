---
title: VM insights alertas de saúde dos hóspedes (pré-visualização)
description: Descreve os alertas criados pela VM insights saúde dos hóspedes, incluindo como habilitar e configurar notificações.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: a32ba9f1c4cf5d6bb9de69e1a6860c858e3ee2a6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707511"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>VM insights alertas de saúde dos hóspedes (pré-visualização)
A saúde do hóspede permite-lhe ver a saúde de uma máquina virtual como definida por um conjunto de medições de desempenho que são amostradas a intervalos regulares. Um alerta pode ser criado quando uma máquina virtual ou monitor muda para um estado pouco saudável. Pode ver e gerir estes alertas com [os criados pelas regras de alerta no Azure Monitor](../alerts/alerts-overview.md) e optar por ser notificado proativamente quando um novo alerta for criado.

## <a name="configure-alerts"></a>Configurar alertas
Não é possível criar uma regra de alerta explícita para a saúde dos hóspedes em VM enquanto esta funcionalidade estiver em pré-visualização. Por predefinição, serão criados alertas para cada máquina virtual, mas não para cada monitor.  Isto significa que se um monitor muda para um estado que não afeta o estado atual da máquina virtual, então nenhum alerta é criado porque o estado da máquina virtual não mudou. 

Pode desativar alertas para uma determinada máquina virtual ou para um determinado monitor numa máquina virtual a partir da definição de **estado de Alerta** na configuração para a máquina virtual no portal Azure. Consulte [a monitorização de configuração em informações VM para a saúde dos hóspedes (pré-visualização)](vminsights-health-configure.md) para obter detalhes sobre monitores de configuração no portal Azure. Consulte [a monitorização de configuração em informações VM para a saúde dos hóspedes utilizando regras de recolha de dados (pré-visualização)](vminsights-health-configure-dcr.md) para obter detalhes sobre a configuração de monitores através de um conjunto de máquinas virtuais.

## <a name="alert-severity"></a>Gravidade do alerta
A gravidade do alerta criado pela saúde dos hóspedes mapeia diretamente a gravidade da máquina virtual ou monitor que desencadeia o alerta.

| Estado de monitorização | Gravidade do alerta |
|:---|:---|
| Crítico | Sev1 |
| Aviso  | Sev2 |
| Bom estado de funcionamento  | Sev4 |

## <a name="alert-lifecycle"></a>Ciclo de vida de alerta
Será criado um [alerta Azure](../alerts/alerts-overview.md) para cada máquina virtual sempre que este se alterar para um estado **de Aviso** ou **Crítico.** Veja o alerta a partir de **Alertas** no menu **Azure Monitor** ou no menu da máquina virtual no portal Azure.

Se um alerta já estiver em estado **de Despedimento** quando o estado da máquina virtual mudar, então não será criado um segundo alerta, mas a gravidade do mesmo alerta será alterada para corresponder ao estado da máquina virtual. Por exemplo, se a máquina virtual mudar para o estado **crítico** quando um alerta de **aviso** já estava em estado **de Despedimento,** a gravidade desse alerta será alterada para **Sev1**. Se a máquina virtual mudar para um estado **de aviso** quando um alerta **Sev1** já estava em estado **de Despedimento,** a gravidade desse alerta será alterada para **Sev2**. Se a máquina virtual voltar para um estado **saudável,** então o alerta será resolvido com a severidade alterada para **Sev4**.

## <a name="viewing-alerts"></a>Alertas de visualização
Ver alertas criados pela VM insights saúde dos hóspedes com [outros alertas no portal Azure](../platform/alerts-overview.md#alerts-experience). Pode selecionar **Alertas** do menu **Azure Monitor** para visualizar alertas para todos os recursos monitorizados ou selecionar **Alertas** do menu de uma máquina virtual para visualizar alertas apenas para essa máquina virtual.

## <a name="alert-properties"></a>Propriedades do alerta

### <a name="properties-in-the-azure-portal"></a>Propriedades no portal Azure
As propriedades do alerta quando o vê no portal Azure são descritos na tabela seguinte.

| Propriedade | Descrição |
|:---|:---|
| Monitore o estado antes de o alerta ter sido criado | Estado do monitor ou máquina virtual antes deste alerta ter sido disparado da primeira vez. |
| Monitore o estado quando o alerta foi criado | Estado do monitor ou máquina virtual quando o alerta foi disparado pela primeira vez. Este é o estado que causou o alerta para o incêndio. |
| Para saber mais sobre a transição do Estado quando o alerta foi criado | Ligue à página VM Health onde pode ver a transição exata do estado. Esta transição estatal representa o caso quando o monitor passou de Estado **Saudável** para um estado não saudável. |

Por exemplo, um monitor vai de **Saudável** a **Crítico** no momento t0, e um novo alerta é disparado com **Sev1**. Em seguida, vai de **Crítico** a **Aviso** no momento t1, e a gravidade do alerta é atualizada para **Sev2**. Torna-se **saudável** no momento t2, e o alerta é resolvido.

As propriedades de alerta terão estes valores durante toda esta sequência.

- Monitor estado antes de o alerta ser criado: Saudável
- Monitore o estado quando o alerta foi criado: Critical
- Para saber mais sobre a transição do Estado quando o alerta foi criado: A ligação de navegação à transição do Estado aconteceu no momento t0.


### <a name="properties-in-notifications"></a>Propriedades em notificações
As propriedades do alerta incluídas nas notificações são descritas no quadro seguinte.

| Propriedade | Descrição |
|:---|:---|
| Estado de monitorização anterior | Estado de monitor ou máquina virtual antes de mudar o estado. Se a atualização de gravidade de alerta desencadear esta notificação, esta propriedade representa o estado que estava pouco antes da atualização da severidade. |
| Estado de monitorização atual | Estado do monitor ou máquina virtual quando mudou o estado. Se a atualização de gravidade de alerta desencadear esta notificação, esta propriedade representa o novo estado. |
| Para saber mais sobre esta transição de estado | Ligue à página VM Health onde pode ver a transição exata do estado. Esta transição estatal representa o caso quando o monitor mudou o estado que desencadeou esta notificação. |

Utilizando o exemplo acima, as notificações teriam as seguintes propriedades em cada momento.

Notificação recebida no momento t0
- Estado do monitor anterior: Saudável
- Estado do monitor atual: Crítico
- Para saber mais sobre esta transição de estado: A ligação de navegação à transição do Estado aconteceu no momento t0.

Notificação recebida no momento t1
- Estado do monitor anterior: Crítico
- Estado do monitor atual: Aviso
- Para saber mais sobre esta transição de estado: A ligação de navegação à transição do Estado aconteceu no momento t1.

Notificação recebida no momento t2
- Estado do monitor anterior: Aviso
- Estado do monitor atual: Saudável
- Para saber mais sobre esta transição de estado: A ligação de navegação à transição do Estado aconteceu no momento t2.

## <a name="configure-notifications"></a>Configurar notificações
Para ser notificado proativamente de um alerta desencadeado pela saúde dos hóspedes, crie um grupo de [ação](../alerts/action-groups.md) para definir as diferentes ações a executar, tais como o envio de uma mensagem SMS ou o início de uma App Lógica. Em seguida, crie uma [regra de ação](../alerts/alerts-action-rules.md) que especifique o âmbito dos monitores e das máquinas virtuais e utilize esse grupo de ação.

No menu **Monitor** no portal Azure, selecione **Alertas**.  **Selecione Gerir as ações** e, em seguida, **regras de ação (pré-visualização)**. 

![Nova regra de ação](media/vminsights-health-alerts/action-rule-new.png)

Clique em **Nova regra de ação** para criar uma nova regra. Clique **em Selecionar** ao lado do âmbito e selecione uma subscrição, grupo de recursos ou uma ou mais máquinas virtuais específicas. A notificação será apenas disparada para máquinas virtuais que se enquadram no âmbito.

![Âmbito de regras de ação](media/vminsights-health-alerts/action-rule-scope.png)

Clique **em Adicionar** ao lado do **Filtro**. Crie um filtro onde **o serviço Monitor é igual a VM Insights - Saúde**. Adicione outros filtros para especificar os alertas específicos que devem desencadear a notificação. Por exemplo, pode utilizar **a Severidade** para combinar alertas de todos os monitores que correspondam a uma gravidade particular.

![Filtro de regras de ação](media/vminsights-health-alerts/action-rule-filter.png)

Em **Definir neste âmbito,** selecione **Action group** e, em seguida, selecione o grupo de ação para associar ao monitor. Dê à regra um nome e selecione o grupo de recursos em que deve ser guardado. Clique em **Criar** para criar a regra.

![Regra de ação](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Passos seguintes

- [Capacitar a saúde dos hóspedes em insights VM e agentes a bordo.](vminsights-health-enable.md)
- [Configure os monitores utilizando o portal Azure.](vminsights-health-configure.md)
- [Configure os monitores utilizando as regras de recolha de dados.](vminsights-health-configure-dcr.md)