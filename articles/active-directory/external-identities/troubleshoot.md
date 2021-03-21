---
title: Resolução de problemas da colaboração B2B - Azure Ative Directory | Microsoft Docs
description: Remédios para problemas comuns com a colaboração B2B do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365434"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Resolução de problemas Azure Ative Directy B2B colaboração

Aqui estão alguns remédios para problemas comuns com a colaboração B2B do Azure Ative Directory (Azure AD).

   > [!IMPORTANT]
   > - **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **A partir de outubro de 2021, a** Microsoft deixará de apoiar o resgate de convites através da criação de contas AZure AD não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um utilizador externo, mas não os vejo no meu Livro de Endereços Global ou no selecionador de pessoas

Nos casos em que os utilizadores externos não são preenchidos na lista, o objeto pode demorar alguns minutos a replicar-se.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um utilizador convidado B2B não aparece no picker de pessoas SharePoint Online/OneDrive

A capacidade de procurar utilizadores convidados existentes no picker de pessoas SharePoint Online (SPO) é OFF por padrão para corresponder ao comportamento do legado.

Pode ativar esta funcionalidade utilizando a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível do arrendatário e da recolha do site. Pode definir a funcionalidade utilizando os Set-SPOTenant e Set-SPOSite cmdlets, que permitem aos membros pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do arrendatário não afetam os sítios SPO já previstos.

## <a name="invitations-have-been-disabled-for-directory"></a>Convites foram desativados para diretório

Se for notificado de que não tem permissões para convidar os utilizadores, verifique se a sua conta de utilizador está autorizada a convidar utilizadores externos ao abrigo do Azure Ative Directory > as definições do Utilizador > utilizadores externos > Gerir as definições de colaboração externa:

![Screenshot mostrando as definições de utilizadores externos](media/troubleshoot/external-user-settings.png)

Se recentemente modificou estas definições ou atribuiu o papel de Convidado a um utilizador, poderá haver um atraso de 15 a 60 minutos antes de as alterações produzirem efeitos.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O utilizador que convidei está a receber um erro durante o resgate

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>A Administração do Invitee não permitiu que os Utilizadores EmailVerified fossem criados no seu inquilino

Ao convidar utilizadores cuja organização está a utilizar o Azure Ative Directory, mas onde a conta específica do utilizador não existe (por exemplo, o utilizador não existe em Azure AD contoso.com). O administrador da contoso.com pode ter uma política em vigor que impeça a criação dos utilizadores. O utilizador deve verificar com o seu administrador para determinar se os utilizadores externos são autorizados. O administrador externo pode ter de permitir Enviar por email o artigo Utilizadores verificados no seu domínio (ver este [artigo](/powershell/module/msonline/set-msolcompanysettings) sobre permitir emailss).

