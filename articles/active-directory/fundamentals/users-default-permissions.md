---
title: Permissões de utilizador - Azure Active Directory predefinidas | Documentos da Microsoft
description: Saiba mais sobre as permissões de utilizador diferentes disponíveis no Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341372"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de usuário padrão no Azure Active Directory?
No Azure Active Directory (Azure AD), é concedido a todos os utilizadores um conjunto de permissões predefinidas. Acesso de um utilizador consiste o tipo de utilizador, seus [atribuições de funções](active-directory-users-assign-role-azure-portal.md)e a propriedade de objetos individual. Este artigo descreve essas permissões predefinidas e contém uma comparação entre as predefinições do utilizador membro e convidado. As permissões de utilizador padrão podem ser alteradas apenas nas definições de utilizador no Azure AD.

## <a name="member-and-guest-users"></a>Utilizadores membros e convidados
O conjunto de permissões padrão recebido depende se o usuário é um membro nativo do inquilino (utilizador membro) ou se o utilizador seja colocado de outro diretório como um convidado de colaboração do B2B (utilizador convidado). Ver [o que é a colaboração B2B do Azure AD?](../b2b/what-is-b2b.md) para obter mais informações sobre como adicionar utilizadores convidados.
* Os utilizadores membros podem registar aplicações, gerir a fotografia de perfil e o número de telemóvel, alterar a palavra-passe e convidar convidados B2B. Além disso, os utilizadores podem ler todas as informações de diretório (com algumas exceções). 
* Os utilizadores convidados tem restringido a permissões de diretório. Por exemplo, os utilizadores convidados não podem procurar informações do inquilino além das suas próprias informações de perfil. No entanto, um utilizador convidado pode obter informações sobre outro utilizador, fornecendo o Nome Principal de Utilizador ou o objectId. Um utilizador convidado pode ler as propriedades dos grupos que pertencem, incluindo associação a grupos, independentemente do **permissões dos utilizadores convidados são limitadas** definição. Um convidado não é possível ver informações sobre todos os outros objetos de inquilino.

As permissões predefinidas para os convidados são restritivas por predefinição. Os convidados podem ser adicionados a funções de administrador, que lhes concedem permissões totais de leitura e escrita contidas na função. Há uma restrição adicional disponível, a capacidade de os convidados convidarem outros convidados. Definir **Os convidados podem convidar** para **Não** impede os convidados de convidarem outros convidados. Veja [Delegar convites para colaboração B2B](../b2b/delegate-invitations.md) para saber como. Para conceder aos utilizadores convidados as mesmas permissões dos utilizadores membros por predefinição, defina **As permissões dos utilizadores convidados são limitadas** para **Não**. Esta definição concede a todos os utilizadores membros permissões para convidar utilizadores por predefinição, bem com permite que sejam adicionados convidados a funções administrativas.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar as permissões predefinidas de membro e convidado

