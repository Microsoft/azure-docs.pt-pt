---
title: Solução de problemas de colaboração B2B – Azure Active Directory | Microsoft Docs
description: Soluções para problemas comuns com a colaboração Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f91ddee8668316df69c98ed14fbcabcb06b6da82
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983403"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solução de problemas Azure Active Directory colaboração B2B

Aqui estão algumas soluções para problemas comuns com a colaboração B2B do Azure Active Directory (Azure AD).

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um usuário externo, mas não os vejo no meu catálogo de endereços global ou no seletor de pessoas

Nos casos em que os usuários externos não são populados na lista, o objeto pode levar alguns minutos para ser replicado.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um usuário convidado B2B não está aparecendo no seletor de pessoas do SharePoint Online/OneDrive

A capacidade de Pesquisar usuários convidados existentes no seletor de pessoas do SharePoint Online (SPO) está desativada por padrão para corresponder ao comportamento herdado.

Você pode habilitar esse recurso usando a configuração ' ShowPeoplePickerSuggestionsForGuestUsers ' no nível do locatário e do conjunto de sites. Você pode definir o recurso usando os cmdlets Set-SPOTenant e Set-SPOSite, que permitem que os membros pesquisem todos os usuários convidados existentes no diretório. As alterações no escopo do locatário não afetam os sites SPO já provisionados.

## <a name="invitations-have-been-disabled-for-directory"></a>Os convites foram desabilitados para o diretório

Se você for notificado de que não tem permissões para convidar usuários, verifique se sua conta de usuário está autorizada a convidar usuários externos em Azure Active Directory > configurações de usuário > usuários externos > Gerenciar configurações de colaboração externas:

![Captura de tela mostrando as configurações de usuários externos](media/troubleshoot/external-user-settings.png)

Se você modificou recentemente essas configurações ou atribuiu a função de convite do convidado a um usuário, pode haver um atraso de 15-60 minutos antes que as alterações entrem em vigor.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O usuário que convidou está recebendo um erro durante o resgate

Erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>O administrador do convidado não permitiu a criação de usuários verificados em seu locatário

Ao convidar usuários cuja organização está usando Azure Active Directory, mas onde a conta do usuário específico não existe (por exemplo, o usuário não existe no Azure AD contoso.com). O administrador do contoso.com pode ter uma política em vigor, impedindo que os usuários sejam criados. O usuário deve verificar com seu administrador para determinar se usuários externos são permitidos. O administrador do usuário externo pode precisar permitir usuários verificados por email em seu domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sobre como permitir usuários verificados por email).

![Erro informando que o locatário não permite usuários verificados por email](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>O usuário externo já não existe em um domínio federado

Se você estiver usando a autenticação de Federação e o usuário ainda não existir no Azure Active Directory, o usuário não poderá ser convidado.

Para resolver esse problema, o administrador do usuário externo deve sincronizar a conta do usuário para Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como o '\#', que normalmente não é um caractere válido, é sincronizado com o Azure AD?

"\#" é um caractere reservado em UPNs para colaboração B2B do Azure ad ou usuários externos, pois a conta user@contoso.com convidada se torna user_contoso.@fabrikam.onmicrosoft.comcom # ext #. Portanto, \# em UPNs provenientes do local não têm permissão para entrar no portal do Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar usuários externos a um grupo sincronizado

Os usuários externos podem ser adicionados somente a grupos "atribuídos" ou "segurança" e não a grupos que são mestres no local.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu um email para resgatar

O convidado deve verificar com seu ISP ou filtro de spam para garantir que o seguinte endereço seja permitido:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Observei que a mensagem personalizada não é incluída com mensagens de convite às vezes

Para obedecer às leis de privacidade, nossas APIs não incluem mensagens personalizadas no convite por email quando:

- O emissor do convite não tem um endereço de email no locatário que está convidando
- Quando uma entidade de appservice envia o convite

Se esse cenário for importante para você, você poderá suprimir nosso email de convite de API e enviá-lo por meio do mecanismo de email de sua escolha. Consulte o advogado legal de sua organização para ter certeza de que qualquer email enviado dessa maneira também esteja em conformidade com as leis de privacidade.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Você recebe um erro "AADSTS65005" ao tentar fazer logon em um recurso do Azure

Um usuário que tem uma conta de convidado não pode fazer logon e está recebendo a seguinte mensagem de erro:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

O usuário tem uma conta de usuário do Azure e é um locatário viral que foi abandonado ou não gerenciado. Além disso, não há administradores globais ou de empresa no locatário.

Para resolver esse problema, você deve assumir o locatário abandonado. Consulte [assumir um diretório não gerenciado como administrador em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Você também deve acessar o DNS voltado para a Internet para o sufixo de domínio em questão a fim de fornecer evidências diretas de que você está no controle do namespace. Depois que o locatário for retornado a um estado gerenciado, discuta com o cliente se deixar os usuários e o nome de domínio verificado é a melhor opção para sua organização.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um usuário convidado com um locatário just-in-time ou "viral" não pode redefinir sua senha

Se o locatário de identidade for um locatário JIT (just-in-time) ou viral (ou seja, um locatário do Azure separado e não gerenciado), somente o usuário convidado poderá redefinir sua senha. Às vezes, uma organização [assumirá o gerenciamento de locatários viral](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criados quando os funcionários usam seus endereços de email de trabalho para se inscreverem nos serviços. Depois que a organização assume um locatário viral, somente um administrador na organização pode redefinir a senha do usuário ou habilitar o SSPR. Se necessário, como a organização que convida, você pode remover a conta de usuário convidado do seu diretório e reenviar um convite.

## <a name="next-steps"></a>Passos Seguintes

[Obter suporte para colaboração B2B](get-support.md)
