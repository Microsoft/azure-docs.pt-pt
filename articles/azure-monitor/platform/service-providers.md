---
title: Azure Monitor para provedores de serviço | Microsoft Docs
description: Azure Monitor pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, ISVs (fornecedores independentes de software) e provedores de serviços de hospedagem a gerenciar e monitorar servidores na infraestrutura local ou na nuvem do cliente.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932064"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor para provedores de serviço
Os espaços de trabalho Log Analytics no Azure Monitor podem ajudar provedores de serviços gerenciados (MSPs), grandes empresas, ISVs (fornecedores independentes de software) e provedores de serviços de hospedagem a gerenciar e monitorar servidores na infraestrutura local ou de nuvem do cliente. 

Grandes empresas compartilham muitas semelhanças com provedores de serviços, especialmente quando há uma equipe de ti centralizada que é responsável por gerenciá-la para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor de serviços* , mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e provedores de serviços que fazem parte do programa [CSP (provedor de soluções na nuvem)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analytics em Azure monitor é um dos serviços do Azure disponíveis nas [assinaturas do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arquiteturas para provedores de serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos dados de [log](data-platform-logs.md) e criar uma arquitetura que atenda às suas necessidades de negócios específicas. [Este artigo](design-logs-deployment.md) explica as considerações de design, implantação e migração para um espaço de trabalho e o artigo [gerenciar acesso](manage-access.md) discute como aplicar e gerenciar permissões para dados de log. Os provedores de serviço têm considerações adicionais.

Há três arquiteturas possíveis para provedores de serviços em relação aos espaços de trabalho do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. os logs distribuídos são armazenados em espaços de trabalho localizados no locatário do cliente 

Nessa arquitetura, um espaço de trabalho é implantado no locatário do cliente que é usado para todos os logs desse cliente. Os administradores do provedor de serviços recebem acesso a esse espaço de trabalho usando [Azure Active Directory usuários convidados (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Os administradores do provedor de serviços terão que alternar para o diretório do cliente no portal do Azure para poder acessar esses espaços de trabalho.

As vantagens dessa arquitetura são:
* O cliente pode gerenciar o acesso aos logs usando seu próprio [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Cada cliente pode ter configurações diferentes para seu espaço de trabalho, como retenção e data capping.
* Isolamento entre clientes para regulamentação e conformidade.
* A cobrança de cada espaço de trabalho será transferida para a assinatura do cliente.
* Os logs podem ser coletados de todos os tipos de recursos, não apenas baseados em agente. Por exemplo, logs de auditoria do Azure.

As desvantagens dessa arquitetura são:
* É mais difícil para o provedor de serviços gerenciar um grande número de locatários de clientes ao mesmo tempo.
* Os administradores do provedor de serviços precisam ser provisionados no diretório do cliente.
* O provedor de serviços não pode analisar dados entre seus clientes.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. central-os logs são armazenados em um espaço de trabalho localizado no locatário do provedor de serviço

Nessa arquitetura, os logs não são armazenados nos locatários do cliente, mas apenas em um local central dentro de uma das assinaturas do provedor de serviços. Os agentes instalados nas VMs do cliente são configurados para enviar seus logs para esse espaço de trabalho usando a ID do espaço de trabalho e a chave secreta.

As vantagens dessa arquitetura são:
* É fácil gerenciar um grande número de clientes e integrá-los a vários sistemas de back-end.

* O provedor de serviços tem Propriedade total sobre os logs e os vários artefatos, como funções e consultas salvas.

* O provedor de serviços pode executar análises em todos os seus clientes.

As desvantagens dessa arquitetura são:
* Essa arquitetura é aplicável apenas para dados de VM baseados em agente, ela não abrangerá as fontes de dados PaaS, SaaS e Fabric do Azure.

* Pode ser difícil separar os dados entre os clientes quando eles são mesclados em um único espaço de trabalho. O único método ideal para fazer isso é usar o FQDN (nome de domínio totalmente qualificado) do computador ou por meio da ID de assinatura do Azure. 

* Todos os dados de todos os clientes serão armazenados na mesma região com uma única lista e as mesmas configurações de retenção e configuração.

* Os serviços de malha e PaaS do Azure, como Diagnóstico do Azure e logs de auditoria do Azure, exigem que o espaço de trabalho esteja no mesmo locatário que o recurso, portanto, eles não podem enviar os logs para o espaço de trabalho central.

* Todos os agentes de VM de todos os clientes serão autenticados no espaço de trabalho central usando a mesma ID e chave do espaço de trabalho. Não há nenhum método para bloquear logs de um cliente específico sem interromper outros clientes.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. os logs híbridos são armazenados no espaço de trabalho localizado no locatário do cliente e alguns deles são retirados para um local central.

A terceira arquitetura é combinada entre as duas opções. Ele se baseia na primeira arquitetura distribuída em que os logs são locais para cada cliente, mas usando algum mecanismo para criar um repositório central de logs. Uma parte dos logs é obtida em um local central para relatórios e análises. Essa parte pode ser um pequeno número de tipos de dados ou um resumo da atividade, como estatísticas diárias.

Há duas opções para implementar logs em um local central:

1. Espaço de trabalho central: o provedor de serviços pode criar um espaço de trabalho em seu locatário e usar um script que utiliza a [API de consulta](https://dev.loganalytics.io/) com a API de [coleta de dados](../../azure-monitor/platform/data-collector-api.md) para trazer os dados dos vários espaços de trabalho para esse local central. Outra opção, além de um script, é usar os [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI como um local central: Power BI pode atuar como o local central quando os vários espaços de trabalho exportam dados para ele usando a integração entre o espaço de trabalho Log Analytics e [Power bi](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Passos seguintes

* Automatizar a criação e configuração de espaços de trabalho usando [modelos do Resource Manager](template-workspace-configuration.md)

* Automatizar a criação de espaços de trabalho usando o [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Usar [alertas](../../azure-monitor/platform/alerts-overview.md) para integração com sistemas existentes

* Gerar relatórios de resumo usando [Power bi](../../azure-monitor/platform/powerbi.md)

* Examine o processo de [configuração de log Analytics e Power bi para monitorar vários clientes CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
