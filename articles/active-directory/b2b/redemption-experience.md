---
title: Resgate de convite seletiva em colaboração B2B - Azure AD
description: Descreve a experiência de resgate de convites de colaboração Azure AD B2B para utilizadores finais, incluindo o acordo para termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050840"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Redenção do convite de colaboração Azure Ative Directory B2B

Este artigo descreve a forma como os utilizadores convidados podem aceder aos seus recursos e ao processo de consentimento que irão encontrar. Se enviar um e-mail de convite ao hóspede, o convite inclui um link que o hóspede pode resgatar para ter acesso à sua app ou portal. O e-mail de convite é apenas uma das formas de os hóspedes acederem aos seus recursos. Como alternativa, pode adicionar os hóspedes ao seu diretório e dar-lhes um link direto para o portal ou app que pretende partilhar. Independentemente do método que utilizam, os hóspedes são guiados através de um processo de consentimento pela primeira vez. Este processo garante que os seus hóspedes concordam com os termos de privacidade e aceitam quaisquer [termos de utilização](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que tenha configurado.

Quando adiciona um utilizador convidado ao seu diretório, a conta de utilizador convidado tem um estado de consentimento (visível no PowerShell) que é inicialmente definido para **Aceitação Pendente**. Esta definição permanece até que o hóspede aceite o seu convite e concorde com a sua política de privacidade e termos de uso. Depois disso, o estado de consentimento altera-se ao **Aceito**, e as páginas de consentimento deixaram de ser apresentadas ao hóspede.

   > [!IMPORTANT]
   > A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas AD Azure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optarem pela autenticação de código de acesso único por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="redemption-through-the-invitation-email"></a>Redenção através do e-mail de convite

Quando adiciona um utilizador convidado ao seu diretório [utilizando o portal Azure,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)um e-mail de convite é enviado ao hóspede no processo. Também pode optar por enviar e-mails de convite quando estiver a usar o [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar utilizadores convidados ao seu diretório. Aqui está uma descrição da experiência do hóspede quando eles resgatam o link no e-mail.

1. O hóspede recebe um [e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) de convite enviado a partir de **Convites**microsoft .
2. O hóspede seleciona **Get Started** no e-mail.
3. Se o hóspede não tiver uma conta AD Azure, uma Conta Microsoft (MSA), ou uma conta de e-mail numa organização federada, é solicitado a criar uma MSA (a menos que a funcionalidade [de senha única](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) esteja ativada, o que não requer uma MSA).
4. O hóspede é guiado através da experiência de [consentimento](#consent-experience-for-the-guest) descrita abaixo.

## <a name="redemption-through-a-direct-link"></a>Redenção através de uma ligação direta

Como alternativa ao e-mail de convite, pode dar a um hóspede um link direto para a sua app ou portal. Primeiro, é necessário adicionar o utilizador convidado ao seu diretório através do [portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Em seguida, pode utilizar qualquer uma das [formas personalizáveis de implementar aplicações para os utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), incluindo links de sessão direta. Quando um hóspede usa um link direto em vez do e-mail de convite, eles ainda serão guiados através da experiência de consentimento pela primeira vez.

> [!IMPORTANT]
> O link direto deve ser específico do inquilino. Por outras palavras, deve incluir uma identificação do inquilino ou domínio verificado para que o hóspede possa ser autenticado no seu inquilino, onde está localizada a aplicação partilhada. Um URL https://myapps.microsoft.com comum como não vai funcionar para um hóspede porque vai redirecionar para o seu inquilino para autenticação. Aqui estão alguns exemplos de ligações diretas com o contexto do inquilino:
 > - Painel de https://myapps.microsoft.com/?tenantid=&ltacesso a aplicativos: .id inquilino&gt; 
 > - Painel de acesso a apps https://myapps.microsoft.com/&ltpara um domínio verificado: ;domínio verificado&gt;
 > - Portal Azure: https://portal.azure.com/&lt&gt;
 > - Aplicativo individual: ver como usar um [link de acesso direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Há alguns casos em que o e-mail de convite é recomendado por um link direto. Se estes casos especiais forem importantes para a sua organização, recomendamos que convide os utilizadores usando métodos que ainda enviam o e-mail de convite:
 - O utilizador não tem uma conta Azure AD, uma MSA ou uma conta de e-mail numa organização federada. A menos que esteja a usar a funcionalidade de senha única, o hóspede precisa resgatar o e-mail de convite para ser guiado através dos passos para criar um MSA.
 - Por vezes, o objeto de utilizador convidado pode não ter um endereço de e-mail devido a um conflito com um objeto de contacto (por exemplo, um objeto de contacto do Outlook). Neste caso, o utilizador deve clicar no URL de resgate no e-mail de convite.
 - O utilizador pode iniciar sessão com um pseudónimo do endereço de e-mail que foi convidado. (Um pseudónimo é um endereço de e-mail adicional associado a uma conta de e-mail.) Neste caso, o utilizador deve clicar no URL de resgate no e-mail de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o hóspede

Quando um hóspede entra para aceder a recursos numa organização parceira pela primeira vez, são guiados pelas páginas seguintes. 

1. O hóspede revê a página de **permissões da Revisão** descrevendo a declaração de privacidade da organização convidativa. Um utilizador deve **aceitar** a utilização das suas informações de acordo com as políticas de privacidade da organização convidativa para continuar.

   ![Screenshot mostrando a página de permissões de Revisão](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você como administrador de inquilino pode ligar-se à declaração de privacidade da sua organização, consulte Como: Adicionar as [informações de privacidade da sua organização no Diretório Ativo Azure](https://aka.ms/adprivacystatement).

2. Se os termos de utilização estiverem configurados, o hóspede abre e revê os termos de utilização e, em seguida, seleciona **Aceitar**. 

   ![Screenshot mostrando novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Pode configurar [os termos de utilização](../governance/active-directory-tou.md) em **Gerir** >  > **relações organizacionais****Termos de utilização**.

3. Salvo especificação em contrário, o hóspede é redirecionado para o painel de acesso apps, que lista as aplicações a que o hóspede pode aceder.

   ![Screenshot mostrando o painel de acesso apps](media/redemption-experience/myapps.png) 

No seu diretório, o Convite do Hóspede **aceitou** alterações de valor para **Sim**. Se foi criado um MSA, a **Fonte** do hóspede mostra a **Conta Microsoft**. Para obter mais informações sobre as propriedades da conta de utilizador convidado, consulte [Propriedades de um utilizador de colaboração Azure AD B2B.](user-properties.md) 

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicione utilizadores de colaboração azure Ative Directory B2B no portal Azure](add-users-administrator.md)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B ao Diretório Ativo Azure?](add-users-information-worker.md)
- [Adicione utilizadores de colaboração de Diretório Ativo Azure B2B utilizando a PowerShell](customize-invitation-api.md#powershell)
- [Deixe uma organização como utilizador convidado](leave-the-organization.md)
