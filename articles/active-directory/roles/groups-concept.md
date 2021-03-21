---
title: Utilize grupos de nuvem para gerir atribuições de funções no Azure Ative Directory | Microsoft Docs
description: Pré-visualizar funções Azure AD personalizadas para delegar a gestão de identidade. Gerir atribuições de funções Azure no portal Azure, PowerShell ou Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5df7088551e7e7f616077342b762baca179f8640
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123365"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Utilize grupos de nuvem para gerir atribuições de funções no Azure Ative Directory (pré-visualização)

O Azure Ative Directory (Azure AD) está a introduzir uma pré-visualização pública na qual pode atribuir um grupo de nuvem a funções incorporadas AD AZure. Com esta funcionalidade, pode utilizar grupos para conceder acesso administrativo em Azure AD com o mínimo esforço dos seus administradores de funções Global e Privileged.

Considere este exemplo: a Contoso contratou pessoas através de geografias para gerir e redefinir senhas para funcionários da sua organização Azure AD. Em vez de pedir a um administrador privilegiado ou administrador global para atribuir o papel de administrador helpdesk a cada pessoa individualmente, eles podem criar um grupo Contoso_Helpdesk_Administrators e atribuí-lo ao papel. Quando as pessoas se juntam ao grupo, são atribuídas indiretamente ao papel. O seu fluxo de trabalho de governação existente pode então cuidar do processo de aprovação e auditoria da adesão do grupo para garantir que apenas os utilizadores legítimos são membros do grupo e, portanto, são designados para o papel de administração helpdesk.

## <a name="how-this-feature-works"></a>Como funciona esta funcionalidade

Crie um novo Microsoft 365 ou um grupo de segurança com a propriedade "isAssignableToRole" definida como "verdadeira". Você também pode ativar esta propriedade ao criar um grupo no portal Azure, ligando **as funções AD Azure pode ser atribuída ao grupo**. De qualquer forma, pode atribuir o grupo a uma ou mais funções AD do Azure da mesma forma que atribui funções aos utilizadores. Um máximo de 250 grupos atribuíveis por funções pode ser criado numa única organização AZure AD (inquilino).

Se não quiser que os membros do grupo tenham acesso permanente ao papel, pode utilizar a Azure AD Gestão de Identidade Privilegiada. Atribua um grupo como membro elegível de um papel de AD Azure. Cada membro do grupo é então elegível para ter a sua atribuição ativada para o papel a que o grupo é atribuído. Podem então ativar a sua função por uma duração fixa.

