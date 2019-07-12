---
title: Ver e gerir os clientes e os recursos delegados no portal do Azure
description: Como um fornecedor de serviços a utilizar o Azure delegadas a gestão de recursos, pode ver todos os seus recursos de cliente delegado e as subscrições ao aceder aos meus clientes no portal do Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809856"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ver e gerir os clientes e os recursos delegados

Fornecedores de serviços usando [Azure delegadas a gestão de recursos](../concepts/azure-delegated-resource-management.md) pode utilizar o **meus clientes** página no [portal do Azure](https://portal.azure.com) para ver os recursos de cliente delegado e assinaturas. Enquanto faremos referência a fornecedores de serviços e clientes aqui, as empresas a gerenciar vários inquilinos podem utilizar o mesmo processo para consolidar sua experiência de gestão.

Para aceder a **meus clientes** página no portal do Azure, selecione **todos os serviços**, em seguida, procure **meus clientes** e selecioná-lo. Também pode encontrá-lo ao introduzir "Meus clientes" na caixa de pesquisa junto à parte superior do portal do Azure.

Tenha em atenção que o **meus clientes** página apenas apresenta informações sobre os clientes que ter delegado subscrições ou grupos de recursos. Se trabalha com outros clientes (como por meio do [programa Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), não verá informações sobre esses clientes aqui, a menos que integre seus recursos para o delegado gestão de recursos.

> [!NOTE]
> Os clientes podem ver informações sobre os fornecedores de serviços ao navegar para **fornecedores de serviços** no portal do Azure. Para mais informações, veja [ver e gerir fornecedores de serviços](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Ver e gerir os detalhes do cliente

Para ver os detalhes do cliente, selecione **os clientes** no lado esquerdo do **meus clientes** página.

Para cada cliente, verá o nome do cliente, o ID de cliente (ID de inquilino) e a oferta associada com o envolvimento. Na **delegações** coluna, verá o número de subscrições de delegado e/ou o número de grupos de recurso delegado.

Filtros na parte superior da página permitem-lhe ordenar e agrupar as informações de clientes ou filtrar por clientes específicos, ofertas ou palavras-chave.

Pode ver as seguintes informações a partir desta página:

- Para ver todas as subscrições, oferece, e as delegações associadas ao cliente, selecione o nome do cliente.
- Para ver mais detalhes sobre uma oferta e seu delegações, selecione o nome da oferta.
- Para ver mais detalhes sobre acrolecess atribuições para as subscrições de delegados ou grupos de recursos, selecione a entrada no **delegações** coluna.

## <a name="view-delegations"></a>Delegações do Vista

As delegações mostram o grupo de recursos/subscrição que tem sido delegado, juntamente com os utilizadores e permissões que tem acesso à mesma. Para ver estas informações, selecione **delegações** no lado esquerdo do **meus clientes** página.

Filtros na parte superior da página permitem-lhe ordenar e agrupar as informações de atribuição de acesso ou filtro por clientes específicos, ofertas ou palavras-chave.

Os utilizadores e permissões associadas a cada delegação aparecem no **atribuições de funções** coluna. Pode selecionar cada entrada para ver a lista completa de utilizadores, grupos e os principais de serviço que foi concedidos acesso à subscrição ou grupo de recursos. A partir daí, pode selecionar um determinado utilizador, grupo ou nome principal de serviço para obter mais detalhes.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Funciona no contexto de uma assinatura do delegado

Pode trabalhar diretamente no contexto de uma assinatura do delegado no portal do Azure, sem mudar de diretório que está a trabalhar. Para tal:

1. Selecione o **diretório + subscrição** ícone junto à parte superior do portal do Azure.
2. Na **subscrição Global** filtrar, certifique-se de que apenas a caixa para essa subscrição delegada é selecionada. Pode utilizar o **atual + diretórios delegados** caixa pendente para mostrar apenas as subscrições dentro de um diretório específico. (Não utilize o **trocar diretório** opção, uma vez que as alterações feitas no diretório ao qual tem sessão iniciada.)

Se, em seguida, aceder a um serviço que suporta [experiências de gestão entre inquilinos](../concepts/cross-tenant-management-experience.md), o serviço será predefinido para o contexto da subscrição do delegado que selecionou. Pode alterá-lo ao seguir os passos acima e a verificar se o **Selecionar tudo** caixa (ou ao escolher uma ou mais subscrições para funcionar em vez disso).

> [!NOTE]
> Se tiver sido concedido acesso a um ou mais grupos de recursos, em vez de acesso a uma subscrição completa, pode selecionar a subscrição a que pertence esse grupo de recursos. Em seguida, irá trabalhar no contexto dessa subscrição, mas só será capaz de acessar os grupos de recursos designado.

Também pode aceder a funcionalidades relacionadas à delegado subscrições ou grupos de recursos de dentro de serviços que suportam as experiências de gestão entre inquilinos ao selecionar o subscrição ou grupo de recursos de dentro desse serviço.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [experiências de gestão entre inquilinos](../concepts/cross-tenant-management-experience.md).
- Saiba como seus clientes podem [ver e gerir fornecedores de serviços](view-manage-service-providers.md) indo até **fornecedores de serviços** no portal do Azure.
