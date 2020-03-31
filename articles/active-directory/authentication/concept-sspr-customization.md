---
title: Personalize o reset de senha de autosserviço - Diretório Ativo Azure
description: Opções de personalização para reset de senha de autosserviço da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979466"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalize a funcionalidade Azure AD para reset de senha de autosserviço

Os profissionais de TI que pretendam implementar o reset de senhade self-service (SSPR) no diretório Azure Ative (Azure AD) podem personalizar a experiência de forma a corresponder às necessidades dos seus utilizadores.

## <a name="customize-the-contact-your-administrator-link"></a>Personalize o link "Contacte o seu administrador"

Os utilizadores de redefinição de palavra-passe de autosserviço têm um link "Contacte o seu administrador" no portal de reset de palavra-passe. Se um utilizador selecionar este link, fará uma de duas coisas:

* Se deixada no estado padrão:
   * O e-mail é enviado aos seus administradores e pede-lhes que prestem assistência na alteração da palavra-passe do utilizador. Veja o [e-mail](#sample-email) abaixo.
* Se personalizado:
   * Envia o seu utilizador para uma página web ou endereço de e-mail especificado pelo administrador para assistência.

> [!TIP]
> Se personalizar isto, recomendamos que este ajuste para algo que os utilizadores já estão familiarizados com o suporte

> [!WARNING]
> Se personalizar esta definição com um endereço de e-mail e uma conta que necessite de uma redefinição de senha, o utilizador poderá não poder pedir assistência.

### <a name="sample-email"></a>E-mail de amostra

![Pedido de amostra para redefinir e-mail enviado ao Administrador][Contact]

O e-mail de contacto é enviado aos seguintes destinatários na seguinte ordem:

1. Se for atribuída a função de administrador de **helpdesk** ou de administrador de **passwords,** os administradores com estas funções são notificados.
1. Se não forem atribuídos administradores de helpdesk ou administrador de passwords, os administradores com a função de administrador do **utilizador** são notificados.
1. Se nenhuma das funções anteriores for atribuída, os administradores globais são **notificados.**

Em todos os casos, são notificados um máximo de 100 destinatários.

Para saber mais sobre as diferentes funções de administrador e como atribuí-las, consulte as funções de [administrador de atribuição no Diretório Ativo azure](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desativar os e-mails "Contacte o seu administrador"

Se a sua organização não quiser notificar os administradores sobre pedidos de reset de palavra-passe, pode ativar a seguinte configuração:

* Ativar o reset da palavra-passe de autosserviço para todos os utilizadores finais. Esta opção encontra-se em > **propriedades**de **reset de palavra-passe**. Se não quiser que os utilizadores reporem as suas próprias palavras-passe, pode aceder a um grupo vazio. *Não recomendamos esta opção.*
* Personalize o link de helpdesk para fornecer um URL ou mailto web: endereço que os utilizadores podem usar para obter assistência. Esta opção está em password **Reset** > **Personalizization** > **Email ou URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalize a página de inscrição aD FS para SSPR

Os administradores da Ative Directory Federation Services (AD FS) podem adicionar um link à sua página de inscrição utilizando a orientação encontrada no artigo de descrição da página de [inscrição Add.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Para adicionar um link para a página de sessão aD FS, utilize o seguinte comando no seu servidor AD FS. Os utilizadores podem utilizar esta página para introduzir o fluxo de trabalho sSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalize a página de inscrição e o olhar do painel de acesso e a sensação

Pode personalizar a página de inscrição. Pode adicionar um logótipo que aparece juntamente com a imagem que se adequa à marca da sua empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois de um utilizador introduzir o seu nome de utilizador
* Se o utilizador aceder ao URL personalizado:
   * Ao passar `whr` o parâmetro para a página de reset da palavra-passe, como`https://login.microsoftonline.com/?whr=contoso.com`
   * Ao passar `username` o parâmetro para a página de reset da palavra-passe, como`https://login.microsoftonline.com/?username=admin@contoso.com`

Encontre detalhes sobre como configurar a marca da empresa no artigo [Adicione a marca da empresa à sua página de entrada no Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nome do diretório

Pode alterar o atributo de nome de diretório no **Azure Ative Directory** > **Properties**. Pode mostrar um nome de organização amigável que é visto no portal e nas comunicações automatizadas. Esta opção é a mais visível nos e-mails automatizados nos formulários que se seguem:

* O nome amigável no e-mail, por exemplo "Microsoft em nome da demonstração DECONTOSO"
* A linha de assunto no e-mail, por exemplo "código de verificação de email de demonstração DA CONTOSO"

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contacte o seu administrador para obter ajuda para reajustar o seu exemplo de email de palavra-passe"
