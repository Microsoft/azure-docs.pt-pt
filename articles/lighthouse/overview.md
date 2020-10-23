---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os prestadores de serviços entreguem serviços geridos aos seus clientes com maior automatização e eficiência em escala.
ms.date: 10/19/2020
ms.topic: overview
ms.openlocfilehash: a76606ff48a09c0c31584882e3d2aa164ec97325
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203794"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse permite a gestão multi-inquilino e entre inquilinos, o que garante uma maior automatização, escalabilidade e melhor governação de recursos e inquilinos.

Com o Azure Lighthouse, os prestadores de serviços podem fornecer serviços geridos usando ferramentas de gestão abrangentes e robustas incorporadas na plataforma Azure. Os clientes mantêm o controlo sobre quem pode aceder ao seu inquilino, que recursos podem aceder e que ações podem ser tomadas. Esta oferta também pode beneficiar [organizações de TI empresariais](concepts/enterprise.md) que gerem recursos em vários inquilinos.

![Diagrama de visão geral do Farol de Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda os prestadores de serviços a construir e a prestar serviços geridos de forma eficiente. As vantagens incluem:

- **Gestão em escala**: As operações de envolvimento do cliente e ciclo de vida para gerir os recursos dos clientes são mais fáceis e escaláveis. As APIs existentes, ferramentas de gestão e fluxos de trabalho podem ser usados com recursos delegados, incluindo máquinas hospedadas fora de Azure, independentemente das regiões em que estão localizadas.
- **Maior visibilidade e controlo para os clientes**: Os clientes têm um controlo preciso sobre os âmbitos que delegam para gestão e as permissões que são permitidas. Podem auditar as ações do prestador de serviços e remover completamente o acesso, se desejar.
- **Ferramenta de plataforma abrangente e unificada**: A nossa experiência de ferramentas aborda cenários chave do prestador de serviços, incluindo vários modelos de licenciamento como EA, CSP e pay-as-you-go. O Azure Lighthouse trabalha com ferramentas e APIs existentes, modelos de licenciamento, [aplicações geridas pela Azure,](concepts/managed-applications.md)e programas parceiros como o [programa Cloud Solution Provider (CSP)](/partner-center/csp-overview). Pode integrar o Farol Azure nos seus fluxos de trabalho e aplicações existentes e acompanhar o seu impacto nos compromissos com os clientes [ligando o seu ID do parceiro.](./how-to/partner-earned-credit.md)

Não existem custos adicionais associados à utilização do Farol Azure para gerir os recursos da Azure. Qualquer cliente ou parceiro Azure pode usar o Farol Azure.

## <a name="capabilities"></a>Capacidades

O Farol Azure inclui múltiplas formas de ajudar a agilizar o envolvimento e a gestão:

- **Gestão de recursos delegada da Azure**: [Gerencie os recursos Azure dos seus clientes de forma segura a partir do seu próprio inquilino,](concepts/azure-delegated-resource-management.md)sem ter de mudar de contexto e controlar os aviões. As subscrições de clientes e grupos de recursos podem ser delegadas em utilizadores e funções especificados no inquilino gerente, com a capacidade de remover o acesso conforme necessário.
- **Experiências do portal Azure**: Ver informações de inquilinos cruzados na página [ **dos meus clientes** ](how-to/view-manage-customers.md) no portal Azure. Uma página correspondente [ **de prestadores de serviços** ](how-to/view-manage-service-providers.md) permite que os clientes vejam e gerem o acesso do seu prestador de serviços.
- **Modelos do Gestor de Recursos Azure**: Use modelos ARM para [obter recursos de clientes delegados](how-to/onboard-customer.md) a bordo e [execute tarefas de gestão de inquilinos cruzados.](samples/index.md)
- **Ofertas de Serviço Gerido no Azure Marketplace**: [Ofereça os seus serviços aos clientes](concepts/managed-services-offers.md) através de ofertas privadas ou públicas e a bordo automaticamente para o Farol Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Explore [experiências de gestão de inquilinos cruzados.](concepts/cross-tenant-management-experience.md)
- Veja como usar o [Farol Azure dentro de uma empresa.](concepts/enterprise.md)
