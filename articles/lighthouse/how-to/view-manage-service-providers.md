---
title: Ver e gerir fornecedores de serviços
description: Os clientes podem usar a página provedores de serviço no portal do Azure para exibir informações sobre provedores de serviço, ofertas de provedor de serviço e recursos delegados.
ms.date: 11/15/2019
ms.topic: overview
ms.openlocfilehash: baa5a77b481779ab511ff473aacda5d40e0d771c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132172"
---
# <a name="view-and-manage-service-providers"></a>Ver e gerir fornecedores de serviços

Os clientes podem usar a página **provedores de serviço** no [portal do Azure](https://portal.azure.com) para exibir informações sobre provedores de serviço e ofertas de provedor de serviço, delegar recursos específicos por meio [do gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md)e comprar ofertas adicionais de provedor de serviços. Embora possamos nos referir aos provedores de serviços e clientes aqui, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **provedores de serviço** no portal do Azure, o cliente pode selecionar **todos os serviços**e, em seguida, Pesquisar provedores de **Serviços** e selecioná-lo. Eles também podem encontrá-lo inserindo "provedores de serviço" na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a página **provedores de serviço** mostra apenas informações sobre os provedores de serviço que têm acesso às assinaturas ou aos grupos de recursos do cliente por meio do gerenciamento de recursos delegado do Azure. Se um cliente trabalha com provedores de serviço adicionais que não usam o gerenciamento de recursos delegado do Azure para acessar os recursos do cliente, as informações sobre esses provedores de serviços não são mostradas aqui.

> [!NOTE]
> Os provedores de serviços podem exibir informações sobre seus clientes navegando até **meus clientes** no portal do Azure. Para obter mais informações, consulte [Exibir e gerenciar clientes e recursos delegados](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Exibir detalhes do provedor de serviços

Para exibir informações sobre os provedores de serviços com os quais um cliente está trabalhando, eles podem selecionar **ofertas de provedor** no lado esquerdo da página **provedores de serviços** .

Para cada oferta de provedor de serviços, o cliente verá o nome do provedor de serviços e a oferta associada a ele, juntamente com o nome que o cliente inseriu durante o processo de integração.

Na coluna **delegações** , o cliente vê quantas assinaturas e/ou grupos de recursos foram delegados para o provedor de serviços para essa oferta. O provedor de serviços poderá acessar e gerenciar essas assinaturas e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="delegate-resources"></a>Delegar recursos

Para que um provedor de serviços possa acessar e gerenciar os recursos de um cliente, eles devem ser delegados. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, verá uma observação na parte superior da seção **ofertas do provedor** . Isso permite que o cliente saiba que precisa tomar medidas antes que o provedor de serviços possa acessar qualquer um dos recursos do cliente.

Para delegar assinaturas ou grupos de recursos:

1. Marque a caixa da linha que contém o provedor de serviços, a oferta e o nome. Em seguida, selecione **delegar recursos** na parte superior da tela.
1. Na seção **detalhes da oferta** da página **delegar recursos** , examine os detalhes sobre o provedor de serviços e a oferta. Para examinar as atribuições de função da oferta, selecione **clique aqui para ver os detalhes da oferta selecionada**.
1. Na seção **delegar** , selecione **delegar assinaturas** ou **delegar grupos de recursos**.
1. Escolha as assinaturas e/ou os grupos de recursos que você deseja delegar para esta oferta e, em seguida, selecione **Adicionar**.
1. Marque a caixa de seleção na parte inferior da página para confirmar que você deseja conceder a esse provedor de serviços acesso aos recursos que você selecionou e, em seguida, selecione **delegar**.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas de provedor de serviço

Um cliente pode adicionar uma nova oferta de provedor de serviços na página de **ofertas de provedor** selecionando **Adicionar oferta**. O provedor de serviços deve ter publicado uma oferta para este cliente. O cliente pode selecionar essa oferta na tela de **ofertas particulares** e, em seguida, selecionar **criar**.

Se o cliente quiser remover uma oferta de provedor de serviços, ele poderá selecionar o ícone de lixeira na linha dessa oferta. Depois de confirmar a exclusão, esse provedor de serviços não terá mais acesso aos recursos do cliente que antes eram delegados para essa oferta.

## <a name="update-service-provider-offers"></a>Atualizar ofertas do provedor de serviços

Depois que um cliente tiver adicionado uma oferta, um provedor de serviços poderá publicar uma versão atualizada da mesma oferta no Azure Marketplace. Por exemplo, eles podem querer adicionar uma nova definição de função. Se uma nova versão da oferta tiver sido publicada, a página de **ofertas do provedor** mostrará um ícone de "atualização" na linha dessa oferta. O cliente pode selecionar esse ícone para ver as diferenças entre a versão atual da oferta e a nova. 

Depois de revisar as alterações, o cliente pode optar por atualizar para a nova versão. Depois disso, as autorizações e outras configurações especificadas na nova versão serão aplicadas a quaisquer assinaturas e/ou grupos de recursos que tenham sido delegados para essa oferta.

## <a name="view-delegations"></a>Exibir delegações

As delegações representam as atribuições de função que concedem permissões ao provedor de serviços para os recursos que um cliente delegou. Para exibir essas informações, selecione **delegações** no lado esquerdo da página **provedores de serviços** .

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de delegação ou filtre por clientes, ofertas ou palavras-chave específicas.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Azure Lighthouse](../overview.md).
- Saiba como os provedores de serviços podem [Exibir e gerenciar clientes](view-manage-customers.md) acessando **meus clientes** na portal do Azure.