---
title: Resolução de problemas de colaboração do B2B - Azure Active Directory | Documentos da Microsoft
description: Soluções para problemas comuns com a colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: af106650f6e1d139ec7af2c8d243dc50f2e963fc
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293963"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Resolução de problemas de colaboração do Azure Active Directory B2B

Aqui estão algumas soluções para problemas comuns com a colaboração B2B do Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Eu adicionei um utilizador externo, mas não vê-los no meu livro de endereços globais ou no selecionador de pessoas

Em casos em que os utilizadores externos não são preenchidos na lista, o objeto poderá demorar alguns minutos a replicar.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um utilizador de convidados B2B não está visível no selecionador de pessoas do SharePoint Online/OneDrive 
 
A capacidade de pesquisar para utilizadores convidados existentes no selecionador de pessoas de SharePoint Online (SPO) está DESATIVADO por predefinição de acordo com o comportamento herdado.

Pode ativar esta funcionalidade, utilize a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível da coleção inquilino e o site. Pode definir a funcionalidade com os cmdlets Set-SPOTenant e Set-SPOSite, que permitem aos membros procurar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do inquilino não afetam os sites SPO já aprovisionados.

## <a name="invitations-have-been-disabled-for-directory"></a>Convites foram desativados para o diretório

Se for notificado de que não tem permissões para convidar utilizadores, certifique-se de que a sua conta de utilizador está autorizada a convidar utilizadores externos em definições do utilizador:

![Captura de ecrã que mostra as definições de utilizadores externos](media/troubleshoot/external-user-settings.png)

Se tiver modificado recentemente estas definições ou atribuída a função de utilizador que convida convidados para um utilizador, poderá haver um atraso de 60 de 15 minutos antes das alterações entrem em vigor.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O utilizador que eu convidado está a receber um erro durante o resgate

Erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Administrador do convidado não permitiu EmailVerified utilizadores de que está a ser criados no seu inquilino

Ao convidar utilizadores cuja organização está a utilizar o Azure Active Directory, mas em que não existe conta de utilizador específico (por exemplo, o utilizador não existe no Azure AD contoso.com). O administrador de contoso.com pode ter uma política de impedir que os usuários a ser criada. O utilizador tem de verificar com o seu administrador para determinar se são permitidos a utilizadores externos. Administrador do utilizador externo poderá ter de permitir que os utilizadores de verificado por E-Mail no respetivo domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) permitir que os utilizadores verificado de E-Mail).

![Erro a indicar que o inquilino não permite e-mail verificado utilizadores](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Utilizador externo não pode já existir num domínio federado

Se estiver a utilizar a autenticação de Federação e o utilizador ainda não existir no Azure Active Directory, o utilizador não pode ser convidado.

Para resolver este problema, o administrador do utilizador externo tem de sincronizar a conta de utilizador para o Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como dos faz\#', que não é normalmente um caráter válido, a sincronização com o Azure AD?

"\#" é um caractere reservado no UPNs para colaboração B2B do Azure AD ou utilizadores externos, porque a conta de convidado user@contoso.com torna-se user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Por conseguinte, \# UPNs proveniente no local não são permitidos para iniciar sessão no portal do Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar utilizadores externos a um grupo sincronizado

Utilizadores externos podem ser adicionados apenas para grupos de "Segurança" ou "atribuído" e não aos grupos que são controlado no local.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu uma mensagem de e-mail para resgatar

Mesmo o convidado deve verificar junto do respetivo filtro ISP ou spam para se certificar de que o seguinte endereço é permitido: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Eu tenha em atenção que a mensagem personalizada não é incluída com mensagens de convite às vezes

Para cumprir as leis de privacidade, as nossas APIs não incluem mensagens personalizadas no convite de e-mail quando:

- O autor do convite não tem um endereço de e-mail no inquilino do convite
- Quando um principal de serviço de aplicações envia o convite

Se este cenário é importante para si, pode suprimir a nossa e-mail de convite de API e enviá-lo através do mecanismo de e-mail da sua preferência. Consulte a orientador jurídico também sua organização certificar-se de que todos os e-mails que envia que também cumprem as leis de privacidade.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um utilizador convidado com um inquilino just-in-time ou "viral" não é possível repor a palavra-passe

Se o inquilino de identidade for um just-in-time (JIT) ou o inquilino viral (ou seja, é um inquilino do Azure separado e não gerenciado), apenas o utilizador convidado pode repor a palavra-passe. Por vezes, uma organização será [assuma a gestão de inquilinos virais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criadas quando os funcionários utilizam os respetivos endereços de e-mail de trabalho para se inscrever para os serviços. Depois da organização assume-se um inquilino viral, apenas um administrador nessa organização pode repor a palavra-passe do utilizador ou ativar a SSPR. Se necessário, como a organização que o convidou, pode remover a conta de utilizador convidado a partir do seu diretório e volte a enviar um convite.

## <a name="next-steps"></a>Passos Seguintes

- [Obtenha suporte para a colaboração B2B](get-support.md)
