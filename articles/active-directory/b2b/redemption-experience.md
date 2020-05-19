---
title: Resgate de convite seletiva em colaboração B2B - Azure AD
description: Descreve a experiência de resgate de convites de colaboração Azure AD B2B para utilizadores finais, incluindo o acordo para termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e3a2ddda4529cee584f5eabf6677af940d2bdd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585900"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Redenção do convite de colaboração Azure Ative Directory B2B

Este artigo descreve a forma como os utilizadores convidados podem aceder aos seus recursos e ao processo de consentimento que irão encontrar. Se enviar um e-mail de convite ao hóspede, o convite inclui um link que o hóspede pode resgatar para ter acesso à sua app ou portal. O e-mail de convite é apenas uma das formas de os hóspedes acederem aos seus recursos. Como alternativa, pode adicionar os hóspedes ao seu diretório e dar-lhes um link direto para o portal ou app que pretende partilhar. Independentemente do método que utilizam, os hóspedes são guiados através de um processo de consentimento pela primeira vez. Este processo garante que os seus hóspedes concordam com os termos de privacidade e aceitam quaisquer [termos de utilização](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que tenha configurado.

Quando adiciona um utilizador convidado ao seu diretório, a conta de utilizador convidado tem um estado de consentimento (visível no PowerShell) que é inicialmente definido para **Aceitação Pendente**. Esta definição permanece até que o hóspede aceite o seu convite e concorde com a sua política de privacidade e termos de uso. Depois disso, o estado de consentimento altera-se ao **Aceito**, e as páginas de consentimento deixaram de ser apresentadas ao hóspede.

   > [!IMPORTANT]
   > A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas AD Azure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optarem pela autenticação de código de acesso único por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="redemption-through-the-invitation-email"></a>Redenção através do e-mail de convite

Quando adiciona um utilizador convidado ao seu diretório [utilizando o portal Azure,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)um e-mail de convite é enviado ao hóspede no processo. Também pode optar por enviar e-mails de convite quando estiver a usar o [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar utilizadores convidados ao seu diretório. Aqui está uma descrição da experiência do hóspede quando eles resgatam o link no e-mail.

1. O hóspede recebe um [e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) de convite enviado a partir de **Convites**microsoft .
2. O hóspede seleciona **Aceitar convite** no e-mail.
3. O hóspede usará as suas próprias credenciais para iniciar sessão no seu diretório. Se o hóspede não tiver uma conta que possa ser federada para o seu diretório e a funcionalidade de [senha única (OTP)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) não estiver ativada; o hóspede é solicitado a criar uma [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal ou uma [conta de self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Consulte o fluxo de resgate de [convites](#invitation-redemption-flow) para mais detalhes.
4. O hóspede é guiado através da experiência de [consentimento](#consent-experience-for-the-guest) descrita abaixo.

## <a name="redemption-through-a-direct-link"></a>Redenção através de uma ligação direta

Como alternativa ao e-mail de convite, pode dar a um hóspede um link direto para a sua app ou portal. Primeiro, é necessário adicionar o utilizador convidado ao seu diretório através do [portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Em seguida, pode utilizar qualquer uma das [formas personalizáveis de implementar aplicações para os utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), incluindo links de sessão direta. Quando um hóspede usa um link direto em vez do e-mail de convite, eles ainda serão guiados através da experiência de consentimento pela primeira vez.

> [!IMPORTANT]
> O link direto deve ser específico do inquilino. Por outras palavras, deve incluir uma identificação do inquilino ou domínio verificado para que o hóspede possa ser autenticado no seu inquilino, onde está localizada a aplicação partilhada. Um URL comum como https://myapps.microsoft.com não vai funcionar para um hóspede porque vai redirecionar para o seu inquilino para autenticação. Aqui estão alguns exemplos de ligações diretas com o contexto do inquilino:
 > - Painel de acesso a aplicativos: https://myapps.microsoft.com/?tenantid=&lt .id inquilino&gt; 
 > - Painel de acesso a apps para um domínio verificado: https://myapps.microsoft.com/&lt ;domínio verificado&gt;
 > - Portal https://portal.azure.com/&lt Azure:&gt;
 > - Aplicativo individual: ver como usar um [link de acesso direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Há alguns casos em que o e-mail de convite é recomendado por um link direto. Se estes casos especiais forem importantes para a sua organização, recomendamos que convide os utilizadores usando métodos que ainda enviam o e-mail de convite:
 - O utilizador não tem uma conta Azure AD, uma MSA ou uma conta de e-mail numa organização federada. A menos que esteja a usar a funcionalidade de senha única, o hóspede precisa resgatar o e-mail de convite para ser guiado através dos passos para criar um MSA.
 - Por vezes, o objeto de utilizador convidado pode não ter um endereço de e-mail devido a um conflito com um objeto de contacto (por exemplo, um objeto de contacto do Outlook). Neste caso, o utilizador deve clicar no URL de resgate no e-mail de convite.
 - O utilizador pode iniciar sessão com um pseudónimo do endereço de e-mail que foi convidado. (Um pseudónimo é um endereço de e-mail adicional associado a uma conta de e-mail.) Neste caso, o utilizador deve clicar no URL de resgate no e-mail de convite.

## <a name="invitation-redemption-flow"></a>Fluxo de redenção de convite

Quando um utilizador clica no link **de convite Aceitar** num [e-mail](invitation-email-elements.md)de convite, a Azure AD resgata automaticamente o convite com base no fluxo de resgate, como mostrado abaixo:

![Screenshot mostrando o diagrama de fluxo de redenção](media/redemption-experience/invitation-redemption-flow.png)

**Se o nome principal do utilizador (UPN) corresponder tanto a uma ad AD Azure existente como a uma conta pessoal de MSA, o utilizador será solicitado a escolher com que conta pretende resgatar.*

1. A Azure AD realiza a descoberta baseada no utilizador para determinar se o utilizador existe num [inquilino Azure AD existente.](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal)

2. Se um administrador tiver ativado a [federação direta,](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)o Azure AD verifica se o sufixo de domínio do utilizador corresponde ao domínio de um fornecedor de identidade SAML/WS-Fed configurado e redireciona o utilizador para o fornecedor de identidade pré-configurado.

3. Se um administrador tiver ativado a [federação da Google,](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)o Azure AD verifica se o sufixo de domínio do utilizador é gmail.com ou googlemail.com e redireciona o utilizador para a Google.

4. O processo de resgate verifica se o utilizador tem uma conta pessoal da [Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)existente.

5. Uma vez identificado o **diretório inicial** do utilizador, o utilizador é enviado ao fornecedor de identidade correspondente para iniciar sessão.  

6. Se os passos 1 a 4 não encontrarem um diretório para o utilizador convidado, então a Azure AD determina se o inquilino convidado permitiu a funcionalidade de [senha de e-mail única (OTP)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) para os hóspedes.

7. Se [estiver ativada uma senha única para os hóspedes por e-mail,](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)é enviada uma senha para o utilizador através do e-mail convidado. O utilizador irá recuperar e introduzir esta senha na página de entrada da AD Azure.

8. Se a senha de um e-mail para os hóspedes for desativada, a AD Azure verifica o sufixo de domínio para determinar se pertence a uma conta de consumo. Em caso afirmativo, o utilizador é solicitado a criar uma conta pessoal da [Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Caso contrário, o utilizador é solicitado a criar uma [conta de self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. A Azure AD tenta criar uma [conta de self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) verificando o acesso ao e-mail. A verificação da conta é feita enviando um código para o e-mail, e fazendo com que o utilizador recupere e envie-o para a AD Azure. No entanto, se o inquilino do utilizador convidado for federado ou se o campo AllowEmailVerifiedUsers estiver definido como falso no inquilino do utilizador convidado, o utilizador não pode completar o resgate e o fluxo resulta num erro. Para mais informações, consulte a [colaboração de Troubleshooting Azure Ative Directory B2B.](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption)

10. O utilizador é solicitado a criar uma conta pessoal da [Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Depois de autenticar o fornecedor de identidade certo, o utilizador é redirecionado para a Azure AD para completar a experiência de [consentimento](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

Para os resgates just-in-time (JIT), onde o resgate é através de uma ligação de candidatura arrendada, os passos 8 a 10 não estão disponíveis. Se um utilizador chegar ao passo 6 e a funcionalidade de senha de um e-mail não estiver ativada, o utilizador recebe uma mensagem de erro e não pode resgatar o convite. Para evitar este erro, os administradores devem ativar a [senha de um e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) de uma vez ou garantir que o utilizador clica num link de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o hóspede

Quando um hóspede entra para aceder a recursos numa organização parceira pela primeira vez, são guiados pelas páginas seguintes. 

1. O hóspede revê a página de **permissões da Revisão** descrevendo a declaração de privacidade da organização convidativa. Um utilizador deve **aceitar** a utilização das suas informações de acordo com as políticas de privacidade da organização convidativa para continuar.

   ![Screenshot mostrando a página de permissões de Revisão](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você como administrador de inquilino pode ligar-se à declaração de privacidade da sua organização, consulte Como: Adicionar as [informações de privacidade da sua organização no Diretório Ativo Azure](https://aka.ms/adprivacystatement).

2. Se os termos de utilização estiverem configurados, o hóspede abre e revê os termos de utilização e, em seguida, seleciona **Aceitar**. 

   ![Screenshot mostrando novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   Pode configurar [termos de utilização](../governance/active-directory-tou.md) em **identidades externas**  >  **Termos de utilização**.

3. Salvo especificação em contrário, o hóspede é redirecionado para o painel de acesso apps, que lista as aplicações a que o hóspede pode aceder.

   ![Screenshot mostrando o painel de acesso apps](media/redemption-experience/myapps.png) 

No seu diretório, o Convite do Hóspede **aceitou** alterações de valor para **Sim**. Se foi criado um MSA, a **Fonte** do hóspede mostra a **Conta Microsoft**. Para obter mais informações sobre as propriedades da conta de utilizador convidado, consulte [Propriedades de um utilizador de colaboração Azure AD B2B.](user-properties.md) 

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicione utilizadores de colaboração azure Ative Directory B2B no portal Azure](add-users-administrator.md)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B ao Diretório Ativo Azure?](add-users-information-worker.md)
- [Adicione utilizadores de colaboração de Diretório Ativo Azure B2B utilizando a PowerShell](customize-invitation-api.md#powershell)
- [Deixe uma organização como utilizador convidado](leave-the-organization.md)
