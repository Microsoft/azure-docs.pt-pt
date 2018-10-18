---
title: Portal do OMS para o Azure | Documentos da Microsoft
description: Portal do OMS está a ser sunsetted com todas as funcionalidades de mudar para o portal do Azure. Este artigo fornece detalhes sobre esta transição.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 6e3026ea3aca897ab63f4ad7d72eac438fdb7199
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389116"
---
# <a name="oms-portal-moving-to-azure"></a>Portal do OMS para o Azure

> [!NOTE]
> Este artigo aplica-se para a cloud pública do Azure e a cloud de governo, exceto onde indicado o contrário.

O portal do Azure é o hub para todos os serviços do Azure e oferece uma gestão avançada de experiência com recursos como os dashboards para fixar recursos, a pesquisa inteligente para localizar recursos e a identificação para gestão de recursos. Para consolidar e simplificar o fluxo de trabalho de monitorização e gestão, começamos a adicionar as capacidades de portais do OMS no portal do Azure. Todas as funcionalidades do portal do OMS agora fazem parte do portal do Azure. Na verdade, alguns dos novos recursos, tais como análise de tráfego só estão disponíveis no portal do Azure. Será capaz de realizar tudo que estava a fazer no portal do OMS com o portal do Azure e muito mais. Se ainda não o fez, deve começar a utilizar o portal do Azure hoje mesmo!

**Portal do OMS será oficialmente retirado a 15 de Janeiro de 2019.** Temos o prazer de passar para o portal do Azure e esperar que a transição para ser fácil. Mas estamos cientes de alterações são difíceis e podem ser prejudicais. Envie dúvidas, comentários ou preocupações para **LAUpgradeFeedback@microsoft.com**. O restante deste artigo passa pelos cenários-chave e o plano para essa transição.

## <a name="what-is-changing"></a>O que está a mudar? 
As seguintes alterações estejam sendo anunciadas com a descontinuação do portal do OMS. Cada uma dessas alterações é descrita mais detalhadamente nas secções abaixo.

