---
title: Personalizando a redefinição de senha de autoatendimento do Azure AD-Azure Active Directory
description: Opções de personalização para redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527dd99f122ec70cc47305947a5cbce3207b9664
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666308"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade do Azure AD para redefinição de senha de autoatendimento

Os profissionais de ti que desejam implantar SSPR (redefinição de senha de autoatendimento) no Azure Active Directory (Azure AD) podem personalizar a experiência de acordo com as necessidades dos usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Os usuários de redefinição de senha de autoatendimento têm um link "Contate o administrador" disponível para eles no portal de redefinição de senha. Se um usuário selecionar esse link, ele fará uma destas duas coisas:

* Se deixado no estado padrão:
   * O email é enviado aos seus administradores e solicita que eles forneçam assistência para alterar a senha do usuário. Consulte o [email de exemplo](#sample-email) abaixo.
* Se personalizado:
   * Envia o usuário para uma página da Web ou endereço de email especificado pelo administrador para obter assistência.

> [!TIP]
> Se você personalizar isso, é recomendável definir isso para algo que os usuários já conhecem para obter suporte

> [!WARNING]
> Se você personalizar essa configuração com um endereço de email e uma conta que precise de uma redefinição de senha, talvez o usuário não possa solicitar assistência.

### <a name="sample-email"></a>Email de exemplo

![Exemplo de solicitação para redefinir email enviado ao administrador][Contact]

O email de contato é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **administrador de senha** for atribuída, os administradores com essa função serão notificados.
2. Se não forem atribuídos administradores de senha, os administradores com a função de **administrador de usuários** serão notificados.
3. Se nenhuma das funções anteriores for atribuída, os **administradores globais** serão notificados.

Em todos os casos, um máximo de 100 destinatários é notificado.

Para saber mais sobre as diferentes funções de administrador e como atribuí-las, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar emails de "Contate o administrador"

Se sua organização não deseja notificar os administradores sobre solicitações de redefinição de senha, você pode habilitar a seguinte configuração:

* Habilite a redefinição de senha de autoatendimento para todos os usuários finais. Essa opção está em > **Propriedades**de redefinição de senha. Se você não quiser que os usuários redefinam suas próprias senhas, poderá definir o escopo de acesso a um grupo vazio. *Não recomendamos essa opção.*
* Personalize o link da assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência.  > Esta opção está em**email ou URL do helpdesk personalizado de**redefinição > de senha.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizar a página de entrada AD FS para SSPR

Os administradores do Serviços de Federação do Active Directory (AD FS) (AD FS) podem adicionar um link à sua página de entrada usando as diretrizes encontradas no artigo [Descrição da página Adicionar entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) .

Para adicionar um link à página de entrada AD FS, use o comando a seguir no servidor de AD FS. Os usuários podem usar essa página para inserir o fluxo de trabalho do SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar a página de entrada e a aparência do painel de acesso

Você pode personalizar a página de entrada. Você pode adicionar um logotipo que aparece junto com a imagem que se ajusta à identidade visual da sua empresa.

Os elementos gráficos que você escolher serão mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
   * Passando o `whr` parâmetro para a página de redefinição de senha, como`https://login.microsoftonline.com/?whr=contoso.com`
   * Passando o `username` parâmetro para a página de redefinição de senha, como`https://login.microsoftonline.com/?username=admin@contoso.com`

Encontre detalhes sobre como configurar a identidade visual da empresa no artigo [Adicionar identidade visual da empresa à sua página de entrada no Azure ad](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nome do diretório

Você pode alterar o atributo de nome de diretório em**Propriedades** **Azure Active Directory** > . Você pode mostrar um nome de organização amigável que é visto no portal e nas comunicações automatizadas. Essa opção é a mais visível em emails automatizados nos formulários a seguir:

* O nome amigável no email, por exemplo "Microsoft em nome da demonstração da CONTOSO"
* A linha de assunto no email, por exemplo "código de verificação de email da conta de demonstração da CONTOSO"

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

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contate o administrador para obter ajuda com a redefinição do exemplo de email de senha"
