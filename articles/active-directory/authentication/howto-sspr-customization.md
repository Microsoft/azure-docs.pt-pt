---
title: Personalizar o reset da palavra-passe de self-service - Azure Ative Directory
description: Saiba como personalizar as opções de visualização e experiência do utilizador para reposição da palavra-passe de autosserviço Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741631"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalize a experiência do utilizador para o reposição da palavra-passe de autosserviço do Azure Ative Directory

O reset da palavra-passe de autosserviço (SSPR) dá aos utilizadores do Azure Ative Directory (Azure AD) a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento no balcão de ajuda. Se a conta de um utilizador estiver bloqueada ou se esquecerem da sua palavra-passe, podem seguir as instruções para se desbloquearem e voltarem ao trabalho. Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar sômata ao seu dispositivo ou a uma aplicação.

Para melhorar a experiência SSPR para os utilizadores, pode personalizar o aspeto e a sensação da página de reset da palavra-passe, notificações de e-mail ou páginas de entrada. Estas opções de personalização permitem-lhe deixar claro ao utilizador que estão no lugar certo, e dar-lhes confiança de que estão a aceder aos recursos da empresa.
    
Este artigo mostra-lhe como personalizar o link de e-mail SSPR para utilizadores, branding da empresa e link de página de sN FS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalize o link "Contacte o seu administrador"

Para ajudar os utilizadores a procurar assistência com a redefinição da palavra-passe de autosserviço, é apresentado um link "Contacte o seu administrador" no portal de reset da palavra-passe. Se um utilizador selecionar este link, faz uma de duas coisas:

* Se este link de contacto for deixado no estado predefinido, é enviado um e-mail aos seus administradores e pede-lhes que prestem assistência na alteração da palavra-passe do utilizador. O seguinte e-mail de amostra mostra esta mensagem de correio eletrónico predefinido:

    ![Pedido de amostra para repor e-mail enviado ao administrador](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizado, envia o utilizador para uma página web ou endereço de e-mail especificado pelo administrador para assistência.
    * Se você personalizar isto, recomendamos definir isto para algo que os utilizadores já estão familiarizados com o suporte.

    > [!WARNING]
    > Se personalizar esta definição com um endereço de e-mail e conta que necessite de uma redefinição de palavra-passe, o utilizador poderá não conseguir pedir assistência.

### <a name="default-email-behavior"></a>Comportamento padrão do e-mail

O e-mail de contacto predefinido é enviado aos destinatários na seguinte encomenda:

1. Se a função *de administrador de helpdesk* ou *função de administrador de senha* for atribuída, os administradores com estas funções são notificados.
1. Se não for atribuído nenhum administrador ou administrador de passwords, os administradores com a função *de administrador* do utilizador são notificados.
1. Se nenhuma das funções anteriores for atribuída, os administradores globais são *notificados.*

Em todos os casos, são notificados no máximo 100 destinatários.

Para saber mais sobre as diferentes funções de administrador e como atribuí-las, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Desativar os e-mails "Contacte o seu administrador"

Se a sua organização não quiser notificar os administradores sobre pedidos de reset de palavra-passe, podem ser utilizadas as seguintes opções de configuração:

* Personalize o link helpdesk para fornecer um URL web ou mailto: endereço que os utilizadores podem usar para obter assistência. Esta opção está em **Password Reset**  >  **Customization**  >  **Customdesk email ou URL**.
* Ativar a palavra-passe de autosserviço para todos os utilizadores. Esta opção **encontra-se** em Propriedades reset  >  **password.** Se não quiser que os utilizadores repuvam as suas próprias palavras-passe, pode aceder a um grupo vazio. *Não recomendamos esta opção.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalize a página de inscrição e o painel de acesso

Pode personalizar a página de início de sing, como adicionar um logótipo que aparece juntamente com a imagem que se adequa à marca da sua empresa. Para obter mais informações sobre como configurar a marca da empresa, consulte [a marca Add company na sua página de sing-in em Azure AD](../fundamentals/customize-branding.md).

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois de um utilizador introduzir o seu nome de utilizador
* Se o utilizador aceder ao URL personalizado:
   * Ao passar o `whr` parâmetro para a página de reset da palavra-passe, como `https://login.microsoftonline.com/?whr=contoso.com`
   * Ao passar o `username` parâmetro para a página de reset da palavra-passe, como `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome do diretório

Para que as coisas pareçam mais fáceis de utilizar, pode alterar o nome da organização no portal e nas comunicações automatizadas. Para alterar o atributo de nome de diretório no portal Azure, consulte a **Azure Ative Directory**  >  **Properties**. Esta opção de nome de organização amigável é a mais visível em e-mails automatizados, como nos seguintes exemplos:

* O nome amigável no e-mail, por exemplo "*Microsoft em nome da demo CONTOSO*"
* A linha de assunto no e-mail, por exemplo "*CÓDIGO de verificação de conta de demonstração CONTOSO*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalize a página de sinse-in AD FS

Se utilizar os Serviços da Federação de Diretórios Ativos (AD FS) para eventos de inscrição do utilizador, pode adicionar um link à página de entrada, utilizando as orientações no artigo para adicionar a [descrição da página de entrada.](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Forneça aos utilizadores um link para a página para que estes entrem no fluxo de trabalho SSPR, tais como *https://passwordreset.microsoftonline.com* . Para adicionar um link à página de sindes de sação AD FS, utilize o seguinte comando no seu servidor AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Passos seguintes

Para compreender a utilização da SSPR no seu ambiente, consulte [opções de Reporte para a gestão de passwords AD AZure.](howto-sspr-reporting.md)

Se você ou utilizadores tiverem problemas com SSPR, consulte [o reset da palavra-passe de autosserviço de resolução de problemas](./troubleshoot-sspr.md)