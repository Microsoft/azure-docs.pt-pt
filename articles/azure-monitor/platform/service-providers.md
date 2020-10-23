---
title: Registos Azure Monitor para Prestadores de Serviços Microsoft Docs
description: Os Registos Azure Monitor podem ajudar os Fornecedores de Serviços Geridos (MSPs), grandes empresas, Fornecedores independentes de Software (ISVs) e fornecedores de serviços de hospedagem a gerir e monitorizar servidores nas instalações do cliente ou na infraestrutura em nuvem.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: d92dd191c8bfe590f6dab392ff679e5d7712ae6c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143859"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Registos de monitores Azure para Prestadores de Serviços

Log Analytics espaços de trabalho no Azure Monitor podem ajudar fornecedores de serviços geridos (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e alojando fornecedores de serviços gerir e monitorizar servidores nas instalações do cliente ou infraestruturas de nuvem.

As grandes empresas partilham muitas semelhanças com os prestadores de serviços, especialmente quando há uma equipa de TI centralizada que é responsável pela gestão de TI para muitas unidades de negócio diferentes. Para simplificar, este documento utiliza o *prestador de serviços* de termo, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e prestadores de serviços que fazem parte do programa [Cloud Solution Provider (CSP),](https://partner.microsoft.com/membership/cloud-solution-provider) o Log Analytics in Azure Monitor é um dos serviços Azure disponíveis nas subscrições do Azure CSP.

O Log Analytics in Azure Monitor também pode ser utilizado por um prestador de serviços que gere os recursos dos clientes através da capacidade delegada de gestão de recursos da [Azure no Farol de Azure.](../../lighthouse/overview.md)

## <a name="architectures-for-service-providers"></a>Arquiteturas para Prestadores de Serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e isolamento dos dados de [registo](data-platform-logs.md) e criar uma arquitetura que responda às suas necessidades específicas de negócio. [Este artigo](design-logs-deployment.md) explica as considerações de design, implantação e migração para um espaço de trabalho, e o artigo [de acesso de gestão](manage-access.md) discute como aplicar e gerir permissões para registar dados. Os prestadores de serviços têm considerações adicionais.

Existem três arquiteturas possíveis para prestadores de serviços no que diz respeito aos espaços de trabalho do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuídos - Os registos são armazenados em espaços de trabalho localizados no arrendatário do cliente

Nesta arquitetura, é implantado um espaço de trabalho no arrendatário do cliente que é utilizado para todos os registos desse cliente.

Existem duas formas de os administradores de prestadores de serviços terem acesso a um espaço de trabalho log analytics num inquilino do cliente:

- Um cliente pode adicionar utilizadores individuais do prestador de serviços como [utilizadores convidados do Azure Ative Directory (B2B)](../../active-directory/external-identities/what-is-b2b.md). Os administradores do prestador de serviços terão de subscrever o diretório de cada cliente no portal Azure para poderem aceder a estes espaços de trabalho. Isto também requer que os clientes gerem o acesso individual para cada administrador de prestador de serviços.
- Para uma maior escalabilidade e flexibilidade, os prestadores de serviços podem utilizar a capacidade [delegada](../../lighthouse/concepts/azure-delegated-resource-management.md) de gestão de recursos do [Azure Lighthouse](../../lighthouse/overview.md) para aceder ao inquilino do cliente. Com este método, os administradores do prestador de serviços estão incluídos num grupo de utilizadores AD AZure no arrendatário do prestador de serviços, e este grupo tem acesso durante o processo de embarque para cada cliente. Estes administradores podem então aceder aos espaços de trabalho de cada cliente a partir do seu próprio inquilino prestador de serviços, em vez de terem de aceder individualmente ao inquilino de cada cliente. O acesso aos espaços de trabalho do Log Analytics dos seus clientes reduz desta forma o trabalho exigido do lado do cliente, podendo facilitar a recolha e análise de dados em vários clientes geridos pelo mesmo prestador de serviços através de ferramentas como [os Livros de Trabalho do Monitor Azure.](./workbooks-overview.md) Para obter mais informações, consulte [os recursos do cliente do Monitor em escala.](../../lighthouse/how-to/monitor-at-scale.md)

As vantagens da arquitetura distribuída são:

* O cliente pode confirmar níveis específicos de permissões através da [gestão de recursos delegados da Azure,](../../lighthouse/concepts/azure-delegated-resource-management.md)ou pode gerir o acesso aos registos utilizando o seu próprio [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).
* Os registos podem ser recolhidos de todos os tipos de recursos, e não apenas dados VM baseados em agentes. Por exemplo, Registos de Auditoria Azure.
* Cada cliente pode ter diferentes configurações para o seu espaço de trabalho, como retenção e cobertura de dados.
* Isolamento entre clientes para regulação e complicância.
* A taxa de cada espaço de trabalho será enrolada na subscrição do cliente.

As desvantagens da arquitetura distribuída são:

* Visualizar e analisar dados centralmente entre os inquilinos do cliente com ferramentas como os Azure Monitor Workbooks pode resultar em experiências mais lentas, especialmente quando analisa dados em mais de 50 espaços de trabalho.
* Se os clientes não estiverem a bordo para a gestão de recursos delegada da Azure, os administradores dos prestadores de serviços devem ser aprovisionados no diretório de clientes, e é mais difícil para o prestador de serviços gerir um grande número de inquilinos de um só momento.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - Os registos são armazenados num espaço de trabalho localizado no arrendatário do prestador de serviços

Nesta arquitetura, os registos não são armazenados nos inquilinos do cliente, mas apenas numa localização central dentro de uma das subscrições do prestador de serviços. Os agentes instalados nos VM do cliente estão configurados para enviar os seus registos para este espaço de trabalho utilizando o ID do espaço de trabalho e a chave secreta.

As vantagens da arquitetura centralizada são:

* É fácil gerir um grande número de clientes e integrá-los em vários sistemas backend.
* O prestador de serviços tem total propriedade sobre os registos e os vários artefactos, tais como funções e consultas guardadas.
* O prestador de serviços pode realizar análises em todos os seus clientes.

As desvantagens da arquitetura centralizada são:

* Esta arquitetura é aplicável apenas para dados de VM baseados em agentes, não abrangerá fontes de dados de tecido PaaS, SaaS e Azure.
* Pode ser difícil separar os dados entre os clientes quando são fundidos num único espaço de trabalho. O único bom método para o fazer é utilizar o nome de domínio totalmente qualificado do computador (FQDN) ou através do ID de assinatura Azure.
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única nota e as mesmas definições de retenção e configuração.
* Os serviços de tecido azul e PaaS, como o Azure Diagnostics e os Registos de Auditoria Azure, exigem que o espaço de trabalho esteja no mesmo inquilino que o recurso, pelo que não podem enviar os registos para o espaço de trabalho central.
* Todos os agentes VM de todos os clientes serão autenticados no espaço de trabalho central usando o mesmo ID e chave do espaço de trabalho. Não existe um método para bloquear registos de um cliente específico sem interromper outros clientes.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido - Os registos são armazenados no espaço de trabalho localizado no inquilino do cliente e alguns deles são puxados para um local central.

A terceira mistura de arquitetura entre as duas opções. Baseia-se na primeira arquitetura distribuída onde os registos são locais para cada cliente, mas utilizando algum mecanismo para criar um repositório central de registos. Uma parte dos troncos é puxada para um local central para relatórios e análises. Esta parte pode ser um pequeno número de tipos de dados ou um resumo da atividade, como estatísticas diárias.

Existem duas opções para implementar registos numa localização central:

1. Espaço de trabalho central: O prestador de serviços pode criar um espaço de trabalho no seu inquilino e utilizar um script que utilize a [API de Consulta](https://dev.loganalytics.io/) com a [API de Recolha](./data-collector-api.md) de Dados para trazer os dados dos vários espaços de trabalho para esta localização central. Outra opção, além de um script, é utilizar [aplicações Azure Logic.](../../logic-apps/logic-apps-overview.md)

2. Power BI como localização central: O Power BI pode funcionar como a localização central quando os vários espaços de trabalho exportam dados para ele utilizando a integração entre o espaço de trabalho Log Analytics e [o Power BI](./powerbi.md).

## <a name="next-steps"></a>Passos seguintes

* Automatizar a criação e configuração de espaços de trabalho usando [modelos de Gestor de Recursos](../samples/resource-manager-workspace.md)

* Automatizar a criação de espaços de trabalho utilizando [o PowerShell](./powershell-workspace-configuration.md)

* Utilizar [alertas](./alerts-overview.md) para integrar-se com os sistemas existentes

* Gerar relatórios sumários usando [Power BI](./powerbi.md)

* Clientes a bordo da [Azure delegaram gestão de recursos.](../../lighthouse/concepts/azure-delegated-resource-management.md)