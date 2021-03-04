---
title: Portal da OMS muda-se para Azure | Microsoft Docs
description: O portal OMS está a ser aparafusado com toda a funcionalidade a mover-se para o portal Azure. Este artigo fornece detalhes sobre esta transição.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: fc9da5c9bc6c9f1b5afd0dbcd74b19d13fe42222
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032063"
---
# <a name="oms-portal-moving-to-azure"></a>Portal OMS movendo-se para Azure

> [!NOTE]
> Este artigo aplica-se tanto à nuvem pública azure como à nuvem governamental, exceto quando se nota o contrário.

**O portal OMS para a nuvem pública de Azure foi oficialmente retirado. O portal OMS para a nuvem do Governo dos EUA foi oficialmente retirado a 15 de maio de 2019.** Estamos entusiasmados por nos mudarmos para o portal Azure e esperar que a transição seja fácil. Mas entendemos que as mudanças são difíceis e podem ser disruptivas. O resto deste artigo passa por cima dos cenários-chave e do roteiro para esta transição.

O portal Azure é o hub de todos os serviços da Azure e oferece uma rica experiência de gestão com capacidades como dashboards para fixar recursos, pesquisa inteligente para encontrar recursos, e marcação para gestão de recursos. Para consolidar e agilizar o fluxo de trabalho de monitorização e gestão, começámos a adicionar as capacidades do portal OMS ao portal Azure. Todas as funcionalidades do portal OMS fazem agora parte do portal Azure. De facto, algumas das novas funcionalidades, como o Traffic Analytics, apenas estão disponíveis no portal Azure. Poderá realizar tudo o que estava a fazer no portal OMS com o portal Azure e muito mais. Se ainda não o fez, deve começar hoje a usar o portal Azure!

## <a name="what-is-changing"></a>O que está a mudar? 
Estão a ser anunciadas as seguintes alterações com a depreciação do portal OMS. Cada uma destas alterações é descrita mais detalhadamente nas secções abaixo.

