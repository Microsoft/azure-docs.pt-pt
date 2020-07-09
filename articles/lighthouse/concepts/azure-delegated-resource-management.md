---
title: Gestão de recursos delegados do Azure
description: A gestão de recursos delegados da Azure é uma parte fundamental do Farol de Azure, permitindo aos prestadores de serviços gerir recursos delegados em escala com agilidade e precisão.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: bbe3c28cdcd252755b8350eaa5d2e72044981174
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120896"
---
# <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

A gestão de recursos delegados da Azure é um dos componentes-chave do [Farol Azure.](../overview.md) Com a gestão de recursos delegada da Azure, os prestadores de serviços podem simplificar o envolvimento do cliente e as experiências de embarque, ao mesmo tempo que gerem recursos delegados em escala com agilidade e precisão.

## <a name="what-is-azure-delegated-resource-management"></a>O que é a gestão de recursos delegada da Azure?

A gestão de recursos delegados Azure permite a projeção lógica de recursos de um inquilino para outro inquilino. Isto permite que os utilizadores autorizados de um inquilino Azure Ative (Azure AD) realizem operações de gestão em diferentes inquilinos da AD Azure pertencentes aos seus clientes. Os prestadores de serviços podem inscrever-se no seu próprio inquilino Azure AD e ter autorização para trabalhar em assinaturas de clientes delegados e grupos de recursos. Isto permite-lhes realizar operações de gestão em nome dos seus clientes, sem ter de se inscrever em cada cliente inquilino individual.

> [!NOTE]
> A gestão de recursos delegados Azure também pode ser usada [dentro de uma empresa que tem vários inquilinos AD Azure próprios](enterprise.md) para simplificar a gestão de inquilinos cruzados.

Com a Azure delegada na gestão de recursos, os utilizadores autorizados podem trabalhar diretamente no contexto de uma subscrição de clientes sem terem uma conta no inquilino desse cliente ou serem coproprietários do inquilino do cliente. Também podem [visualizar e gerir todas as subscrições de clientes delegadas na nova página **dos Meus clientes** ](../how-to/view-manage-customers.md) no portal Azure.

A [experiência de gestão de inquilinos cross-tenant ajuda-o](cross-tenant-management-experience.md) a trabalhar de forma mais eficiente com serviços de gestão da Azure como a Azure Policy, O Azure Security Center, e muito mais. Toda a atividade do prestador de serviços é rastreada no registo de atividades, que é armazenado no arrendatário do cliente (e pode ser visto pelos utilizadores no inquilino gerente). Isto significa que tanto o cliente como o prestador de serviços podem facilmente identificar o utilizador associado a quaisquer alterações.

Quando embarcar num cliente para a Azure delegada na gestão de recursos, eles terão acesso à página **de prestadores de serviços** no portal Azure, onde podem [confirmar e gerir as suas ofertas, prestadores de serviços e recursos delegados.](../how-to/view-manage-service-providers.md) Se o cliente quiser revogar o acesso a um prestador de serviços, pode fazê-lo aqui a qualquer momento.

Pode [publicar o novo tipo de oferta de Serviço Gerido para o Azure Marketplace](../how-to/publish-managed-services-offers.md) para facilmente embarcar clientes para a Azure delegada gestão de recursos. Em alternativa, pode [concluir o processo de embarque através da implementação de modelos do Gestor de Recursos Azure](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Como funciona a gestão de recursos delegada da Azure

A um nível elevado, eis como a Azure delegada gestão de recursos funciona:

1. Como prestador de serviços, identifica o acesso (funções) que os seus grupos, diretores de serviços ou utilizadores precisarão para gerir os recursos Azure do cliente. A definição de acesso contém o ID do inquilino do prestador de serviços juntamente com o acesso necessário à oferta, definido utilizando identidades **principais** do seu inquilino mapeados para [funções incorporadas Valores **dedefinição** ](../../role-based-access-control/built-in-roles.md) (Contribuinte, VM Contribuinte, Leitor, etc.).
2. Especifica este acesso e a bordo do cliente para a Azure delegada gestão de recursos de uma de duas maneiras:
   - [Publique uma oferta de serviço gerida aZure Marketplace](../how-to/publish-managed-services-offers.md) (privada ou pública) que o cliente aceitará
   - [Implementar um modelo de Gestor de Recursos Azure ao inquilino do cliente](../how-to/onboard-customer.md) para uma ou mais subscrições específicas ou grupos de recursos
3. Uma vez que o cliente tenha sido acedido, os utilizadores autorizados podem iniciar sômência no seu prestador de serviços e executar tarefas de gestão no âmbito dado pelo cliente, com base no acesso que definiu.

> [!NOTE]
> Pode gerir recursos delegados que estão localizados em [diferentes regiões.](../../availability-zones/az-overview.md#regions) No entanto, a delegação de assinaturas através de uma [nuvem nacional](../../active-directory/develop/authentication-national-cloud.md) e da nuvem pública de Azure, ou através de duas nuvens nacionais separadas, não é apoiada.

## <a name="support-for-azure-delegated-resource-management"></a>Apoio à gestão de recursos delegada da Azure

Se precisar de ajuda relacionada com a gestão de recursos delegados da Azure, pode abrir um pedido de apoio no portal Azure. Para **o tipo de emissão,** escolha **Técnico**. Selecione uma subscrição e, em seguida, **selecione Farol** (em Gestão de & de **Monitorização).**

## <a name="next-steps"></a>Próximos passos

- Conheça as [experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Conheça as [ofertas de serviços geridos no Azure Marketplace.](managed-services-offers.md)