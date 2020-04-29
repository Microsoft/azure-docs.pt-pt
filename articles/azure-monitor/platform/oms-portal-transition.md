---
title: Portal OMS que se desloca para Azure Microsoft Docs
description: O portal OMS está a ser pôr-do-sol com toda a funcionalidade a mover-se para o portal Azure. Este artigo fornece detalhes sobre esta transição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77659259"
---
# <a name="oms-portal-moving-to-azure"></a>Portal OMS que se desloca para Azure

> [!NOTE]
> Este artigo aplica-se tanto à nuvem pública de Azure como à nuvem governamental, exceto quando se nota o contrário.

**O portal OMS para a nuvem pública de Azure foi oficialmente retirado. O portal OMS para a nuvem do Governo dos EUA foi oficialmente retirado a 15 de maio de 2019.** Estamos entusiasmados por nos mudarmos para o portal Azure e esperamos que a transição seja fácil. Mas entendemos que as mudanças são difíceis e podem ser disruptivas. O resto deste artigo passa por cima dos cenários-chave e do roteiro para esta transição.

O portal Azure é o centro de todos os serviços Azure e oferece uma rica experiência de gestão com capacidades como dashboards para fixar recursos, procura inteligente para encontrar recursos e marcação para gestão de recursos. Para consolidar e dinamizar o fluxo de trabalho de monitorização e gestão, começámos a adicionar as capacidades do portal OMS ao portal Azure. Todas as características do portal OMS fazem agora parte do portal Azure. De facto, algumas das novidades, como o Traffic Analytics, só estão disponíveis no portal Azure. Poderá realizar tudo o que estava a fazer no portal OMS com o portal Azure e muito mais. Se ainda não o fez, deve começar a usar o portal Azure hoje!

## <a name="what-is-changing"></a>O que está a mudar? 
As seguintes alterações estão a ser anunciadas com a depreciação do portal OMS. Cada uma destas alterações é descrita mais detalhadamente nas secções abaixo.

