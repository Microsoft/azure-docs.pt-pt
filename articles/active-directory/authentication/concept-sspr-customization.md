---
title: Personalizar do Azure AD self-service de redefinição de senha - Azure Active Directory
description: Reposição de palavra-passe self-service do Azure AD, as opções de personalização
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a77c6ce205c40b5814f9b26f9099d868d434d3ce
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316461"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade do Azure AD para a reposição de palavra-passe self-service

Os profissionais de TI que pretendem implementar a reposição de palavra-passe self-service (SSPR) no Azure Active directory (Azure AD), podem personalizar a experiência de acordo com as necessidades dos usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar a ligação "Contacte o administrador"

Mesmo se não estiver ativada a SSPR, os usuários ainda têm uma ligação "Contacte o administrador" sobre a palavra-passe, portal de reposição. Se um usuário selecionar esse link, ele de qualquer:

   * Os administradores de e-mails e pede-los para obter assistência na alteração de palavra-passe do utilizador.
   * Envia os seus utilizadores para uma URL que especificar para obter assistência.

Recomendamos que defina esta contacto para algo como um endereço de e-mail ou site que os utilizadores já utilizam para questões de suporte.

![Contact][Contact]

O e-mail de contacto é enviado para os seguintes destinatários pela seguinte ordem:

1. Se o **administrador de palavras-passe** função é atribuída, os administradores com esta função são notificados.
2. Se nenhum administrador de palavras-passe forem atribuídos, em seguida, os administradores com o **administrador de utilizadores** são notificados de função.
3. Se nenhuma das funções anteriores forem atribuídas, em seguida, o **os administradores globais** são notificados.

Em todos os casos, um máximo de 100 destinatários serão notificados.

Para obter mais informações sobre as funções de administrador diferentes e como atribuí-las, veja [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desativar "Contacte o administrador" mensagens de correio eletrónico

Se não pretender que sua organização notificar os administradores sobre a palavra-passe de solicitações de redefinição, pode ativar a seguinte configuração:

* Ative senhas de auto-atendimento repor para todos os utilizadores finais. Esta opção está sob **reposição de palavra-passe** > **propriedades**. Se não pretender que os utilizadores reponham as respetivas palavras-passe, pode definir o âmbito de acesso a um grupo vazio. *Não recomendamos esta opção.*
* Personalizar a ligação de suporte técnico para fornecer um URL de web ou mailto: endereço que os utilizadores podem utilizar para obter assistência. Esta opção está sob **reposição de palavra-passe** > **personalização** > **URL ou e-mail de suporte técnico personalizado**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizar a página de início de sessão do AD FS para SSPR

Administradores do Active Directory Federation Services (AD FS), podem adicionar uma ligação para a página de início de sessão usando a orientação encontrada no [descrição da página de início de sessão de adicionar](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artigo.

Para adicionar uma ligação para a página de início de sessão do AD FS, utilize o seguinte comando no seu servidor AD FS. Os utilizadores podem utilizar esta página para introduzir o fluxo de trabalho do SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar o início de sessão na página e acesso painel aspeto e funcionalidade

Pode personalizar a página de início de sessão. Pode adicionar um logótipo que é apresentada juntamente com a imagem que se adequa a marca da empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois de um utilizador introduz o respetivo nome de utilizador
* Se o usuário acessa o URL personalizado:
    * Passando o `whr` parâmetro para a palavra-passe de reposição de página, como `https://login.microsoftonline.com/?whr=contoso.com`
    * Passando o `username` parâmetro para a palavra-passe de reposição de página, como `https://login.microsoftonline.com/?username=admin@contoso.com`

Obter detalhes sobre como configurar a imagem corporativa no artigo [adicionar imagem corporativa à sua página de início de sessão no Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nome do diretório

Pode alterar o atributo de nome de diretório sob **do Azure Active Directory** > **propriedades**. Pode mostrar um nome de organização amigável que é visto no portal e na automatizada de comunicações. Esta opção é mais visível no correio eletrónico automatizado em formulários que se seguem:

* O nome amigável do e-mail, por exemplo "Microsoft em nome de demonstração CONTOSO"
* A linha de assunto do e-mail, por exemplo "CONTOSO demonstração conta e-mail código de verificação"

## <a name="next-steps"></a>Passos Seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contacte o administrador para obter ajuda a repor o seu exemplo de e-mail de palavra-passe"
