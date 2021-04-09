---
title: Resgate de convites em colaboração B2B - Azure AD
description: Descreve a experiência de resgate de convites de colaboração Azure AD B2B para utilizadores finais, incluindo o acordo para termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd0febe5ffbc1b17718043d5fc97b804f87cc46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199730"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate de convite para colaboração B2B do Azure Active Directory

Este artigo descreve as formas como os utilizadores convidados podem aceder aos seus recursos e ao processo de consentimento que irão encontrar. Se enviar um e-mail de convite ao hóspede, o convite inclui um link que o hóspede pode resgatar para ter acesso à sua app ou portal. O e-mail de convite é apenas uma das formas de os hóspedes poderem ter acesso aos seus recursos. Como alternativa, pode adicionar os hóspedes ao seu diretório e dar-lhes uma ligação direta ao portal ou app que pretende partilhar. Independentemente do método que usam, os hóspedes são guiados através de um processo de consentimento pela primeira vez. Este processo garante que os seus hóspedes concordam com os termos de privacidade e aceitam quaisquer [termos de uso](../conditional-access/terms-of-use.md) que tenha configurado.

Quando adiciona um utilizador convidado ao seu diretório, a conta de utilizador do hóspede tem um estado de consentimento (visível no PowerShell) que está inicialmente definido para **PendenteAcceptance**. Esta configuração permanece até que o hóspede aceite o seu convite e concorde com a sua política de privacidade e termos de uso. Depois disso, o estado de consentimento muda para **Aceito**, e as páginas de consentimento deixaram de ser apresentadas ao hóspede.

   > [!IMPORTANT]
   > - **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **A partir de outubro de 2021, a** Microsoft deixará de apoiar o resgate de convites através da criação de contas AZure AD não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Redenção e inscrição através de um ponto final comum

Os utilizadores convidados podem agora iniciar sposição nas suas aplicações de primeira parte ou da Microsoft através de um ponto final comum (URL), por `https://myapps.microsoft.com` exemplo. Anteriormente, um URL comum redirecionaria um utilizador convidado para o seu inquilino de casa em vez do seu inquilino de recursos para a autenticação, pelo que era necessário um link específico para o inquilino (por `https://myapps.microsoft.com/?tenantid=<tenant id>` exemplo). Agora o utilizador convidado pode ir ao URL comum da aplicação, escolher **opções de Inscrição** e, em seguida, selecionar **Iniciar sposição numa organização**. Em seguida, o utilizador escreve o nome da sua organização.

![Sinal de ponto final comum](media/redemption-experience/common-endpoint-flow-small.png)

O utilizador é então redirecionado para o seu ponto final, onde pode iniciar sôms com o seu endereço de e-mail ou selecionar um fornecedor de identidade que configuraste.

## <a name="redemption-through-a-direct-link"></a>Redenção através de um link direto

Como alternativa ao e-mail de convite ou url comum de uma aplicação, pode dar a um hóspede um link direto para a sua app ou portal. Primeiro tem de adicionar o utilizador convidado ao seu diretório através do [portal Azure](./b2b-quickstart-add-guest-users-portal.md) ou [PowerShell](./b2b-quickstart-invite-powershell.md). Em seguida, pode utilizar qualquer uma das [formas personalizáveis de implementar aplicações para os utilizadores](../manage-apps/end-user-experiences.md), incluindo links de acesso direto. Quando um hóspede usa um link direto em vez do e-mail de convite, eles ainda serão guiados através da experiência de consentimento pela primeira vez.