![Erro declarando que o inquilino não permite e-mail verificado utilizadores](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Utilizador externo já não existe num domínio federado

Se estiver a utilizar a autenticação da Federação e o utilizador já não existir no Diretório Azure Ative, o utilizador não pode ser convidado.

Para resolver este problema, o administrador do utilizador externo deve sincronizar a conta do utilizador com o Azure Ative Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como é que ' \# ', que normalmente não é um personagem válido, sincroniza com Azure AD?

\#" " é um personagem reservado em UPNs para colaboração Azure AD B2B ou utilizadores externos, porque a conta convidada user@contoso.com torna-se user_contoso.com#EXT# @fabrikam.onmicrosoft.com . Portanto, \# em UPNs vindos de instalações não é permitido entrar no portal Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar utilizadores externos a um grupo sincronizado

Os utilizadores externos só podem ser adicionados a grupos "atribuídos" ou "de segurança" e não a grupos que são dominados no local.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>O meu utilizador externo não recebeu um e-mail para resgatar

O convidado deve verificar com o seu ISP ou filtro de spam para garantir que o seguinte endereço é permitido: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Noto que a mensagem personalizada não é incluída com mensagens de convite às vezes

Para cumprir as leis de privacidade, as nossas APIs não incluem mensagens personalizadas no convite por e-mail quando:

- O convidado não tem um endereço de e-mail no inquilino convidado
- Quando um diretor de serviço de aplicações envia o convite

Se este cenário for importante para si, pode suprimir o nosso e-mail de convite da API e enviá-lo através do mecanismo de e-mail à sua escolha. Consulte o advogado da sua organização para se certificar de que qualquer e-mail que envie desta forma também está em conformidade com as leis de privacidade.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Recebe um erro "AADSTS65005" quando tenta iniciar sessão num recurso Azure

Um utilizador que tenha uma conta de hóspedes não pode iniciar sessão e está a receber a seguinte mensagem de erro:

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

O utilizador tem uma conta de utilizador Azure e é um inquilino viral que foi abandonado ou não gerido. Além disso, não existem administradores globais no inquilino.

Para resolver este problema, tem de tomar conta do inquilino abandonado. Consulte a  [tomada de posse de um diretório não gerido como administrador no Azure Ative Directory](../enterprise-users/domains-admin-takeover.md). Também deve aceder ao DNS virado para a Internet para o sufixo de domínio em questão, de modo a fornecer provas diretas de que está a controlar o espaço de nome. Depois de o arrendatário ser devolvido a um estado gerido, por favor discuta com o cliente se deixar os utilizadores e o nome de domínio verificado é a melhor opção para a sua organização.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um utilizador convidado com um inquilino just-in-time ou "viral" é incapaz de redefinir a sua palavra-passe

Se o inquilino de identidade for um inquilino just-in-time (JIT) ou inquilino viral (o que significa que é um inquilino Azure separado e não gerido), apenas o utilizador convidado pode redefinir a sua senha. Por vezes, uma organização [assume a gestão de inquilinos virais](../enterprise-users/domains-admin-takeover.md) que são criados quando os funcionários usam os seus endereços de e-mail de trabalho para se inscreverem para serviços. Após a organização assumir um inquilino viral, apenas um administrador nessa organização pode redefinir a palavra-passe do utilizador ou ativar o SSPR. Se necessário, como organização convidativa, pode remover a conta de utilizador do seu diretório e reensuitar um convite.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Um utilizador convidado não pode utilizar o módulo AzureAD PowerShell V1

A partir de 18 de novembro de 2019, os utilizadores convidados do seu diretório (definidos como contas de utilizador onde a propriedade **userType** é igual **a Guest)** estão impedidos de usar o módulo AzureAD PowerShell V1. Para a frente, um utilizador terá de ser um utilizador membro (onde **o utilizadorType** é igual **a membro)** ou utilizar o módulo AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Num inquilino do Governo dos EUA, não posso convidar um utilizador convidado de colaboração B2B

Dentro da nuvem do Governo dos EUA, a colaboração B2B é atualmente apenas apoiada entre inquilinos que estão dentro da nuvem do Governo dos EUA e que ambos apoiam a colaboração B2B. Se convidar um utilizador para um inquilino que não faça parte da nuvem do Governo dos EUA ou que ainda não apoie a colaboração B2B, terá um erro. Para mais detalhes e limitações, consulte [Azure Ative Directory Premium P1 e P2 Variações.](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Eu recebo o erro que a AZure AD não pode encontrar a aad-extensions-app no meu inquilino

Ao utilizar funcionalidades de inscrição de autosserviço, como atributos personalizados do utilizador ou fluxos de utilizador, uma aplicação chamada `aad-extensions-app. Do not modify. Used by AAD for storing user data.` é criada automaticamente. É usado pela Azure AD Identidades Externas para armazenar informações sobre utilizadores que se inscrevam e atributos personalizados recolhidos.

Se tiver eliminado acidentalmente a `aad-extensions-app`, terá 30 dias para a recuperar. Pode restaurar a aplicação utilizando o módulo Azure AD PowerShell.

1. Lançar o módulo Azure AD PowerShell e executar `Connect-AzureAD` .
1. Inscreva-se como administrador global para o inquilino da Azure AD para o que pretende recuperar a aplicação eliminada.
1. Executar o comando PowerShell `Get-AzureADDeletedApplication` .
1. Encontre a aplicação na lista onde o nome do visor começa `aad-extensions-app` e copie o seu `ObjectId` valor patrimonial.
1. Executar o comando PowerShell `Restore-AzureADDeletedApplication -ObjectId {id}` . Substitua a `{id}` parte do comando pela do passo `ObjectId` anterior.

Deverá agora ver a aplicação restaurada no portal Azure.

## <a name="next-steps"></a>Passos seguintes

[Obtenha apoio para a colaboração B2B](../fundamentals/active-directory-troubleshooting-support-howto.md)
