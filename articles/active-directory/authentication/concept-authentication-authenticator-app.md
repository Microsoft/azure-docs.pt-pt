---
title: Método de autenticação de aplicativos Microsoft Authenticator - Azure Ative Directory
description: Saiba mais sobre a utilização da aplicação Microsoft Authenticator no Azure Ative Directory para ajudar a melhorar e garantir eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530506"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Métodos de autenticação no Azure Ative Directory - Microsoft Authenticator app

A aplicação Microsoft Authenticator fornece um nível adicional de segurança ao seu trabalho de AD ou conta escolar do Azure ou à sua conta Microsoft e está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS.](https://go.microsoft.com/fwlink/?linkid=866594) Com a aplicação Microsoft Authenticator, os utilizadores podem autenticar de forma sem palavras-passe durante o sent-in ou como uma opção de verificação adicional durante o reset da palavra-passe de autosserviço (SSPR) ou a autenticação multi-factor Ad Azure.

Os utilizadores podem receber uma notificação através da aplicação móvel para que possam aprovar ou negar, ou utilizar a aplicação Authenticator para gerar um código de verificação da OAUTH que possa ser introduzido numa interface de entrada. Se ativar um código de notificação e verificação, os utilizadores que registem a aplicação Authenticator podem utilizar qualquer método para verificar a sua identidade.

Para utilizar a aplicação Authenticator num pedido de entrada em vez de um nome de utilizador e uma combinação de palavra-passe, consulte [Ativar o início de sção sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os utilizadores não têm a opção de registar a sua aplicação móvel quando ativam o SSPR. Em vez disso, os utilizadores podem registar a sua aplicação móvel no [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou como parte do registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Entrada sem palavra-passe

Em vez de ver uma solicitação para uma palavra-passe depois de introduzir um nome de utilizador, um utilizador que tenha ativado o acesso ao telefone a partir da aplicação Microsoft Authenticator vê uma mensagem para tocar num número na sua aplicação. Quando o número correto é selecionado, o processo de inscrição está completo.

![Exemplo de um sinal de inscrição no navegador pedindo ao utilizador que aprove o sign-in](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Este método de autenticação proporciona um elevado nível de segurança e elimina a necessidade de o utilizador fornecer uma palavra-passe no início da sação. 

Para começar com o início do sent in sem palavras-passe, consulte Ativar o [início de sing in sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

A aplicação Authenticator pode ajudar a impedir o acesso não autorizado a contas e a impedir transações fraudulentas, empurrando uma notificação para o seu smartphone ou tablet. Os utilizadores visualizam a notificação e, se for legítima, **selecione Verificar**. Caso contrário, podem selecionar **Deny**.

![Screenshot de exemplo pedido de navegador web para notificação de aplicação autenticador para completar processo de sação](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a notificação através do método *de aplicação móvel* em dispositivos Android não funciona nesse país/região, uma vez que os serviços de jogo da Google (incluindo notificações push) estão bloqueados na região. No entanto, a notificação do iOS funciona. Para dispositivos Android, devem ser disponibilizados métodos alternativos de autenticação para esses utilizadores.

## <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

A aplicação Authenticator pode ser usada como um símbolo de software para gerar um código de verificação do OATH. Depois de introduzir o seu nome de utilizador e senha, introduza o código fornecido pela aplicação Authenticator na interface de entrada. O código de verificação proporciona uma segunda forma de autenticação.

Os utilizadores podem ter uma combinação de até cinco fichas de hardware OATH ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para ser utilizada a qualquer momento.

> [!WARNING]
> Para garantir o mais alto nível de segurança para a redefinição de senha de autosserviço quando apenas um método é necessário para o reset, um código de verificação é a única opção disponível para os utilizadores.
>
> Quando são necessários dois métodos, os utilizadores podem reiniciar usando um código de notificação ou verificação para além de quaisquer outros métodos ativados.

## <a name="next-steps"></a>Passos seguintes

Para começar com o início do sent in sem palavras-passe, consulte Ativar o [início de sing in sem palavras-passe com a aplicação Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Saiba mais sobre a configuração dos métodos de autenticação utilizando a API do [Microsoft Graph REST](/graph/api/resources/authenticationmethods-overview).
