---
title: Permissões de utilizador predefinidas - Azure Ative Directory / Microsoft Docs
description: Saiba mais sobre as diferentes permissões de utilizador disponíveis no Azure Ative Directory.
services: active-directory
author: ajburnle
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
ms.openlocfilehash: bae802d8aa9378155bcca0713992a8cc041ea1a9
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799027"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de utilizador predefinidas no Azure Ative Directory?
No Azure Active Directory (Azure AD), é concedido a todos os utilizadores um conjunto de permissões predefinidas. O acesso de um utilizador consiste no tipo de utilizador, nas suas [atribuições de papel](active-directory-users-assign-role-azure-portal.md)e na sua propriedade de objetos individuais. Este artigo descreve essas permissões predefinidas e contém uma comparação entre as predefinições do utilizador membro e convidado. As permissões do utilizador predefinidas só podem ser alteradas nas definições do utilizador em Azure AD.

## <a name="member-and-guest-users"></a>Utilizadores membros e convidados
O conjunto de permissões por defeito recebidas depende se o utilizador é um membro nativo do inquilino (utilizador membro) ou se o utilizador é trazido de outro diretório como convidado de colaboração B2B (utilizador convidado). Veja [o que é a colaboração Azure AD B2B?](../b2b/what-is-b2b.md)
* Os utilizadores membros podem registar aplicações, gerir a fotografia de perfil e o número de telemóvel, alterar a palavra-passe e convidar convidados B2B. Além disso, os utilizadores podem ler todas as informações de diretório (com algumas exceções). 
* Os utilizadores convidados têm permissões restritas de diretório. Por exemplo, os utilizadores convidados não podem procurar informações do inquilino além das suas próprias informações de perfil. No entanto, um utilizador convidado pode obter informações sobre outro utilizador, fornecendo o Nome Principal de Utilizador ou o objectId. Um utilizador convidado pode ler propriedades de grupos a que pertencem, incluindo a adesão ao grupo, independentemente das permissões dos **utilizadores do Guest serem limitadas.** Um hóspede não pode ver informações sobre quaisquer outros objetos de inquilino.

As permissões predefinidas para os convidados são restritivas por predefinição. Os convidados podem ser adicionados a funções de administrador, que lhes concedem permissões totais de leitura e escrita contidas na função. Há uma restrição adicional disponível, a capacidade de os convidados convidarem outros convidados. Definir **Os convidados podem convidar** para **Não** impede os convidados de convidarem outros convidados. Veja [Delegar convites para colaboração B2B](../b2b/delegate-invitations.md) para saber como. Para conceder aos utilizadores convidados as mesmas permissões dos utilizadores membros por predefinição, defina **As permissões dos utilizadores convidados são limitadas** para **Não**. Esta definição concede a todos os utilizadores membros permissões para convidar utilizadores por predefinição, bem com permite que sejam adicionados convidados a funções administrativas.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar as permissões predefinidas de membro e convidado

