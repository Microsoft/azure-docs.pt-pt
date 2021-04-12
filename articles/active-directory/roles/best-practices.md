---
title: Boas práticas para funções AZURE AD - Azure Ative Directory
description: Melhores práticas para a utilização de funções de Diretório Ativo Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111777"
---
# <a name="best-practices-for-azure-ad-roles"></a>Melhores práticas para funções de AD Azure

Este artigo descreve algumas das melhores práticas para a utilização do controlo de acesso baseado em funções do Azure Ative Directory (Azure AD RBAC). Estas boas práticas derivam da nossa experiência com o Azure AD RBAC e das experiências de clientes como você. Encorajamo-lo a também ler as nossas orientações de segurança detalhadas para [garantir o acesso privilegiado para implantações híbridas e em nuvem em Azure AD.](security-planning.md)

## <a name="1-manage-to-least-privilege"></a>1. Conseguir menos privilégios

Ao planear a sua estratégia de controlo de acesso, é uma boa prática para conseguir menos privilégios. O mínimo privilégio significa que concedes aos teus administradores exatamente a permissão que eles precisam para fazer o seu trabalho. Há três aspetos a ter em conta quando atribui uma função aos seus administradores: um conjunto específico de permissões, sobre um âmbito específico, por um período de tempo específico. Evite atribuir funções mais amplas a âmbitos mais amplos, mesmo que inicialmente pareça mais conveniente fazê-lo. Limitando papéis e âmbitos, limita-se os recursos em risco se o diretor de segurança estiver alguma vez comprometido. A Azure AD RBAC suporta mais de 65 [funções incorporadas.](permissions-reference.md) Existem funções de AD Azure para gerir objetos de diretório como utilizadores, grupos e aplicações, e também para gerir serviços microsoft 365 como Exchange, SharePoint e Intune. Para melhor compreender os papéis integrados da AD AD, consulte [os papéis de Compreender no Diretório Ativo Azure.](concept-understand-roles.md) Se não houver um papel incorporado que atenda à sua necessidade, pode criar os seus [próprios papéis personalizados.](custom-create.md)  
 
### <a name="finding-the-right-roles"></a>Encontrar os papéis certos

Siga estes passos para ajudá-lo a encontrar o papel certo.

1. No portal Azure, [open Roles e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) para ver a lista de funções Azure AD.

1. Utilize o filtro **de serviço** para reduzir a lista de funções.

    ![Funções e página de administradores em AD AZure com filtro de serviço aberto](./media/best-practices/roles-administrators.png)

