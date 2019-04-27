---
title: Azure Monitor extinção do modelo de implementação clássica APIs para métricas e dimensionamento automático
description: Métricas e dimensionamento automático APIs clássicas, também denominada gestão de serviço do Azure (ASM) ou o modelo de implementação de RDFE vão ser descontinuados
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709988"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor extinção do modelo de implementação clássica APIs para métricas e dimensionamento automático

O Azure Monitor (anteriormente conhecido como Azure Insights quando lançado pela primeira vez) atualmente oferece a capacidade de criar e gerir as definições de dimensionamento automático e consumir as métricas de VMs clássicas e de serviços de Cloud clássico. O conjunto original de implementação clássica baseado em modelo APIs serão **retirado após 30 de Junho de 2019** em todas as clouds do Azure públicas e privadas em todas as regiões.   

As mesmas operações já são suportadas através de um conjunto do Azure Resource Manager com base em APIs para mais de um ano. O portal do Azure utiliza as novas APIs de REST para dimensionamento automático e métricas. Um novo SDK, o PowerShell e CLI com base nessas APIs do Resource Manager também estão disponíveis. Os nossos serviços de parceiros para monitorização de consumam o novo Gestor de recursos com base em APIs REST no Azure Monitor.  

## <a name="who-is-not-affected"></a>Que não é afetado

Se estiver a gerir o dimensionamento automático através do portal do Azure, o [novo SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), modelos do PowerShell, CLI ou do Resource Manager, é necessária nenhuma ação.  

Se está a consumir métricas através do portal do Azure ou diversos [monitorizar os serviços de parceiro](../../azure-monitor/platform/partners.md), é necessária nenhuma ação. Microsoft está trabalhando com parceiros para migrar para as novas APIs de monitorização.

## <a name="who-is-affected"></a>Quem é afetada

Este artigo aplica-se para se estiver a utilizar os seguintes componentes:

- **Clássico do Azure Insights SDK** – se estiver a utilizar o [clássico do Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), mude para utilizar o novo SDK do Azure Monitor para [.NET](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Transfira o [pacote NuGet do SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Dimensionamento automático clássico** – se estiver chamando o [definições de dimensionamento automático clássico APIs](https://msdn.microsoft.com/library/azure/mt348562.aspx) a partir das suas ferramentas personalizadas ou utilizar o [clássico do Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), deve passar a utilizar o [ API de REST do Gestor de recursos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Métricas clássicas** - se de que estamos consumindo métricas usando o [APIs de REST clássico](https://msdn.microsoft.com/library/azure/dn510374.aspx) ou [clássico do Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) a partir das ferramentas personalizadas, deve passar a utilizar o [ API de REST do Gestor de recursos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Se tiver certeza se seu código ou ferramentas personalizadas são chamar as APIs de clássicas, veja o seguinte:

- Reveja o URI referenciado no seu código ou a ferramenta. As APIs clássicas utilizam o URI https://management.core.windows.net. Deve usar o URI de mais recente para o Resource Manager com base APIs começa com https://management.azure.com/.

- Compare o nome do assembly no seu computador. O assembly clássico mais antigo é em https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Se estiver a utilizar autenticação de certificados para aceder a métricas ou de dimensionamento automático APIs, está a utilizar um ponto de extremidade clássico e a biblioteca. As mais recentes APIs do Resource Manager exigir a autenticação do Azure Active Directory através de um principal de serviço ou o principal de utilizador.

- Se estiver usando chamadas referenciadas na documentação em qualquer um dos links a seguir, está a utilizar as APIs clássicas mais antigas.

  - [Biblioteca de classes de Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorização de .NET (clássico)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interface de IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que motivo deve mudar

Todas as capacidades existentes para o dimensionamento automático e de métricas irão continuar a funcionar através de novas APIs.  

Migrar através de mais recente para APIs são fornecidos com capacidades de baseados no Resource Manager, como suporte consistente baseado em funções para controlo de acesso (RBAC) em todos os serviços de monitorização. Também ganha funcionalidade adicional para métricas: 

- suporte para dimensões
- consistente com granularidade de métrica de 1 minuto em todos os serviços 
- consultar o melhor
- retenção de dados superior (93 dias de métricas vs. 30 dias) 

No geral, como todos os outros serviços do Azure, o Gestor de recursos com base do Azure Monitor APIs são fornecidos com melhor desempenho, escalabilidade e confiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se não migrar

### <a name="before-retirement"></a>Antes de extinção

Não haverá qualquer impacto direto para seus serviços do Azure ou as cargas de trabalho.  

### <a name="after-retirement"></a>Depois de ser retirado

Todas as chamadas para clássica que APIs listadas anteriormente falharão e devolver mensagens de erro semelhante dos seguintes:

Do dimensionamento automático: *Esta API foi preterida. Utilizar o portal do Azure, modelos de SDK do Monitor do Azure, PowerShell, CLI ou do Resource Manager para gerir definições de dimensionamento automático*.  

Para métricas: *Esta API foi preterida. Utilize o portal do Azure, SDK do Azure Monitor, PowerShell, CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por e-mail

Foi enviada uma notificação de extinção para endereços para as seguintes funções de conta de e-mail: 

- Administradores de conta e de serviço
- Coadministradores  

Se tiver alguma questão, contacte-nos em MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referências

- [APIs REST mais recente para o Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Monitor do Azure mais recente SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