> [!Note]
> Você deve estar na versão atualizada de Gestão de Identidade Privilegiada para ser capaz de atribuir um grupo para papel AZure AD via PIM. Você pode estar na versão mais antiga da PIM porque a sua organização Azure AD aproveita a API de Gestão de Identidade Privilegiada. Por favor, contacte o pseudónimo pim_preview@microsoft.com para mover a sua organização e atualizar a sua API. Saiba mais em [funções e funcionalidades da AD Azure em PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Por que impomos a criação de um grupo especial para atribuí-lo a um papel

Se um grupo for atribuído a um papel, qualquer administrador de TI que possa gerir a adesão ao grupo também poderia gerir indiretamente a adesão a esse papel. Por exemplo, assuma que um grupo Contoso_User_Administrators é atribuído à função de administração da conta do Utilizador. Um administrador da Exchange que possa modificar a adesão ao grupo poderia adicionar-se ao grupo Contoso_User_Administrators e, dessa forma, tornar-se um administrador de conta de utilizador. Como pode ver, um administrador pode elevar o seu privilégio de uma forma que não pretendia.

O Azure AD permite-lhe proteger um grupo atribuído a um papel usando uma nova propriedade chamada isAssignableToRole para grupos. Apenas grupos de nuvem que tinham a propriedade isAssignableToRole definida como "verdadeira" no momento da criação podem ser atribuídos a um papel. Esta propriedade é imutável; uma vez que um grupo é criado com esta propriedade definida como 'verdadeira', não pode ser alterada. Não se pode definir a propriedade num grupo existente.
Desenhamos como os grupos são atribuídos a papéis para evitar que este tipo de potencial violação aconteça:

- Apenas administradores globais e administradores de funções privilegiadas podem criar um grupo atribuível a papéis (com a propriedade "isAssignableToRole" ativada).
- Não pode ser um grupo dinâmico AZure AD; isto é, deve ter um tipo de membro de "Atribuído". A população automatizada de grupos dinâmicos poderia levar a que uma conta indesejada fosse adicionada ao grupo e, assim, atribuída ao papel.
- Por padrão, apenas administradores globais e administradores privilegiados podem gerir a adesão a um grupo atribuível a funções, mas pode delegar a gestão de grupos atribuíveis por funções adicionando os proprietários do grupo.
- Para evitar a elevação do privilégio, as credenciais dos membros e proprietários de um grupo atribuível apenas podem ser alterados por um administrador de Autenticação Privilegiada ou por um administrador global.
- Sem nidificação. Um grupo não pode ser adicionado como membro de um grupo atribuível.

## <a name="limitations"></a>Limitações

Os seguintes cenários não são apoiados neste momento:  

- Atribuir grupos no local a funções AD do Azure (incorporado ou personalizado)

## <a name="known-issues"></a>Problemas conhecidos

- *Clientes licenciados Azure AD P2 apenas*: Não atribua um grupo como Ative a um papel através da Azure AD e da Gestão de Identidade Privilegiada (PIM). Especificamente, não atribua um papel a um grupo atribuível a papéis quando está a ser criado *e* atribua um papel ao grupo usando PIM mais tarde. Isto levará a problemas em que os utilizadores não podem ver as suas atribuições de papel ativa no PIM, bem como a incapacidade de remover essa atribuição pim. As atribuições elegíveis não são afetadas neste cenário. Se tentar fazer esta tarefa, poderá ver comportamentos inesperados como:
  - O fim do tempo para a atribuição da função pode ser apresentado incorretamente.
  - No portal PIM, **my Roles** só pode mostrar uma tarefa de papel, independentemente de quantos métodos a atribuição é concedida (através de um ou mais grupos e diretamente).
- O lançamento encenado por Enable para funcionalidade **de inscrição de utilizador gerida** não suporta a atribuição através do grupo.
- *Clientes licenciados Azure AD P2 apenas* Mesmo depois de eliminar o grupo, é ainda mostrado um membro elegível do papel na PIM UI. Funcionalmente não há problema; é apenas um problema de cache no portal Azure.  
- Utilize o novo [Exchange Admin Center](https://admin.exchange.microsoft.com/) para atribuições de funções através da adesão ao grupo. O antigo Exchange Admin Center ainda não suporta esta funcionalidade. Os cmdlets de Câmbio PowerShell funcionarão como esperado.
- O Portal de Proteção de Informação Azure (o portal clássico) ainda não reconhece a adesão ao papel através do grupo. Pode [migrar para a plataforma de rotulagem unificada](/azure/information-protection/configure-policy-migrate-labels) de sensibilidade e, em seguida, utilizar o Centro de Conformidade & de Segurança do Office 365 para utilizar atribuições de grupo para gerir funções.

Estamos a resolver estas questões.

## <a name="required-license-plan"></a>Plano de licença exigido

A utilização desta funcionalidade requer que tenha uma licença Azure AD Premium P1 disponível na sua organização Azure AD. Para utilizar também a Gestão de Identidade Privilegiada para ativação de funções just-in-time requer que você tenha uma licença Azure AD Premium P2 disponível. Para encontrar a licença certa para os seus requisitos, consulte [Comparar as funcionalidades geralmente disponíveis dos planos Free and Premium.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Passos seguintes

- [Criar um grupo ao qual se pode atribuir funções](groups-create-eligible.md)
- [Atribuir um papel a um grupo atribuível a funções](groups-assign-role.md)
