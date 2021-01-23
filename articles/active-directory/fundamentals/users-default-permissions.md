---
title: Permissões de utilizador predefinidas - Azure Ative Directory | Microsoft Docs
description: Saiba mais sobre as diferentes permissões de utilizador disponíveis no Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724276"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de utilizador predefinidas no Azure Ative Directory?
No Azure Active Directory (Azure AD), é concedido a todos os utilizadores um conjunto de permissões predefinidas. O acesso de um utilizador consiste no tipo de utilizador, nas suas [atribuições de papel](active-directory-users-assign-role-azure-portal.md)e na sua propriedade de objetos individuais. Este artigo descreve essas permissões predefinidas e contém uma comparação entre as predefinições do utilizador membro e convidado. As permissões do utilizador predefinidas só podem ser alteradas nas definições do utilizador em Azure AD.

## <a name="member-and-guest-users"></a>Utilizadores membros e convidados
O conjunto de permissões por defeito recebidas depende se o utilizador é um membro nativo do inquilino (utilizador membro) ou se o utilizador é trazido de outro diretório como convidado de colaboração B2B (utilizador convidado). Veja [o que é a colaboração Azure AD B2B?](../external-identities/what-is-b2b.md)
* Os utilizadores membros podem registar aplicações, gerir a fotografia de perfil e o número de telemóvel, alterar a palavra-passe e convidar convidados B2B. Além disso, os utilizadores podem ler todas as informações de diretório (com algumas exceções). 
* Os utilizadores convidados têm permissões restritas de diretório. Podem gerir o seu próprio perfil, alterar a sua própria palavra-passe e recuperar algumas informações sobre outros utilizadores, grupos e aplicações, no entanto, não conseguem ler todas as informações do diretório. Por exemplo, os utilizadores convidados não podem enumerar a lista de todos os utilizadores, grupos e outros objetos de diretório. Os convidados podem ser adicionados a funções de administrador, que lhes concedem permissões totais de leitura e escrita contidas na função. Os hóspedes também podem convidar outros hóspedes.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar as permissões predefinidas de membro e convidado

**Área** | **Permissões dos utilizadores membros** | **Permissões de utilizador de hóspedes predefinidos** | **Permissões restritas do utilizador do hóspede (Pré-visualização)**
------------ | --------- | ---------- | ----------
Utilizadores e contactos | <ul><li>Lista enumerada de todos os utilizadores e contactos<li>Ler todas as propriedades públicas de utilizadores e contactos</li><li>Convidar convidados<li>Alterar a própria palavra-passe<li>Gerir o próprio número de telemóvel<li>Gerir a própria fotografia<li>Invalidar os próprios tokens de atualização</li></ul> | <ul><li>Ler as próprias propriedades<li>Leia o nome do visor, e-mail, sinal no nome, foto, nome principal do utilizador e propriedades do tipo de utilizador de outros utilizadores e contactos<li>Alterar a própria palavra-passe<li>Procure por outro utilizador por ObjectId (se permitido)<li>Leia informações de gestor e relatório direto de outros utilizadores</li></ul> | <ul><li>Ler as próprias propriedades<li>Alterar a própria palavra-passe</li></ul>
Grupos | <ul><li>Criar grupos de segurança<li>Criar grupos Microsoft 365<li>Lista enumerada de todos os grupos<li>Ler todas as propriedades dos grupos<li>Ler as associações de grupo não oculto<li>Leia membros escondidos do grupo Microsoft 365 para grupo aderido<li>Gerir propriedades, propriedade e adesão de grupos que o utilizador detém<li>Adicionar convidados a grupos detidos<li>Gerir definições de associação dinâmica<li>Eliminar grupos detidos<li>Restaurar grupos Microsoft 365</li></ul> | <ul><li>Ler propriedades de grupos não escondidos, incluindo membros e propriedade (mesmo grupos não acompanhados)<li>Leia membros do grupo Microsoft 365 escondidos para grupos unidos<li>Procure por grupos por Display Name ou ObjectId (se permitido)</li></ul> | <ul><li>Leia o id de objeto para grupos unidos<li>Leia a adesão e a propriedade de grupos unidos em algumas aplicações da Microsoft 365 (se permitido)</li></ul>
Aplicações | <ul><li>Registar (criar) nova aplicação<li>Lista enumerada de todas as candidaturas<li>Ler as propriedades de aplicações registadas e empresariais<li>Gerir as propriedades da aplicação, atribuições e credenciais para aplicações detidas<li>Criar ou eliminar palavra-passe de aplicação do utilizador<li>Eliminar aplicações detidas<li>Restaurar aplicações detidas</li></ul> | <ul><li>Ler as propriedades de aplicações registadas e empresariais</li></ul> | <ul><li>Ler as propriedades de aplicações registadas e empresariais
Dispositivos</li></ul> | <ul><li>Lista enumerada de todos os dispositivos<li>Ler todas as propriedades dos dispositivos<li>Gerir todas as propriedades dos dispositivos detidos</li></ul> | Sem permissões | Sem permissões
Diretório | <ul><li>Ler todas as informações da empresa<li>Ler todos os domínios<li>Ler todos os contratos de parceiros</li></ul> | <ul><li>Leia o nome da exposição da empresa<li>Ler todos os domínios</li></ul> | <ul><li>Leia o nome da exposição da empresa<li>Ler todos os domínios</li></ul>
Funções e Âmbitos | <ul><li>Ler todas as funções e associações administrativas<li>Ler todas as propriedades e associação de unidades administrativas</li></ul> | Sem permissões | Sem permissões
Subscrições | <ul><li>Ler todas as subscrições<li>Ativar Membro do Plano de Serviço</li></ul> | Sem permissões | Sem permissões
Políticas | <ul><li>Ler todas as propriedades das políticas<li>Gerir todas as propriedades da política detida</li></ul> | Sem permissões | Sem permissões

