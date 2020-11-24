---
title: APIs de implantação de aposentadoria para métricas do Monitor Azure e autoescalação
description: Métricas e APIs clássicos de autoescalação, também chamados de Azure Service Management (ASM) ou modelo de implantação RDFE sendo aposentado
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536698"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor reforma do modelo de implantação clássico APIs para métricas e autoescala

O Azure Monitor (anteriormente Azure Insights quando foi lançado pela primeira vez) oferece atualmente a capacidade de criar e gerir configurações de autoescala para e consumir métricas de VMs clássicos e serviços cloud clássicos. O conjunto original de APIs de implementação clássica baseada em modelos será **retirado após 30 de junho de 2019** em todas as nuvens públicas e privadas de Azure em todas as regiões.   

As mesmas operações têm sido suportadas através de um conjunto de APIs baseadas em recursos Azure há mais de um ano. O portal Azure utiliza as novas APIs REST tanto para autoescalagens como para métricas. Estão também disponíveis novos SDK, PowerShell e CLI com base nestas APIs do Gestor de Recursos. Os nossos serviços de parceiros para monitorização consomem as novas APIs de REST baseadas em Recursos no Azure Monitor.  

## <a name="who-is-not-affected"></a>Quem não é afetado

Se estiver a gerir a autoescalação através do portal Azure, os novos modelos [Azure Monitor SDK,](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)PowerShell, CLI ou Resource Manager, não é necessária qualquer ação.  

Se estiver a consumir métricas através do portal Azure ou através de vários [serviços de parceiros de monitorização,](./partners.md)não é necessária qualquer ação. A Microsoft está a trabalhar com parceiros de monitorização para migrar para as novas APIs.

## <a name="who-is-affected"></a>Quem é afetado

Este artigo aplica-se a si se estiver a utilizar os seguintes componentes:

- **Classic Azure Insights SDK** - Se estiver a utilizar o [clássico Azure Insights SDK,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)mude para utilizar o novo Azure Monitor SDK para [.NET](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Descarregue o [pacote Azure Monitor SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Classic Autoscale** - Se estiver a chamar as [definições clássicas de autoescala APIs](/previous-versions/azure/reference/mt348562(v=azure.100)) das suas ferramentas personalizadas ou utilizando o [clássico Azure Insights SDK,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)deverá mudar para o uso do [Gestor de Recursos Azure Monitor REST API](/rest/api/monitor/autoscalesettings).

- **Métricas Clássicas** - Se estiver a consumir métricas utilizando as [APIs clássicas de REST](/previous-versions/azure/reference/dn510374(v=azure.100)) ou  [o clássico Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) a partir de ferramentas personalizadas, deverá mudar para o recurso [Azure Monitor REST API](/rest/api/monitor/autoscalesettings). 

Se não tiver a certeza se o seu código ou ferramentas personalizadas estão a chamar as APIs clássicas, veja o seguinte:

- Reveja o URI referenciado no seu código ou ferramenta. As APIs clássicas usam o https://management.core.windows.net URI. Deve utilizar o URI mais recente para as APIs baseadas em `https://management.azure.com/` recursos.

- Compare o nome de montagem na sua máquina. A montagem clássica mais antiga está em  https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ .

- Se estiver a utilizar a autenticação de certificados para aceder a métricas ou APIs de autoescalação, está a utilizar um ponto final clássico e uma biblioteca. As NOVAS APIs do Gestor de Recursos requerem a autenticação do Azure Ative Directory através de um diretor de serviço ou de um responsável pelo utilizador.

- Se estiver a utilizar chamadas referenciadas na documentação em qualquer um dos seguintes links, está a utilizar as APIs clássicas mais antigas.

  - [Windows.Azure.Management.Monitoring Class Library](/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorização (clássica) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations Interface](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que deve trocar

Todas as capacidades existentes para autoescalação e métricas continuarão a funcionar através das novas APIs.  

A migração para novas APIs vem com capacidades baseadas em Gestores de Recursos, tais como suporte para um consistente controlo de acesso baseado em funções Azure (Azure RBAC) em todos os seus serviços de monitorização. Também ganha funcionalidade adicional para métricas: 

- apoio a dimensões
- granularidade métrica consistente de 1 minuto em todos os serviços 
- melhor consulta
- maior retenção de dados (93 dias de métricas vs. 30 dias) 

No geral, como todos os outros serviços em Azure, o Gestor de Recursos Azure Monitor APIs vem com melhor desempenho, escalabilidade e fiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se não migrar

### <a name="before-retirement"></a>Antes da reforma

Não haverá qualquer impacto direto nos seus serviços Azure ou nas suas cargas de trabalho.  

### <a name="after-retirement"></a>Depois da reforma

Quaisquer chamadas para as APIs clássicas listadas anteriormente falharão e devolverão mensagens de erro semelhantes às seguintes:

Para uma escala automática: *Esta API foi depreciada. Utilize os modelos Azure Monitor SDK, PowerShell, CLI ou Resource Manager para gerir configurações de autoescalação*.  

Para métricas: *Esta API foi depreciada. Utilize o portal Azure, Azure Monitor SDK, PowerShell, CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por e-mail

Foi enviada uma notificação de aposentadoria para endereços de e-mail para as seguintes funções de conta: 

- Administradores de conta e serviços
- Coadministradores  

Se tiver alguma dúvida, contacte-nos em MonitorClassicAPIhelp@microsoft.com .  

## <a name="references"></a>Referências

- [Novas APIs REST para Monitor Azure](/rest/api/monitor/) 
- [Newer Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

