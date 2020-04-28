---
title: Permissões de utilizador padrão - Diretório Ativo Azure / Microsoft Docs
description: Conheça as diferentes permissões de utilizador disponíveis no Diretório Ativo Azure.
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
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366258"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões padrão dos utilizadores no Diretório Ativo Azure?
No Azure Active Directory (Azure AD), é concedido a todos os utilizadores um conjunto de permissões predefinidas. O acesso de um utilizador consiste no tipo de utilizador, nas atribuições de [funções](active-directory-users-assign-role-azure-portal.md)e na propriedade de objetos individuais. Este artigo descreve essas permissões predefinidas e contém uma comparação entre as predefinições do utilizador membro e convidado. As permissões de utilizador predefinidas só podem ser alteradas nas definições do utilizador em AD Azure.

## <a name="member-and-guest-users"></a>Utilizadores membros e convidados
O conjunto de permissões por defeito recebidas depende se o utilizador é um membro nativo do inquilino (utilizador membro) ou se o utilizador é trazido de outro diretório como convidado de colaboração B2B (utilizador convidado). Veja o que é a [colaboração Azure AD B2B?](../b2b/what-is-b2b.md)
* Os utilizadores membros podem registar aplicações, gerir a fotografia de perfil e o número de telemóvel, alterar a palavra-passe e convidar convidados B2B. Além disso, os utilizadores podem ler todas as informações de diretório (com algumas exceções). 
* Os utilizadores convidados têm permissões restritas de diretório. Por exemplo, os utilizadores convidados não podem procurar informações do inquilino além das suas próprias informações de perfil. No entanto, um utilizador convidado pode obter informações sobre outro utilizador, fornecendo o Nome Principal de Utilizador ou o objectId. Um utilizador convidado pode ler propriedades de grupos a que pertencem, incluindo membros do grupo, independentemente das **permissões** dos utilizadores convidados são configurações limitadas. Um hóspede não pode ver informações sobre quaisquer outros objetos de inquilino.

As permissões predefinidas para os convidados são restritivas por predefinição. Os convidados podem ser adicionados a funções de administrador, que lhes concedem permissões totais de leitura e escrita contidas na função. Há uma restrição adicional disponível, a capacidade de os convidados convidarem outros convidados. Definir **Os convidados podem convidar** para **Não** impede os convidados de convidarem outros convidados. Veja [Delegar convites para colaboração B2B](../b2b/delegate-invitations.md) para saber como. Para conceder aos utilizadores convidados as mesmas permissões dos utilizadores membros por predefinição, defina **As permissões dos utilizadores convidados são limitadas** para **Não**. Esta definição concede a todos os utilizadores membros permissões para convidar utilizadores por predefinição, bem com permite que sejam adicionados convidados a funções administrativas.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar as permissões predefinidas de membro e convidado

