---
title: Personalize o reset de senha de autosserviço - Diretório Ativo Azure
description: Saiba como personalizar as opções de visualização e experiência do utilizador para redefinir palavra-passe autosserviço da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394257"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalize a experiência do utilizador para o reset de palavra-passe self-service Do Diretório Ativo Azure

O reset da palavra-passe self-service (SSPR) confere aos utilizadores do Azure Ative Directory (Azure AD) a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer envolvimento de administrador ou de secretária. Se a conta de um utilizador estiver bloqueada ou se esquecer em palavra-passe, pode seguir as indicações para se desbloquear em si e voltar ao trabalho. Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode iniciar sessão no seu dispositivo ou numa aplicação.

Para melhorar a experiência SSPR para os utilizadores, pode personalizar o aspeto e a sensação da página de reset de palavra-passe, notificações de e-mail ou páginas de início de sessão. Estas opções de personalização permitem-lhe deixar claro ao utilizador que estão no lugar certo, e dar-lhes confiança de que estão a aceder aos recursos da empresa.
    
Este artigo mostra-lhe como personalizar o link de e-mail SSPR para utilizadores, marca de empresa e link de página de entrada de AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalize o link "Contacte o seu administrador"

Para ajudar os utilizadores a procurar assistência com o reset da palavra-passe self-service, é mostrado um link "Contacte o seu administrador" no portal de reset da palavra-passe. Se um utilizador selecionar este link, faz uma de duas coisas:

* Se este link de contacto for deixado no estado padrão, um e-mail é enviado aos seus administradores e pede-lhes que prestem assistência na alteração da palavra-passe do utilizador. O e-mail da amostra seguinte mostra esta mensagem de correio eletrónico predefinida:

    ![Pedido de amostra para redefinir e-mail enviado ao administrador](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizado, envia o utilizador para uma página web ou endereço de e-mail especificado pelo administrador para assistência.
    * Se personalizar isto, recomendamos que este ajuste para algo que os utilizadores já estão familiarizados com o suporte.

    > [!WARNING]
    > Se personalizar esta definição com um endereço de e-mail e uma conta que necessite de uma redefinição de senha, o utilizador poderá não poder pedir assistência.

### <a name="default-email-behavior"></a>Comportamento de e-mail padrão

O e-mail de contacto predefinido é enviado aos destinatários na seguinte ordem:

1. Se for atribuída a função de administrador de *helpdesk* ou de administrador de *passwords,* os administradores com estas funções são notificados.
1. Se não for atribuído nenhum administrador de helpdesk ou administrador de palavra-passe, os administradores com a função de administrador do *utilizador* são notificados.
1. Se nenhuma das funções anteriores for atribuída, os administradores globais são *notificados.*

Em todos os casos, são notificados um máximo de 100 destinatários.

Para saber mais sobre as diferentes funções de administrador e como atribuí-las, consulte as funções de [administrador de atribuição no Diretório Ativo azure](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desativar os e-mails "Contacte o seu administrador"

Se a sua organização não quiser notificar os administradores sobre pedidos de reset de palavras-passe, podem ser utilizadas as seguintes opções de configuração:

* Personalize o link de helpdesk para fornecer um URL ou mailto web: endereço que os utilizadores podem usar para obter assistência. Esta opção está em password **Reset** > **Personalizization** > **Email ou URL**.
* Ativar o reset da palavra-passe self-service para todos os utilizadores. Esta opção encontra-se em > **propriedades**de **reset de palavra-passe**. Se não quiser que os utilizadores reporem as suas próprias palavras-passe, pode aceder a um grupo vazio. *Não recomendamos esta opção.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalize a página de inscrição e o painel de acesso

Pode personalizar a página de login, como adicionar um logótipo que aparece juntamente com a imagem que se adequa à marca da sua empresa. Para obter mais informações sobre como configurar o branding da empresa, consulte [adicionar a marca da empresa à sua página de entrada no Azure AD](../fundamentals/customize-branding.md).

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois de um utilizador introduzir o seu nome de utilizador
* Se o utilizador aceder ao URL personalizado:
   * Ao passar `whr` o parâmetro para a página de reset da palavra-passe, como`https://login.microsoftonline.com/?whr=contoso.com`
   * Ao passar `username` o parâmetro para a página de reset da palavra-passe, como`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome do diretório

Para que as coisas pareçam mais fáceis de utilizar, pode alterar o nome da organização no portal e nas comunicações automatizadas. Para alterar o nome de diretório atribuído no portal Azure, navegue pela **Azure Ative Directory** > **Properties**. Esta opção de nome de organização amigável é a mais visível em e-mails automatizados, como nos seguintes exemplos:

* O nome amigável no e-mail, por exemplo "*Microsoft em nome da demonstração DECONTOSO*"
* A linha de assunto no e-mail, por exemplo " CÓDIGO de verificação de email de*demonstração CONTOSO*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalize a página de inscrição aD FS

Se utilizar os Serviços da Federação de DirectórioActivo (AD FS) para eventos de sessão de utilizador, pode adicionar um link à página de sessão utilizando a orientação no artigo para adicionar a descrição da [página de sessão](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Forneça aos utilizadores um link para a página para que entrem no fluxo de trabalho sspr, tais como *https://passwordreset.microsoftonline.com*. Para adicionar um link à página de sessão aD FS, utilize o seguinte comando no seu servidor AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Passos seguintes

Para compreender o uso de SSPR no seu ambiente, consulte opções de [Reporte para gestão de passwords Azure AD](howto-sspr-reporting.md).

Se você ou utilizadores tiverem problemas com o SSPR, consulte o reset da [palavra-passe self-service Troubleshoot](active-directory-passwords-troubleshoot.md)