> [!NOTE]
> Uma ligação direta é específica do inquilino. Por outras palavras, inclui um ID do inquilino ou domínio verificado para que o hóspede possa ser autenticado no seu inquilino, onde a app partilhada está localizada. Aqui estão alguns exemplos de ligações diretas com o contexto do inquilino:
 > - Painel de acesso a apps: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Painel de acesso de aplicações para um domínio verificado: `https://myapps.microsoft.com/<;verified domain>`
 > - Portal Azure: `https://portal.azure.com/<tenant id>`
 > - Aplicação individual: veja como usar um [link de acesso direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Existem alguns casos em que o e-mail de convite é recomendado por um link direto. Se estes casos especiais forem importantes para a sua organização, recomendamos que convide os utilizadores utilizando métodos que ainda enviam o e-mail do convite:
 - O utilizador não tem uma conta AD Azure, um MSA ou uma conta de e-mail numa organização federada. A menos que você esteja usando a funcionalidade de senha única, o hóspede precisa resgatar o e-mail de convite para ser guiado através dos passos para criar um MSA.
 - Por vezes, o objeto de utilizador convidado pode não ter um endereço de e-mail devido a um conflito com um objeto de contacto (por exemplo, um objeto de contacto do Outlook). Neste caso, o utilizador deve clicar no URL de resgate no e-mail do convite.
 - O utilizador pode iniciar sôms com um pseudónimo do endereço de e-mail que foi convidado. (Um pseudónimo é um endereço de e-mail adicional associado a uma conta de e-mail.) Neste caso, o utilizador deve clicar no URL de resgate no e-mail do convite.

## <a name="redemption-through-the-invitation-email"></a>Redenção através do e-mail de convite

Quando adiciona um utilizador convidado ao seu diretório [utilizando o portal Azure,](./b2b-quickstart-add-guest-users-portal.md)é enviado um e-mail de convite ao hóspede no processo. Também pode optar por enviar e-mails de convite quando estiver a [usar o PowerShell](./b2b-quickstart-invite-powershell.md) para adicionar utilizadores convidados ao seu diretório. Aqui está uma descrição da experiência do hóspede quando eles resgatar o link no e-mail.

1. O hóspede recebe um [e-mail de convite](./invitation-email-elements.md) enviado da **Microsoft Invitations.**
2. O hóspede seleciona **Aceite convite** no e-mail.
3. O hóspede usará as suas credenciais para se inscrever no seu diretório. Se o hóspede não tiver uma conta que possa ser federada para o seu diretório e a funcionalidade [de senha única (OTP) por e-mail](./one-time-passcode.md) não estiver ativada; o hóspede é solicitado a criar um [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal ou uma [conta de autosserviço AZure AD](../enterprise-users/directory-self-service-signup.md). Consulte o fluxo de resgate de [convites](#invitation-redemption-flow) para mais detalhes.
4. O hóspede é guiado através da [experiência de consentimento](#consent-experience-for-the-guest) descrita abaixo.
## <a name="invitation-redemption-flow"></a>Fluxo de resgate de convites

Quando um utilizador clica no link de **convite Accept** num [e-mail de convite,](invitation-email-elements.md)a Azure AD resgata automaticamente o convite com base no fluxo de resgate, como mostrado abaixo:

![Screenshot mostrando o diagrama do fluxo de redenção](media/redemption-experience/invitation-redemption-flow.png)

**Se o Nome Principal do Utilizador (UPN) corresponder tanto a uma conta Azure AD existente como a uma conta MSA pessoal, o utilizador será solicitado a escolher com que conta pretendem resgatar.*

1. A Azure AD realiza uma descoberta baseada no utilizador para determinar se o utilizador existe num [inquilino AD Azure existente.](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)

2. Se um administrador tiver ativado a [federação direta,](./direct-federation.md)o Azure AD verifica se o sufixo de domínio do utilizador corresponde ao domínio de um fornecedor de identidade CONFIGURADO SAML/WS-Fed e redireciona o utilizador para o fornecedor de identidade pré-configurado.

3. Se um administrador tiver ativado a [federação](./google-federation.md)da Google , o Azure AD verifica se o sufixo de domínio do utilizador é gmail.com ou googlemail.com e redireciona o utilizador para o Google.

4. O processo de resgate verifica se o utilizador tem uma conta pessoal da [Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) existente para reembolsos just-in-time (JIT), mas não para o resgate de link de e-mail de convite. Se o utilizador já tiver um MSA existente, eles vão iniciar súm em assal com a MSA existente.

5. Uma vez identificado o **diretório domiciliário** do utilizador, o utilizador é enviado ao fornecedor de identidade correspondente para iniciar seduque.  

6. Se os passos 1 a 4 não encontrarem um diretório de casa para o utilizador convidado, então a Azure AD determina se o inquilino convidado ativou a funcionalidade [de senha única (OTP)](./one-time-passcode.md) para os hóspedes.

7. Se [o e-mail de senha única para os hóspedes estiver ativado,](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)é enviada uma senha para o utilizador através do e-mail convidado. O utilizador irá recuperar e introduzir esta senha na página de início de sção AZure AD.

8. Se o código de acesso de uma vez por e-mail para os hóspedes for desativado, a Azure AD verifica o sufixo de domínio para determinar se pertence a uma conta de consumo. Em caso afirmativo, o utilizador é solicitado a criar uma conta pessoal [da Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Caso contrário, o utilizador é solicitado a criar uma [conta de autosserviço AZure AD](../enterprise-users/directory-self-service-signup.md).

9. A Azure AD tenta criar uma [conta de self-service Azure AD](../enterprise-users/directory-self-service-signup.md) verificando o acesso ao e-mail. A verificação da conta é feita enviando um código para o e-mail, e fazendo com que o utilizador recupere e submeta-o para Azure AD. No entanto, se o inquilino do utilizador convidado for federado ou se o campo AllowEmailVerifiedUsers for definido como falso no inquilino do utilizador convidado, o utilizador não pode completar o resgate e o fluxo resulta num erro. Para obter mais informações, consulte [a colaboração do Azure Ative Directory B2B](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. O utilizador é solicitado a criar uma conta pessoal [da Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Após autenticação ao fornecedor de identidade certo, o utilizador é redirecionado para Azure AD para completar a [experiência de consentimento](#consent-experience-for-the-guest).  

Para resgates just-in-time (JIT), onde o resgate é através de um link de aplicação arrendado, os passos 8 a 10 não estão disponíveis. Se um utilizador chegar ao passo 6 e a função de senha de acesso de uma vez por e-mail não estiver ativada, o utilizador recebe uma mensagem de erro e não consegue resgatar o convite. Para evitar este erro, os administradores devem [ativar a senha de e-mail uma vez](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) ou garantir que o utilizador clica num link de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o hóspede

Quando um hóspede assina para aceder a recursos numa organização parceira pela primeira vez, eles são guiados através das páginas seguintes. 

1. O hóspede analisa a página **de permissões de comentários** descrevendo a declaração de privacidade da organização convidativa. Um utilizador deve **aceitar** o uso das suas informações de acordo com as políticas de privacidade da organização convidativa para continuar.

   ![Screenshot mostrando a página de permissões de revisão](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você como administrador de inquilino pode ligar-se à declaração de privacidade da sua organização, consulte [Como-a- Adicionar as informações de privacidade da sua organização no Azure Ative Directory](../fundamentals/active-directory-properties-area.md).

2. Se os termos de utilização forem configurados, o hóspede abre e revê os termos de utilização e, em seguida, seleciona **Aceitar**. 

   ![Screenshot mostrando novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   Pode configurar [termos de utilização](../conditional-access/terms-of-use.md) em **Identidades Externas**  >  **Termos de utilização**.

3. Salvo especificação em contrário, o hóspede é redirecionado para o painel de acesso apps, que lista as aplicações a que o hóspede pode aceder.

   ![Screenshot mostrando o painel de acesso apps](media/redemption-experience/myapps.png) 

No seu diretório, o Convite do Hóspede **aceitou** alterações de valor para **Sim.** Se um MSA foi criado, a **Fonte** do Hóspede mostra a **Conta Microsoft**. Para obter mais informações sobre as propriedades da conta de utilizador do hóspede, consulte [propriedades de um utilizador de colaboração Azure AD B2B.](user-properties.md) 

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar utilizadores de colaboração Azure Ative Directory B2B no portal Azure](add-users-administrator.md)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B ao Azure Ative Directory?](add-users-information-worker.md)
- [Adicione utilizadores de colaboração B2B do Azure Ative Directory através da utilização do PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como utilizador convidado](leave-the-organization.md)
