---
title: O que é o Azure Lighthouse?
description: Lighthouse do Azure permite que os fornecedores de serviços fornecer serviços geridos para os clientes com uma maior automação e eficiência à escala.
author: JnHs
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: eb55af5a1121eb193bb76efc9f9e0b833f4b5a1f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809821"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

Lighthouse do Azure oferece um plano de controlo único para ver e gerir o Azure em todos os seus clientes com uma maior automação, dimensionar e governação aprimorada de fornecedores de serviços. Com o Azure Lighthouse, fornecedores de serviços podem fornecer os serviços geridos com ferramentas de gerenciamento abrangente e robusta incorporadas na plataforma do Azure. Esta oferta também pode trazer benefícios para TI corporativos as organizações a gestão de recursos em vários inquilinos.

![Diagrama de descrição geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

Lighthouse do Azure ajuda-o a maior rentabilidade e eficiente criar e fornecer os serviços geridos para os seus clientes. As vantagens incluem:

- **Gestão em escala**: Operações de envolvimento e ciclo de vida de cliente para gerir os recursos de cliente são mais simples e dimensionável.
- **Maior visibilidade e precisão para os clientes**: Os clientes cujos recursos estiver a gerir terão maior visibilidade de suas ações e um controlo preciso sobre o âmbito, delegam para a gestão, enquanto o seu IP é preservado.
- **As ferramentas de plataforma abrangente e unificada**: Nossa experiência de ferramentas aborda cenários do Provedor de serviços de chave, incluindo vários modelos de licenciamento, como EA, o CSP e pay as you go. Os novos recursos de trabalhar com APIs, modelos, de licenciamento e ferramentas existentes e programas de parceiros como a [programa de fornecedor de soluções Cloud (CSP)](https://docs.microsoft.com/partner-center/csp-overview). As opções de Lighthouse do Azure que escolher podem ser integradas em seus fluxos de trabalho existentes e aplicações, e pode acompanhar o seu impacto no envolvimento com o cliente por [ligar o seu ID de parceiro](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Não existem sem custos adicionais associados com o uso Lighthouse do Azure para gerir os recursos do Azure dos seus clientes.

## <a name="capabilities"></a>Capacidades

Lighthouse do Azure inclui várias formas de ajudar a simplificar a gestão e envolvimento do cliente:

- **Gestão de recursos de delegado de Azure**: Gerir recursos do Azure dos seus clientes em segurança a partir no seu próprio inquilino, sem precisar alternar o contexto e o controlo de planos. Para mais informações, veja [gestão de recursos de delegado de Azure](./concepts/azure-delegated-resource-management.md).
- **Ocorre com o novo portal do Azure**: Ver informações de entre inquilinos no novo **meus clientes** página no [portal do Azure](https://portal.azure.com). Odpovídající **fornecedores de serviços** painel permite que os seus clientes, ver e gerir o acesso ao fornecedor de serviço. Para mais informações, veja [ver e gerir os clientes](./how-to/view-manage-customers.md) e [ver e gerir fornecedores de serviços](./how-to/view-manage-service-providers.md).
- **Modelos Azure Resource Manager**: Execute tarefas de gestão mais facilmente, incluindo a inclusão de clientes para a gestão de recursos do Azure de delegado. Para obter mais informações, consulte nosso [exemplos de repositório](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) e [carregar um cliente para o Azure delegadas a gestão de recursos](how-to/onboard-customer.md).
- **Gerido ofertas de serviços no Azure Marketplace**: Oferecer seus serviços aos clientes através de ofertas privadas ou públicas e integrou-las automaticamente à gestão de recursos do Azure de delegados, como alternativa ao integração utilizando modelos Azure Resource Manager. Para mais informações, veja [geridos ofertas de serviços no Azure Marketplace](./concepts/managed-services-offers.md).
- **Aplicações geridas do Azure**: Aplicações de pacote e ship que são fáceis para os seus clientes implantar e usar em seus próprios subscrições. A aplicação é implementada num grupo de recursos que acessar no seu inquilino, permitindo-lhe gerir o serviço como parte da experiência do Azure Lighthouse geral. Para mais informações, veja [descrição geral de aplicativos de geridas do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> As funcionalidades descritas acima estão atualmente disponíveis em clouds públicas. Para obter disponibilidade regional de serviços individuais, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).