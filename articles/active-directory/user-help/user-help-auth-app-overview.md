---
title: O que é a aplicação Microsoft Authenticator? -O azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre o aplicativo Microsoft Authenticator, incluindo o que ele é, como ele funciona e quais informações estão incluídas nesta seção do conteúdo.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468005094ab8a9c42d7eacdfefa990565a3297f3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155813"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>O que é a aplicação Microsoft Authenticator?

O aplicativo Microsoft Authenticator ajuda você a entrar em suas contas se usar a verificação de dois fatores. A verificação de dois fatores ajuda você a acessar suas contas com mais segurança, especialmente ao mesmo tempo em que exibe informações confidenciais. Como as senhas podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é uma etapa de segurança adicional que ajuda a proteger sua conta, dificultando a interrupção de outras pessoas.

Você pode usar o aplicativo Microsoft Authenticator de várias maneiras, incluindo:

- Responda a uma solicitação de autenticação depois de entrar com seu nome de usuário e senha.

- Entre sem inserir uma senha, usar seu nome de usuário, o aplicativo autenticador e seu dispositivo móvel com sua impressão digital, face ou PIN.

- Como um gerador de código para outras contas que dão suporte a aplicativos autenticadores.

> [!Important]
> O aplicativo Microsoft Authenticator funciona com qualquer conta que usa a verificação de dois fatores e dá suporte aos padrões TOTP (senha de uso único) com base no tempo.
>
>Este artigo destina-se a usuários tentando baixar e usar o aplicativo Microsoft Authenticator como um método de verificação de segurança. Se você for um administrador procurando informações sobre como ativar a entrada sem senha usando o aplicativo autenticador para seus funcionários e outros usos, consulte Habilitar a entrada sem [senha com o aplicativo Microsoft Authenticator (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone).

## <a name="terminology"></a>Terminologia

| Duração|Descrição|
| ----|-----------|
| Verificação de dois fatores | Um processo de verificação que exige que você use especificamente apenas duas partes de informações de verificação, como uma senha e um PIN. O aplicativo Microsoft Authenticator dá suporte à verificação padrão de dois fatores e à entrada sem senha. |
| Multi-Factor Authentication (MFA) | Toda a verificação de dois fatores é a autenticação multifator, exigindo que você use *pelo menos* duas partes de informações de verificação, com base nos requisitos da sua organização. |
| Conta Microsoft (também denominada, MSA) | Você cria suas próprias contas pessoais, para obter acesso aos seus produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook, OneDrive, Xbox LIVE ou Office 365. Sua conta Microsoft é criada e armazenada no sistema de contas de identidades de consumidor de Microsoft que é executado pela Microsoft. |
| Conta escolar ou profissional | Sua organização cria sua conta corporativa ou de estudante (como alain@contoso.com) para permitir que você acesse recursos internos e potencialmente restritos, como Microsoft Azure, Windows Intune e Office 365. |
| Código de verificação | O código de seis dígitos que aparece no aplicativo autenticador, em cada conta adicionada. O código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes. Isso também é conhecido como uma senha de uso único (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Como a verificação de dois fatores funciona com o aplicativo

A verificação de dois fatores funciona com o aplicativo Microsoft Authenticator das seguintes maneiras:

- **Notification.** Digite seu nome de usuário e senha no dispositivo no qual você está fazendo logon para sua conta corporativa ou de estudante ou sua conta Microsoft pessoal e, em seguida, o aplicativo Microsoft Authenticator envia uma notificação solicitando que você **aprove a entrada**. Escolha **aprovar** se você reconhece a tentativa de entrada. Caso contrário, escolha **negar**. Se você escolher **negar**, também poderá marcar a solicitação como fraudulenta.

- **Código de verificação.** Digite seu nome de usuário e senha no dispositivo no qual você está fazendo logon para sua conta corporativa ou de estudante ou sua conta Microsoft pessoal e, em seguida, copie o código de verificação associado da tela **contas** do aplicativo Microsoft Authenticator. O código de verificação também é conhecido como autenticação OTP (senha de uso único).

- **Entrada sem senha.** Digite seu nome de usuário no dispositivo no qual você está fazendo logon para sua conta corporativa ou de estudante ou sua conta Microsoft pessoal e, em seguida, use seu dispositivo móvel para verificar se você está usando sua impressão digital, face ou PIN. Para esse método, você não precisa inserir sua senha.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Se os recursos biométricos do dispositivo devem ser usados

Se você usar um PIN para concluir o processo de autenticação, poderá configurar o aplicativo Microsoft Authenticator para usar os recursos de impressão digital ou de reconhecimento facial (biométrica) do dispositivo. Você pode configurar isso na primeira vez em que usar o aplicativo autenticador para verificar sua conta, selecionando a opção para usar seus recursos biométricos de dispositivo como identificação em vez de seu PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?

Dependendo de seu tipo de conta, sua organização pode decidir que você deve usar a verificação de dois fatores ou pode decidir por conta própria.

- **Conta escolar ou profissional.** Se você estiver usando uma conta corporativa ou de estudante (por exemplo, alain@contoso.com), cabe à sua organização se você deve usar a verificação de dois fatores, juntamente com os métodos de verificação específicos. Para obter mais informações sobre como adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator, consulte [adicionar suas contas corporativas ou de estudante](user-help-auth-app-add-work-school-account.md).

- **Conta Microsoft pessoais.** Você pode optar por configurar a verificação de dois fatores para suas contas pessoais da Microsoft (por exemplo, alain@outlook.com). Para obter mais informações sobre como adicionar seus conta Microsoft pessoais, consulte [adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

- **Não conta Microsoft.** Você pode optar por configurar a verificação de dois fatores para suas contas que não são da Microsoft (por exemplo, alain@gmail.com). Suas contas que não são da Microsoft podem não usar o termo de verificação de dois fatores, mas você deve ser capaz de encontrar o recurso dentro das configurações de **segurança** ou de **entrada** . O aplicativo Microsoft Authenticator funciona com qualquer conta que dê suporte aos padrões TOTP. Para obter mais informações sobre como adicionar suas contas que não são da Microsoft, consulte [adicionar suas contas que não são da Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Nesta secção

| Artigo | Descrição |
| ------ | ------------ |
| [Transferir e instalar a aplicação](user-help-auth-app-download-install.md) | Descreve onde e como obter e instalar o aplicativo Microsoft Authenticator para dispositivos que executam o Android e o iOS. |
| [Adicionar suas contas corporativas ou de estudante](user-help-auth-app-add-work-school-account.md) | Descreve como adicionar suas várias contas corporativas ou de estudante e pessoais ao aplicativo Microsoft Authenticator. |
| [Adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md) | Descreve como adicionar suas contas pessoais da Microsoft ao aplicativo Microsoft Authenticator. |
| [Adicionar suas contas que não são da Microsoft](user-help-auth-app-add-non-ms-account.md) | Descreve como adicionar suas contas que não são da Microsoft ao aplicativo Microsoft Authenticator. |
| [Adicionar manualmente suas contas](user-help-auth-app-add-account-manual.md) | Descreve como adicionar manualmente suas contas ao aplicativo Microsoft Authenticator, caso não seja possível verificar o código QR fornecido. |
| [Entrar usando o aplicativo](user-help-auth-app-sign-in.md) | Descreve como entrar em suas várias contas, usando o aplicativo Microsoft Authenticator.|
| [Cópia de segurança e credenciais de recuperação da conta](user-help-auth-app-backup-recovery.md) | Apresenta informações sobre como criar cópias de segurança e recuperar as suas credenciais da conta através da aplicação Microsoft Authenticator. |
| [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md) | Disponibiliza respostas a perguntas mais frequentes sobre a aplicação. |
