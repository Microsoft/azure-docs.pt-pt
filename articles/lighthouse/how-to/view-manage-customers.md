---
title: Exibir e gerenciar clientes e recursos delegados
description: Como um provedor de serviços usando o gerenciamento de recursos delegado do Azure, você pode exibir todos os recursos e assinaturas do cliente delegado Acessando meus clientes na portal do Azure.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543431"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Exibir e gerenciar clientes e recursos delegados

Provedores de serviço que usam o [Gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md) podem usar a página **meus clientes** no [portal do Azure](https://portal.azure.com) para exibir recursos e assinaturas delegados do cliente. Embora possamos nos referir aos provedores de serviços e clientes aqui, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **meus clientes** no portal do Azure, selecione **todos os serviços**e, em seguida, pesquise **meus clientes** e selecione-o. Você também pode encontrá-lo inserindo "meus clientes" na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a seção principais **clientes** da página **meus clientes** mostra apenas informações sobre os clientes que delegaram assinaturas ou grupos de recursos. Se você trabalhar com outros clientes (por exemplo, por meio do [programa provedor de soluções na nuvem](https://docs.microsoft.com/partner-center/csp-overview), você não verá informações sobre esses clientes na seção **clientes** , a menos que tenha integrado seus recursos para o gerenciamento de recursos delegado do Azure.

Inferior na página, uma seção separada chamada **provedor de soluções de nuvem (versão prévia)** mostra informações de cobrança e recursos para seus clientes CSP que [assinaram o Microsoft Customer Agreement (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e estão [no plano do Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Para obter mais informações, consulte Introdução [à sua conta de cobrança do Microsoft Partner Agreement](../../billing/mpa-overview.md). Observe que esses clientes do CSP aparecem nesta seção se você também os tiver integrado para o gerenciamento de recursos delegado do Azure. Da mesma forma, um cliente CSP não precisa aparecer na seção **provedor de soluções na nuvem (versão prévia)** de **meus clientes** para que você possa integrá-los para o gerenciamento de recursos delegado do Azure.

> [!NOTE]
> Seus clientes podem exibir informações sobre provedores de serviço navegando até **provedores de serviço** no portal do Azure. Para obter mais informações, consulte [Exibir e gerenciar provedores de serviços](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Exibir e gerenciar detalhes do cliente

Para exibir os detalhes do cliente, selecione **clientes** no lado esquerdo da página **meus clientes** .

Para cada cliente, você verá o nome do cliente, a ID do cliente (ID do locatário) e a oferta associada ao envolvimento. Na coluna **delegações** , você verá o número de assinaturas delegadas e/ou o número de grupos de recursos delegados.

> [!IMPORTANT]
> Para ver uma delegação, os usuários devem ter recebido a função [leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclua acesso de leitor) no processo de integração.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de cliente ou filtre por clientes, ofertas ou palavras-chave específicas.

Você pode exibir as seguintes informações desta página:

- Para ver todas as assinaturas, ofertas e delegações associadas a um cliente, selecione o nome do cliente.
- Para ver mais detalhes sobre uma oferta e suas delegações, selecione o nome da oferta.
- Para exibir mais detalhes sobre as atribuições de função para assinaturas delegadas ou grupos de recursos, selecione a entrada na coluna **delegações** .

## <a name="view-and-manage-delegations"></a>Exibir e gerenciar delegações

As delegações mostram a assinatura/o grupo de recursos que foi delegado, junto com os usuários e permissões que têm acesso a ele. Para exibir essas informações, selecione **delegações** no lado esquerdo da página **meus clientes** .

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de atribuição de acesso ou filtre por clientes, ofertas ou palavras-chave específicas.

### <a name="view-role-assignments"></a>Ver atribuições de funções

Os usuários e as permissões associados a cada delegação aparecem na coluna **atribuições de função** . É possível selecionar cada entrada para exibir a lista completa de usuários, grupos e entidades de serviço que receberam acesso à assinatura ou ao grupo de recursos. A partir daí, você pode selecionar um determinado usuário, grupo ou nome da entidade de serviço para obter mais detalhes.

### <a name="remove-delegations"></a>Remover delegações

Se você tiver incluído usuários com a [atribuição de registro de serviços gerenciados excluir função](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao integrar um cliente para o gerenciamento de recursos delegado do Azure, esses usuários poderão remover uma delegação selecionando o ícone de lixeira que aparece na linha para essa delegação. Quando eles fizerem isso, nenhum usuário no locatário do provedor de serviços poderá acessar os recursos que foram previamente delegados.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabalhar no contexto de uma assinatura delegada

Você pode trabalhar diretamente no contexto de uma assinatura delegada dentro do portal do Azure, sem alternar o diretório no qual está trabalhando. Para tal:

1. Selecione o ícone **diretório + assinatura** próximo à parte superior da portal do Azure.
2. No filtro de **assinatura global** , certifique-se de que somente a caixa para essa assinatura delegada esteja selecionada. Você pode usar a caixa suspensa de **diretórios + delegados atuais** para mostrar apenas as assinaturas em um diretório específico. (Não use a opção de **diretório switch** , pois isso altera o diretório no qual você está conectado.)

Se você acessar um serviço que dá suporte a [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md), o serviço usará como padrão o contexto da assinatura delegada que você selecionou. Você pode alterar isso seguindo as etapas acima e marcando a caixa **selecionar tudo** (ou escolhendo uma ou mais assinaturas para trabalhar em vez disso).

> [!NOTE]
> Se tiver recebido acesso a um ou mais grupos de recursos, em vez de acessar uma assinatura inteira, você poderá selecionar a assinatura à qual o grupo de recursos pertence. Em seguida, você trabalhará no contexto dessa assinatura, mas só será capaz de acessar os grupos de recursos designados.

Você também pode acessar a funcionalidade relacionada a assinaturas delegadas ou grupos de recursos de dentro de serviços que dão suporte a experiências de gerenciamento entre locatários selecionando a assinatura ou o grupo de recursos de dentro desse serviço.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- Saiba como os clientes podem [Exibir e gerenciar provedores](view-manage-service-providers.md) de serviços acessando **provedores de serviços** no portal do Azure.
