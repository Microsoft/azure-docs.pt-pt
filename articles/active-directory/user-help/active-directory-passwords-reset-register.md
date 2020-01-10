---
title: Registrar informações de autenticação para redefinir sua própria senha-Azure AD
description: Registre suas informações de método de verificação para redefinição de senha de autoatendimento do Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6dddd13f31366e5a99d6c68ab82b048de4064b2
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681366"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre suas informações do método de verificação para redefinir sua própria senha

> [!IMPORTANT]
> Você está aqui porque não consegue entrar? Nesse caso, consulte [redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md).

Como um usuário final, você pode redefinir sua senha ou desbloqueá-la por conta própria se você Azure Active Directory usar a SSPR (redefinição de senha de autoatendimento) do AD do Azure. Antes de usar essa funcionalidade, você precisa registrar seus métodos de verificação ou confirmar os métodos de verificação predefinidos que o administrador preencheu.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registar ou confirmar dados de autenticação com SSPR

1. Abra o navegador da Web em seu dispositivo e vá para a [página de registro de redefinição de senha](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).
2. Insira seu nome de usuário e a senha que o administrador forneceu.
3. Dependendo de como sua equipe de ti configurou as coisas, uma ou mais das opções a seguir estão disponíveis para você configurar e verificar. Se o administrador tiver sua permissão para usar suas informações, eles poderão preencher algumas das informações para você.
    * **Telefone comercial**: somente o administrador pode definir essa opção.
    * **Telefone de autenticação**: defina essa opção para outro número de telefone ao qual você tenha acesso. Um exemplo é um telefone celular que pode receber um texto ou uma chamada.
    * **Email de autenticação**: defina essa opção como um endereço de email alternativo que você pode acessar sem usar a senha que deseja redefinir.
    * **Perguntas de segurança**: o administrador aprovou esta lista de perguntas para que você responda. Você não pode usar a mesma pergunta ou resposta mais de uma vez.
4. Forneça e verifique as informações exigidas pelo administrador. Se mais de uma opção estiver disponível, sugerimos que você registre vários métodos. Isso oferece flexibilidade quando um dos métodos não está disponível. Um exemplo é quando você está viajando e não consegue acessar seu telefone comercial.

    ![Registrar métodos de verificação e selecionar concluir][Register]

5. Selecione **concluir**. Agora você pode usar SSPR quando precisar no futuro.

Se você inserir dados para o **telefone de autenticação** ou email de **autenticação**, ele não estará visível no diretório global. As únicas pessoas que podem ver estes dados são os administradores. Somente você pode ver as respostas para suas perguntas de segurança.

Seus administradores podem exigir que você confirme seus métodos de verificação após um período de tempo para garantir que você ainda tenha os métodos apropriados registrados.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Caso de erro| Que erro você vê?| Solução |
| --- | --- | --- |
| Recebo uma página "entre em contato com seu administrador" depois de inserir minha ID de usuário | Contacte o administrador. <br> <br> Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Como resultado, não é possível redefinir sua senha automaticamente. <br> <br> Entre em contato com a equipe de ti para obter mais assistência. | Você está vendo esta mensagem porque sua equipe de ti gerencia sua senha em seu ambiente local e não permite que você Redefina sua senha no link **não consegue acessar sua conta** . <br> <br> Para redefinir sua senha, entre em contato com a equipe de ti diretamente para obter ajuda. Informe que você deseja redefinir sua senha para que ela possa habilitar esse recurso para você.|
| Recebo um erro "sua conta não está habilitada para redefinição de senha" depois de inserir minha ID de usuário | Sua conta não está habilitada para redefinição de senha. <br> <br> Lamentamos, mas sua equipe de ti não configurou sua conta para uso com este serviço. <br> <br> Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Você está vendo esta mensagem porque sua equipe de ti não habilitou a redefinição de senha para sua organização no link **não consegue acessar sua conta** ou não licenciou você para usar o recurso. <br> <br> Para redefinir sua senha, selecione o link **contate um administrador** . Um email será enviado para a equipe de ti da sua empresa. O email permite que eles saibam que você deseja redefinir sua senha, para que eles possam habilitar esse recurso para você. |
| Recebo um erro "não foi possível verificar sua conta" depois de inserir minha ID de usuário | Não foi possível verificar sua conta. <br> <br> Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Você está vendo esta mensagem porque você está habilitado para redefinição de senha, mas não se registrou para usar o serviço. Para se registrar para a redefinição de senha, vá para a [página de registro de redefinição de senha](https://aka.ms/ssprsetup) depois que você tiver reobtido o acesso à sua conta. <br> <br> Para redefinir sua senha, selecione o link **entre em contato com um administrador** para enviar um email para a equipe de ti da sua empresa. |

## <a name="next-steps"></a>Passos seguintes

* [Alterar sua senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)
* [Página de registo de reposição de palavra-passe](https://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Quando não for possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro de redefinição de senha mostrando os métodos registrados e o botão concluir"

