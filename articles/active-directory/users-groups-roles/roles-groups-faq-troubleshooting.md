---
title: Funções de resolução de problemas atribuídas ao grupo de nuvem FAQ - Azure Ative Directory Microsoft Docs
description: Aprenda algumas perguntas comuns e dicas de resolução de problemas para atribuir funções a grupos no Azure Ative Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acbb9aa443cde8df7016d3f2a38d58002b98dcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317400"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Resolver problemas de funções atribuídas a grupos de cloud

Aqui estão algumas perguntas comuns e dicas de resolução de problemas para atribuir funções a grupos no Azure Ative Directory (Azure AD).

**Q:** Sou administrador de grupos, mas não vejo que os papéis da AD do **Azure possam ser atribuídos à** mudança de grupo.

**A:** Apenas administradores de funções privilegiadas ou administradores globais podem criar um grupo que seja elegível para a atribuição de funções. Apenas os utilizadores nessas funções vêem este controlo.

**Q:** Quem pode modificar a adesão de grupos atribuídos a funções de AD Azure?

**A:** Por padrão, apenas o Administrador privilegiado e o Administrador Global gerem a adesão a um grupo atribuível a funções, mas pode delegar a gestão de grupos atribuíveis por funções adicionando os proprietários do grupo.

**P:** Sou administrador de helpdesk na minha organização mas não posso atualizar a palavra-passe de um utilizador que é um Leitor de Diretório. Porque é que isto acontece?

**R**: O utilizador pode ter obtido o Directory Reader através de um grupo atribuível a funções. Todos os membros e proprietários de um grupo atribuível a papéis estão protegidos. Apenas os utilizadores nas funções de Administrador de Autenticação Privilegiada ou Administrador Global podem redefinir credenciais para um utilizador protegido.

**Q:** Não posso atualizar a palavra-passe de um utilizador. Não têm nenhum papel privilegiado mais elevado atribuído. Porque é que está a acontecer?

**A:** O utilizador pode ser proprietário de um grupo atribuível a funções. Protegemos os proprietários de grupos atribuíveis para evitar a elevação do privilégio. Um exemplo pode ser se um grupo Contoso_Security_Admins for atribuído ao papel de administrador de Segurança, onde Bob é o proprietário do grupo e Alice é administradora de passwords na organização. Se esta proteção não estivesse presente, a Alice podia repor as credenciais do Bob e assumir a sua identidade. Depois disso, Alice poderia adicionar-se ou qualquer um ao grupo Contoso_Security_Admins grupo para se tornar um administrador de segurança na organização. Para saber se um utilizador é proprietário de um grupo, obtenha a lista de objetos próprios desse utilizador e veja se algum dos grupos tem o ToRole assignable definido para ser verdadeiro. Se sim, então esse utilizador está protegido e o comportamento é por design. Consulte estas documentações para obter objetos próprios:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [Lista de proprietáriasObjects](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**Q:** Posso criar uma revisão de acesso a grupos que podem ser atribuídos a funções AZure AD (especificamente, grupos com propriedade ToRole é assignable definido para verdade)?  

**A:** Sim, tu podes. Se estiver na versão mais recente do Access Review, então os seus revisores são direcionados para o My Access por padrão, e apenas os administradores globais podem criar revisões de acesso em grupos atribuíveis por funções. No entanto, se estiver na versão mais antiga do Access Review, os seus revisores são direcionados para o Painel de Acesso por padrão, e tanto os administradores globais como o administrador do Utilizador podem criar revisões de acesso em grupos atribuíveis por funções. A nova experiência será lançada a todos os clientes no dia 28 de julho de 2020, mas se quiser atualizar mais cedo, faça um pedido para [Azure AD Access Reviews - Experiência atualizada de revisor no My Access Signup](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**Q:** Posso criar um pacote de acesso e colocar grupos que podem ser atribuídos a funções de AZure AD nele?

**A:** Sim, tu podes. O Administrador Global e o Administrador de Utilizador têm o poder de colocar qualquer grupo num pacote de acesso. Nada muda para o Administrador Global, mas há uma ligeira alteração nas permissões de função de administrador do utilizador. Para colocar um grupo atribuível a funções num pacote de acesso, tem de ser administrador do utilizador e também proprietário do grupo que atribui funções. Aqui está a tabela completa mostrando quem pode criar pacote de acesso na Gestão de Licenças empresariais:

Papel de diretório AD Azure | Papel de gestão de direitos | Pode adicionar grupo de segurança\* | Pode adicionar o grupo Microsoft 365\* | Pode adicionar app | Pode adicionar site SharePoint Online
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Administrador global | n/a | ✔️ | ✔️ | ✔️  | ✔️
Administrador de utilizadores  | n/a  | ✔️  | ✔️  | ✔️
Administrador do Intune | Proprietário do catálogo | ✔️  | ✔️  | &nbsp;  | &nbsp;
Administrador do Exchange  | Proprietário do catálogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrador de serviço de equipas | Proprietário do catálogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrador do SharePoint | Proprietário do catálogo | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Administrador de aplicação | Proprietário do catálogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Administrador de aplicação em nuvem | Proprietário do catálogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Utilizador | Proprietário do catálogo | Só se o dono do grupo | Só se o dono do grupo | Só se o proprietário da aplicação  | &nbsp;

\*O grupo não é atribuível; isto é, isAssignableToRole = falso. Se um grupo é atribuível a funções, então a pessoa que cria o pacote de acesso também deve ser proprietária do grupo que atribui funções.

**Q:** Não consigo encontrar a opção "Remover a atribuição" em "Papéis Atribuídos". Como posso eliminar a atribuição de funções a um utilizador?

**A:** Esta resposta aplica-se apenas às organizações Azure AD Premium P1.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o **Azure Ative Directory**.
1. Selecione os utilizadores e abra um perfil de utilizador.
1. Selecione **funções atribuídas**.
1. Selecione o ícone de engrenagem. Abre-se um painel que pode dar esta informação. Há um botão "Remover" ao lado de missões diretas. Para remover a atribuição de funções indiretas, remova o utilizador do grupo a quem foi atribuído o papel.

**Q:** Como vejo todos os grupos que atribuem funções?

**A:** Siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o **Azure Ative Directory**.
1. Selecione **Grupos**  >  **Todos os grupos**.
1. **Selecione Adicionar filtros**.
1. Filtrar para **função atribuível**.

**Q:** Como sei qual é o papel atribuído a um diretor direta e indiretamente?

**A:** Siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o **Azure Ative Directory**.
1. Selecione os utilizadores e abra um perfil de utilizador.
1. Selecione **funções atribuídas**e, em seguida,:

    - Em organizações licenciadas Azure AD Premium P1: Selecione o ícone de engrenagem. Abre-se um painel que pode dar esta informação.
    - Em Azure AD Premium P2 organizações licenciadas: Você encontrará informações diretas e herdadas de licença na coluna **Membership.**

**Q:** Por que aplicamos a criação de um novo grupo de nuvem para atribuí-lo ao papel?  

**A:** Se atribuir um grupo existente a um papel, o proprietário do grupo existente poderá adicionar outros membros a este grupo sem que os novos membros percebam que terão o papel. Como os grupos atribuíveis são poderosos, estamos a impor muitas restrições para os proteger. Não quer alterações no grupo que seriam surpreendentes para a pessoa que gere o grupo.

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Criar um grupo ao qual se pode atribuir funções](roles-groups-create-eligible.md)