**Área** | **Permissões dos utilizadores membros** | **Permissões de utilizador convidado**
------------ | --------- | ----------
Utilizadores e contactos | Ler todas as propriedades públicas de utilizadores e contactos<br>Convidar convidados<br>Alterar a própria palavra-passe<br>Gerir o próprio número de telemóvel<br>Gerir a própria fotografia<br>Invalidar os próprios tokens de atualização | Ler as próprias propriedades<br>Leia o nome do visor, e-mail, sinal no nome, foto, nome principal do utilizador e propriedades do tipo de utilizador de outros utilizadores e contactos<br>Alterar a própria palavra-passe
Grupos | Criar grupos de segurança<br>Criar grupos do Office 365<br>Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações de grupo de leitura oculto do Office 365 para grupo associado<br>Gerir propriedades, propriedade e adesão de grupos que o utilizador detém<br>Adicionar convidados a grupos detidos<br>Gerir definições de associação dinâmica<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365 | Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações a grupo de leitura oculto do Office 365 para grupos associados<br>Gerir grupos detidos<br>Adicionar convidados a grupos detidos (se permitido)<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365<br>Leia propriedades de grupos a que pertencem, incluindo a adesão.
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
Os utilizadores podem registar a aplicação | Definir esta opção para Não impede os utilizadores de criar registos de aplicações. A capacidade pode então ser concedida a indivíduos específicos adicionando-os à função de Desenvolvedor de Aplicações.
Permitir que os utilizadores conectem o trabalho ou a conta escolar com o LinkedIn | Definir esta opção para Não impede que os utilizadores conectem o seu trabalho ou conta escolar com a sua conta LinkedIn. Para mais informações, consulte [a partilha e consentimento de contactos da conta LinkedIn.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)
Capacidade de criar grupos de segurança | Definir esta opção para Não impede que os utilizadores criem grupos de segurança. Os administradores globais e os administradores de utilizadores ainda podem criar grupos de segurança. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos do Office 365 | Definir esta opção para Não impede que os utilizadores criem grupos do Office 365. Definir esta opção para Alguns permite que um conjunto selecionado de utilizadores crie grupos do Office 365. Os administradores globais e os administradores de utilizadores ainda poderão criar grupos do Office 365. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir esta opção para Não permite que os não administradores utilizem o portal de administração AD Azure para ler e gerir os recursos Azure AD. Sim, restringe todos os não administradores de acederem a quaisquer dados AD do Azure no portal da administração. Importante a notar: esta definição não restringe o acesso aos dados AD do Azure utilizando o PowerShell ou outros clientes, como o Visual Studio. Quando definido para Sim, conceder a um utilizador específico não administrador a capacidade de usar o portal de administração AD Azure atribui qualquer papel administrativo, como o papel de Leitores de Diretório. Esta função permite a leitura de informações básicas do diretório, que os utilizadores membros têm por padrão (os hóspedes e os principais de serviço não).
Capacidade de ler outros utilizadores | Esta definição só está disponível no PowerShell. A colocação desta bandeira em $false impede que todos os não administradores leiam a informação do utilizador a partir do diretório. Esta bandeira não impede a leitura de informações dos utilizadores noutros serviços da Microsoft, como o Exchange Online. Esta definição destina-se a circunstâncias especiais, e não é recomendada a colocação desta bandeira $false.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registo de aplicação
Quando um utilizador regista uma aplicação, é adicionado automaticamente como um proprietário da aplicação. Como proprietário, pode gerir os metadados da aplicação, como o nome e as permissões que a aplicação requer. Também pode gerir a configuração específica de inquilino da aplicação, como a configuração do início de sessão único e as atribuições de utilizador. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões do proprietário de aplicações empresariais
Quando um utilizador adiciona uma nova aplicação da empresa, é automaticamente adicionado como proprietário. Como proprietário, podem gerir a configuração específica do arrendatário da aplicação, como a configuração SSO, provisionamento e atribuição de utilizadores. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um utilizador cria um grupo, é adicionado automaticamente como um proprietário desse grupo. Como proprietário, podem gerir propriedades do grupo como o nome, bem como gerir a filiação do grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de utilizadores, os proprietários só podem gerir grupos que possuam. Para atribuir um proprietário de grupo, veja [Gerir os proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas que se seguem descrevem as permissões específicas no Azure Ative Directory os utilizadores têm sobre objetos próprios. O utilizador só tem estas permissões em objetos que possuem.

#### <a name="owned-application-registrations"></a>Registos de candidaturas de propriedade
Os utilizadores podem realizar as seguintes ações em registos de aplicações próprias.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/aplicações/audiência/atualização | Atualizar aplicações.propriedade audiência em Azure Ative Directory. |
| microsoft.diretório/aplicações/autenticação/atualização | Atualizar aplicações.autenticação propriedade no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas em aplicações no Azure Ative Directory. |
| microsoft.diretório/aplicações/credenciais/atualização | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/permissões/atualização | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/atualização | Atualizar aplicações.apólices propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/restaurar | Restaurar aplicações no Diretório Ativo Azure. |

#### <a name="owned-enterprise-applications"></a>Aplicações empresariais próprias
Os utilizadores podem realizar as seguintes ações em aplicações empresariais próprias. Um pedido de empresa é composto por um principal de serviço, uma ou mais políticas de aplicação, e às vezes um objeto de aplicação no mesmo inquilino que o diretor de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/políticas/básico/atualização | Atualizar propriedades básicas sobre políticas no Azure Ative Directory. |
| microsoft.diretório/políticas/excluir | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/proprietários/atualização | Atualizar apólices.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Serviço de actualizaçãoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/audience/update | Serviço de actualizaçãoPrincipals.propriedade do público em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/autenticação/atualização | Serviço de actualizaçãoPrincipals.authentication property in Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em serviçoPrincipals em Azure Ative Directy. |
| microsoft.diretório/serviçoPrincipals/credenciais/atualização | Serviço de actualizaçãoPrincipals.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/delete | Eliminar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/atualização | Serviço de atualizaçãoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/permissões/atualização | Serviço de actualizaçãoPrincipals.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |

#### <a name="owned-devices"></a>Dispositivos de propriedade
Os utilizadores podem realizar as seguintes ações em dispositivos próprios.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/dispositivos/desativar | Desativar dispositivos no Diretório Ativo Azure. |

#### <a name="owned-groups"></a>Grupos de propriedade
Os utilizadores podem realizar as seguintes ações em grupos de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupos/appRoleAssignments/update | Atualizar a propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos no Azure Ative Directory. |
| microsoft.diretório/grupos/delete | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/dynamicMembershipRule/update | Atualizar grupos.dynamicMembershipRule propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/restaurar | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/definições/atualização | Atualizar a propriedade grupos.configurações em Azure Ative Directory. |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como atribuir funções de administrador AZure, consulte [atribuir um utilizador às funções de administrador no Azure Ative Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](add-users-azure-active-directory.md)
