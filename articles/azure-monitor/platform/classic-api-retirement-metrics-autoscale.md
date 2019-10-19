---
title: Azure Monitor desativação de APIs de modelo de implantação clássica para métricas e dimensionamento automático
description: As métricas e as APIs clássicas de dimensionamento automático, também chamadas de gerenciamento de serviços do Azure (ASM) ou modelo de implantação RDFE sendo desativado
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 11/19/2018
ms.openlocfilehash: 7a93419ee84e6a50ce07cefa941a8df9f85b7b6e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552208"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor desativação de APIs de modelo de implantação clássica para métricas e dimensionamento automático

O Azure Monitor (anteriormente conhecido como Azure insights quando lançado) atualmente oferece a capacidade de criar e gerenciar configurações de dimensionamento automático para e consumir métricas de VMs clássicas e serviços de nuvem clássicos. O conjunto original de APIs baseadas no modelo de implantação clássica será **desativado após 30 de junho de 2019** em todas as nuvens públicas e privadas do Azure em todas as regiões.   

As mesmas operações têm suporte por meio de um conjunto de APIs baseadas em Azure Resource Manager por mais de um ano. O portal do Azure usa as novas APIs REST para dimensionamento automático e métricas. Um novo SDK, PowerShell e CLI com base nessas APIs do Resource Manager também estão disponíveis. Nossos serviços de parceiros para monitoramento consomem as novas APIs REST baseadas no Resource Manager no Azure Monitor.  

## <a name="who-is-not-affected"></a>Quem não é afetado

Se você estiver gerenciando o dimensionamento automático por meio do portal do Azure, o [novo SDK do Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, CLI ou modelos do Resource Manager, nenhuma ação será necessária.  

Se você estiver consumindo métricas por meio do portal do Azure ou por meio de vários [serviços de parceiros de monitoramento](../../azure-monitor/platform/partners.md), nenhuma ação será necessária. A Microsoft está trabalhando com parceiros de monitoramento para migrar para as novas APIs.

## <a name="who-is-affected"></a>Quem é afetado

Este artigo se aplica a você se você estiver usando os seguintes componentes:

- **SDK do Azure insights clássico** -se você estiver usando [o SDK clássico do Azure insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), mude para usando o novo SDK Azure monitor para [.net](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Baixe o [pacote NuGet do SDK Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Autoescala clássica** -se você estiver chamando as [APIs de configurações de autoescala clássicas](https://msdn.microsoft.com/library/azure/mt348562.aspx) de suas ferramentas personalizadas ou usando o [SDK do Azure insights clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), você deverá alternar para usando o [Gerenciador de recursos Azure monitor API REST](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Métricas clássicas** -se você estiver consumindo métricas usando as [APIs REST clássicas](https://msdn.microsoft.com/library/azure/dn510374.aspx) ou o [SDK do Azure insights clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) de ferramentas personalizadas, você deverá alternar para o uso da [API REST do Gerenciador de recursos Azure monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Se você não tiver certeza se seu código ou suas ferramentas personalizadas estão chamando as APIs clássicas, examine o seguinte:

- Examine o URI referenciado em seu código ou ferramenta. As APIs clássicas usam o URI https://management.core.windows.net. Você deve usar o URI mais recente para as APIs baseadas no Resource Manager começar com https://management.azure.com/.

- Compare o nome do assembly em seu computador. O assembly clássico mais antigo está em https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Se você estiver usando a autenticação de certificado para acessar métricas ou APIs de dimensionamento automático, você está usando um ponto de extremidade clássico e uma biblioteca. As APIs mais recentes do Resource Manager exigem autenticação Azure Active Directory por meio de uma entidade de serviço ou entidade de usuário.

- Se você estiver usando chamadas referenciadas na documentação em qualquer um dos links a seguir, você está usando as APIs clássicas mais antigas.

  - [Biblioteca de classes Windows. Azure. Management. Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitoramento (clássico) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interface IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que você deve alternar

Todos os recursos existentes para dimensionamento automático e métricas continuarão a funcionar por meio das novas APIs.  

Migrar para APIs mais recentes é fornecido com recursos baseados no Resource Manager, como suporte para RBAC (controle de acesso baseado em função) consistente em todos os seus serviços de monitoramento. Você também obterá funcionalidade adicional para métricas: 

- suporte para dimensões
- granularidade de métrica de 1 minuto consistente em todos os serviços 
- consulta melhor
- maior retenção de dados (93 dias de métricas versus 30 dias) 

Em geral, como todos os outros serviços no Azure, o Resource Manager baseado Azure Monitor APIs vem com melhor desempenho, escalabilidade e confiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se você não migrar

### <a name="before-retirement"></a>Antes da aposentadoria

Não haverá nenhum impacto direto para os serviços do Azure ou suas cargas de trabalho.  

### <a name="after-retirement"></a>Após a aposentadoria

Todas as chamadas para as APIs clássicas listadas anteriormente falharão e retornarão mensagens de erro semelhantes às seguintes:

Para dimensionamento automático: *essa API foi preterida. Use os modelos portal do Azure, SDK do Azure Monitor, PowerShell, CLI ou Resource Manager para gerenciar as configurações de dimensionamento automático*.  

Para métricas: *essa API foi preterida. Use o portal do Azure, o SDK do Azure Monitor, o PowerShell, a CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por e-mail

Uma notificação de aposentadoria foi enviada para endereços de email para as seguintes funções de conta: 

- Administradores de contas e serviços
- Coadministradores  

Se você tiver dúvidas, entre em contato conosco em MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referências

- [APIs REST mais recentes para Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [SDK de Azure Monitor mais recente](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
