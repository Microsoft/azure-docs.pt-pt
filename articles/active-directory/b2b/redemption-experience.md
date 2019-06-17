---
title: Resgate de convite em colaboração B2B - Azure Active Directory | Documentos da Microsoft
description: Descreve a experiência do resgate de convite colaboração B2B do Azure AD para utilizadores finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052519"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate de convite de colaboração do Azure Active Directory B2B

Este artigo descreve as formas como os utilizadores convidados podem aceder aos seus recursos e o processo de autorização que irá se deparar. Se enviar um e-mail de convite para o convidado, o convite inclui uma ligação pode resgatar o convidado para aceder a sua aplicação ou o portal. O e-mail de convite é apenas uma das formas convidados podem obter acesso aos seus recursos. Como alternativa, pode adicionar convidados para o seu diretório e dar-lhes uma ligação direta para o portal ou a aplicação que pretende partilhar. Independentemente do método que utilizarem, os convidados são direcionados através de um processo de consentimento de iniciantes. Este processo garante que os convidados aceitar os termos de privacidade e aceitam qualquer [termos de utilização](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que configurou.

Quando adiciona um utilizador convidado ao seu diretório, a conta de utilizador convidado tem um Estado de consentimento (podem ser visualizado no PowerShell) que é inicialmente definido como **PendingAcceptance**. Esta definição permanece até que o convidado aceita o convite e concorda em sua política de privacidade e termos de utilização. Depois disso, o estado de consentimento é alterado para **aceite**, e as páginas de consentimento já não são apresentadas para o convidado.

## <a name="redemption-through-the-invitation-email"></a>Resgate através de e-mail de convite

Quando adiciona um utilizador convidado ao diretório ao [com o portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), é enviado um e-mail de convite para o convidado no processo. Também pode optar por enviar e-mails de convite, quando estiver [com o PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar utilizadores convidados para o seu diretório. Eis uma descrição da experiência do convidado resgata a ligação no e-mail.

1. O convidado recebe uma [e-mail de convite](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) que é enviado do **Microsoft Invitations**.
2. Seleciona o convidado **começar** no e-mail.
3. Se o convidado não tiver uma conta do Azure AD, uma conta Microsoft (MSA) ou uma conta de e-mail numa organização federada, for pedidos para criar uma MSA (a menos que o [código de acesso único](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) funcionalidade estiver ativada, o que não requer uma MSA ).
4. O convidado é orientado através do [experiência de consentimento](#consent-experience-for-the-guest) descrito abaixo.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de uma ligação direta

Como alternativa para o e-mail de convite, pode dar um convidado uma ligação direta à sua aplicação ou o portal. Primeiro tem de adicionar o utilizador convidado ao diretório do através da [portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Pode utilizar qualquer um da [personalizáveis formas de implementar aplicações para utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), incluindo links de início de sessão em direto. Quando um convidado utiliza uma ligação direta em vez do e-mail de convite, ainda vai ser orientados na sua experiência de consentimento de iniciantes.

> [!IMPORTANT]
> A ligação direta tem de ser específico de inquilino. Em outras palavras, ela tem de incluir um ID de inquilino ou domínio verificado, para que os convidados podem ser autenticados no seu inquilino, onde está localizada a aplicação partilhada. Um URL comuns, como https://myapps.microsoft.com não funciona para um convidado porque ele será redirecionado para o respetivo inquilino inicial para a autenticação. Aqui estão alguns exemplos de ligações diretas com o contexto do inquilino:
 > - Painel de acesso de aplicações: https://myapps.microsoft.com/?tenantid=&lt; id do inquilino&gt; 
 > - Painel de acesso de aplicações para um domínio verificado: https://myapps.microsoft.com/&lt; verificado domínio&gt;
 > - Portal do Azure: https://portal.azure.com/&lt; id do inquilino&gt;
 > - Aplicações individuais: Veja como usar um [ligação de início de sessão em direta](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Existem alguns casos em que o e-mail de convite é recomendado para uma ligação direta. Se estes casos especiais são importantes para a sua organização, recomendamos que Convide utilizadores através de métodos que ainda enviar o e-mail de convite:
 - O utilizador não tem uma conta do Azure AD, uma MSA ou uma conta de e-mail numa organização federada. A menos que esteja a utilizar a funcionalidade de código de acesso único, o convidado tem de resgatar o e-mail de convite para ser orientado ao longo dos passos para criar uma MSA.
 - Por vezes, o objeto de utilizador convidado não pode ter um endereço de e-mail devido a um conflito com um objeto de contato (por exemplo, um Outlook contacto objeto). Neste caso, o utilizador tem de clicar o URL de resgate no e-mail de convite.
 - O utilizador pode iniciar sessão com um alias do endereço de e-mail que foi convidado. (É um endereço de e-mail adicionais associado a uma conta de e-mail de um alias.) Neste caso, o utilizador tem de clicar o URL de resgate no e-mail de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o convidado

Quando um convidado inicia sessão aceder aos recursos numa organização parceira pela primeira vez, eles estão orientados por meio das páginas seguintes. 

1. As revisões de convidado do **rever permissões** página que descreve a declaração de privacidade da organização de convite. Tem de um usuário **Accept** a utilização das informações de acordo com as políticas de privacidade da organização de convite para continuar.

   ![Captura de ecrã que mostra a página de permissões de revisão](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como, como um administrador inquilino pode ligar a declaração de privacidade da sua organização, consulte [procedimentos: Adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se estiverem configurados termos de utilização, o convidado é aberto e analisa os termos de utilização e, em seguida, seleciona **Accept**. 

   ![Captura de ecrã que mostra os novos termos de utilização](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Pode configurar ver [termos de utilização](../governance/active-directory-tou.md) na **gerir** > **relações organizacionais** > **termos de utilização**.

3. Salvo especificação em contrário, o convidado é redirecionado para o painel de acesso de aplicações, que lista as aplicações que pode aceder a convidado.

   ![Captura de ecrã que mostra o painel de acesso de aplicações](media/redemption-experience/myapps.png) 

Da seu diretório, o convidado **Convite aceite** valor é alterado para **Sim**. Se tiver sido criada uma MSA, o convidado **origem** mostra **Account Microsoft**. Para obter mais informações sobre as propriedades de conta de utilizador convidado, consulte [propriedades de um utilizador de colaboração B2B do Azure AD](user-properties.md). 

## <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar utilizadores de colaboração do Azure Active Directory B2B no portal do Azure](add-users-administrator.md)
- [Como os operadores de informações adicionar utilizadores de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar utilizadores de colaboração do Azure Active Directory B2B com o PowerShell](customize-invitation-api.md#powershell)
- [Sair de uma organização como um utilizador convidado](leave-the-organization.md)
