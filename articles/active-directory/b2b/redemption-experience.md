---
title: Resgate de convite na colaboração B2B – Azure AD
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato para termos de privacidade.
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
ms.openlocfilehash: 3e85b0ae298589c0e0e051a24e5db89eae81db62
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272141"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate de convite de colaboração B2B Azure Active Directory

Este artigo descreve as maneiras como os usuários convidados podem acessar seus recursos e o processo de consentimento que eles encontrarão. Se você enviar um email de convite para o convidado, o convite incluirá um link que o convidado pode resgatar para obter acesso ao seu aplicativo ou Portal. O email de convite é apenas uma das maneiras como os convidados podem obter acesso aos seus recursos. Como alternativa, você pode adicionar convidados ao seu diretório e dar a eles um link direto para o portal ou aplicativo que você deseja compartilhar. Independentemente do método que eles usam, os convidados são guiados por meio de um processo de consentimento pela primeira vez. Esse processo garante que seus convidados concordem com os termos de privacidade e aceitem os [termos de uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que você configurou.

Quando você adiciona um usuário convidado ao seu diretório, a conta de usuário convidado tem um status de consentimento (visível no PowerShell) inicialmente definido como **PendingAcceptance**. Essa configuração permanece até que o convidado aceite seu convite e concorde com a política de privacidade e os termos de uso. Depois disso, o status de consentimento muda para **aceito**e as páginas de consentimento não são mais apresentadas ao convidado.

## <a name="redemption-through-the-invitation-email"></a>Resgate pelo email de convite

Quando você adiciona um usuário convidado ao seu diretório [usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), um email de convite é enviado para o convidado no processo. Você também pode optar por enviar emails de convite quando estiver [usando o PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar usuários convidados ao seu diretório. Aqui está uma descrição da experiência do convidado ao resgatar o link no email.

1. O convidado recebe um [email de convite](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) enviado por **convites da Microsoft**.
2. O convidado seleciona **introdução** no email.
3. Se o convidado não tiver uma conta do Azure AD, uma MSA (conta da Microsoft) ou uma conta de email em uma organização federada, será solicitado a criar uma MSA (a menos que o recurso de [senha de uso único](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) esteja habilitado, o que não exige um MSA).
4. O convidado é guiado por meio da [experiência de consentimento](#consent-experience-for-the-guest) descrita abaixo.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Como alternativa ao email de convite, você pode dar a um convidado um link direto para seu aplicativo ou Portal. Primeiro, você precisa adicionar o usuário convidado ao seu diretório por meio do [portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou do [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Em seguida, você pode usar qualquer uma das [maneiras personalizáveis para implantar aplicativos para usuários](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), incluindo links de logon diretos. Quando um convidado usa um link direto em vez do email de convite, ele ainda será guiado pela primeira experiência de consentimento.

> [!IMPORTANT]
> O link direto deve ser específico do locatário. Em outras palavras, ele deve incluir uma ID de locatário ou um domínio verificado para que o convidado possa ser autenticado em seu locatário, onde o aplicativo compartilhado está localizado. Uma URL comum como https://myapps.microsoft.com não funcionará para um convidado porque ela será redirecionada para seu locatário inicial para autenticação. Aqui estão alguns exemplos de links diretos com o contexto do locatário:
 > - Painel de acesso de aplicativos: https://myapps.microsoft.com/?tenantid=&lt; ID do locatário&gt; 
 > - Painel de acesso de aplicativos para um domínio verificado: https://myapps.microsoft.com/&lt;&gt; de domínio verificado
 > - Portal do Azure: https://portal.azure.com/&lt; ID do locatário&gt;
 > - Aplicativo individual: consulte como usar um [link de logon direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Há alguns casos em que o email de convite é recomendado em um link direto. Se esses casos especiais forem importantes para sua organização, recomendamos que você convide os usuários usando métodos que ainda enviam o email de convite:
 - O usuário não tem uma conta do Azure AD, uma MSA ou uma conta de email em uma organização federada. A menos que você esteja usando o recurso de senha de uso único, o convidado precisa resgatar o email de convite para ser guiado pelas etapas para criar um MSA.
 - Às vezes, o objeto de usuário convidado pode não ter um endereço de email devido a um conflito com um objeto de contato (por exemplo, um objeto de contato do Outlook). Nesse caso, o usuário deve clicar na URL de resgate no email de convite.
 - O usuário pode entrar com um alias do endereço de email que foi convidado. (Um alias é um endereço de email adicional associado a uma conta de email.) Nesse caso, o usuário deve clicar na URL de resgate no email de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o convidado

Quando um convidado entra para acessar recursos em uma organização parceira pela primeira vez, eles são guiados pelas páginas a seguir. 

1. O convidado revisa a página **examinar permissões** descrevendo a política de privacidade da organização que está convidando. Um usuário deve **aceitar** o uso de suas informações de acordo com as políticas de privacidade da organização que estão convidando para continuar.

   ![Captura de tela mostrando a página examinar permissões](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você como um administrador de locatários pode vincular à política de privacidade da sua organização, consulte [como adicionar informações de privacidade da sua organização em Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se os termos de uso estiverem configurados, o convidado será aberto e examinará os termos de uso e, em seguida, selecionará **aceitar**. 

   ![Captura de tela mostrando novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Você pode configurar Ver os [termos de uso](../governance/active-directory-tou.md) em **gerenciar** > **relações organizacionais** > **termos de uso**.

3. A menos que especificado de outra forma, o convidado é redirecionado para o painel de acesso de aplicativos, que lista os aplicativos que o convidado pode acessar.

   ![Captura de tela mostrando o painel de acesso de aplicativos](media/redemption-experience/myapps.png) 

No seu diretório, o valor de **convite aceito** do convidado muda para **Sim**. Se um MSA foi criado, a **origem** do convidado mostrará a **conta da Microsoft**. Para obter mais informações sobre as propriedades de conta de usuário convidado, consulte [Propriedades de um usuário de colaboração B2B do Azure ad](user-properties.md). 

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](add-users-administrator.md)
- [Como os operadores de informações adicionam usuários de colaboração B2B a Azure Active Directory?](add-users-information-worker.md)
- [Adicionar Azure Active Directory usuários de colaboração B2B usando o PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como um usuário convidado](leave-the-organization.md)
