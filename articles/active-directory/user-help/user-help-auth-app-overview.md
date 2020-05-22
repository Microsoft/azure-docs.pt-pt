---
title: O que é a aplicação Microsoft Authenticator? - Diretório Ativo Azure / Microsoft Docs
description: Saiba mais sobre a aplicação Microsoft Authenticator, incluindo o que é, como funciona e que informações estão incluídas nesta secção do conteúdo.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 60c2b3ed1f367dc5c501a51ed52440f7d9d3ed6b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741175"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>O que é a aplicação Microsoft Authenticator?

A aplicação Microsoft Authenticator ajuda-o a iniciar sessão nas suas contas se utilizar a verificação de dois fatores. A verificação de dois fatores ajuda-o a aceder mais seguro às suas contas, especialmente ao visualizar informações sensíveis. Como as palavras-passe podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é um passo adicional de segurança que ajuda a proteger a sua conta, dificultando a entrada de outras pessoas.

Pode utilizar a aplicação Microsoft Authenticator de várias formas, incluindo:

- Responda a um pedido de autenticação depois de iniciar sessão com o seu nome de utilizador e senha.

- Iniciar sessão sem introduzir uma palavra-passe, utilizando o seu nome de utilizador, a aplicação autenticadora e o seu dispositivo móvel com a sua impressão digital, rosto ou PIN.

- Como gerador de código para quaisquer outras contas que suportem aplicações autenticadoras.

> [!Important]
> A aplicação Microsoft Authenticator funciona com qualquer conta que utilize verificação de dois fatores e suporte os padrões de senha de uma vez (TOTP) baseados no tempo.
>
>Este artigo destina-se a utilizadores que tentem descarregar e utilizar a aplicação Microsoft Authenticator como um método de verificação de segurança. Se for um administrador à procura de informações sobre como ativar o sessão sem palavras-passe utilizando a aplicação Autenticador para os seus colaboradores e outras utilizações, consulte o ['Enable' sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone).

## <a name="terminology"></a>Terminologia

