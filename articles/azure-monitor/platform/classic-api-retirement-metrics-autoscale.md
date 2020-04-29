---
title: ApIs de implantação de aposentadoria para métricas do Monitor Azure e escala automática
description: Métricas e APIs clássicos de escala automática, também chamados de Modelo de Gestão de Serviços Azure (ASM) ou RDFE sendo aposentado
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: d9b6118bc165a9b0a78ae9c4da895719249b22b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114219"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Reforma do Azure Monitor do modelo clássico de implantação APIs para métricas e escala automática

O Azure Monitor (anteriormente Azure Insights quando foi lançado) oferece atualmente a capacidade de criar e gerir configurações de escala automática para e consumir métricas a partir de VMs clássicos e serviços clássicos de Cloud. O conjunto original de APIs clássicos baseados em modelos de implantação será retirado após 30 de junho de **2019** em todas as nuvens públicas e privadas azure em todas as regiões.   

As mesmas operações foram apoiadas através de um conjunto de APIs baseados em Recursos Azure há mais de um ano. O portal Azure utiliza as novas APIs REST tanto para autoescala como para métricas. Um novo SDK, PowerShell e CLI baseados nestes APIs do Gestor de Recursos também estão disponíveis. Os nossos serviços parceiros de monitorização consomem as novas APIs REST baseadas em Gestor de Recursos no Monitor Azure.  

## <a name="who-is-not-affected"></a>Quem não é afetado

Se estiver a gerir a escala automática através do portal Azure, os novos modelos [Azure Monitor SDK,](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)PowerShell, CLI ou Resource Manager, não é necessária qualquer ação.  

Se estiver a consumir métricas através do portal Azure ou através de vários serviços de parceiros de [monitorização,](../../azure-monitor/platform/partners.md)não é necessária qualquer ação. A Microsoft está a trabalhar com parceiros de monitorização para migrar para as novas APIs.

## <a name="who-is-affected"></a>Quem é afetado

Este artigo aplica-se a si se estiver a utilizar os seguintes componentes:

- **Classic Azure Insights SDK** - Se estiver a utilizar o [clássico Azure Insights SDK,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)mude para utilizar o novo Azure Monitor SDK para [.NET](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Descarregue o [pacote NuGet Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Escala automática clássica** - Se estiver a chamar as [configurações clássicas](https://msdn.microsoft.com/library/azure/mt348562.aspx) de escala automática APIs das suas ferramentas personalizadas ou usando o [clássico Azure Insights SDK,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)deverá mudar para utilizar o Gestor de [Recursos Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Métricas Clássicas** - Se estiver a consumir métricas utilizando as [APIs clássicas](https://msdn.microsoft.com/library/azure/dn510374.aspx) do REST ou [o clássico Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) a partir de ferramentas personalizadas, deve mudar para utilizar o Gestor de [Recursos Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Se não tem a certeza se o seu código ou ferramentas personalizadas estão a chamar as APIs clássicas, veja o seguinte:

- Reveja o URI referenciado no seu código ou ferramenta. As APIs clássicas https://management.core.windows.netusam o URI. Você deve estar usando o URI mais recente para `https://management.azure.com/`as APIs baseadas em Recursos começa com .

- Compare o nome de montagem na sua máquina. A montagem clássica https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/mais antiga está em .

- Se estiver a usar a autenticação de certificado para aceder a métricas ou APIs de escala automática, está a utilizar um ponto final clássico e uma biblioteca. As APIs do newresource Manager exigem a autenticação do Diretório Ativo Azure através de um diretor de serviço ou do utilizador principal.

- Se estiver a utilizar chamadas referenciadas na documentação em qualquer um dos seguintes links, está a utilizar as APIs clássicas mais antigas.

  - [Windows.Azure.Management.Biblioteca de classes de monitorização](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorização (clássica) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations Interface](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que deve trocar

Todas as capacidades existentes para a escala automática e métricas continuarão a funcionar através das novas APIs.  

A migração para apis mais recentes vem com capacidades baseadas em Recursos Manager, tais como suporte para controlo de acesso baseado em funções consistente (RBAC) em todos os seus serviços de monitorização. Também ganha funcionalidade adicional para métricas: 

- apoio para dimensões
- granularidade métrica consistente de 1 minuto em todos os serviços 
- melhor consulta
- maior retenção de dados (93 dias de métricas vs. 30 dias) 

No geral, como todos os outros serviços no Azure, as APIs do Monitor de Recursos baseadas em Recursos vêm com melhor desempenho, escalabilidade e fiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se não migrar

### <a name="before-retirement"></a>Antes da reforma

Não haverá qualquer impacto direto nos seus serviços Azure ou nas suas cargas de trabalho.  

### <a name="after-retirement"></a>Depois da reforma

Quaisquer chamadas para as APIs clássicas listadas anteriormente falharão e devolverão mensagens de erro semelhantes às seguintes:

Para a escala automática: *Esta API foi depreciada. Utilize os modelos do portal Azure, Azure Monitor SDK, PowerShell, CLI ou Resource Manager para gerir as Definições*de Escala Automática .  

Para métricas: *Esta API foi depreciada. Utilize o portal Azure, Azure Monitor SDK, PowerShell, CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por e-mail

Foi enviada uma notificação de aposentadoria para endereços de e-mail para as seguintes funções de conta: 

- Administradores de conta e de serviços
- Coadministradores  

Se tiver alguma dúvida, MonitorClassicAPIhelp@microsoft.comcontacte-nos em .  

## <a name="references"></a>Referências

- [APIs de repouso mais recentes para o Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) 
- [Newer Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
