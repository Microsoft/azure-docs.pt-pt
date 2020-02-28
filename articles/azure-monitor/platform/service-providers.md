---
title: Registos de Monitores Azure para Prestadores de Serviços  Microsoft Docs
description: Os Registos do Monitor Azure podem ajudar os Fornecedores de Serviços Geridos (MSPs), grandes empresas, Fornecedores de Software Independentes (ISVs) e fornecedores de serviços de hospedagem a gerir e monitorizar servidores nas instalações ou na infraestrutura de nuvem do cliente.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658885"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Registos de Monitores Azure para prestadores de serviços

Os espaços de trabalho do Log Analytics no Azure Monitor podem ajudar fornecedores de serviços geridos (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e fornecedores de serviços de hospedagem gerir e monitorizar servidores nas instalações ou na infraestrutura de nuvem do cliente.

As grandes empresas compartilham diversas semelhanças com fornecedores de serviços, especialmente quando existe uma equipa de TI centralizada, que é responsável por gerenciar IT para muitas unidades de negócios diferentes. Para a simplicidade, este documento utiliza o prestador de *serviços* a prazo, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e prestadores de serviços que fazem parte do programa [Cloud Solution Provider (CSP),](https://partner.microsoft.com/Solutions/cloud-reseller-overview) o Log Analytics no Azure Monitor é um dos serviços Azure disponíveis nas subscrições do Azure CSP.

O Log Analytics no Azure Monitor também pode ser utilizado por um prestador de serviços que gere os recursos dos clientes através da capacidade de gestão de recursos delegado do Azure no [Farol Azure.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="architectures-for-service-providers"></a>Arquiteturas para fornecedores de serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e isolamento de dados de [registo](data-platform-logs.md) e criar uma arquitetura que responda às suas necessidades específicas de negócio. [Este artigo](design-logs-deployment.md) explica as considerações de design, implantação e migração para um espaço de trabalho, e o artigo de [acesso de gestão](manage-access.md) discute como aplicar e gerir permissões para registar dados. Fornecedores de serviços tem considerações adicionais.

Existem três arquiteturas possíveis para fornecedores de serviços em relação a áreas de trabalho do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuídos - Os registos são armazenados em espaços de trabalho localizados no inquilino do cliente

Nesta arquitetura, uma área de trabalho é implementada no inquilino do cliente que é utilizado para todos os registos de cliente.

Existem duas formas de os administradores de prestadores de serviços poderem ter acesso a um espaço de trabalho do Log Analytics num inquilino de um cliente:

- Um cliente pode adicionar utilizadores individuais ao prestador de serviços como utilizadores convidados do [Azure Ative Directory (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Os administradores do prestador de serviços terão de subscrever o diretório de cada cliente no portal Azure para poderem aceder a estes espaços de trabalho. Isto também requer que os clientes gerem o acesso individual de cada administrador de prestador de serviços.
- Para uma maior escalabilidade e flexibilidade, os prestadores de serviços podem utilizar a capacidade de [gestão](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) de recursos delegado do [Azure Para](https://docs.microsoft.com/azure/lighthouse/overview) aceder ao inquilino do cliente. Com este método, os administradores do prestador de serviços estão incluídos num grupo de utilizadores da Azure AD no inquilino do prestador de serviços, sendo que este grupo tem acesso durante o processo de embarque para cada cliente. Estes administradores podem então aceder aos espaços de trabalho de cada cliente a partir do seu próprio fornecedor de serviços, em vez de terem de iniciar sessão individualmente no inquilino de cada cliente. Aceder aos recursos de workspaces do Log Analytics dos seus clientes desta forma reduz o trabalho exigido do lado do cliente, e pode facilitar a recolha e análise de dados em vários clientes geridos pelo mesmo prestador de serviços através de ferramentas como o [Azure Monitor Workbooks.](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview) Para mais informações, consulte [monitor recursos do cliente em escala](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

As vantagens da arquitetura distribuída são:

* O cliente pode confirmar níveis específicos de permissões através da [gestão de recursos delegados do Azure,](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)ou pode gerir o acesso aos registos utilizando o seu próprio [acesso baseado em papéis.](https://docs.microsoft.com/azure/role-based-access-control/overview)
* Os registos podem ser recolhidos de todos os tipos de recursos, e não apenas dados VM baseados em agentes. Por exemplo, registos de auditoria do Azure.
* Cada cliente pode ter diferentes configurações para a sua área de trabalho, como a retenção e capping de dados.
* Isolamento entre os clientes de regulamentação e conformidade.
* A cobrança para cada área de trabalho será revertida para a subscrição do cliente.

As desvantagens da arquitetura distribuída são:

* Visualizar e analisar centralmente dados entre os inquilinos com ferramentas como o Azure Monitor Workbooks pode resultar em experiências mais lentas , especialmente quando analisamos dados em mais de 50 espaços de trabalho.
* Se os clientes não estiverem a bordo para a gestão de recursos delegados do Azure, os administradores de prestadores de serviços devem ser aprovisionados no diretório de clientes, e é mais difícil para o prestador de serviços gerir um grande número de inquilinos de clientes de uma só vez.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - Os registos são armazenados num espaço de trabalho localizado no inquilino do prestador de serviços

Nesta arquitetura, os registos não são armazenados em inquilinos do cliente, mas apenas num local central dentro de uma das subscrições do fornecedor de serviços. Os agentes que estão instalados em VMs do cliente estão configurados para enviar os registos para esta área de trabalho com o ID de área de trabalho e a chave secreta.

As vantagens da arquitetura centralizada são:

* É fácil de gerenciar um grande número de clientes e integrá-las para vários sistemas de back-end.
* O fornecedor de serviços tem propriedade completa sobre os registos e de vários artefactos, como as funções e consultas guardadas.
* O fornecedor de serviços pode efetuar análises em todos os seus clientes.

As desvantagens da arquitetura centralizada são:

* Esta arquitetura é aplicável apenas para dados baseados em agente da VM, não abordará origens de dados de recursos de infraestrutura PaaS, SaaS e no Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados numa única área de trabalho. O método apenas bom para o fazer consiste em utilizar o nome de domínio completamente qualificado do computador (FQDN) ou por meio de subscrição do Azure ID. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e a configuração.
* Recursos de infraestrutura do Azure e PaaS serviços como o diagnóstico do Azure e os registos de auditoria do Azure requer a área de trabalho para estar no mesmo inquilino, como o recurso, portanto, eles não é possível enviar os registos para a área de trabalho central.
* Todos os agentes VM de todos os clientes serão autenticados no espaço de trabalho central utilizando o mesmo ID e chave do espaço de trabalho. Não existe nenhum método para bloquear os registos de um cliente específico sem interromper a outros clientes.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido - Os registos são armazenados no espaço de trabalho localizado no inquilino do cliente e alguns deles são puxados para uma localização central.

A terceira mistura de arquitetura entre as duas opções. Baseia-se na arquitetura distribuída primeiro onde os registos são locais para cada cliente, mas usando algum mecanismo para criar um repositório central de registos. Uma parte dos registos é extraída para uma localização central para relatórios e análises. Essa parte pode ser pequeno número de tipos de dados ou um resumo da atividade como estatísticas diárias.

Existem duas opções para implementar registos numa localização central:

1. Espaço de trabalho central: O prestador de serviços pode criar um espaço de trabalho no seu inquilino e utilizar um script que utiliza a [API](https://dev.loganalytics.io/) de Consulta com a [API](../../azure-monitor/platform/data-collector-api.md) de recolha de dados para levar os dados dos vários espaços de trabalho para esta localização central. Outra opção, além de um guião, é usar [aplicações lógicas azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

2. Power BI como uma localização central: O Power BI pode funcionar como a localização central quando os vários espaços de trabalho exportam dados para o mesmo utilizando a integração entre o espaço de trabalho log Analytics e [o Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Passos seguintes

* Automatizar a criação e configuração de espaços de trabalho usando [modelos de Gestor de Recursos](template-workspace-configuration.md)

* Automatizar a criação de espaços de trabalho usando [o PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Utilizar [Alertas](../../azure-monitor/platform/alerts-overview.md) para integrar com os sistemas existentes

* Gerar relatórios sumários usando [Power BI](../../azure-monitor/platform/powerbi.md)

* Clientes a bordo da [Azure delegaram gestão de recursos.](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)
