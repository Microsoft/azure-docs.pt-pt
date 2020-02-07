---
title: O que é a aplicação Microsoft Authenticator? -O azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre o aplicativo Microsoft Authenticator, incluindo o que ele é, como ele funciona e quais informações estão incluídas nesta seção do conteúdo.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: c95ae2e48a7d5a3b9e9e28b1d4e14b670205c043
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062290"
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
>Este artigo destina-se a usuários tentando baixar e usar o aplicativo Microsoft Authenticator como um método de verificação de segurança. Se for um administrador à procura de informações sobre como ativar o sessão sem palavras-passe utilizando a aplicação Autenticador para os seus colaboradores e outras utilizações, consulte o ['Enable' sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone).

## <a name="terminology"></a>Terminologia

| Duração|Descrição|
| ----|-----------|
| Verificação de dois fatores | Um processo de verificação que exige que você use especificamente apenas duas partes de informações de verificação, como uma senha e um PIN. O aplicativo Microsoft Authenticator dá suporte à verificação padrão de dois fatores e à entrada sem senha. |
| Multi-Factor Authentication (MFA) | Toda a verificação de dois fatores é a autenticação de vários fatores, exigindo que utilize *pelo menos* duas peças de informação de verificação, com base nos requisitos da sua organização. |
| Conta Microsoft (também denominada, MSA) | Você cria suas próprias contas pessoais, para obter acesso aos seus produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook, OneDrive, Xbox LIVE ou Office 365. Sua conta Microsoft é criada e armazenada no sistema de contas de identidades de consumidor de Microsoft que é executado pela Microsoft. |
| Conta escolar ou profissional | A sua organização cria o seu trabalho ou conta escolar (como alain@contoso.com) para permitir o acesso a recursos internos e potencialmente restritos, como o Microsoft Azure, Windows Intune e Office 365. |
| Código de verificação | O código de seis dígitos que aparece no aplicativo autenticador, em cada conta adicionada. O código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes. Isso também é conhecido como uma senha de uso único (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Como a verificação de dois fatores funciona com o aplicativo

A verificação de dois fatores funciona com o aplicativo Microsoft Authenticator das seguintes maneiras:

- **A notificação.** Digite o seu nome de utilizador e palavra-passe no dispositivo em que está a iniciar sessão para a sua conta de trabalho ou escola ou para a sua conta pessoal da Microsoft, e depois a aplicação Microsoft Authenticator envia uma notificação **pedindo-lhe**para aprovar o login . Escolha **Aprovar** se reconhecer a tentativa de inscrição. Caso contrário, escolha **Negar.** Se escolher **o Deny,** também pode marcar o pedido como fraudulento.

- **Código de verificação.** Digite o seu nome de utilizador e palavra-passe no dispositivo em que está a iniciar sessão para a sua conta de trabalho ou escola ou para a sua conta pessoal da Microsoft, e, em seguida, copie o código de verificação associado do ecrã de **Contas** da aplicação Microsoft Authenticator. O código de verificação também é conhecido como autenticação OTP (senha de uso único).

- **Entrada sem palavras-passe.** Digite seu nome de usuário no dispositivo no qual você está fazendo logon para sua conta corporativa ou de estudante ou sua conta Microsoft pessoal e, em seguida, use seu dispositivo móvel para verificar se você está usando sua impressão digital, face ou PIN. Para esse método, você não precisa inserir sua senha.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Se os recursos biométricos do dispositivo devem ser usados

Se você usar um PIN para concluir o processo de autenticação, poderá configurar o aplicativo Microsoft Authenticator para usar os recursos de impressão digital ou de reconhecimento facial (biométrica) do dispositivo. Você pode configurar isso na primeira vez em que usar o aplicativo autenticador para verificar sua conta, selecionando a opção para usar seus recursos biométricos de dispositivo como identificação em vez de seu PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?

Dependendo de seu tipo de conta, sua organização pode decidir que você deve usar a verificação de dois fatores ou pode decidir por conta própria.

- **Trabalho ou conta escolar.** Se estiver a usar uma conta de trabalho ou de escola (por exemplo, alain@contoso.com), cabe à sua organização utilizar a verificação de dois fatores, juntamente com os métodos de verificação específicos. Para obter mais informações sobre a adição do seu trabalho ou conta escolar à aplicação Microsoft Authenticator, consulte [Adicionar o seu trabalho ou contas escolares](user-help-auth-app-add-work-school-account.md).

- **Conta pessoal da Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas pessoais da Microsoft (por exemplo, alain@outlook.com). Para mais informações sobre a adição da sua conta pessoal da Microsoft, consulte [Adicionar as suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

- **Conta não Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas não Microsoft (por exemplo, alain@gmail.com). As suas contas não Microsoft podem não utilizar o termo, verificação de dois fatores, mas deverá ser capaz de encontrar a funcionalidade dentro das definições de **Segurança** ou **de Iniciar Sessão.** O aplicativo Microsoft Authenticator funciona com qualquer conta que dê suporte aos padrões TOTP. Para obter mais informações sobre a adição das suas contas não Microsoft, consulte [Adicionar as suas contas não Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Nesta secção

| Artigo | Descrição |
| ------ | ------------ |
| [Transferir e instalar a aplicação](user-help-auth-app-download-install.md) | Descreve onde e como obter e instalar o aplicativo Microsoft Authenticator para dispositivos que executam o Android e o iOS. |
| [Adicione o seu trabalho ou contas escolares](user-help-auth-app-add-work-school-account.md) | Descreve como adicionar suas várias contas corporativas ou de estudante e pessoais ao aplicativo Microsoft Authenticator. |
| [Adicione as suas contas pessoais](user-help-auth-app-add-personal-ms-account.md) | Descreve como adicionar suas contas pessoais da Microsoft ao aplicativo Microsoft Authenticator. |
| [Adicione as suas contas não Microsoft](user-help-auth-app-add-non-ms-account.md) | Descreve como adicionar suas contas que não são da Microsoft ao aplicativo Microsoft Authenticator. |
| [Adicione manualmente as suas contas](user-help-auth-app-add-account-manual.md) | Descreve como adicionar manualmente suas contas ao aplicativo Microsoft Authenticator, caso não seja possível verificar o código QR fornecido. |
| [Iniciar sessão utilizando a aplicação](user-help-auth-app-sign-in.md) | Descreve como entrar em suas várias contas, usando o aplicativo Microsoft Authenticator.|
| [Cópia de segurança e credenciais de recuperação da conta](user-help-auth-app-backup-recovery.md) | Apresenta informações sobre como criar cópias de segurança e recuperar as suas credenciais da conta através da aplicação Microsoft Authenticator. |
| [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md) | Disponibiliza respostas a perguntas mais frequentes sobre a aplicação. |
