---
title: Azure de delegado de gestão de recursos - Lighthouse do Azure
description: Ofertas de permitir que os fornecedores de serviços vender ofertas de gestão de recursos para os clientes no Azure Marketplace de serviços geridos.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: cec6453cdf339e82420a1b12af6c8e60526fdc03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809968"
---
# <a name="azure-delegated-resource-management"></a>Gestão de recursos do Azure de delegado

Gestão de recursos do Azure de delegado é um dos principais componentes do Azure Lighthouse. Com a gestão de recursos do Azure de delegados, fornecedores de serviços podem simplificar a experiências de envolvimento e a inclusão do cliente, gerindo recursos delegados em escala com agilidade e precisão.

## <a name="what-is-azure-delegated-resource-management"></a>O que é o Azure, gestão de recursos é delegada?

Gestão de recursos do Azure de delegado permite a projeção lógica de recursos de um inquilino para outro inquilino. Isso permite que os utilizadores autorizados de um inquilino do Azure Active Directory (Azure AD) realizar operações de gestão em diferente do Azure AD inquilinos que pertencem aos clientes. Fornecedores de serviços podem iniciar sessão no seu próprio inquilino do Azure AD e tem autorização para trabalhar em subscrições de cliente delegado e grupos de recursos. Isto permite efetuar operações de gestão em nome dos seus clientes, sem ter de iniciar sessão a cada inquilino individuais dos clientes.

> [!NOTE]
> Gestão de recursos do Azure de delegado também pode ser utilizado dentro de uma empresa que tem vários inquilinos do Azure AD da sua própria para simplificar a gestão de entre inquilinos.

Com a gestão de recursos do Azure de delegado, os utilizadores autorizados podem trabalhar diretamente no contexto de subscrição de um cliente sem ter uma conta no inquilino do cliente ou que está a ser um coproprietário de inquilino do cliente. Também pode [ver e gerir todas as subscrições de cliente delegado no novo **meus clientes** página](../how-to/view-manage-customers.md) no portal do Azure.

O [experiência de gestão entre inquilinos](cross-tenant-management-experience.md) a trabalhar com mais eficiência com os serviços de gestão do Azure como o Azure Policy, o Centro de segurança do Azure e muito mais. Todas as atividades de fornecedor de serviço é controlada no registo de atividades, o que é armazenado em inquilinos do fornecedor de serviços e do cliente. Isso significa que o cliente e o serviço fornecedor possa identificar facilmente o utilizador associado a todas as alterações.

Quando carregar um cliente para o Azure foi delegado gestão de recursos, eles terão acesso à nova **fornecedores de serviços** página no portal do Azure, onde podem [confirmar e gerenciar suas ofertas, provedores de serviços e recursos de delegado](../how-to/view-manage-service-providers.md). Se o cliente deseja nunca revogar o acesso para um fornecedor de serviços, ela poderá fazer isso aqui em qualquer altura.

Pode [publicar o novo tipo de oferta de serviços geridos pelo Azure Marketplace](../how-to/publish-managed-services-offers.md) para facilmente integrar clientes para o Azure de gestão de recursos delegada. Em alternativa, pode [concluir o processo de integração ao implementar os modelos Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Funciona como o Azure delegadas a gestão de recursos

Um nível elevado, eis como o Azure delegadas funciona a gestão de recursos:

1. Como um fornecedor de serviços, identifique o acesso (funções) que seus grupos, os principais de serviço ou os utilizadores têm de gerir os recursos do Azure do cliente. A definição de acesso contém o ID de inquilino do fornecedor de serviços, juntamente com o acesso necessário para a oferta, definido usando **principalId** identidades do seu inquilino mapeado para [incorporadas  **roleDefinition** valores](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (Contribuidor, VM Contribuidor, leitor, etc.).
2. Especifique este acesso e carregar o cliente para o Azure delegada de gerenciamento de recursos em uma das seguintes formas:
   - [Publicar uma oferta de serviços geridos do Azure Marketplace](../how-to/publish-managed-services-offers.md) (privada ou pública) que o cliente aceitará
   - [Implementar um modelo Azure Resource Manager para o inquilino do cliente](../how-to/onboard-customer.md) para um ou mais subscrições específicas ou grupos de recursos
3. Assim que o cliente tiver sido carregada, os utilizadores autorizados podem iniciar sessão no seu inquilino do fornecedor de serviço e efetuar tarefas de gestão no âmbito determinado cliente, com base no acesso que definiu.

## <a name="support-for-azure-delegated-resource-management"></a>Gestão de recursos de delegado de suporte para o Azure

Se precisar de ajuda relacionados com a gestão de recursos de delegada do Azure, pode abrir um pedido de suporte no portal do Azure. Para **tipo de problema**, escolha **técnica**. Selecione uma subscrição, em seguida, selecione **gestão de recursos de delegado** (sob **monitorização e gestão**).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [experiências de gestão entre inquilinos](cross-tenant-management-experience.md).
- Saiba mais sobre [geridos ofertas de serviços no Azure Marketplace](managed-services-offers.md).