| Termo|Descrição|
| ----|-----------|
| Verificação de dois fatores | Um processo de verificação que requer que utilize especificamente apenas duas peças de informação de verificação, como uma palavra-passe e um PIN. A aplicação Microsoft Authenticator suporta tanto a verificação padrão de dois fatores como o sem palavras-de-cartaz. |
| Multi-Factor Authentication (MFA) | Toda a verificação de dois fatores é a autenticação de vários fatores, exigindo que utilize *pelo menos* duas peças de informação de verificação, com base nos requisitos da sua organização. |
| Conta Microsoft (também chamada, MSA) | Cria as suas próprias contas pessoais, para ter acesso aos seus produtos microsoft orientados para o consumidor e serviços na nuvem, tais como Outlook, OneDrive, Xbox LIVE ou Office 365. A sua conta Microsoft é criada e armazenada no sistema de conta de identidade de consumo da Microsoft que é gerido pela Microsoft. |
| Conta escolar ou profissional | A sua organização cria o seu trabalho ou conta escolar alain@contoso.com (como) para permitir o acesso a recursos internos e potencialmente restritos, como o Microsoft Azure, Windows Intune e Office 365. |
| Código de verificação | O código de seis dígitos que aparece na aplicação autenticadora, em cada conta adicionada. O código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes. Isto também é conhecido como uma senha única (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Como a verificação de dois fatores funciona com a app

A verificação de dois fatores funciona com a aplicação Microsoft Authenticator das seguintes formas:

- **A notificação.** Digite o seu nome de utilizador e palavra-passe no dispositivo em que está a iniciar sessão para a sua conta de trabalho ou escola ou para a sua conta pessoal da Microsoft, e depois a aplicação Microsoft Authenticator envia uma notificação **pedindo-lhe**para aprovar o login . Escolha **Aprovar** se reconhecer a tentativa de inscrição. Caso contrário, escolha **Negar.** Se escolher **o Deny,** também pode marcar o pedido como fraudulento.

- **Código de verificação.** Digite o seu nome de utilizador e palavra-passe no dispositivo em que está a iniciar sessão para a sua conta de trabalho ou escola ou para a sua conta pessoal da Microsoft, e, em seguida, copie o código de verificação associado do ecrã de **Contas** da aplicação Microsoft Authenticator. O código de verificação também é conhecido como autenticação de senha única (OTP).

- **Entrada sem palavras-passe.** Digite o seu nome de utilizador no dispositivo em que está a iniciar sessão para o seu trabalho ou conta escolar ou para a sua conta pessoal da Microsoft, e depois use o seu dispositivo móvel para verificar se é você usando a sua impressão digital, rosto ou PIN. Para este método, não precisa de introduzir a sua palavra-passe.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Se usar as capacidades biométricas do seu dispositivo

Se utilizar um PIN para completar o processo de autenticação, pode configurar a aplicação Microsoft Authenticator para, em vez disso, utilizar as capacidades de impressão digital ou reconhecimento facial (biométrico) do seu dispositivo. Pode configurar isto na primeira vez que utilizar a aplicação autenticadora para verificar a sua conta, selecionando a opção de utilizar as capacidades biométricas do seu dispositivo como identificação em vez do SEU PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se usa este recurso?

Dependendo do tipo de conta, a sua organização pode decidir que deve usar a verificação de dois fatores, ou poderá decidir por si mesmo.

- **Trabalho ou conta escolar.** Se está a usar uma conta de trabalho ou de escola (por exemplo), cabe à sua organização utilizar a alain@contoso.com verificação de dois fatores, juntamente com os métodos de verificação específicos. Para obter mais informações sobre a adição do seu trabalho ou conta escolar à aplicação Microsoft Authenticator, consulte [Adicionar o seu trabalho ou contas escolares](user-help-auth-app-add-work-school-account.md).

- **Conta pessoal da Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas pessoais da Microsoft (por exemplo, alain@outlook.com ). Para mais informações sobre a adição da sua conta pessoal da Microsoft, consulte [Adicionar as suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

- **Conta não Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas não Microsoft (por exemplo, alain@gmail.com ). As suas contas não Microsoft podem não utilizar o termo, verificação de dois fatores, mas deverá ser capaz de encontrar a funcionalidade dentro das definições de **Segurança** ou **de Iniciar Sessão.** A aplicação Microsoft Authenticator funciona com quaisquer contas que suportem os padrões TOTP. Para obter mais informações sobre a adição das suas contas não Microsoft, consulte [Adicionar as suas contas não Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Nesta secção

| Artigo | Descrição |
| ------ | ------------ |
| [Transferir e instalar a aplicação](user-help-auth-app-download-install.md) | Descreve onde e como obter e instalar a aplicação Microsoft Authenticator para dispositivos que executam Android e iOS. |
| [Adicione o seu trabalho ou contas escolares](user-help-auth-app-add-work-school-account.md) | Descreve como adicionar os seus vários trabalhos ou contas escolares e pessoais à aplicação Microsoft Authenticator. |
| [Adicione as suas contas pessoais](user-help-auth-app-add-personal-ms-account.md) | Descreve como adicionar as suas contas pessoais da Microsoft à aplicação Microsoft Authenticator. |
| [Adicione as suas contas não Microsoft](user-help-auth-app-add-non-ms-account.md) | Descreve como adicionar as suas contas não Microsoft à aplicação Microsoft Authenticator. |
| [Adicione manualmente as suas contas](user-help-auth-app-add-account-manual.md) | Descreve como adicionar manualmente as suas contas à aplicação Microsoft Authenticator, se não conseguir digitalizar o código QR fornecido. |
| [Iniciar sessão utilizando a aplicação](user-help-auth-app-sign-in.md) | Descreve como iniciar sessão nas suas várias contas, utilizando a aplicação Microsoft Authenticator.|
| [Cópia de segurança e credenciais de recuperação da conta](user-help-auth-app-backup-recovery.md) | Apresenta informações sobre como criar cópias de segurança e recuperar as suas credenciais da conta através da aplicação Microsoft Authenticator. |
| [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md) | Disponibiliza respostas a perguntas mais frequentes sobre a aplicação. |
