---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os provedores de serviços forneçam serviços gerenciados para seus clientes com maior automação e eficiência em escala.
author: JnHs
ms.author: jenhayes
ms.date: 11/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: ae8194e6d5764769019ac803a52cd54497a618c1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929572"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse oferece aos provedores de serviços um único plano de controle para exibir e gerenciar o Azure em todos os seus clientes com maior automação, escala e governança aprimorada. Com o Azure Lighthouse, os provedores de serviços podem entregar serviços gerenciados usando ferramentas de gerenciamento abrangentes e robustas incorporadas à plataforma Azure. Essa oferta também pode beneficiar as organizações de ti empresariais Gerenciando recursos em vários locatários.

![Diagrama de visão geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda a criar e fornecer de forma lucrativa e eficiente serviços gerenciados para seus clientes. Os benefícios incluem:

- **Gerenciamento em escala**: o envolvimento do cliente e as operações do ciclo de vida para gerenciar os recursos do cliente são mais fáceis e escaláveis.
- **Maior visibilidade e precisão para os clientes**: os clientes cujos recursos você está gerenciando terão maior visibilidade de suas ações e controle preciso sobre o escopo delegado para gerenciamento, enquanto o IP é preservado.
- **Ferramentas de plataforma unificadas e abrangentes**: nossa experiência de ferramentas aborda os principais cenários de provedor de serviços, incluindo vários modelos de licenciamento, como ea, CSP e pré-pago. Os novos recursos funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas de parceiros, como o [CSP (programa de provedor de soluções na nuvem)](https://docs.microsoft.com/partner-center/csp-overview). As opções de Lighthouse do Azure escolhidas podem ser integradas aos seus fluxos de trabalho e aplicativos existentes, e você pode acompanhar seu impacto nos compromissos do cliente [vinculando sua ID de parceiro](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Não há custos adicionais associados ao uso do Azure Lighthouse para gerenciar os recursos do Azure dos seus clientes.

## <a name="capabilities"></a>Capacidades

O Azure Lighthouse inclui várias maneiras de ajudar a simplificar o envolvimento do cliente e o gerenciamento:

- **Gerenciamento de recursos delegados do Azure**: Gerencie os recursos do Azure de seus clientes com segurança em seu próprio locatário, sem precisar alternar os planos de contexto e de controle. Para obter mais informações, consulte [Azure delegated Resource Management](./concepts/azure-delegated-resource-management.md).
- **Novas experiências de portal do Azure**: exibir informações de locatário cruzado na página novos **meus clientes** no [portal do Azure](https://portal.azure.com). Uma folha de **provedores de serviços** correspondente permite que seus clientes exibam e gerenciem o acesso do provedor de serviço. Para obter mais informações, consulte [Exibir e gerenciar clientes](./how-to/view-manage-customers.md) e [Exibir e gerenciar provedores de serviços](./how-to/view-manage-service-providers.md).
- **Modelos de Azure Resource Manager**: Execute tarefas de gerenciamento mais facilmente, incluindo clientes de integração para o gerenciamento de recursos delegado do Azure. Para obter mais informações, consulte nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) e integre [um cliente ao gerenciamento de recursos delegado do Azure](how-to/onboard-customer.md).
- **Ofertas de serviços gerenciados no Azure Marketplace**: ofereça seus serviços aos clientes por meio de ofertas públicas ou privadas e faça com que eles sejam integrados automaticamente ao gerenciamento de recursos delegado do Azure, como uma alternativa à integração usando modelos de Azure Resource Manager. Para obter mais informações, consulte [ofertas de serviços gerenciados no Azure Marketplace](./concepts/managed-services-offers.md).
- **Aplicativos gerenciados do Azure**: empacotar e enviar aplicativos que são fáceis para seus clientes de implantar e usar em suas próprias assinaturas. O aplicativo é implantado em um grupo de recursos que você acessa do seu locatário, permitindo que você gerencie o serviço como parte da experiência geral do Azure Lighthouse. Para obter mais informações, consulte [visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](concepts/cross-tenant-management-experience.md).
