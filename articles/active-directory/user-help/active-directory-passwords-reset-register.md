---
title: Registe-se para reposição de palavra-passe self-service - Azure Active Directory | Documentos da Microsoft
description: Registar dados de autenticação de palavra-passe self-service do Azure AD repor
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
ms.openlocfilehash: f113c42ff45811f31eb77a92873fb187dd51ea6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530901"
---
# <a name="register-for-self-service-password-reset"></a>Registar-se na reposição personalizada de palavra-passe

> [!IMPORTANT]
> Está aqui porque não consigo iniciar sessão? Se assim for, veja [repor a palavra-passe profissional ou escolar](active-directory-passwords-update-your-own-password.md).

Como um utilizador final, pode repor a palavra-passe ou desbloquear a conta por si próprio, se utilizar a reposição de palavra-passe self-service (SSPR) do Azure Active Directory (Azure AD). Antes de poder utilizar esta funcionalidade, terá de registar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que o administrador tem preenchido.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registar ou confirmar dados de autenticação com SSPR

1. Abra o browser no seu dispositivo e vá para o [página de registo de reposição de palavra-passe](https://aka.ms/ssprsetup).
2. Introduza o seu nome de utilizador e a palavra-passe que o administrador fornecido.
3. Dependendo de como a sua equipa de TI configurou as coisas, uma ou mais das seguintes opções estão disponíveis para configurar e verificar. Se o administrador tem sua permissão para utilizar as suas informações, eles podem preencher algumas das informações para.
    * **Telefone do escritório**: Apenas o administrador pode definir esta opção.
    * **Telefone de autenticação**: Defina esta opção para outro número de telefone que tem acesso. Um exemplo é um telefone celular que podem receber uma mensagem de texto ou de uma chamada.
    * **E-Mail de autenticação**: Defina esta opção para um endereço de correio eletrónico alternativo que pode aceder sem utilizar a palavra-passe que pretende repor.
    * **Perguntas de segurança**: O administrador aprovou esta lista de perguntas para que possa responder. Não é possível utilizar a mesma pergunta ou responder a mais de uma vez.
4. Forneça e verifique se as informações que o administrador necessita. Se mais do que uma opção está disponível, sugerimos que registe vários métodos. Isso lhe dá flexibilidade quando um dos métodos não está disponível. Um exemplo é quando estiver viajando e não for possível aceder ao seu telefone do escritório.

    ![Registre-se métodos de autenticação e selecione concluir][Register]

5. Selecione **concluir**. Agora, pode utilizar o SSPR quando precisar no futuro.

Se introduzir dados para **telefone de autenticação** ou **E-Mail de autenticação**, não é visível no diretório global. As únicas pessoas que podem ver estes dados são os administradores. Apenas verá as respostas para suas perguntas de segurança.

Os administradores podem pedir para confirmar os métodos de autenticação após um período de tempo para se certificar de que ainda terá os métodos adequados registados.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e as suas soluções

 Aqui estão alguns casos de erro comuns e as respectivas soluções:

| Caso de erro| Que erro vê?| Solução |
| --- | --- | --- |
| Posso obter uma página "Contacte o administrador" depois de introduzir o meu ID de utilizador | Contacte o administrador. <br> <br> Detetámos que a palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, estamos não é possível repor automaticamente a sua palavra-passe. <br> <br> Contacte o seu departamento de TI para obter ajuda adicional. | Está a ver esta mensagem porque o seu departamento de TI gere a sua palavra-passe no seu ambiente no local e não permite-lhe repor a palavra-passe a **não é possível aceder à sua conta** ligação. <br> <br> Para repor a palavra-passe, contacte o seu departamento de TI diretamente para obter ajuda. Informá-los de que pretende repor a palavra-passe, de modo que podem ativar esta funcionalidade para.|
| Recebo um erro "o sua conta não está ativada para a reposição de palavra-passe" depois de introduzir o meu ID de utilizador | Sua conta não está ativada para a reposição de palavra-passe. <br> <br> Lamentamos, mas sua equipe de TI não definiu a sua conta para utilizar com este serviço. <br> <br> Se desejar, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque o seu departamento de TI não ativada para a sua organização a partir de reposição de palavra-passe a **não é possível aceder à sua conta** ligar ou ainda não licenciado para utilizar a funcionalidade. <br> <br> Para repor a palavra-passe, selecione o **contacte o administrador** ligação. Será enviado um e-mail para a sua empresa a equipe de TI. O e-mail permite-lhes saber que pretende repor a palavra-passe, para que eles podem ativar esta funcionalidade para. |
| Recebo um erro "não foi possível verificar a conta" depois de introduzir o meu ID de utilizador | Não foi possível verificar a sua conta. <br> <br> Se desejar, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque está ativada para a reposição de palavra-passe, mas ainda não registada para utilizar o serviço. Para se registar na reposição de palavra-passe, vá para o [página de registo de reposição de palavra-passe](https://aka.ms/ssprsetup) depois de ter voltou a ter acesso à sua conta. <br> <br> Para repor a palavra-passe, selecione o **contacte o administrador** ligação para enviar um e-mail para a sua empresa a equipe de TI. |

## <a name="next-steps"></a>Passos Seguintes

* [Alterar a palavra-passe através da reposição de palavra-passe self-service](active-directory-passwords-update-your-own-password.md)
* [Página de registo de reposição de palavra-passe](https://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Quando não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registo de reposição de palavra-passe que mostra os métodos registados e o botão Concluir"

