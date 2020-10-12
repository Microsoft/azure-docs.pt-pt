---
author: baanders
description: incluir arquivo para visão geral de passos e pré-requisito de permissão na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009672"
---
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com o poder de gerir tanto os recursos como o acesso do utilizador na subscrição do Azure. Embora alguns passos possam ser concluídos com permissões mais baixas, a cooperação de alguém com estas permissões será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para um novo exemplo de Azure Digital Twins consiste em três partes:
1. **Criar o caso**
2. **Configuração das permissões**de acesso ao utilizador : Os utilizadores do Azure precisam de ter o papel *de Azure Digital Twins Owner (Preview)* na instância Azure Digital Twins para poderem geri-lo e os seus dados. Neste passo, você como Proprietário/administrador da subscrição Azure irá atribuir esta função à pessoa que irá gerir a sua instância Azure Digital Twins. Pode ser você ou outra pessoa na sua organização.
3. **Configurar permissões de acesso para aplicações de clientes**: É comum escrever uma aplicação de cliente que será usada para interagir com o seu exemplo. Para que essa aplicação de clientes aceda aos seus Azure Digital Twins, é necessário configurar um *registo de aplicações* no [Azure Ative Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que a aplicação do cliente utilizará para autenticar o caso.

Para prosseguir, você precisará de uma assinatura Azure. Pode configurar um de graça [aqui.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

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