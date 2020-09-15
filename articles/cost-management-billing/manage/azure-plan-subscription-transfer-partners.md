---
title: Transferir subscrições do Azure de um parceiro para outro (Pré-visualização)
description: Este artigo ajuda-o a compreender o que necessita de saber antes e depois de transferir a propriedade de faturação da sua subscrição do Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554207"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Transferir subscrições do Azure de um parceiro para outro (Pré-visualização)

Este artigo ajuda-o a compreender o que necessita de saber antes e depois de transferir a propriedade de faturação da sua subscrição do Azure. Para iniciar uma transferência de subscrição do Azure que se encontra num plano do Azure de um parceiro da Microsoft para outro, necessita de entrar em contacto com o seu parceiro. O parceiro irá enviar instruções sobre como começar. Após o processo de transferência estar concluído, a propriedade de faturação da sua subscrição fica alterada.

## <a name="user-access"></a>Acesso do utilizador

O acesso a utilizadores, grupos ou principais de serviço existentes que tenham sido atribuídos através do RBAC (controlo de acesso baseado em funções) do Azure, não é afetado durante a transição. O [RBAC do Azure](../../role-based-access-control/overview.md) ajuda-o a gerir quem tem acesso aos recursos do Azure, o que podem fazer com esses recursos e a que áreas têm acesso. O seu parceiro novo não recebe nenhum acesso de RBAC aos seus recursos através da transferência da subscrição. O seu parceiro anterior mantém o respetivo acesso RBAC.

Consequentemente, é importante que remova o acesso do RBAC do Azure para o parceiro antigo e adicione acesso para o parceiro novo. Para obter mais informações sobre como dar acesso ao seu novo parceiro, veja [O que é o RBAC do Azure (controlo de acesso baseado em funções)?](../../role-based-access-control/overview.md) Para obter mais informações sobre como remover o acesso RBAC do parceiro anterior, veja [Remover uma atribuição de função](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Além disso, o seu parceiro novo não obtém automaticamente acesso de [Administrador em Nome de (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) às suas subscrições. O AOBO é necessário para que o seu parceiro faça a gestão das subscrições do Azure em seu nome. Para obter mais informações sobre como dar acesso ao seu parceiro AOBO novo, veja [Transferir a propriedade de faturação de uma conta de subscrição do Azure para outra conta](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Parar uma transferência

Após receber a confirmação de que um pedido de transferência foi enviado, pode utilizar uma das opções seguintes, **se não quiser que a transferência continue**.

- Se o pedido ainda não tiver sido aceite pelo seu parceiro atual, poderá pedir ao seu parceiro novo para cancelar o pedido de transferência iniciado (se o estado estiver pendente).
- Peça ao parceiro atual que não execute nenhuma ação quando receber o pedido de transferência. Sem o consentimento do seu parceiro atual, a transferência não pode continuar. O pedido vai expirar.
- Pode _remover a sua relação de revendedor_ com o parceiro novo. Essa ação remove a capacidade de movimentação da sua subscrição. Isto cancela efetivamente o pedido.

Também pode procurar ajuda, comunicar dolo ou atividades suspeitas, utilizando qualquer uma das opções no site [Microsoft Legal](https://www.microsoft.com/legal/). A opção de comunicar uma preocupação encontra-se em Conformidade e ética.

## <a name="next-steps"></a>Próximos passos

- Para dar acesso RBAC ao seu novo parceiro, veja [O que é o RBAC do Azure (controlo de acesso baseado em funções)?](../../role-based-access-control/overview.md)
- Para dar acesso ao seu parceiro AOBO novo, veja [Transferir a propriedade de faturação de uma conta de subscrição do Azure para outra conta](billing-subscription-transfer.md).