**Área** | **Permissões de utilizador membro** | **Permissões de utilizador convidado**
------------ | --------- | ----------
Utilizadores e contactos | Ler todas as propriedades públicas de utilizadores e contactos<br>Convidar convidados<br>Alterar a própria palavra-passe<br>Gerir o próprio número de telemóvel<br>Gerir a própria fotografia<br>Invalidar os próprios tokens de atualização | Ler as próprias propriedades<br>Leia o nome do ecrã, e-mail, sinal de nome, foto, nome principal do utilizador e propriedades do tipo de utilizador de outros utilizadores e contactos<br>Alterar a própria palavra-passe
Grupos | Criar grupos de segurança<br>Criar grupos do Office 365<br>Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações de grupo de leitura oculto do Office 365 para grupo associado<br>Gerir propriedades, propriedade e adesão de grupos que o utilizador detém<br>Adicionar convidados a grupos detidos<br>Gerir definições de associação dinâmica<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365 | Ler todas as propriedades dos grupos<br>Ler as associações de grupo não oculto<br>Associações a grupo de leitura oculto do Office 365 para grupos associados<br>Gerir grupos detidos<br>Adicionar convidados a grupos detidos (se permitido)<br>Eliminar grupos detidos<br>Restaurar grupos detidos do Office 365<br>Leia propriedades de grupos a que pertencem, incluindo a desafiliação.
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
Os utilizadores podem registar a aplicação | Definir esta opção a Não impede os utilizadores de criar registos de aplicações. A capacidade pode então ser concedida de volta a indivíduos específicos, adicionando-os ao papel de Desenvolvedor de Aplicações.
Permitir que os utilizadores conectem trabalho ou conta escolar com o LinkedIn | Definir esta opção a Não impede que os utilizadores conectem o seu trabalho ou conta escolar com a sua conta LinkedIn. Para mais informações, consulte a partilha e consentimento das ligações da [conta LinkedIn.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)
Capacidade de criar grupos de segurança | Definir esta opção para Não impede que os utilizadores criem grupos de segurança. Administradores globais e administradores de utilizadores ainda podem criar grupos de segurança. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos do Office 365 | Definir esta opção para Não impede que os utilizadores criem grupos do Office 365. Definir esta opção para Alguns permite que um conjunto selecionado de utilizadores crie grupos do Office 365. Os administradores globais e os administradores de utilizadores ainda poderão criar grupos do Office 365. Veja [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir esta opção para Não permite que os não administradores utilizem o portal de administração da AD Azure para ler e gerir os recursos da AD Azure. Sim, restringe todos os não administradores de acederem a quaisquer dados da AD Azure no portal da administração. Importante a notar: esta definição não restringe o acesso aos dados da AD Azure utilizando o PowerShell ou outros clientes, como o Visual Studio. Quando definido para Sim, para conceder a um utilizador não administrador específico a capacidade de usar o portal de administração da AD Azure atribuir qualquer papel administrativo, como o papel de Leitores de Diretório. Esta função permite a leitura de informações básicas de diretório, que os utilizadores membros têm por defeito (os hóspedes e os diretores de serviço não têm).
Capacidade de ler outros utilizadores | Esta definição só está disponível no PowerShell. A definição desta bandeira para $false impede que todos os não administradores leiam as informações dos utilizadores do diretório. Esta bandeira não impede a leitura de informações dos utilizadores noutros serviços da Microsoft, como o Exchange Online. Esta definição destina-se a circunstâncias especiais e não é recomendada a colocação desta bandeira $false.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registo de aplicação
Quando um utilizador regista uma aplicação, é adicionado automaticamente como um proprietário da aplicação. Como proprietário, pode gerir os metadados da aplicação, como o nome e as permissões que a aplicação requer. Também pode gerir a configuração específica de inquilino da aplicação, como a configuração do início de sessão único e as atribuições de utilizador. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões do proprietário da aplicação da empresa
Quando um utilizador adiciona uma nova aplicação empresarial, é adicionado automaticamente como proprietário. Como proprietário, podem gerir a configuração específica do arrendatário da aplicação, tais como a configuração, provisionamento e atribuição de utilizadores SSO. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos Administradores Globais, os proprietários só podem gerir as aplicações que possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um utilizador cria um grupo, é adicionado automaticamente como um proprietário desse grupo. Como proprietário, eles podem gerir propriedades do grupo, como o nome, bem como gerir a adesão ao grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de utilizadores, os proprietários só podem gerir os grupos que possuem. Para atribuir um proprietário de grupo, veja [Gerir os proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas que se seguem descrevem as permissões específicas nos utilizadores do Azure Ative Directory que têm sobre objetos propriedade. O utilizador só tem estas permissões em objetos que possuem.

#### <a name="owned-application-registrations"></a>Registos de candidaturas próprias
Os utilizadores podem realizar as seguintes ações nos registos de aplicações de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/applications/audience/update | Atualizar aplicações.propriedade do público em Azure Ative Directory. |
| microsoft.directy/applications/autenticação/atualização | Atualizar aplicações.propriedade de autenticação no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/credentials/update | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/applications/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/applications/permissions/update | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.directy/applications/policies/update | Atualizar aplicações.policies property in Azure Ative Directory. |
| microsoft.diretório/aplicações/restauro | Restaurar as aplicações no Diretório Ativo Azure. |

#### <a name="owned-enterprise-applications"></a>Aplicações empresariais próprias
Os utilizadores podem realizar as seguintes ações em aplicações empresariais próprias. Uma aplicação empresarial é composta por um diretor de serviço, uma ou mais políticas de aplicação, e por vezes um objeto de aplicação no mesmo inquilino que o diretor de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/policies/basic/update | Atualizar propriedades básicas sobre políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/apagar | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/owners/update | Atualizar políticas.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/update | Atualizar serviçoSPrincipais.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/audience/update | Atualizar serviçoSPrincipais.propriedade do público em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/autenticação/atualização | Atualizar serviçoSPrincipais.propriedade de autenticação em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/update | Atualizar propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/credenciais/atualização | Atualizar serviçoSPrincipais.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/delete | Elimine os principais de serviço no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/proprietários/atualização | Atualizar serviçoSPrincipais.proprietários propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/permissões/atualização | Atualizar serviçoSPrincipais.permissions propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |

#### <a name="owned-devices"></a>Dispositivos de propriedade
Os utilizadores podem realizar as seguintes ações em dispositivos propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/desativar | Desative os dispositivos no Diretório Ativo Azure. |

#### <a name="owned-groups"></a>Grupos de propriedade
Os utilizadores podem realizar as seguintes ações em grupos de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/groups/appRoleAssignments/update | Update groups.appRoleAssignments property in Azure Ative Directory. |
| microsoft.directy/groups/basic/update | Atualizar propriedades básicas em grupos em Azure Ative Directory. |
| microsoft.diretório/grupos/eliminar | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/dynamicMembershipRule/update | Atualizar grupos.dynamicMembershipRule propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/restauro | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/settings/update | Atualizar grupos.configura a propriedade no Diretório Ativo Azure. |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como atribuir funções de administrador da AD Azure, consulte Atribuir um utilizador às funções de [administrador no Diretório Ativo do Azure](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](add-users-azure-active-directory.md)
