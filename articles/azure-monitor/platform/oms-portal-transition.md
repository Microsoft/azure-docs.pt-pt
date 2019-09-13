---
title: Portal do OMS migrando para o Azure | Microsoft Docs
description: O portal do OMS está sendo coincluído com todas as funcionalidades que se movem para a portal do Azure. Este artigo fornece detalhes sobre essa transição.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: bwren
ms.openlocfilehash: e8fd49781301406e0c35e1de57cea3040167c6c3
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915872"
---
# <a name="oms-portal-moving-to-azure"></a>Portal do OMS migrando para o Azure

> [!NOTE]
> Este artigo se aplica à nuvem pública do Azure e à nuvem governamental, exceto onde indicado de outra forma.

**O portal do OMS para a nuvem pública do Azure foi oficialmente desativado. O portal do OMS para a nuvem do governo dos EUA do Azure foi oficialmente desativado em 15 de maio de 2019.** Estamos empolgados para migrar para a portal do Azure e esperar que a transição seja fácil. Mas entendemos que as alterações são difíceis e podem causar interrupções. O restante deste artigo vai além dos principais cenários e do roteiro para essa transição.

O portal do Azure é o Hub de todos os serviços do Azure e oferece uma experiência de gerenciamento avançada com recursos como painéis para fixação de recursos, pesquisa inteligente para localizar recursos e marcação para o gerenciamento de recursos. Para consolidar e simplificar o fluxo de trabalho de monitoramento e gerenciamento, começamos a adicionar os recursos do portal do OMS à portal do Azure. Todos os recursos do portal do OMS agora fazem parte do portal do Azure. Na verdade, alguns dos novos recursos, como Análise de Tráfego, só estão disponíveis no portal do Azure. Você poderá realizar tudo o que estava fazendo no portal do OMS com o portal do Azure e muito mais. Se você ainda não fez isso, comece a usar o portal do Azure hoje mesmo!

## <a name="what-is-changing"></a>O que está mudando? 
As seguintes alterações estão sendo anunciadas com a reprovação do portal do OMS. Cada uma dessas alterações é descrita mais detalhadamente nas seções a seguir.

