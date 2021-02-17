---
title: Transferir subscrições num plano do Azure de um parceiro para outro (Pré-visualização)
description: Este artigo ajuda-o a compreender o que necessita de saber antes e depois de transferir a propriedade de faturação da sua subscrição do Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: d234227de98beeed6898697fb6ed8e0865546e16
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555845"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Transferir subscrições num plano do Azure de um parceiro para outro (Pré-visualização)

Este artigo ajuda-o a compreender o que necessita de saber antes e depois de transferir a propriedade de faturação da sua subscrição do Azure. Para iniciar uma transferência de subscrição do Azure que se encontra num plano do Azure de um parceiro da Microsoft para outro, necessita de entrar em contacto com o seu parceiro. O parceiro irá enviar instruções sobre como começar. Após o processo de transferência estar concluído, a propriedade de faturação da sua subscrição fica alterada.

## <a name="user-access"></a>Acesso do utilizador

O acesso de utilizadores, grupos ou principais de serviço existentes que tenham sido atribuídos através do controlo de acesso baseado em funções do Azure (RBAC do Azure), não é afetado durante a transição. O [RBAC do Azure](../../role-based-access-control/overview.md) ajuda-o a gerir quem tem acesso aos recursos do Azure, o que podem fazer com esses recursos e a que áreas têm acesso. O seu parceiro novo não recebe nenhum acesso de RBAC do Azure aos seus recursos através da transferência da subscrição. O seu parceiro anterior mantém o respetivo acesso RBAC do Azure.

Consequentemente, é importante que remova o acesso do RBAC do Azure para o parceiro antigo e adicione acesso para o parceiro novo. Para obter mais informações sobre como dar acesso ao seu novo parceiro, veja [O que é o RBAC do Azure (controlo de acesso baseado em funções)?](../../role-based-access-control/overview.md) Para obter mais informações sobre a remoção do acesso do seu parceiro anterior, o RBAC do seu parceiro anterior, consulte [as atribuições de funções remove Azure](../../role-based-access-control/role-assignments-remove.md).

Além disso, o seu parceiro novo não obtém automaticamente acesso de [Administrador em Nome de (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) às suas subscrições. O AOBO é necessário para que o seu parceiro faça a gestão das subscrições do Azure em seu nome. Para obter mais informações sobre os privilégios do Azure, veja [Obter permissões para gerir o serviço ou a subscrição de um cliente](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Parar uma transferência

Após receber a confirmação de que um pedido de transferência foi enviado, pode utilizar uma das opções seguintes, **se não quiser que a transferência continue**.

- Se o pedido ainda não tiver sido aceite pelo seu parceiro atual, poderá pedir ao seu parceiro novo para cancelar o pedido de transferência iniciado (se o estado estiver pendente).
- Peça ao parceiro atual que não execute nenhuma ação quando receber o pedido de transferência. Sem o consentimento do seu parceiro atual, a transferência não pode continuar. O pedido vai expirar.
- Pode _remover a sua relação de revendedor_ com o parceiro novo. Essa ação remove a capacidade de movimentação da sua subscrição. Isto cancela efetivamente o pedido.

Também pode procurar ajuda, comunicar dolo ou atividades suspeitas, utilizando qualquer uma das opções no site [Microsoft Legal](https://www.microsoft.com/legal/). A opção de comunicar uma preocupação encontra-se em Conformidade e ética.

## <a name="next-steps"></a>Próximos passos

- Para dar ao seu parceiro novo acesso RBAC do Azure, veja [O que é o controlo de acesso baseado em funções do Azure (RBAC do Azure)?](../../role-based-access-control/overview.md)
- [Obter permissões para gerir um serviço ou uma subscrição de um cliente](/partner-center/customers-revoke-admin-privileges).