**Área** | **Permissões de utilizador membro** | **Permissões de utilizador convidado**
------------ | --------- | ----------
Utilizadores e contactos | Ler todas as propriedades públicas de utilizadores e contactos<br>Convidar convidados<br>Alterar a própria palavra-passe<br>Gerir o próprio número de telemóvel<br>Gerir a própria fotografia<br>Invalidar os próprios tokens de atualização | Ler as próprias propriedades<br>Ler o nome a apresentar, envie um e-mail, inicie sessão no nome, fotos, nome principal de utilizador e propriedades de tipo de utilizador de outros utilizadores e contactos<br>Alterar a própria palavra-passe
Grupos | Criar grupos de segurança<br>Criar grupos do Office 365<br>Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações de grupo de leitura oculto do Office 365 para grupo associado<br>Gerir propriedades, da propriedade e associação de grupos que do utilizador<br>Adicionar convidados a grupos detidos<br>Gerir definições de associação dinâmica<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365 | Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações a grupo de leitura oculto do Office 365 para grupos associados<br>Gerir grupos detidos<br>Adicionar convidados a grupos detidos (se permitido)<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365<br>Ler as propriedades dos grupos que pertencem, incluindo associação.
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
Os utilizadores podem registar aplicações | Definir esta opção para não impede que os usuários a criação de registos de aplicação. A capacidade, em seguida, pode ser concedida para indivíduos específicos ao adicioná-los para a função de desenvolvedor do aplicativo.
Permitir que os usuários se conectem a conta escolar ou profissional com o LinkedIn | Definir esta opção para não impede os utilizadores de se ligar a respetiva conta escolar ou profissional com a respetiva conta do LinkedIn. Para obter mais informações, consulte [partilha de dados de ligações e o consentimento de contas LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Capacidade de criar grupos de segurança | Definir esta opção para Não impede que os utilizadores criem grupos de segurança. Os administradores globais e administradores de utilizador ainda podem criar grupos de segurança. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos do Office 365 | Definir esta opção para Não impede que os utilizadores criem grupos do Office 365. Definir esta opção para Alguns permite que um conjunto selecionado de utilizadores crie grupos do Office 365. Os administradores globais e administradores de utilizador ainda poderá criar grupos do Office 365. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir esta opção para Sim impede os utilizadores de aceder ao Azure Active Directory através do portal do Azure apenas.
Capacidade de ler outros utilizadores | Esta definição só está disponível no PowerShell. Definir este sinalizador para $false impede que todos os não-administradores ler as informações de utilizador do diretório. Este sinalizador não impede a leitura de informações do utilizador noutros serviços Microsoft, como o Exchange Online. Esta definição destina-se a circunstâncias especiais, e definir este sinalizador para $false não é recomendado.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registo de aplicação
Quando um utilizador regista uma aplicação, é adicionado automaticamente como um proprietário da aplicação. Como proprietário, pode gerir os metadados da aplicação, como o nome e as permissões que a aplicação requer. Também pode gerir a configuração específica de inquilino da aplicação, como a configuração do início de sessão único e as atribuições de utilizador. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões de proprietário do aplicativo empresarial
Quando um usuário adiciona uma nova aplicação de enterprise, eles são adicionados automaticamente como proprietário. Como proprietário, podem gerir a configuração de inquilino específico do aplicativo, como o SSO configuração, aprovisionamento e atribuições do utilizador. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário, os administradores globais, os proprietários podem gerir apenas as aplicações que possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um utilizador cria um grupo, é adicionado automaticamente como um proprietário desse grupo. Como proprietário, eles podem gerir propriedades do grupo, como o nome, bem como gerir a associação de grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de utilizador, só podem gerir grupos seus proprietários. Para atribuir um proprietário de grupo, veja [Gerir os proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas seguintes descrevem as permissões específicas no Azure Active Directory utilizadores membros têm sobre objetos pertencentes à empresa. O utilizador só tem estas permissões em objetos possuem.

#### <a name="owned-application-registrations"></a>Registos de aplicação pertencentes à empresa
Os utilizadores podem efetuar as seguintes ações em registos de aplicação pertencentes à empresa.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas em aplicações no Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Elimine aplicações no Azure Active Directory. |
| microsoft.directory/applications/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/applications/restore | Restaure aplicações no Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Aplicações empresariais pertencentes à empresa
Os utilizadores podem efetuar as seguintes ações em aplicações empresariais pertencentes à empresa. Uma aplicação empresarial é composta de principal de serviço, uma ou mais políticas de aplicação e, às vezes, um objeto de aplicação no mesmo inquilino, como o principal de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.directory/policies/delete | Elimine políticas no Azure Active Directory. |
| microsoft.directory/policies/owners/update | Atualize a propriedade policies.owners no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualize a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Elimine servicePrincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualize a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivos pertencentes à empresa
Os utilizadores podem efetuar as seguintes ações em dispositivos pertencentes à empresa.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/devices/disable | Desative dispositivos no Azure Active Directory. |

#### <a name="owned-groups"></a>Grupos pertencentes à empresa
Os utilizadores podem efetuar as seguintes ações em grupos pertencentes à empresa.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas sobre os grupos no Azure Active Directory. |
| microsoft.directory/groups/delete | Elimine grupos no Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Atualize a propriedade groups.dynamicMembershipRule no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade groups.settings no Azure Active Directory. |

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como atribuir funções de administrador do Azure AD, veja [atribuir um utilizador a funções de administrador no Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](add-users-azure-active-directory.md)