## <a name="restrict-member-users-default-permissions"></a>Restringir permissões por defeito dos utilizadores membros 

As permissões por predefinição para os utilizadores membros podem ser restringidas das seguintes formas:

Permissão | Explicação da definição
---------- | ------------
Os utilizadores podem registar a aplicação | Definir esta opção para Não impede os utilizadores de criar registos de aplicações. A capacidade pode então ser concedida a indivíduos específicos adicionando-os à função de Desenvolvedor de Aplicações.
Permitir que os utilizadores conectem o trabalho ou a conta escolar com o LinkedIn | Definir esta opção para Não impede que os utilizadores conectem o seu trabalho ou conta escolar com a sua conta LinkedIn. Para mais informações, consulte [a partilha e consentimento de contactos da conta LinkedIn.](../enterprise-users/linkedin-user-consent.md)
Capacidade de criar grupos de segurança | Definir esta opção para Não impede que os utilizadores criem grupos de segurança. Os administradores globais e os administradores de utilizadores ainda podem criar grupos de segurança. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos Microsoft 365 | Definir esta opção para No impede os utilizadores de criar grupos Microsoft 365. Definir esta opção para Alguns permite que um conjunto selecionado de utilizadores crie grupos Microsoft 365. Os administradores globais e os administradores de utilizadores ainda poderão criar grupos Microsoft 365. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir esta opção para Não permite que os não administradores utilizem o portal de administração AD Azure para ler e gerir os recursos Azure AD. Sim, restringe todos os não administradores de acederem a quaisquer dados AD do Azure no portal da administração.<p>**Nota:** esta definição não restringe o acesso aos dados AD do Azure utilizando o PowerShell ou outros clientes, como o Visual Studio.Quando definido para Sim, para conceder a um utilizador específico não administrador a capacidade de usar o portal de administração AD Azure atribuir qualquer papel administrativo, como o papel de Leitores de Diretório.<p>Esta função permite a leitura de informações básicas do diretório, que os utilizadores membros têm por padrão (os hóspedes e os principais de serviço não).
Capacidade de ler outros utilizadores | Esta definição só está disponível no PowerShell. A colocação desta bandeira em $false impede que todos os não administradores leiam a informação do utilizador a partir do diretório. Esta bandeira não impede a leitura de informações dos utilizadores noutros serviços da Microsoft, como o Exchange Online. Esta definição destina-se a circunstâncias especiais, e não é recomendada a colocação desta bandeira $false.

## <a name="restrict-guest-users-default-permissions"></a>Restringir permissões por defeito dos utilizadores de hóspedes

As permissões por predefinição para utilizadores convidados podem ser restringidas das seguintes formas:

>[!NOTE]
>A definição de restrições de acesso ao utilizador dos hóspedes substituiu as **permissões dos utilizadores do Guest são** de definição limitada. Para obter orientações sobre a utilização desta funcionalidade, consulte [permissões de acesso ao hóspede (pré-visualização) no Azure Ative Directory](../enterprise-users/users-restrict-guest-permissions.md).

Permissão | Explicação da definição
---------- | ------------
Restrições de acesso ao utilizador dos hóspedes (Pré-visualização) | Definir esta opção para **os utilizadores do Guest tem o mesmo acesso que os membros** concedem todas as permissões do utilizador membro aos utilizadores convidados por padrão.<p>Definir esta opção para **o acesso do utilizador do Hóspede está restrito a propriedades e membros dos seus próprios objetos de diretório** restringem o acesso do hóspede apenas ao seu próprio perfil de utilizador por padrão. O acesso a outros utilizadores já não é permitido mesmo quando pesquisa por Nome Principal do Utilizador, ObjectId ou Nome de Exibição. O acesso a informações de grupos, incluindo membros de grupos, também já não é permitido.<p>**Nota:** Esta definição não impede o acesso a grupos unidos em alguns serviços da Microsoft 365, como o Microsoft Teams. Consulte [o acesso do Microsoft Teams Guest](/MicrosoftTeams/guest-access) para saber mais.<p>Os utilizadores convidados ainda podem ser adicionados às funções de administrador, independentemente destas definições de permissão.
Os convidados podem convidar | Definir esta opção para Sim permite que os hóspedes convidem outros hóspedes. Consulte [os convites do Delegado para a colaboração B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) para saber mais.
Os membros podem convidar | Definir esta opção para Sim permite que membros não administrativos do seu diretório convidem os hóspedes. Consulte [os convites do Delegado para a colaboração B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) para saber mais.
Os administradores e utilizadores na função de autor de convite podem convidar | Definir esta opção para Sim permite que administradores e utilizadores no papel de "Convidado Convidado" convidem os hóspedes. Quando definido para Sim, os utilizadores no papel de convidado convidado ainda poderão convidar os hóspedes, independentemente dos Membros podem convidar a configuração. Consulte [os convites do Delegado para a colaboração B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) para saber mais.

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

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre a definição de restrições de acesso ao utilizador dos hóspedes, consulte [permissões de acesso ao hóspede (pré-visualização) em Azure Ative Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Para saber mais sobre como atribuir funções de administrador AZure, consulte [atribuir um utilizador às funções de administrador no Azure Ative Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](add-users-azure-active-directory.md)