- Você pode criar novos [espaços de trabalho somente](#new-workspaces) no portal do Azure.
- A nova experiência de gerenciamento [de alertas substitui a solução gerenciamento de alertas](#changes-to-alerts).
- O [Gerenciamento de acesso do usuário](#user-access-and-role-migration) agora é feito no portal do Azure usando o controle de acesso baseado em função do Azure.
- O [conector do Application insights não é mais necessário](#application-insights-connector-and-solution) , pois a mesma funcionalidade é habilitada por meio de consultas entre espaços de trabalho.
- O [aplicativo móvel do OMS](#oms-mobile-app) está sendo preterido. 
- A [solução NSG está sendo substituída](#azure-network-security-group-analytics) pela funcionalidade avançada disponível por meio da solução análise de tráfego.
- Novas conexões de System Center Operations Manager para Log Analytics exigem [pacotes de gerenciamento atualizados](#system-center-operations-manager).
- Consulte [migrar suas implantações de atualização do OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter detalhes sobre as alterações no [Gerenciamento de atualizações](../../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>O que devo fazer agora?
Embora a maioria dos recursos continue a funcionar sem executar nenhuma migração, você precisa executar as seguintes tarefas:

- Você precisa [migrar suas permissões de usuário](#user-access-and-role-migration) para o portal do Azure.
- Consulte [migrar suas implantações de atualização do OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter detalhes sobre como fazer a transição da solução de gerenciamento de atualizações.

Consulte as [perguntas comuns para a transição do portal do OMS para Portal do Azure para log Analytics usuários](oms-portal-faq.md) para obter informações sobre como fazer a transição para o portal do Azure. 

## <a name="user-access-and-role-migration"></a>Acesso de usuário e migração de função
O gerenciamento de acesso portal do Azure é mais rico e mais potente do que o gerenciamento de acesso no portal do OMS. Consulte [criando seu espaço de trabalho de logs de Azure monitor](design-logs-deployment.md) para obter detalhes de gerenciamento de acesso no log Analytics.

> [!NOTE]
> As versões anteriores deste artigo declararam que as permissões seriam automaticamente convertidas do portal do OMS para o portal do Azure. Essa conversão automática não está mais planejada e você deve executar a conversão por conta própria.

Talvez você já tenha acesso apropriado na portal do Azure, caso em que você não precisa fazer nenhuma alteração. Há alguns casos em que você pode não ter o acesso apropriado, caso em que o administrador deve atribuir permissões.

- Você tem permissões de usuário somente leitura no portal do OMS, mas não há permissões no portal do Azure. 
- Você tem permissões de colaborador no portal do OMS, mas somente acesso de leitor no portal do Azure.
 
Em ambos os casos, o administrador precisa atribuir manualmente a você a função apropriada da tabela a seguir. Recomendamos que você atribua essa função no nível do grupo de recursos ou da assinatura.  Mais diretrizes prescritivas serão fornecidas em breve para esses dois casos.

| Permissão do portal do OMS | Função do Azure |
|:---|:---|
| ReadOnly | Leitor do Log Analytics |
| Contribuinte | Contribuidor do Log Analytics |
| Admistrador | Owner | 
 

## <a name="new-workspaces"></a>Novos espaços de trabalho
Você não poderá mais criar novos espaços de trabalho usando o portal do OMS. Siga as orientações em [criar um log Analytics espaço de trabalho no portal do Azure](../learn/quick-create-workspace.md) para criar um novo espaço de trabalho no portal do Azure.

## <a name="changes-to-alerts"></a>Alterações em alertas

### <a name="alert-extension"></a>Extensão de alerta  

Os alertas foram [estendidos para o portal do Azure](alerts-extend.md) alertas existentes continuarão a ser listados no portal do OMS, mas você só pode gerenciá-los no portal do Azure. Se você acessar alertas programaticamente usando o Log Analytics API REST de alerta ou Log Analytics modelo de recurso de alerta, você precisará usar grupos de ação em vez de ações em suas chamadas à API, modelos de Azure Resource Manager e comandos do PowerShell.

### <a name="alert-management-solution"></a>Solução de gerenciamento de alertas
Como uma alteração de um anúncio anterior, a [solução de gerenciamento de alertas](alert-management-solution.md) continuará disponível e terá suporte total na portal do Azure. Você pode continuar a instalar a solução do Azure Marketplace.

Enquanto a solução de gerenciamento de alertas continua disponível, incentivamos você a usar a [interface de alerta unificada do Azure monitor](alerts-overview.md) para visualizar e gerenciar todos os alertas no Azure. Essa nova experiência agrega nativamente alertas de várias fontes no Azure, incluindo alertas de log de Log Analytics. Se você estiver usando a interface de alerta unificada do Azure Monitor, a solução de gerenciamento de alertas só será necessária para habilitar a integração de alertas do System Center Operations Manager com o Azure. Na interface de alerta unificada do Azure Monitor, você pode ver as distribuições de seus alertas, aproveitar o agrupamento automatizado de alertas relacionados por meio de grupos inteligentes e exibir alertas em várias assinaturas ao aplicar filtros avançados. Futuros avanços no gerenciamento de alertas estarão disponíveis principalmente nessa nova experiência. 

Os dados coletados pela solução de gerenciamento de alertas (registros com um tipo de alerta) continuam em Log Analytics contanto que a solução esteja instalada para o espaço de trabalho. 

## <a name="oms-mobile-app"></a>Aplicativo móvel do OMS
O aplicativo móvel do OMS será coconjunto com o portal do OMS. Em vez do aplicativo móvel do OMS, para acessar informações sobre sua infraestrutura de ti, painéis e consultas salvas, você pode acessar o portal do Azure diretamente do seu navegador em seu dispositivo móvel. Para obter alertas, você deve configurar [grupos de ações do Azure](action-groups.md) para receber notificações na forma de SMS ou uma chamada de voz

## <a name="application-insights-connector-and-solution"></a>Conector do Application Insights e solução
[Conector do Application insights](app-insights-connector.md) fornece uma maneira de incluir Application insights dados em um espaço de trabalho log Analytics. Essa duplicação de dados era necessária para habilitar a visibilidade na infraestrutura e nos dados do aplicativo. Com Application Insights suporte estendido à retenção de dados em março de 2019 e a capacidade de executar [consultas entre recursos](../log-query/cross-workspace-query.md) , além de poder [exibir vários recursos de Application insights de Azure monitor](../log-query/unify-app-resource-data.md), não há necessidade de duplicar dados de seus recursos de Application Insights e enviá-los para Log Analytics. Além disso, o conector envia um subconjunto das propriedades de aplicativos para Log Analytics, enquanto as consultas de recurso cruzado fornecem flexibilidade aprimorada.  

Dessa forma, Conector do Application Insights foi preterida e removida do Azure Marketplace junto com a substituição do portal do OMS em 30 de março de 2019. As conexões existentes continuarão a funcionar até 30 de junho de 2019. Com a substituição do portal do OMS, não há como configurar e remover conexões existentes do Portal. Isso terá suporte usando a API REST que será disponibilizada em janeiro de 2019 e uma notificação será lançada nas [atualizações do Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Análise do Grupo de Segurança de Rede do Azure
A [solução de análise do grupo de segurança de rede do Azure](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) será substituída pelo [análise de tráfego](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) lançado recentemente, que fornece visibilidade da atividade do usuário e do aplicativo em redes de nuvem. Análise de Tráfego ajuda a auditar a atividade de rede de sua organização, proteger aplicativos e dados, otimizar o desempenho da carga de trabalho e manter a conformidade. 

Essa solução analisa os logs de fluxo do NSG e fornece informações sobre o seguinte.

- O tráfego flui em suas redes entre o Azure e a Internet, regiões de nuvem pública, VNETs e sub-redes.
- Aplicativos e protocolos em sua rede, sem a necessidade de sniffers ou de dispositivos de coleção de fluxo dedicados.
- Os principais palestrantes, aplicativos informativos, conversas de VM na nuvem, hotspots de tráfego.
- Origens e destinos de tráfego entre VNETs, entre os serviços comerciais e os aplicativos críticos.
- Segurança, incluindo tráfego mal-intencionado, portas abertas para a Internet, aplicativos ou VMs que tentam acessar a Internet.
- Utilização da capacidade, que ajuda a eliminar problemas de provisionamento ou subutilização.

Você pode continuar a contar com as configurações de diagnóstico para enviar logs do NSG para Log Analytics para que suas pesquisas salvas, alertas, painéis continuem a funcionar. Os clientes que já instalaram a solução podem continuar a usá-lo até um aviso adicional. A partir de 5 de setembro, a solução de análise do grupo de segurança de rede será removida do Marketplace e disponibilizada por meio da Comunidade como um [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Se você [conectou seu grupo de gerenciamento de Operations Manager ao log Analytics](om-agents.md), ele continuará funcionando sem alterações. Para novas conexões, no entanto, você deve seguir as diretrizes no [pacote de gerenciamento do Microsoft System Center Operations Manager para configurar o Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Passos seguintes
- Consulte [perguntas comuns para a transição do portal do OMS para Portal do Azure para log Analytics usuários](oms-portal-faq.md) para obter diretrizes sobre como migrar do portal do OMS para o portal do Azure.
