---
title: Passe início de sessão com a aplicação Microsoft Authenticator (pré-visualização) - Azure Active Directory
description: Início de sessão no Azure AD com a aplicação Microsoft Authenticator sem utilizar a sua palavra-passe (pré-visualização pública)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3125de0d1fd784b30c000bb287b457397c0fbebb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703028"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Início de sessão no telefone com a aplicação Microsoft Authenticator (pré-visualização pública)

A aplicação Microsoft Authenticator pode ser utilizada para iniciar sessão em qualquer conta do Azure AD sem utilizar uma palavra-passe. Similar à tecnologia de [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator utiliza autenticação baseada em chave para ativar uma credencial de utilizador que está associada a um dispositivo e utiliza um biometria ou PIN.

![Exemplo de um browser início de sessão pedir ao utilizador aprovar o início de sessão](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um pedido para uma palavra-passe depois de introduzir um nome de utilizador, uma pessoa que ativou o início de sessão no telefone na aplicação Microsoft Authenticator irá ver uma mensagem que indica para toque num número na respetiva aplicação. Na aplicação, o utilizador tem de corresponder ao número, escolha aprovar, em seguida, fornecer o PIN ou biométrico, em seguida, a autenticação será concluída.

## <a name="enable-my-users"></a>Ativar os meus utilizadores

Para a pré-visualização pública, um administrador tem, primeiro, adicione uma política através do powershell para permitir a utilização da credencial no inquilino. Reveja a secção de "Known Issues" antes de efetuar este passo.

### <a name="tenant-prerequisites"></a>Pré-requisitos do inquilino

* Azure Active Directory
* Utilizadores finais ativados para multi-factor Authentication do Azure
* Os utilizadores podem registar os respetivos dispositivos

### <a name="steps-to-enable"></a>Passos para ativar

Siga os passos no artigo [ativar o início de sessão para o Azure AD](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods), para ativar métodos de autenticação de início no seu diretório.

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Como ativar início de sessão no telefone os meus utilizadores finais?

Para a pré-visualização pública, não é possível para impor que os usuários para criar ou utilizar nesta credencial nova. Um utilizador final só irá encontrar o início de sessão passe assim que um administrador tiver ativado o respetivo inquilino e o utilizador atualizou a aplicação Microsoft Authenticator para ativar o início de sessão no telefone.

> [!NOTE]
> Esta capacidade está na aplicação desde Março de 2017, para que haja uma possibilidade que quando a política está ativada para um inquilino, os utilizadores poderão encontrar este fluxo imediatamente. Lembre-se e preparar os seus utilizadores para esta alteração.
>

1. Inscreva-se no multi-factor Authentication
1. Versão mais recente do Microsoft Authenticator instalada em dispositivos com iOS 8.0 ou superior, ou o Android 6.0 ou superior.
1. Conta escolar ou profissional com notificações push adicionado à aplicação. Documentação de utilizador final pode ser encontrada em [ https://aka.ms/authappstart ](https://aka.ms/authappstart).

Assim que o utilizador tiver a conta MFA com notificações push, configuradas na aplicação Microsoft Authenticator, podem seguir os passos no artigo [iniciar sessão com o seu telemóvel, não a palavra-passe](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para concluir o registo de início de sessão no telefone.

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="ad-fs-integration"></a>Integração do AD FS

Quando um utilizador tiver ativado a credencial de passe do Microsoft Authenticator, a autenticação para que o usuário sempre será predefinido para enviar uma notificação para aprovação. Essa lógica impede que os utilizadores num inquilino híbrido sejam direcionadas para AD FS para a verificação de início de sessão sem que o usuário tirar uma etapa adicional de clicar em "Use a palavra-passe em vez disso,". Este processo também irá ignorar quaisquer políticas de acesso condicional no local e fluxos de autenticação pass-through. A exceção a este processo é se um login_hint for especificado, um utilizador serão autoforwarded para o AD FS e ignorar a opção para utilizar as credenciais de início.

### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Os utilizadores finais que estão ativados para a MFA através do servidor de MFA do Azure no local de uma organização pode ainda criar e utilizar uma credencial de início de sessão de telefone de início. Se o utilizador tenta atualizar várias instalações (5 +) do Microsoft Authenticator com a credencial, esta alteração poderá resultar num erro.  

### <a name="device-registration"></a>Registo de dispositivos

Um dos pré-requisitos para criar esta credencial novas, forte, é que o dispositivo em que reside está registrado no inquilino do Azure AD, para um utilizador individual. Devido a restrições de registo do dispositivo, um dispositivo só pode ser registrado num único inquilino. Este limite significa que apenas uma conta profissional ou escolar na aplicação Microsoft Authenticator pode ser ativada para o início de sessão no telemóvel.

## <a name="next-steps"></a>Passos Seguintes

[O que é passe?](concept-authentication-passwordless.md)

[Saiba mais sobre o registo de dispositivos](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre o multi-factor Authentication](../authentication/howto-mfa-getstarted.md)