- Pode criar novos [áreas de trabalho apenas](#new-workspaces) no portal do Azure.
- A nova experiência de gestão de alertas [substitui a solução de gestão de alertas](#changes-to-alerts).
- [Gestão de acesso de utilizador](#user-access-and-role-migration) é agora feita no portal do Azure com o controlo de acesso baseado em função do Azure.
- O [conector do Application Insights já não é necessário](#application-insights-connector-and-solution) , uma vez que a mesma funcionalidade é ativada através da área de trabalho em várias consultas.
- O [aplicação OMS Mobile](#oms-mobile-app) está a ser preterido. 
- O [solução NSG está a ser substituída](#azure-network-security-group-analytics) com funcionalidade disponível por meio de solução de análise de tráfego.
- Necessitam de novas ligações a partir do System Center Operations Manager ao Log Analytics [atualizados os pacotes de gestão](#system-center-operations-manager).
- Ver [migrar as implementações de atualização do OMS para o Azure](../automation/migrate-oms-update-deployments.md) para obter detalhes sobre as alterações [gestão de atualizações](../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>O que devo fazer agora?
Embora a maioria dos recursos vão continuar a funcionar sem executar qualquer migração, tem de efetuar as seguintes tarefas:

- Precisa [migrar as suas permissões de utilizador](#user-access-and-role-migration) ao portal do Azure.
- Ver [migrar as implementações de atualização do OMS para o Azure](../automation/migrate-oms-update-deployments.md) para obter detalhes sobre a transição da solução de gestão de atualizações.

Consulte a [perguntas comuns para a transição do portal do OMS para o portal do Azure para utilizadores do Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) para obter informações sobre como efetuar a transição para o portal do Azure. Enviar comentários, dúvidas ou preocupações para **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Migração de acesso e a função de utilizador
Gestão de acesso ao portal do Azure é mais rica e mais poderoso do que a gestão de acesso no Portal do OMS. Ver [gerir áreas de trabalho](log-analytics-manage-access.md#manage-accounts-and-users) para obter detalhes sobre a gestão de acesso no Log Analytics.

> [!NOTE]
> Versões anteriores deste artigo indicado que as permissões seja convertidas do portal do OMS para o portal do Azure. Esta conversa automática já não está prevista e tem de efetuar a conversão por conta própria.

Pode já ter acesso adequado no portal do Azure caso em que não precisa de fazer quaisquer alterações. Existem alguns casos em que pode não ter acesso apropriado, caso em que o administrador tem de atribuir permissões.

- Tem as permissões de utilizador só de leitura no portal do OMS, mas não existem permissões no portal do Azure. 
- Tem as permissões de contribuinte no portal do OMS, mas apenas o acesso de leitor no portal do Azure.
 
Em ambos os casos, o administrador tem de atribuir manualmente a função adequada da tabela seguinte. Recomendamos que atribua esta função ao nível de grupo ou uma subscrição do recurso.  Diretrizes prescritivas mais serão fornecida em breve para ambos os casos.

| Permissão de portal do OMS | Função do Azure |
|:---|:---|
| ReadOnly | Leitor do Log Analytics |
| Contribuinte | Contribuidor do Log Analytics |
| Administrador | Proprietário | 
 

## <a name="new-workspaces"></a>Novas áreas de trabalho
Já não estão a ser capaz de criar novas áreas de trabalho através do portal do OMS. Siga as orientações no [criar uma área de trabalho do Log Analytics no portal do Azure](log-analytics-quick-create-workspace.md) para criar uma nova área de trabalho no portal do Azure.

## <a name="changes-to-alerts"></a>Alterações para alertas

### <a name="alert-extension"></a>Extensão de alerta  

> [!NOTE]
> Alertas agora foram estendidos totalmente no portal do Azure para a cloud pública. Regras de alerta existentes podem ser visualizadas no portal do OMS, mas só podem ser geridos no portal do Azure. Extensão de alertas no portal do Azure será iniciado para a cloud do Azure government em Outubro de 2018.

Foram removidos [expandidos para o portal do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Quando isso for concluído, as ações de gestão de alertas só estará disponíveis no portal do Azure. Alertas existentes continuarão a ser listados no portal do OMS. Se aceder aos alertas através de programação, utilizando a API de REST de alerta do Log Analytics ou o modelo de recurso alerta do Log Analytics, terá de utilizar grupos de ação em vez de ações em suas chamadas de API, modelos Azure Resource Manager e comandos do PowerShell.

### <a name="alert-management-solution"></a>Solução de gestão de alertas
Em vez do [solução de gestão de alertas](log-analytics-solution-alert-management.md), pode utilizar [Azure Monitor unified interface alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para visualizar e gerir os alertas. Esta nova experiência agrega alertas de várias origens dentro do Azure incluindo alertas de registos do Log Analytics. Pode ver distribuições os alertas, tirar partido do agrupamento automático de alertas relacionados por meio de grupos inteligentes e ver alertas em várias subscrições ao aplicar filtros avançados. Todos esses recursos estão disponíveis em pré-visualização, a partir de 4 de Junho de 2018. A solução de gestão de alertas não estarão disponível no portal do Azure. 

Os dados recolhidos pela solução de gestão de alertas (registos com um tipo de alerta) continuam a ser no Log Analytics, desde que a solução está instalada para a área de trabalho. A partir de Agosto de 2018, transmissão em fluxo de alertas a partir de alertas unificada em áreas de trabalho será ativada, esta capacidade a substituir. Algumas alterações de esquema são esperadas e serão anunciadas numa data posterior.

## <a name="oms-mobile-app"></a>Aplicação móvel do OMS
A aplicação móvel do OMS será sunsetted, juntamente com o portal do OMS. Em vez da aplicação móvel do OMS, para aceder às informações sobre a infraestrutura sua TI, dashboards e consultas guardadas, pode aceder o portal do Azure diretamente a partir do seu browser no seu dispositivo móvel. Para obter alertas, deve configurar [grupos de ação do Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) para receber notificações na forma de SMS ou uma chamada de voz

## <a name="application-insights-connector-and-solution"></a>Conector do Application Insights e solução
[Conector do Application Insights](log-analytics-app-insights-connector.md) fornece uma forma de trazer dados do Application Insights para uma área de trabalho do Log Analytics. Essa duplicação de dados era necessária para ativar a visibilidade em todos os dados de infraestrutura e a aplicação.

Com o apoio da [consultas entre recursos](log-analytics-cross-workspace-search.md), já não existe essa necessidade de duplicar os dados. Como tal, a solução existente do Application Insights vai ser preterida. A partir de Outubro, não será capaz de ligar os novos recursos do Application Insights a áreas de trabalho do Log Analytics. Ligações existentes e dashboards vão continuar a funcionar até 15 de Janeiro de 2019.


## <a name="azure-network-security-group-analytics"></a>Análise do Grupo de Segurança de Rede do Azure
O [solução de análise de grupo de segurança de rede do Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) será substituído com lançado recentemente [análise de tráfego](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) que fornece visibilidade para a atividade de utilizador e da aplicação em redes em nuvem. Análise de tráfego ajuda-o a atividade de rede da sua organização, aplicativos seguros e dados de auditoria, otimizar o desempenho da carga de trabalho e esteja em conformidade. 

Esta solução analisa os registos de fluxo de NSG e fornece informações sobre o seguinte.

- Fluxos de tráfego nas suas redes entre o Azure e Internet, regiões de cloud pública, VNETs e sub-redes.
- Os aplicativos e protocolos na sua rede, sem a necessidade de (farejadores) ou aplicações de coleção de fluxo dedicado.
- Principais tópicos de conversação, aplicativos chatty, conversações de VM na nuvem, hotspots de tráfego.
- Origens e destinos de tráfego nas VNETs, entre as relações entre aplicativos e serviços críticos de negócio.
- Segurança incluindo o tráfego malicioso, portas abertas para a Internet, aplicativos ou VMs tentar acesso à Internet.
- Utilização da capacidade, que o ajuda a eliminar problemas de através de aprovisionamento ou subutilização.

Pode continuar a confiar em definições de diagnóstico para enviar registos NSG para o Log Analytics, para que os já existentes guardar pesquisas, alertas, dashboards continuarão a funcionar. Os clientes que já tenham instalado a solução podem continuar a utilizá-lo até aviso prévio. A partir 5 de Setembro, a solução de análise do grupo de segurança de rede será removida do marketplace e disponibilizada por meio da Comunidade como um [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Se tiver [ligado o grupo de gestão do Operations Manager ao Log Analytics](log-analytics-om-agents.md), em seguida, irá continuar a trabalhar sem alterações. Em novas ligações no entanto, tem de seguir as orientações no [Microsoft System Center Operations Manager Management Pack para configurar o Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Passos Seguintes
- Ver [perguntas comuns para a transição do portal do OMS para o portal do Azure para utilizadores do Log Analytics](log-analytics-oms-portal-faq.md) para obter orientações sobre a mudança do portal do OMS para o portal do Azure.