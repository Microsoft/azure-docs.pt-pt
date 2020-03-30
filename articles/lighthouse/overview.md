---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os prestadores de serviços ofereçam serviços geridos aos seus clientes com maior automatização e eficiência em escala.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646199"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse oferece aos prestadores de serviços um único plano de controlo para visualizar e gerir o Azure em todos os seus clientes com maior automação, escala e governação reforçada. Com o Farol Azure, os prestadores de serviços podem fornecer serviços geridos utilizando ferramentas de gestão abrangentes e robustas incorporadas na plataforma Azure. Esta oferta também pode beneficiar as organizações de TI da empresa que gerem recursos em vários inquilinos.

![Diagrama de visão geral do Farol de Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Vantagens

O Azure Lighthouse ajuda-o a construir e prestar serviços geridos de forma rentável e eficiente para os seus clientes. Os benefícios incluem:

- **Gestão à escala**: O envolvimento do cliente e as operações de ciclo de vida para gerir os recursos dos clientes são mais fáceis e escaláveis.
- **Maior visibilidade e precisão para os clientes**: Os clientes cujos recursos está a gerir terão maior visibilidade nas suas ações e controlo preciso sobre o âmbito que delegam para a gestão, enquanto o seu IP é preservado.
- **Ferramentas abrangentes e unificadas de plataforma**: A nossa experiência de ferramentas aborda cenários de prestadores de serviços chave, incluindo vários modelos de licenciamento como EA, CSP e pay-as-you-go. As novas capacidades funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros como o [programa Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). As opções do Farol Azure que escolher podem ser integradas nos seus fluxos de trabalho e aplicações existentes, e pode acompanhar o seu impacto nos compromissos do cliente [ligando o ID do seu parceiro](../billing/billing-partner-admin-link-started.md).

Não existem custos adicionais associados à utilização do Farol Azure para gerir os recursos Azure dos seus clientes.

## <a name="capabilities"></a>Capacidades

O Farol Azure inclui múltiplas formas de ajudar a agilizar o envolvimento e gestão do cliente:

- **Gestão de recursos delegados da Azure**: Gerencie os recursos Azure dos seus clientes de forma segura dentro do seu próprio inquilino, sem ter de mudar de contexto e controlar aviões. Para mais informações, consulte a [gestão de recursos delegados do Azure.](concepts/azure-delegated-resource-management.md)
- **Novas experiências do portal Azure**: Veja informações sobre inquilinos cruzados na nova página dos **meus clientes** no [portal Azure.](https://portal.azure.com) Uma lâmina de **prestador de serviços** correspondente permite que os seus clientes vejam e gerem o acesso ao prestador de serviços. Para mais informações, consulte [ver e gerir os clientes](./how-to/view-manage-customers.md) e ver e gerir os [prestadores de serviços.](how-to/view-manage-service-providers.md)
- **Modelos**de Gestor de Recursos Azure : Execute tarefas de gestão mais facilmente, incluindo clientes de bordo para gestão de recursos delegados do Azure. Para mais informações, consulte as [nossas amostras repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) e Onboard um cliente para a [gestão de recursos delegados do Azure.](how-to/onboard-customer.md)
- **Ofertas de Serviços Geridos no Azure Marketplace**: Ofereça os seus serviços aos clientes através de ofertas privadas ou públicas, e coloque-os automaticamente a bordo para a gestão de recursos delegados do Azure, como um suplente para o embarque utilizando modelos do Gestor de Recursos Azure. Para mais informações, consulte [ofertas de serviços geridos no Azure Marketplace.](concepts/managed-services-offers.md)
- **Aplicações geridas pela Azure**: Pacote e aplicações de envio que são fáceis de implantar e utilizar nas suas próprias subscrições. A aplicação é implantada num grupo de recursos a que acede ao seu inquilino, permitindo-lhe gerir o serviço como parte da experiência global do Farol Azure. Para mais informações, consulte a [visão geral das aplicações geridas pelo Azure.](../azure-resource-manager/managed-applications/overview.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Conheça [as experiências de gestão de inquilinos cruzados.](concepts/cross-tenant-management-experience.md)
