---
title: Gestão de recursos delegados do Azure
description: As ofertas de serviços geridos permitem aos prestadores de serviços vender ofertas de gestão de recursos aos clientes do Azure Marketplace.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904312"
---
# <a name="azure-delegated-resource-management"></a>Gestão de recursos delegados do Azure

A gestão de recursos delegados da Azure é um dos componentes-chave do Farol Azure. Com a gestão de recursos delegados da Azure, os prestadores de serviços podem simplificar o envolvimento do cliente e experiências de embarque, ao mesmo tempo que gerem recursos delegados em escala com agilidade e precisão.

## <a name="what-is-azure-delegated-resource-management"></a>O que é a gestão de recursos delegados do Azure?

A gestão de recursos delegados do Azure permite a projeção lógica de recursos de um inquilino para outro inquilino. Isto permite que os utilizadores autorizados de um inquilino azure Ative Directory (Azure AD) realizem operações de gestão em diferentes inquilinos da Azure AD pertencentes aos seus clientes. Os prestadores de serviços podem inscrever-se no seu próprio inquilino Da D.I. Azure e ter autorização para trabalhar em subscrições de clientes e grupos de recursos delegados. Isto permite-lhes realizar operações de gestão em nome dos seus clientes, sem terem de se inscrever em cada inquilino individual.

> [!NOTE]
> A gestão de recursos delegados da Azure também pode ser utilizada dentro de [uma empresa que tem vários inquilinos da Azure AD para](enterprise.md) simplificar a gestão de inquilinos transversais.

Com a gestão de recursos delegado sancionada pela Azure, os utilizadores autorizados podem trabalhar diretamente no contexto de uma subscrição de cliente sem ter uma conta no inquilino desse cliente ou ser coproprietário do inquilino do cliente. Também podem [ver e gerir todas as subscrições de clientes delegadas na nova página dos meus **clientes** ](../how-to/view-manage-customers.md) no portal Azure.

A [experiência de gestão de inquilinos cruzadas](cross-tenant-management-experience.md) ajuda-o a trabalhar de forma mais eficiente com serviços de gestão Azure como a Azure Policy, Azure Security Center, e muito mais. Toda a atividade do prestador de serviços é rastreada no registo de atividade, que é armazenado tanto no prestador de serviços como nos inquilinos do cliente. Isto significa que tanto o cliente como o prestador de serviços podem identificar facilmente o utilizador associado a quaisquer alterações.

Quando embarcar um cliente para a gestão de recursos delegados do Azure, eles terão acesso à página dos novos **fornecedores** de serviços no portal Azure, onde podem [confirmar e gerir as suas ofertas, prestadores de serviços e recursos delegados.](../how-to/view-manage-service-providers.md) Se o cliente quiser revogar o acesso a um prestador de serviços, pode fazê-lo aqui a qualquer momento.

Pode publicar o novo tipo de oferta de [Serviços Geridos ao Azure Marketplace](../how-to/publish-managed-services-offers.md) para facilmente embarcar clientes a bordo para a gestão de recursos delegados da Azure. Em alternativa, pode concluir o processo de [embarque implantando modelos](../how-to/onboard-customer.md)do Gestor de Recursos Azure .

## <a name="how-azure-delegated-resource-management-works"></a>Como funciona a gestão de recursos delegados da Azure

A um nível elevado, eis como funciona a gestão de recursos delegado seletiva do Azure:

1. Como prestador de serviços, identifica os acessos (funções) que os seus grupos, diretores de serviço susterão ou utilizadores para gerir os recursos do Cliente Azure. A definição de acesso contém o ID do inquilino do prestador de serviços juntamente com o acesso necessário para a oferta, definido usando identidades **principais do** seu inquilino mapeados para [valores de **roleDefinition** incorporados](../../role-based-access-control/built-in-roles.md) (Contribuinte, Colaborador VM, Leitor, etc.).
2. Especifica este acesso e a bordo do cliente à Azure dedelegada gestão de recursos de uma de duas formas:
   - Publique uma oferta de [serviços geridos pelo Azure Marketplace](../how-to/publish-managed-services-offers.md) (privado ou público) que o cliente irá aceitar
   - [Implemente um modelo](../how-to/onboard-customer.md) de Gestor de Recursos Azure para o inquilino do cliente para uma ou mais subscrições ou grupos de recursos específicos
3. Uma vez que o cliente esteja a bordo, os utilizadores autorizados podem iniciar sessão no seu fornecedor de serviços inquilino e executar tarefas de gestão no âmbito do cliente dado, com base no acesso que definiu.

> [!NOTE]
> A delegação de uma subscrição entre dois inquilinos através de nuvens separadas não é apoiada.

## <a name="support-for-azure-delegated-resource-management"></a>Apoio à gestão de recursos delegados do Azure

Se precisar de ajuda relacionada com a gestão de recursos delegados do Azure, pode abrir um pedido de apoio no portal Azure. Para **o tipo de emissão,** escolha **Técnico**. Selecione uma subscrição e, em seguida, selecione **Farol** (sob **monitorização & Management).**

## <a name="next-steps"></a>Passos seguintes

- Conheça [as experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
- Conheça as ofertas de [serviços geridos no Azure Marketplace.](managed-services-offers.md)