---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os prestadores de serviços entreguem serviços geridos aos seus clientes com maior automatização e eficiência em escala.
ms.date: 05/28/2020
ms.topic: overview
ms.openlocfilehash: 27f99cb398294d570b2d91131fcf40dd0648e981
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219064"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse oferece aos prestadores de serviços um único plano de controlo para visualizar e gerir o Azure em todos os seus clientes com maior automatização, escala e maior governação. Com o Azure Lighthouse, os prestadores de serviços podem fornecer serviços geridos usando ferramentas de gestão abrangentes e robustas incorporadas na plataforma Azure. Esta oferta também pode beneficiar organizações de TI empresariais que gerem recursos em vários inquilinos.

![Diagrama de visão geral do Farol de Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda-o a construir e prestar serviços geridos de forma rentável e eficiente para os seus clientes. Os benefícios incluem:

- **Gestão em escala**: As operações de envolvimento do cliente e ciclo de vida para gerir os recursos dos clientes são mais fáceis e escaláveis. As APIs existentes, ferramentas de gestão e fluxos de trabalho podem ser usados com recursos de clientes delegados, independentemente das regiões em que estão localizadas.
- **Maior visibilidade e precisão para os clientes**: Os clientes terão maior visibilidade nas suas ações e controlo preciso sobre o âmbito que delegam para a gestão, incluindo a capacidade de remover completamente o acesso, enquanto o seu IP é preservado.
- **Ferramenta de plataforma abrangente e unificada**: A nossa experiência de ferramentas aborda cenários chave do prestador de serviços, incluindo vários modelos de licenciamento como EA, CSP e pay-as-you-go. As novas capacidades funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros como o [programa Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). O Farol Azure pode ser integrado nos fluxos de trabalho e aplicações existentes, e pode acompanhar o seu impacto nos compromissos com os clientes [ligando o ID do seu parceiro.](../billing/billing-partner-admin-link-started.md)

Não existem custos adicionais associados à utilização do Farol Azure para gerir os recursos Azure dos seus clientes. Qualquer cliente ou parceiro Azure pode usar o Farol Azure.

## <a name="capabilities"></a>Capacidades

O Azure Lighthouse inclui várias formas de ajudar a agilizar o envolvimento e gestão do cliente:

- **Gestão de recursos delegada da Azure**: Gerencie os recursos Azure dos seus clientes de forma segura a partir do seu próprio inquilino, sem ter de mudar de contexto e controlar os aviões. As subscrições e grupos de recursos podem ser delegados a utilizadores especificados e funções no inquilino gerente, com a capacidade de remover o acesso conforme necessário. Para mais informações, consulte [a gestão de recursos delegada da Azure.](concepts/azure-delegated-resource-management.md)
- **Experiências do novo portal Azure**: Ver informações de inquilinos cruzados na nova página **dos meus clientes** no [portal Azure](https://portal.azure.com). Uma lâmina **de prestadores de serviços** correspondente permite aos seus clientes visualizar e gerir o acesso do prestador de serviços. Para obter mais informações, consulte [Ver e gerir clientes](./how-to/view-manage-customers.md) e [Ver e gerir fornecedores de serviços.](how-to/view-manage-service-providers.md)
- **Modelos de Gestor de Recursos Azure**: Execute tarefas de gestão mais facilmente, incluindo clientes de bordo para gestão de recursos delegados da Azure. Para mais informações, consulte as [nossas amostras repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) e [a bordo de um cliente da Azure delegada na gestão de recursos.](how-to/onboard-customer.md)
- **Ofertas de Serviço Gerido no Azure Marketplace**: Ofereça os seus serviços aos clientes através de ofertas privadas ou públicas, e coloque-os automaticamente a bordo da Azure delegada gestão de recursos, como alternativa ao embarque utilizando modelos do Azure Resource Manager. Para mais informações, consulte [as ofertas do Serviço Gerido no Azure Marketplace.](concepts/managed-services-offers.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Conheça as [experiências de gestão de inquilinos cruzados.](concepts/cross-tenant-management-experience.md)