1. Consulte a documentação [de funções incorporadas Azure.](permissions-reference.md) As permissões associadas a cada papel são listadas em conjunto para uma melhor legibilidade. Para compreender a estrutura e o significado das permissões de papel, consulte [Como compreender as permissões de funções.](permissions-reference.md#how-to-understand-role-permissions)

1. Consulte o papel menos privilegiado através da documentação [da tarefa.](delegate-by-task.md)

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Utilizar a Gestão de Identidade Privilegiada para conceder acesso just-in-time

Um dos princípios de menor privilégio é que o acesso deve ser concedido apenas por um determinado período de tempo. [A Azure AD Gestão de Identidade Privilegiada (PIM)](../privileged-identity-management/pim-configure.md) permite-lhe conceder acesso just-in-time aos seus administradores. A Microsoft recomenda que ative o PIM em Azure AD. Utilizando o PIM, um utilizador pode ser nomeado membro elegível de uma função AD Azure. A pode então ativar o seu papel por um período de tempo limitado cada vez que a necessidade de usá-lo. O acesso privilegiado é automaticamente removido quando o prazo expira. Também pode [configurar as definições de PIM](../privileged-identity-management/pim-how-to-change-default-settings.md) para exigir aprovação ou receber e-mails de notificação quando alguém ativar a sua atribuição de funções. As notificações fornecem um alerta quando novos utilizadores são adicionados a funções altamente privilegiadas. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Ligue a autenticação de vários fatores para todas as suas contas de administrador

[Com base nos nossos estudos,](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)a sua conta tem menos 99,9% de probabilidade suscetível de ser comprometida se utilizar a autenticação multi-factor (MFA). 
 
Pode ativar as funções de MFA em Ad Azure utilizando dois métodos:
- [Definições de funções](../privileged-identity-management/pim-how-to-change-default-settings.md) na Gestão de Identidade Privilegiada
- [Conditional Access](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Configurar revisões de acesso recorrentes para revogar permissões não necessárias ao longo do tempo

As análises de acesso permitem às organizações rever regularmente o acesso do administrador para garantir que apenas as pessoas certas têm acesso continuado. A auditoria regular dos seus administradores é crucial devido às seguintes razões:
- Um ator malicioso pode comprometer uma conta.
- As pessoas movem equipas numa empresa. Se não houver auditoria, podem acumular acesso desnecessário ao longo do tempo.
 
Para obter informações sobre avaliações de acesso para funções, consulte [Criar uma revisão de acesso das funções AZURE AD em PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Para obter informações sobre revisões de acesso de grupos que são atribuídos, consulte [Criar uma revisão de acesso de grupos e aplicações em avaliações de acesso Azure AD](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. Limitar o número de Administradores Globais a menos de 5

Como uma boa prática, a Microsoft recomenda que atribua o papel de Administrador Global a **menos de cinco** pessoas na sua organização. Os Administradores Globais têm as chaves do reino, e é do seu interesse manter a superfície de ataque baixa. Como indicado anteriormente, todas estas contas devem ser protegidas com autenticação de vários fatores.

Por padrão, quando um utilizador se inscreve para um serviço de cloud da Microsoft, é criado um inquilino AZure AD e o utilizador é nomeado membro do papel de Administrador Global. Os utilizadores a quem foi atribuída a função de Administrador Global podem ler e modificar todas as configurações administrativas da sua organização AZure AD. Com algumas exceções, os Administradores Globais também podem ler e modificar todas as definições de configuração na sua organização Microsoft 365. Os Administradores Globais também têm a capacidade de elevar o seu acesso à leitura de dados.

A Microsoft recomenda que mantenha duas contas de vidro de rutura que são permanentemente atribuídas à função de Administrador Global. Certifique-se de que estas contas não requerem o mesmo mecanismo de autenticação de vários fatores que as suas contas administrativas normais para iniciar sedutação, conforme descrito em [Gerir contas de acesso de emergência em Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Utilizar grupos para atribuições de funções AZure E delegar a atribuição de funções

Se você tem um sistema de governação externa que se aproveita de grupos, então você deve considerar atribuir papéis a grupos AD Azure, em vez de utilizadores individuais. Também pode gerir grupos atribuíveis por funções em PIM para garantir que não existem proprietários ou membros permanentes nestes grupos privilegiados. Para obter mais informações, consulte [as capacidades de Gestão para acesso privilegiado aos grupos AD](../privileged-identity-management/groups-features.md)de acesso privilegiado.

Pode atribuir um proprietário a grupos atribuíveis por funções. O proprietário decide quem é adicionado ou removido do grupo, por isso, indiretamente, decide quem fica com a função. Desta forma, um Administrador Global ou Administrador privilegiado pode delegar a gestão de funções numa base por função utilizando grupos. Para obter mais informações, consulte [Utilize grupos de nuvem para gerir atribuições de funções no Azure Ative Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. Ativar várias funções ao mesmo tempo utilizando grupos de acesso privilegiados

Pode ser que um indivíduo tenha cinco ou seis atribuições elegíveis para funções de Azure AD através da PIM. Terão de ativar cada papel individualmente, o que pode reduzir a produtividade. Pior ainda, também podem ter dezenas ou centenas de recursos Azure atribuídos a eles, o que agrava o problema.
 
Neste caso, deve utilizar [grupos de acesso privilegiados.](../privileged-identity-management/groups-features.md) Crie um grupo privilegiado de acesso e conceda-lhe acesso permanente a múltiplas funções (Azure AD e/ou Azure). Faça desse utilizador um membro elegível ou proprietário deste grupo. Com apenas uma ativação, terão acesso a todos os recursos ligados.

![Diagrama de grupo de acesso privilegiado mostrando ativação de múltiplas funções ao mesmo tempo](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Utilize contas nativas em nuvem para funções AZure AD

Evite utilizar contas sincronizadas no local para atribuições de funções Azure AD. Se a sua conta no local estiver comprometida, também pode comprometer os seus recursos Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](security-planning.md)