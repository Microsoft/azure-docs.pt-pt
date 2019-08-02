---
title: Permissões de utilizador - Azure Active Directory predefinidas | Documentos da Microsoft
description: Saiba mais sobre as permissões de utilizador diferentes disponíveis no Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561608"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de usuário padrão no Azure Active Directory?
No Azure Active Directory (Azure AD), é concedido a todos os utilizadores um conjunto de permissões predefinidas. O acesso de um usuário consiste no tipo de usuário, nas [atribuições de função](active-directory-users-assign-role-azure-portal.md)e na propriedade de objetos individuais. Este artigo descreve essas permissões predefinidas e contém uma comparação entre as predefinições do utilizador membro e convidado. As permissões de usuário padrão só podem ser alteradas nas configurações do usuário no Azure AD.

## <a name="member-and-guest-users"></a>Utilizadores membros e convidados
O conjunto de permissões padrão recebidas depende se o usuário é um membro nativo do locatário (usuário membro) ou se o usuário é trazido de outro diretório como convidado de colaboração B2B (usuário convidado). Consulte [o que é a colaboração B2B do Azure ad?](../b2b/what-is-b2b.md) para obter mais informações sobre como adicionar usuários convidados.
* Os utilizadores membros podem registar aplicações, gerir a fotografia de perfil e o número de telemóvel, alterar a palavra-passe e convidar convidados B2B. Além disso, os utilizadores podem ler todas as informações de diretório (com algumas exceções). 
* Os usuários convidados têm permissões de diretório restritas. Por exemplo, os utilizadores convidados não podem procurar informações do inquilino além das suas próprias informações de perfil. No entanto, um utilizador convidado pode obter informações sobre outro utilizador, fornecendo o Nome Principal de Utilizador ou o objectId. Um usuário convidado pode ler as propriedades dos grupos aos quais eles pertencem, incluindo a associação de grupo, independentemente das **permissões de usuários convidados serem** configurações limitadas. Um convidado não pode exibir informações sobre nenhum outro objeto de locatário.

As permissões predefinidas para os convidados são restritivas por predefinição. Os convidados podem ser adicionados a funções de administrador, que lhes concedem permissões totais de leitura e escrita contidas na função. Há uma restrição adicional disponível, a capacidade de os convidados convidarem outros convidados. Definir **Os convidados podem convidar** para **Não** impede os convidados de convidarem outros convidados. Veja [Delegar convites para colaboração B2B](../b2b/delegate-invitations.md) para saber como. Para conceder aos utilizadores convidados as mesmas permissões dos utilizadores membros por predefinição, defina **As permissões dos utilizadores convidados são limitadas** para **Não**. Esta definição concede a todos os utilizadores membros permissões para convidar utilizadores por predefinição, bem com permite que sejam adicionados convidados a funções administrativas.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar as permissões predefinidas de membro e convidado

**Área** | **Permissões de utilizador membro** | **Permissões de utilizador convidado**
------------ | --------- | ----------
Utilizadores e contactos | Ler todas as propriedades públicas de utilizadores e contactos<br>Convidar convidados<br>Alterar a própria palavra-passe<br>Gerir o próprio número de telemóvel<br>Gerir a própria fotografia<br>Invalidar os próprios tokens de atualização | Ler as próprias propriedades<br>Ler nome de exibição, email, nome de entrada, foto, nome principal de usuário e propriedades de tipo de usuário de outros usuários e contatos<br>Alterar a própria palavra-passe
Grupos | Criar grupos de segurança<br>Criar grupos do Office 365<br>Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações de grupo de leitura oculto do Office 365 para grupo associado<br>Gerenciar Propriedades, propriedade e Associação de grupos dos quais o usuário possui<br>Adicionar convidados a grupos detidos<br>Gerir definições de associação dinâmica<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365 | Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações a grupo de leitura oculto do Office 365 para grupos associados<br>Gerir grupos detidos<br>Adicionar convidados a grupos detidos (se permitido)<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365<br>Ler propriedades de grupos aos quais pertencem, incluindo associação.
Aplicações | Registar (criar) nova aplicação<br>Ler as propriedades de aplicações registadas e empresariais<br>Gerir as propriedades da aplicação, atribuições e credenciais para aplicações detidas<br>Criar ou eliminar palavra-passe de aplicação do utilizador<br>Eliminar aplicações detidas<br>Restaurar aplicações detidas | Ler as propriedades de aplicações registadas e empresariais<br>Gerir as propriedades da aplicação, atribuições e credenciais para aplicações detidas<br>Eliminar aplicações detidas<br>Restaurar aplicações detidas
Dispositivos | Ler todas as propriedades dos dispositivos<br>Gerir todas as propriedades dos dispositivos detidos<br> | Sem permissões<br>Eliminar dispositivos detidos<br>
Diretório | Ler todas as informações da empresa<br>Ler todos os domínios<br>Ler todos os contratos de parceiros | Ler o nome a apresentar e os domínios verificados
Funções e Âmbitos | Ler todas as funções e associações administrativas<br>Ler todas as propriedades e associação de unidades administrativas | Sem permissões 
Subscrições | Ler todas as subscrições<br>Ativar Membro do Plano de Serviço | Sem permissões
Políticas | Ler todas as propriedades das políticas<br>Gerir todas as propriedades da política detida | Sem permissões

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Para restringir as permissões predefinidas para os utilizadores membros