- Só pode criar novos [espaços de trabalho](#new-workspaces) no portal Azure.
- A nova experiência de gestão de alerta [substitui a solução De Gestão de Alertas.](#changes-to-alerts)
- [A gestão do acesso](#user-access-and-role-migration) ao utilizador é agora feita no portal Azure utilizando o controlo de acesso baseado em funções Azure.
- O [Conector de Insights de Aplicação já não é necessário,](#application-insights-connector-and-solution) uma vez que a mesma funcionalidade é ativada através de consultas de espaço de trabalho transversal.
- A [Aplicação Móvel OMS](#oms-mobile-app) está a ser depreciada. 
- A [solução NSG está a ser substituída por](#azure-network-security-group-analytics) uma funcionalidade melhorada disponível através da solução Traffic Analytics.
- As novas ligações do Gestor de Operações do System Center para o Log Analytics requerem [pacotes de gestão atualizados.](#system-center-operations-manager)
- Consulte [a Migração das suas Implementações de Atualização OMS para Azure](../../automation/migrate-oms-update-deployments.md) para obter mais detalhes sobre as alterações à [Gestão de Atualização](../../automation/update-management/overview.md).


## <a name="what-should-i-do-now"></a>O que devo fazer agora?
Embora a maioria das funcionalidades continue a funcionar sem realizar qualquer migração, você precisa executar as seguintes tarefas:

- Tem de [migrar as permissões do utilizador](#user-access-and-role-migration) para o portal Azure.
- Consulte [a Migração das suas Implementações de Atualização OMS para Azure](../../automation/migrate-oms-update-deployments.md) para obter mais detalhes sobre a transição da solução de Gestão de Atualização.

Consulte [questões comuns para a transição do portal OMS para o portal Azure para os utilizadores do Log Analytics](../overview.md) para obter informações sobre como transitar para o portal Azure. 

## <a name="user-access-and-role-migration"></a>Acesso ao utilizador e migração de funções
A gestão de acesso ao portal Azure é mais rica e mais poderosa do que a gestão de acessos no Portal OMS. Consulte [o espaço de trabalho do Monitor Azure para](../logs/design-logs-deployment.md) obter detalhes sobre a gestão de acessos no Log Analytics.

> [!NOTE]
> Versões anteriores deste artigo afirmavam que as permissões seriam automaticamente convertidas do portal OMS para o portal Azure. Esta conversão automática já não está planeada, e você deve realizar a conversão por si mesmo.

Pode já ter acesso adequado no portal Azure, caso em que não precisa de fazer alterações. Existem alguns casos em que poderá não ter acesso adequado, caso em que o seu administrador deve atribuir-lhe permissões.

- Tem permissões do Utilizador ReadOnly no portal OMS, mas não tem permissões no portal Azure. 
- Tem permissões de Colaborador no portal OMS mas apenas o leitor tem acesso no portal Azure.
 
Em ambos os casos, o seu administrador precisa de lhe atribuir manualmente o papel adequado a partir da tabela seguinte. Recomendamos que atribua esta função ao nível do grupo de recursos ou da subscrição.  Em breve serão fornecidas orientações mais prescritivas para ambos os casos.

| Permissão do portal OMS | Papel de Azure |
|:---|:---|
| ReadOnly | Leitor do Log Analytics |
| Contribuinte | Contribuidor do Log Analytics |
| Administrador | Proprietário | 
 

## <a name="new-workspaces"></a>Novos espaços de trabalho
Já não é capaz de criar novos espaços de trabalho utilizando o portal OMS. Siga as orientações no [Criar um espaço de trabalho Log Analytics no portal Azure](./quick-create-workspace.md) para criar um novo espaço de trabalho no portal Azure.

## <a name="changes-to-alerts"></a>Alterações nos alertas

### <a name="alert-extension"></a>Extensão de alerta  

Alertas foram [alargados ao portal Azure](../alerts/alerts-unified-log.md) Os alertas existentes continuarão a ser listados no portal OMS, mas só é possível geri-los no portal Azure. Se aceder a alertas programáticamente utilizando o Modelo de Alerta de Alerta de Log Analytics REST ou Modelo de Alerta de Analítica, terá de utilizar grupos de ação em vez de ações nas chamadas API, nos modelos do Gestor de Recursos Azure e nos comandos PowerShell.

### <a name="alert-management-solution"></a>Solução de gestão de alerta
Como uma mudança em comparação com um anúncio anterior, a [solução de gestão de alerta](../insights/alert-management-solution.md) continuará disponível e totalmente suportada no portal Azure. Pode continuar a instalar a solução a partir do Azure Marketplace.

Enquanto a solução de gestão de alerta continua disponível, encorajamos-te a usar [a interface de alerta unificada do Azure Monitor](../alerts/alerts-overview.md) para visualizar e gerir todos os alertas em Azure. Esta nova experiência agrega alertas nativo de várias fontes dentro do Azure, incluindo alertas de registo da Log Analytics. Se estiver a utilizar a interface de alerta unificada do Azure Monitor, a solução de gestão de alerta só é necessária para permitir a integração de alertas do System Center Operation Manager para o Azure. Na interface de alerta unificada do Azure Monitor, pode ver distribuição dos seus alertas, aproveitar o agrupamento automatizado de alertas relacionados através de grupos inteligentes e ver alertas em várias subscrições enquanto aplica filtros ricos. Os futuros avanços na gestão de alertas estarão disponíveis principalmente a partir desta nova experiência. 

Os dados recolhidos pela solução de gestão de Alerta (registos com um tipo de Alerta) continuam a estar no Log Analytics desde que a solução seja instalada para o espaço de trabalho. 

## <a name="oms-mobile-app"></a>Aplicação móvel OMS
A aplicação móvel OMS será aparafusada juntamente com o portal OMS. Em vez da aplicação móvel OMS, para aceder a informações sobre a sua infraestrutura de TI, dashboards e consultas guardadas, pode aceder diretamente ao portal Azure a partir do seu navegador no seu dispositivo móvel. Para obter alertas, deve configurar [grupos de ação do Azure](../alerts/action-groups.md) para receber notificações sob a forma de SMS ou uma chamada de voz

## <a name="application-insights-connector-and-solution"></a>Conector e solução de Insights de Aplicação
[O Application Insights Connector](../logs/app-insights-connector.md) fornece uma forma de incluir os dados de Insights de Aplicação num espaço de trabalho do Log Analytics. Esta duplicação de dados foi necessária para permitir a visibilidade entre os dados das infraestruturas e das aplicações. Com o Application Insights a alargar o suporte à retenção de dados em março de 2019 e a capacidade de realizar [consultas de recursos cruzados,](../logs/cross-workspace-query.md) além de poder [visualizar vários recursos de Azure Monitor Application Insights,](./unify-app-resource-data.md)não há necessidade de duplicar os dados dos recursos da sua Aplicação Insights e enviá-lo para o Log Analytics. Além disso, o Conector envia um subconjunto das propriedades das aplicações para Log Analytics, enquanto as consultas de recursos cruzados lhe conferem uma maior flexibilidade.  

Como tal, o Application Insights Connector foi depreciado e removido do Azure Marketplace juntamente com a depreciação do portal OMS em 30 de março de 2019. As ligações existentes continuarão a funcionar até 30 de junho de 2019. Com a depreciação do portal OMS, não existe forma de configurar e remover as ligações existentes do portal. Isto será suportado através da API REST que será disponibilizada em janeiro de 2019 e será publicada uma notificação sobre [as atualizações do Azure.](https://azure.microsoft.com/updates/) 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [solução Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) será substituída pela recentemente lançada Traffic [Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) que proporciona visibilidade à atividade de utilizador e aplicação em redes cloud. A Traffic Analytics ajuda-o a auditar a atividade de rede da sua organização, a garantir aplicações e dados, a otimizar o desempenho da carga de trabalho e a manter-se em conformidade. 

Esta solução analisa os registos do NSG Flow e fornece informações sobre o seguinte.

- O tráfego flui através das suas redes entre a Azure e a Internet, regiões de nuvem pública, VNETs e sub-redes.
- Aplicações e protocolos na sua rede, sem necessidade de farejadores ou aparelhos dedicados de recolha de fluxos.
- Melhores palestrantes, aplicações tagarelas, conversas em VM na nuvem, pontos de tráfego.
- Fontes e destinos de tráfego através de VNETs, inter-relações entre serviços empresariais críticos e aplicações.
- Segurança, incluindo tráfego malicioso, portas abertas à Internet, aplicações ou VMs que tentam aceder à Internet.
- Utilização da capacidade, o que o ajuda a eliminar questões de excesso de provisão ou subutilização.

Pode continuar a confiar nas Definições de Diagnóstico para enviar registos NSG para Registar Analytics para que as pesquisas, alertas, dashboards escureçam. Os clientes que já tenham instalado a solução podem continuar a usá-la até novo aviso. A partir de 5 de setembro, a solução Network Security Group Analytics será removida do mercado e disponibilizada através da comunidade como [modelo Azure QuickStart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Se ligou o seu grupo de [gestão de Gestor de Operações ao Log Analytics,](../agents/om-agents.md)então continuará a funcionar sem alterações. No entanto, para novas ligações, deve seguir a orientação no [Microsoft System Center Operations Manager Management Pack para configurar o Suite de Gestão de Operações](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog).

## <a name="next-steps"></a>Passos seguintes
- Consulte [questões comuns para a transição do portal OMS para o portal Azure para os utilizadores](../overview.md) do Log Analytics para obter orientações sobre a mudança do portal OMS para o portal Azure.