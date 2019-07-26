---
title: Entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia) – Azure Active Directory
description: Entrar no Azure AD usando o aplicativo Microsoft Authenticator sem usar sua senha (visualização pública)
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
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357130"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Entrada no telefone sem senha com o aplicativo Microsoft Authenticator (visualização pública)

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Semelhante à tecnologia do [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo e usa uma biométrica ou um PIN.

![Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um prompt para uma senha depois de inserir um nome de usuário, uma pessoa que tenha habilitado a entrada pelo telefone no aplicativo Microsoft Authenticator verá uma mensagem informando que ele tocará em um número em seu aplicativo. No aplicativo, o usuário deve corresponder ao número, escolher aprovar e, em seguida, fornecer seu PIN ou biométrica, a autenticação será concluída.

## <a name="enable-my-users"></a>Habilitar meus usuários

### <a name="tenant-prerequisites"></a>Pré-requisitos do locatário

* Azure Active Directory
* Usuários finais habilitados para a autenticação multifator do Azure
* Os usuários podem registrar seus dispositivos

### <a name="steps-to-enable"></a>Etapas para habilitar

Siga as etapas no artigo [habilitar a entrada sem senha para o Azure ad](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods), para habilitar métodos de autenticação sem senha no seu diretório.

> [!NOTE]
> Se você habilitou esse recurso anteriormente para seu locatário usando um script do PowerShell, a definição de uma nova política para usuários e grupos substituirá a política existente em todo o locatário. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Como meus usuários finais habilitam a entrada pelo telefone?

Para a visualização pública, não é possível impor os usuários para criar ou usar essa nova credencial. Um usuário final só encontrará a entrada sem senha quando um administrador tiver habilitado seu locatário e o usuário tiver atualizado seu aplicativo Microsoft Authenticator para habilitar a entrada pelo telefone.

> [!NOTE]
> Esse recurso esteve no aplicativo desde março de 2017, portanto, há a possibilidade de que, quando a política estiver habilitada para um locatário, os usuários possam encontrar esse fluxo imediatamente. Lembre-se e prepare seus usuários para essa alteração.
>

1. Registrar-se na autenticação multifator do Azure
1. Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8,0 ou superior, ou Android 6,0 ou superior.
1. Conta corporativa ou de estudante com notificações por push adicionadas ao aplicativo. A documentação do usuário final pode ser encontrada [https://aka.ms/authappstart](https://aka.ms/authappstart)em.

Depois que o usuário tiver a conta do MFA com notificações por push configuradas no aplicativo Microsoft Authenticator, ele poderá seguir as etapas no artigo [entrar com seu telefone, não sua senha](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para concluir o registro de entrada do telefone.

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="ad-fs-integration"></a>Integração do AD FS

Quando um usuário habilitou a credencial Microsoft Authenticator com senha, a autenticação para esse usuário sempre usará como padrão enviar uma notificação para aprovação. Essa lógica impede que os usuários em um locatário híbrido sejam direcionados ao ADFS para verificação de entrada sem que o usuário execute uma etapa adicional para clicar em "usar sua senha". Esse processo também ignorará quaisquer políticas de acesso condicional local e fluxos de autenticação de passagem. A exceção a esse processo é se um login_hint for especificado, um usuário será encaminhado automaticamente para AD FS e ignorará a opção de usar a credencial sem senha.

### <a name="azure-mfa-server"></a>Servidor do Azure MFA

Os usuários finais que estão habilitados para MFA por meio do servidor do Azure MFA local de uma organização ainda podem criar e usar uma única credencial de entrada de telefone sem senha. Se o usuário tentar atualizar várias instalações (5 +) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.  

### <a name="device-registration"></a>Registo de Dispositivo

Um dos pré-requisitos para criar essa nova credencial forte é que o dispositivo onde ele reside é registrado no locatário do Azure AD para um usuário individual. Devido a restrições de registro do dispositivo, um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada pelo telefone.

## <a name="next-steps"></a>Passos Seguintes

[O que não tem senha?](concept-authentication-passwordless.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