As permissões predefinidas para os utilizadores membros podem ser restringidas das seguintes formas.

Permissão | Explicação da definição
---------- | ------------
Os usuários podem registrar o aplicativo | Definir essa opção como não impede que os usuários criem registros de aplicativo. A capacidade pode então ser concedida de volta a indivíduos específicos, adicionando-os à função de desenvolvedor de aplicativos.
Permitir que os utilizadores liguem a conta escolar ou profissional com o LinkedIn | Definir essa opção como não impede que os usuários conectem sua conta corporativa ou de estudante com sua conta do LinkedIn. Para obter mais informações, consulte o [consentimento e compartilhamento de dados de conexões de contas do LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Capacidade de criar grupos de segurança | Definir esta opção para Não impede que os utilizadores criem grupos de segurança. Administradores globais e administradores de usuários ainda podem criar grupos de segurança. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos do Office 365 | Definir esta opção para Não impede que os utilizadores criem grupos do Office 365. Definir esta opção para Alguns permite que um conjunto selecionado de utilizadores crie grupos do Office 365. Administradores globais e administradores de usuários ainda poderão criar grupos do Office 365. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir essa opção como Sim impede que os usuários acessem Azure Active Directory somente por meio de portal do Azure.
Capacidade de ler outros utilizadores | Esta definição só está disponível no PowerShell. Definir esse sinalizador como $false impede que todos os não-administradores leiam informações do usuário do diretório. Esse sinalizador não impede a leitura de informações do usuário em outros serviços da Microsoft, como o Exchange Online. Essa configuração destina-se a circunstâncias especiais e a definição desse sinalizador como $false não é recomendada.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registo de aplicação
Quando um utilizador regista uma aplicação, é adicionado automaticamente como um proprietário da aplicação. Como proprietário, pode gerir os metadados da aplicação, como o nome e as permissões que a aplicação requer. Também pode gerir a configuração específica de inquilino da aplicação, como a configuração do início de sessão único e as atribuições de utilizador. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões do proprietário do aplicativo empresarial
Quando um usuário adiciona um novo aplicativo empresarial, ele é adicionado automaticamente como um proprietário. Como proprietário, eles podem gerenciar a configuração específica do locatário do aplicativo, como a configuração de SSO, o provisionamento e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários podem gerenciar somente os aplicativos que eles possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um utilizador cria um grupo, é adicionado automaticamente como um proprietário desse grupo. Como proprietário, eles podem gerenciar as propriedades do grupo, como o nome, bem como gerenciar a associação de grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de usuários, os proprietários só podem gerenciar os grupos que eles possuem. Para atribuir um proprietário de grupo, veja [Gerir os proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas a seguir descrevem as permissões específicas em Azure Active Directory usuários Membros têm objetos de propriedade. O usuário só tem essas permissões em objetos de sua propriedade.

#### <a name="owned-application-registrations"></a>Registros de aplicativo de propriedade
Os usuários podem executar as seguintes ações em registros de aplicativo de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/audience/update | Atualizar a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualizar a propriedade applications.authentication no Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Atualize as propriedades básicas das aplicações no Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Atualizar a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminar aplicações no Azure Active Directory. |
| microsoft.directory/applications/owners/update | Atualizar a propriedade applications.owners no Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Atualizar a propriedade applications.permissions no Azure Active Directory. |
| Microsoft. Directory/Applications/Policies/Update | Atualizar a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/applications/restore | Restaurar aplicações no Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Aplicativos empresariais de propriedade
Os usuários podem executar as seguintes ações em aplicativos corporativos de propriedade. Um aplicativo empresarial é composto de entidade de serviço, uma ou mais políticas de aplicativo e, às vezes, um objeto de aplicativo no mesmo locatário que a entidade de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| Microsoft. Directory/Policies/Basic/Update | Atualizar propriedades básicas em políticas no Azure Active Directory. |
| Microsoft. Directory/Policies/Delete | Eliminar políticas no Azure Active Directory. |
| microsoft.directory/policies/owners/update | Atualizar a propriedade policies.owners no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualizar a propriedade servicePrincipals.appRoleAssignedTo no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipalName. Audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipalName. de autenticação no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualizar propriedades básicas nos servicePrincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipalName. Credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminar servicePrincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar a propriedade servicePrincipals.owners no Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades privilegiadas) em signInReports no Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivos de propriedade
Os usuários podem executar as seguintes ações em dispositivos de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Desativar dispositivos no Azure Active Directory. |

#### <a name="owned-groups"></a>Grupos de propriedade
Os usuários podem executar as seguintes ações em grupos próprios.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Atualizar a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualizar as propriedades básicas em grupos no Azure Active Directory. |
| microsoft.directory/groups/delete | Eliminar grupos no Azure Active Directory. |
| Microsoft. Directory/groups/dynamicMembershipRule/Update | Atualizar a propriedade groups.dynamicMembershipRule no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualizar a propriedade groups.members no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade groups.owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaurar grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualizar a propriedade groups.settings no Azure Active Directory. |

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como atribuir funções de administrador do Azure AD, veja [atribuir um utilizador a funções de administrador no Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](add-users-azure-active-directory.md)
