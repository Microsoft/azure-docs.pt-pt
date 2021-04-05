---
author: baanders
description: incluir ficheiro para pré-requisito de permissão na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205501"
---
## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: Requisitos de permissão

Para poder completar todos os passos deste artigo, precisa de ter um [papel na sua subscrição](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) que tenha as seguintes permissões:
* Criar e gerir recursos da Azure
* Gerir o acesso dos utilizadores aos recursos da Azure (incluindo a concessão e delegação de permissões)

As funções comuns que satisfazem este requisito são *Proprietário,* *Administrador de Conta,* ou a combinação de Administrador de Acesso ao *Utilizador* e *Colaborador.* Para uma explicação completa das funções e permissões, incluindo quais as permissões incluídas com outras funções, visite [*funções de administrador de subscrição clássicas, funções de Azure e Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do Azure RBAC.

Para ver a sua função na sua subscrição, visite a [página de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal Azure (pode utilizar este link ou procurar *subscrições* com a barra de pesquisa do portal). Procure o nome da subscrição que está a usar e veja o seu papel na coluna *My Role:*

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Vista da página subscrições no portal Azure, mostrando o utilizador como proprietário" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Se descobrir que o valor é *Contribuinte,* ou outra função que não tenha as permissões necessárias acima descritas, pode contactar o utilizador na sua subscrição que *tenha* essas permissões (como um administrador de subscrição Proprietário ou Conta) e proceder de uma das seguintes formas:
* Solicite que completem os passos deste artigo em seu nome
* Solicite que eleve o seu papel na subscrição para que você tenha as permissões para proceder a si mesmo. Se isso é apropriado depende da sua organização e do seu papel dentro dela.