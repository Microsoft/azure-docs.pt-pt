---
title: Ver e gerir fornecedores de serviços no portal do Azure
description: Os clientes podem utilizar a página de provedores de serviço no portal do Azure para ver informações sobre fornecedores de serviços, ofertas de fornecedor de serviço e recursos delegados.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a45458e7417bba058522fdc0dbc34fee04ad9af8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809842"
---
# <a name="view-and-manage-service-providers"></a>Ver e gerir fornecedores de serviços

Os clientes podem utilizar o **fornecedores de serviços** página no [portal do Azure](https://portal.azure.com) para ver informações sobre fornecedores de serviços e ofertas de fornecedor de serviço, recursos específicos por meio de delegar [Azure gestão de recursos de delegado](../concepts/azure-delegated-resource-management.md)e comprar ofertas de fornecedor de serviço adicional. Enquanto faremos referência a fornecedores de serviços e clientes aqui, as empresas a gerenciar vários inquilinos podem utilizar o mesmo processo para consolidar sua experiência de gestão.

Para o acesso a **fornecedores de serviços** página no portal do Azure, o cliente pode selecionar **todos os serviços**, em seguida, procure **fornecedores de serviços** e selecioná-lo. Eles podem também encontrá-lo ao introduzir "Provedores de serviço" na caixa de pesquisa junto à parte superior do portal do Azure.

Tenha em atenção que o **fornecedores de serviços** página apenas apresenta informações sobre os fornecedores de serviços que têm acesso a subscrições ou grupos de recursos através da gestão de recursos do Azure de delegado do cliente. Se um cliente funciona com fornecedores de serviços adicionais que não utilizam a gestão de recursos do Azure de delegado para acessar os recursos do cliente, informações sobre esses provedores de serviço não é mostrada aqui.

> [!NOTE]
> Fornecedores de serviços podem ver informações sobre os clientes ao navegar até **meus clientes** no portal do Azure. Para mais informações, veja [ver e gerir os clientes e os recursos delegados](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Ver detalhes do fornecedor de serviço

Para ver informações sobre o provider(s) de serviço que está a trabalhar com um cliente, pode selecionar **fornecedor oferece** no lado esquerdo do **fornecedores de serviços** página.

Para cada oferta do fornecedor de serviço, o cliente irá ver o nome do fornecedor de serviços e a oferta associada, juntamente com o nome que o cliente introduzido durante o processo de integração.

Na **delegações** coluna, o cliente vê foram delegados a quantas subscrições de e/ou grupos de recursos para o fornecedor de serviços para essa oferta. O fornecedor de serviços será capaz de aceder e gerir estas subscrições e/ou grupos de recursos, de acordo com os níveis de acesso especificados a oferta.

## <a name="delegate-resources"></a>Delegue recursos

Antes de um fornecedor de serviços pode aceder e gerir recursos de um cliente, eles devem ser delegados. Se um cliente aceitou uma oferta, mas ainda não tiver delegado todos os recursos, verá uma nota na parte superior a **fornecedor oferece** secção. Isso permite que o cliente sabe que têm de tomar uma medida antes do fornecedor de serviços pode aceder a qualquer um dos recursos do cliente.

Para delegar as subscrições ou grupos de recursos:

1. Selecione a caixa para a linha que contém o fornecedor de serviços, a oferta e o nome. Em seguida, selecione **delegar recursos** na parte superior do ecrã.
1. No **detalhes da oferta** secção a **delegar recursos** página, reveja os detalhes sobre o fornecedor de serviços e oferecem. Para rever atribuições de funções para a oferta, selecione **clique aqui para ver os detalhes da oferta selecionada**.
1. Na **delegado** secção, selecione **delegar subscrições** ou **delegar a grupos de recursos**.
1. Escolha as subscrições e/ou grupos de recursos que gostaria de delegar para esta oferta, em seguida, selecione **adicionar**.
1. Selecione a caixa de verificação na parte inferior da página para confirmar que pretende conceder este fornecedor de serviço acesso aos recursos que selecionou, em seguida, selecione **delegado**.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover as ofertas de fornecedor do serviço

Um cliente pode adicionar uma nova oferta do fornecedor de serviço do **fornecedor oferece** página ao selecionar **adicionar oferta**. O fornecedor de serviços tem de ter publicado uma oferta para este cliente. O cliente, em seguida, pode selecionar essa oferta desde o **ofertas privadas** ecrã e, em seguida, selecione **criar**.

Se o cliente deseja remover uma oferta de fornecedor de serviço, pode selecionar o ícone do caixote do lixo na linha para essa oferta. Depois de confirmar a eliminação, esse fornecedor de serviços já não terá acesso aos recursos do cliente que foram anteriormente delegada para essa oferta.

## <a name="view-delegations"></a>Delegações do Vista

As delegações representam as atribuições de funções que concedem permissões para o fornecedor de serviços para os recursos de que um cliente tem delegada. Para ver estas informações, selecione **delegações** no lado esquerdo do **fornecedores de serviços** página.

Filtros na parte superior da página permitem-lhe ordenar e agrupar as informações de delegação ou filtro por clientes específicos, ofertas ou palavras-chave.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Azure Lighthouse](../overview.md).
- Saiba como fornecedores de serviços podem [ver e gerir os clientes](view-manage-customers.md) indo até **meus clientes** no portal do Azure.