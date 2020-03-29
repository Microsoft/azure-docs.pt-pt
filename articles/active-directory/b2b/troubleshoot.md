---
title: Colaboração B2B de resolução de problemas - Diretório Ativo Azure / Microsoft Docs
description: Soluções para problemas comuns com colaboração Azure Ative Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050788"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Resolução de problemas Azure Ative Directory B2B colaboração

Aqui estão alguns remédios para problemas comuns com a colaboração do Azure Ative Directory (Azure AD) B2B.

   > [!IMPORTANT]
   > A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas AD Azure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optarem pela autenticação de código de acesso único por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um utilizador externo, mas não os vejo no meu Livro de Endereços Global ou no selecionador de pessoas.

Nos casos em que os utilizadores externos não são povoados na lista, o objeto pode demorar alguns minutos a replicar-se.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um utilizador convidado B2B não aparece no SharePoint Online/OneDrive picker

A capacidade de pesquisar utilizadores de hóspedes existentes no picker de pessoas SharePoint Online (SPO) é OFF por padrão para corresponder ao comportamento do legado.

Pode ativar esta funcionalidade utilizando a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível do inquilino e da recolha do site. Pode definir a funcionalidade utilizando os cmdlets Set-SPOTenant e Set-SPOSite, que permitem aos membros pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito dos inquilinos não afetam os sítios SPO já previstos.

## <a name="invitations-have-been-disabled-for-directory"></a>Convites foram desativados para diretório

Se for notificado de que não tem permissões para convidar utilizadores, verifique se a sua conta de utilizador está autorizada a convidar utilizadores externos ao abrigo das definições do Utilizador > Do Utilizador > Utilizadores Externos > Gerir as definições de colaboração externa:

![Screenshot mostrando as definições de Utilizadores Externos](media/troubleshoot/external-user-settings.png)

Se modificou recentemente estas definições ou atribuiu a função de Convidado de Hóspedes a um utilizador, pode haver um atraso de 15 a 60 minutos antes de as alterações produzirem efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O utilizador que convidei está a receber um erro durante a redenção.

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>A Administração do Convite repudiou os utilizadores verificados por email de serem criados no seu inquilino

Ao convidar utilizadores cuja organização está a utilizar o Diretório Ativo do Azure, mas onde a conta específica do utilizador não existe (por exemplo, o utilizador não existe no Azure AD contoso.com). O administrador da contoso.com pode ter uma política em vigor que impeça a criação de utilizadores. O utilizador deve consultar o seu administrador para determinar se os utilizadores externos são permitidos. A administração do utilizador externo pode ter de permitir Enviar por email o artigo Utilizadores verificados no seu domínio (ver este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sobre a possibilidade de utilizadores verificados).

![Erro ao declarar que o inquilino não permite utilizadores verificados por e-mail](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Utilizador externo já não existe num domínio federado

Se estiver a utilizar a autenticação da federação e o utilizador já não existir no Diretório Ativo Do Azure, o utilizador não pode ser convidado.

Para resolver este problema, o administrador do utilizador externo deve sincronizar a conta do utilizador ao Diretório Ativo Azure.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como é\#que ' ', que normalmente não é um personagem válido, sincroniza com o Azure AD?

"\#" é um personagem reservado em UPNs para colaboração Azure AD B2B ou utilizadores externos, porque a conta user@contoso.com convidada torna-se user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Portanto, \# nas UPNs provenientes do local não estão autorizados a entrar no portal Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar utilizadores externos a um grupo sincronizado

Os utilizadores externos só podem ser adicionados a grupos "atribuídos" ou "segurança" e não a grupos que são dominados no local.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>O meu utilizador externo não recebeu um e-mail para resgatar

O convidado deve consultar com o seu isp ou filtro de spam para garantir que o seguinte endereço é permitido:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Noto que a mensagem personalizada não é incluída com mensagens de convite às vezes

Para cumprir as leis de privacidade, as nossas APIs não incluem mensagens personalizadas no convite por e-mail quando:

- O convidado não tem um endereço de e-mail no inquilino convidativo
- Quando um diretor de serviço de aplicações envia o convite

Se este cenário for importante para si, pode suprimir o nosso e-mail de convite API e enviá-lo através do mecanismo de e-mail da sua escolha. Consulte o advogado da sua organização para se certificar de que qualquer e-mail que envie desta forma também está em conformidade com as leis de privacidade.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Recebe um erro "AADSTS65005" quando tenta iniciar sessão num recurso Azure

Um utilizador que tenha uma conta de hóspedes não pode iniciar sessão e está a receber a seguinte mensagem de erro:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

O utilizador tem uma conta de utilizador Azure e é um inquilino viral que foi abandonado ou não gerido. Além disso, não existem administradores globais ou da empresa no inquilino.

Para resolver este problema, deve assumir o inquilino abandonado. Consulte a [Assumindo um diretório não gerido como administrador no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Deve também aceder ao DNS virado para a Internet para o sufixo de domínio em questão, a fim de fornecer provas diretas de que está a controlar o espaço de nome. Depois de o inquilino ser devolvido a um estado gerido, por favor discuta com o cliente se deixar os utilizadores e o nome de domínio verificado é a melhor opção para a sua organização.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um utilizador convidado com um inquilino just-in-time ou "viral" é incapaz de redefinir a sua palavra-passe

Se o inquilino de identidade for um inquilino justo (JIT) ou um inquilino viral (o que significa que é um inquilino Azure separado e não gerido), apenas o utilizador hóspede pode redefinir a sua palavra-passe. Por vezes, uma organização assume a [gestão de inquilinos virais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criados quando os funcionários usam os seus endereços de e-mail de trabalho para se inscreverem para serviços. Depois de a organização assumir um inquilino viral, apenas um administrador dessa organização pode redefinir a palavra-passe do utilizador ou ativar o SSPR. Se necessário, como organização convidativa, pode remover a conta de utilizador convidado do seu diretório e reenviar um convite.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Um utilizador convidado não pode utilizar o módulo AzureAD PowerShell V1

A partir de 18 de novembro de 2019, os utilizadores convidados do seu diretório (definidos como contas de utilizador onde a propriedade **userType** é igual ao **Guest)** estão impedidos de usar o módulo AzureAD PowerShell V1. Para a frente, um utilizador terá de ser um utilizador membro (quando o **utilizadorType** é igual a **Membro)** ou utilizar o módulo AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Num inquilino do Governo dos EUA, não posso convidar um utilizador convidado de colaboração B2B.

Dentro da nuvem do Governo dos EUA, a colaboração B2B é atualmente apoiada apenas entre inquilinos que estão tanto dentro da nuvem do Governo dos EUA azure como que ambos apoiam a colaboração B2B. Se convidar um utilizador num inquilino que não faz parte da nuvem do Governo dos EUA ou que ainda não apoia a colaboração B2B, terá um erro. Para mais detalhes e limitações, consulte [O Diretório Ativo Azure Premium P1 e As Variações P2.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)

## <a name="next-steps"></a>Passos seguintes

[Obtenha apoio para colaboração B2B](get-support.md)