- Só é possível criar novos espaços de [trabalho](#new-workspaces) no portal Azure.
- A nova experiência de gestão de alerta [substitui a solução de Gestão de Alerta.](#changes-to-alerts)
- [A gestão](#user-access-and-role-migration) do acesso ao utilizador é agora feita no portal Azure utilizando o controlo de acesso baseado em funções do Azure.
- O [Conector de Insights de Aplicação já não é necessário,](#application-insights-connector-and-solution) uma vez que a mesma funcionalidade é ativada através de consultas de espaço de trabalho cruzado.
- A [Aplicação Móvel OMS](#oms-mobile-app) está a ser depreciada. 
- A [solução NSG está a ser substituída](#azure-network-security-group-analytics) por uma funcionalidade melhorada disponível através da solução Traffic Analytics.
- Novas ligações do System Center Operations Manager para log Analytics requerem pacotes de [gestão atualizados.](#system-center-operations-manager)
- Consulte a [Migração das suas Implementações de Atualização OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter detalhes sobre as alterações à Gestão de [Atualizações](../../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>O que devo fazer agora?
Embora a maioria das funcionalidades continue a funcionar sem realizar qualquer migração, você precisa realizar as seguintes tarefas:

- Precisa [de migrar as suas permissões](#user-access-and-role-migration) de utilizador para o portal Azure.
- Consulte a [Migrate as suas Implementações de Atualização OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter mais informações sobre a transição da solução de Gestão de Atualizações.

Consulte [questões comuns para a transição do portal OMS para](oms-portal-faq.md) o portal Azure para utilizadores de Log Analytics para obter informações sobre como transitar para o portal Azure. 

## <a name="user-access-and-role-migration"></a>Acesso ao utilizador e migração de papéis
A gestão do acesso ao portal Azure é mais rica e mais poderosa do que a gestão de acessos no Portal OMS. Consulte a conceção do seu espaço de [trabalho de Registos De MonitorEs Azure](design-logs-deployment.md) para obter detalhes sobre a gestão de acesso no Log Analytics.

> [!NOTE]
> Versões anteriores deste artigo afirmavam que as permissões seriam automaticamente convertidas do portal OMS para o portal Azure. Esta conversão automática já não está planeada e deve realizar a conversão por si mesmo.

Pode já ter acesso adequado no portal Azure, caso em que não precisa de fazer alterações. Há alguns casos em que pode não ter acesso adequado, caso em que o seu administrador deve atribuir-lhe permissões.

- Tem permissões de utilizador readonly no portal OMS, mas sem permissões no portal Azure. 
- Tem permissões contributivas no portal OMS, mas apenas o acesso do Leitor no portal Azure.
 
Em ambos os casos, o seu administrador precisa atribuir-lhe manualmente o papel adequado a partir da tabela seguinte. Recomendamos que atribua esta função ao nível de recursos ou subscrição.  Em breve, serão fornecidas orientações mais prescritivas para ambos os casos.

| Autorização do portal OMS | Papel Azure |
|:---|:---|
| ReadOnly | Leitor do Log Analytics |
| Contribuinte | Contribuidor do Log Analytics |
| Administrador | Proprietário | 
 

## <a name="new-workspaces"></a>Novos espaços de trabalho
Já não é capaz de criar novos espaços de trabalho utilizando o portal OMS. Siga a orientação em Criar um espaço de [trabalho Log Analytics no portal Azure](../learn/quick-create-workspace.md) para criar um novo espaço de trabalho no portal Azure.

## <a name="changes-to-alerts"></a>Alterações aos alertas

### <a name="alert-extension"></a>Extensão de alerta  

Os alertas foram [estendidos para o portal Azure](alerts-extend.md) Os alertas existentes continuarão a ser listados no portal OMS, mas só é possível geri-los no portal Azure. Se aceder a alertas programáticamente utilizando o Modelo de Alerta de Alerta de Log Analytics REST API ou Log Analytics Alert Resource, terá de utilizar grupos de ação em vez de ações nas suas chamadas API, modelos do Gestor de Recursos Azure e comandos PowerShell.

### <a name="alert-management-solution"></a>Solução de gestão de alerta
Como alteração de um anúncio anterior, a solução de [gestão](alert-management-solution.md) de Alerta continuará disponível e totalmente apoiada no portal Azure. Pode continuar a instalar a solução a partir do Azure Marketplace.

Embora a solução de gestão de Alerta continue disponível, encorajamo-lo a usar a [interface de alerta unificada do Azure Monitor](alerts-overview.md) para visualizar e gerir todos os alertas em Azure. Esta nova experiência agrega alertas de várias fontes dentro do Azure, incluindo alertas de registo do Log Analytics. Se estiver a utilizar a interface de alerta unificada do Azure Monitor, então a solução de gestão de alerta só é necessária para permitir a integração de alertas do Gestor de Operação do System Center para o Azure. Na interface de alerta unificada do Azure Monitor, pode ver distribuição dos seus alertas, tirar partido do agrupamento automatizado de alertas relacionados através de grupos inteligentes e ver alertas em várias subscrições enquanto aplica filtros ricos. Futuros avanços na gestão de alerta estarão principalmente disponíveis a partir desta nova experiência. 

Os dados recolhidos pela solução de gestão de Alerta (registos com um tipo de Alerta) continuam a ser no Log Analytics enquanto a solução estiver instalada para o espaço de trabalho. 

## <a name="oms-mobile-app"></a>Aplicação Móvel OMS
A aplicação móvel OMS será pôr-do-sol juntamente com o portal OMS. Em vez da aplicação móvel OMS, para aceder a informações sobre a sua infraestrutura de TI, dashboards e consultas guardadas, pode aceder diretamente ao portal Azure diretamente do seu navegador no seu dispositivo móvel. Para obter alertas, deve configurar os Grupos de [Ação Azure](action-groups.md) para receber notificações sob a forma de SMS ou uma chamada de voz

## <a name="application-insights-connector-and-solution"></a>Conector e solução de Insights de Aplicação
[O Conector insights](app-insights-connector.md) de aplicação fornece uma forma de incluir os dados de Insights de Aplicação num espaço de trabalho de Log Analytics. Esta duplicação de dados foi necessária para permitir a visibilidade através de dados de infraestruturas e aplicações. Com o Application Insights a alargar o suporte à retenção de dados em março de 2019 e a capacidade de realizar consultas de recursos cruzados, além de poder [visualizar vários recursos do Azure Monitor Application Insights,](../log-query/unify-app-resource-data.md)não há necessidade de duplicar dados dos recursos da sua Aplicação Insights e [enviá-lo](../log-query/cross-workspace-query.md) para o Log Analytics. Além disso, o Conector envia um subconjunto das propriedades das aplicações para o Log Analytics, enquanto as consultas de recursos cruzados conferem-lhe uma maior flexibilidade.  

Como tal, o Application Insights Connector foi depreciado e removido do Azure Marketplace juntamente com a deprecação do portal OMS em 30 de março de 2019. As ligações existentes continuarão a funcionar até 30 de junho de 2019. Com a depreciação do portal OMS, não há forma de configurar e remover as ligações existentes do portal. Isto será suportado através da API REST que será disponibilizada em janeiro de 2019 e será publicada uma notificação nas [atualizações do Azure.](https://azure.microsoft.com/updates/) 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [solução Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) será substituída pela recentemente lançada Traffic [Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) que proporciona visibilidade à atividade de utilizador e aplicação nas redes cloud. O Traffic Analytics ajuda-o a auditar a atividade de rede da sua organização, a garantir aplicações e dados, a otimizar o desempenho da carga de trabalho e a manter-se conforme. 

Esta solução analisa os registos de Fluxo NSG e fornece insights sobre o seguinte.

- O tráfego flui através das suas redes entre o Azure e a Internet, regiões públicas de nuvem, VNETs e subredes.
- Aplicações e protocolos na sua rede, sem necessidade de sniffers ou aparelhos de recolha de fluxos dedicados.
- Faladores de topo, aplicações tagarelas, conversas vm na nuvem, hotspots de tráfego.
- Fontes e destinos de tráfego através de VNETs, inter-relações entre serviços comerciais críticos e aplicações.
- Segurança, incluindo tráfego malicioso, portas abertas à Internet, aplicações ou VMs que tentam aceder à Internet.
- Utilização da capacidade, o que o ajuda a eliminar questões de excesso de provisionamento ou subutilização.

Pode continuar a confiar nas Definições de Diagnóstico para enviar registos DENSG para Log Analytics para que as pesquisas, alertas, dashboards existentes continuem a funcionar. Os clientes que já instalaram a solução podem continuar a utilizá-la até novo aviso. A partir de 5 de setembro, a solução Network Security Group Analytics será removida do mercado e disponibilizada através da comunidade como [modelo De QuickStart Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Se ligou o seu grupo de gestão de Gestão de [Operações ao Log Analytics,](om-agents.md)então continuará a funcionar sem alterações. No entanto, para novas ligações, deve seguir as orientações no [Microsoft System Center Operations Management Management Pack para configurar](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)a Suite de Gestão de Operações .

## <a name="next-steps"></a>Passos seguintes
- Consulte [questões comuns para a transição do portal OMS para](oms-portal-faq.md) o portal Azure para os utilizadores de Log Analytics para obter orientações sobre a mudança do portal OMS para o portal Azure.
