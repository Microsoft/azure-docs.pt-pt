---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os prestadores de serviços entreguem serviços geridos aos seus clientes com maior automatização e eficiência em escala.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 41fa836321da7cdb42b9a3419bc0bb32963a5849
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017868"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse permite a gestão multi-inquilino e entre inquilinos, o que garante uma maior automatização, escalabilidade e melhor governação de recursos e inquilinos. Com o Azure Lighthouse, os prestadores de serviços podem fornecer serviços geridos usando ferramentas de gestão abrangentes e robustas incorporadas na plataforma Azure. Esta oferta também pode beneficiar organizações de TI empresariais que gerem recursos em vários inquilinos.

![Diagrama de visão geral do Farol de Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda-o a construir e prestar serviços geridos de forma rentável e eficiente. Os benefícios incluem:

- **Gestão em escala**: As operações de envolvimento do cliente e ciclo de vida para gerir os recursos dos clientes são mais fáceis e escaláveis. As APIs existentes, ferramentas de gestão e fluxos de trabalho podem ser usados com recursos delegados, independentemente das regiões em que estão localizadas.
- **Maior visibilidade e precisão para os clientes**: Os clientes terão maior visibilidade nas suas ações e controlo preciso sobre o âmbito que delegam para a gestão, incluindo a capacidade de remover completamente o acesso, enquanto o seu IP é preservado.
- **Ferramenta de plataforma abrangente e unificada**: A nossa experiência de ferramentas aborda cenários chave do prestador de serviços, incluindo vários modelos de licenciamento como EA, CSP e pay-as-you-go. As novas capacidades funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros como o [programa Cloud Solution Provider (CSP)](/partner-center/csp-overview). O Farol Azure pode ser integrado nos fluxos de trabalho e aplicações existentes, e pode acompanhar o seu impacto nos compromissos com os clientes [ligando o ID do seu parceiro.](./how-to/partner-earned-credit.md)

Não existem custos adicionais associados à utilização do Farol Azure para gerir os recursos da Azure. Qualquer cliente ou parceiro Azure pode usar o Farol Azure.

## <a name="capabilities"></a>Capacidades

O Farol Azure inclui múltiplas formas de ajudar a agilizar o envolvimento e a gestão:

- **Gestão de recursos delegada da Azure**: Gerencie os recursos Azure dos seus clientes de forma segura a partir do seu próprio inquilino, sem ter de mudar de contexto e controlar os aviões. As subscrições e grupos de recursos podem ser delegados a utilizadores especificados e funções no inquilino gerente, com a capacidade de remover o acesso conforme necessário. Para mais informações, consulte [a gestão de recursos delegada da Azure.](concepts/azure-delegated-resource-management.md)
- **Experiências do portal Azure**: Ver informações de inquilinos cruzados na página [ **dos meus clientes** ](./how-to/view-manage-customers.md) no portal Azure. Uma página correspondente [ **de prestadores de serviços** ](how-to/view-manage-service-providers.md) permite que os clientes vejam e gerem o acesso do seu prestador de serviços.
- **Modelos de Gestor de Recursos Azure**: Os nossos modelos ajudam-no a executar tarefas de gestão de inquilinos cruzados e recursos de clientes delegados a bordo. Para mais informações, consulte as [nossas amostras repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) e [a bordo de um cliente para o Farol de Azure.](how-to/onboard-customer.md)
- **Ofertas de Serviço Gerido no Azure Marketplace**: Ofereça os seus serviços aos clientes através de ofertas privadas ou públicas e coloque-os automaticamente a bordo do Farol Azure. Para mais informações, consulte [as ofertas do Serviço Gerido no Azure Marketplace.](concepts/managed-services-offers.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Explore [experiências de gestão de inquilinos cruzados.](concepts/cross-tenant-management-experience.md)
- Veja como usar o [Farol Azure dentro de uma empresa.](concepts/enterprise.md)
