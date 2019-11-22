---
title: Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia) – Azure Active Directory
description: Habilitar a entrada sem senha no Azure AD usando o aplicativo Microsoft Authenticator (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca668036a8b4a4af261ee85b8a884c22258beb4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269495"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia)

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Semelhante à tecnologia do [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo e usa uma biométrica ou um PIN. Esse método de autenticação pode ser usado em qualquer plataforma de dispositivo, incluindo dispositivos móveis e com qualquer aplicativo ou site que se integre com as bibliotecas de autenticação da Microsoft. 

![Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um prompt para uma senha depois de inserir um nome de usuário, uma pessoa que tenha habilitado a entrada por telefone do aplicativo Microsoft Authenticator verá uma mensagem informando que eles tocam em um número em seu aplicativo. No aplicativo, o usuário deve corresponder ao número, escolher aprovar e, em seguida, fornecer seu PIN ou biométrica, a autenticação será concluída.

> [!NOTE]
> Esse recurso esteve no aplicativo Microsoft Authenticator desde março de 2017, portanto, há a possibilidade de que, quando a política estiver habilitada para um diretório, os usuários possam encontrar esse fluxo imediatamente e ver uma mensagem de erro se eles não tiverem sido habilitados pela política. Lembre-se e prepare seus usuários para essa alteração.

## <a name="prerequisites"></a>Pré-requisitos

- Autenticação multifator do Azure, com notificações por push permitidas como um método de verificação 
- Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8,0 ou superior, ou Android 6,0 ou superior.

> [!NOTE]
> Se você habilitou a visualização de entrada sem senha do aplicativo Microsoft Authenticator anterior usando o PowerShell do Azure AD, ela foi habilitada para todo o diretório. Se você habilitar o uso desse novo método, a política do PowerShell será substituída. É recomendável habilitar para todos os usuários em seu locatário por meio dos novos métodos de autenticação; caso contrário, os usuários que não estiverem na nova política não poderão mais fazer logon em passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Habilitar métodos de autenticação com senha

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com a visualização de registro combinada. Siga as etapas no artigo [habilitar o registro de informações de segurança combinadas (versão prévia)](howto-registration-mfa-sspr-combined.md)para habilitar a visualização de registro combinado.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Habilitar métodos de autenticação de entrada por telefone sem senha

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Procure e selecione *Azure Active Directory*. Selecionar métodos de **autenticação** de > de **segurança** > **política de método de autenticação (versão prévia)**
1. Em **entrada no telefone sem senha**, escolha as seguintes opções
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. **Salvar** para definir a nova política

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registro de usuário e gerenciamento do aplicativo Microsoft Authenticator

1. Navegue até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Entrar se ainda não estiver
1. Adicione um aplicativo autenticador clicando em **Adicionar método**, escolhendo **aplicativo autenticador**e clicando em **Adicionar**
1. Siga as instruções para instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo
1. Clique em **concluído** para concluir o fluxo de instalação do aplicativo MFA do autenticador. 
1. Em **Microsoft Authenticator**, escolha **Habilitar entrada pelo telefone** no menu suspenso conta
1. Siga as instruções no aplicativo para concluir o registro de entrada por telefone sem senha. 

As organizações podem apontar seus usuários para o artigo [entrar com seu telefone, não sua senha](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para obter mais assistência na configuração do Microsoft Authenticator aplicativo e habilitar a entrada pelo telefone.

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

Para a visualização pública, não é possível impor os usuários para criar ou usar essa nova credencial. Um usuário só encontrará a entrada sem senha quando um administrador tiver habilitado seu locatário **e** o usuário tiver atualizado seu aplicativo Microsoft Authenticator para habilitar a entrada pelo telefone.

Depois de digitar seu nome de usuário na Web e selecionar **Avançar**, os usuários receberão um número e receberão uma solicitação em seu aplicativo Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar sua senha. 

![Exemplo de uma entrada no navegador usando o aplicativo Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>O usuário não está habilitado pela política, mas ainda tem um método de entrada de telefone sem senha no Microsoft Authenticator

É possível que um usuário em algum momento tenha criado uma credencial de entrada no telefone sem senha em seu aplicativo Microsoft Authenticator atual ou em um dispositivo anterior. Depois que um administrador habilita a política de método de autenticação para entrada por telefone sem senha, qualquer usuário com uma credencial registrada começará a experimentar o novo prompt de entrada, independentemente de ter sido habilitado para usar a política ou não. Se o usuário não tiver permissão para usar a credencial pela política, ele receberá um erro após a conclusão do fluxo de autenticação. 

O administrador pode optar por permitir que o usuário use a entrada por telefone sem senha ou o usuário deve remover o método. Se o usuário não tiver mais o dispositivo registrado, ele poderá ir para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e removê-lo. Se eles ainda estiverem usando o autenticador para MFA, eles poderão escolher **desabilitar a entrada pelo telefone** no Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integração do AD FS

Quando um usuário habilitou a credencial Microsoft Authenticator com senha, a autenticação para esse usuário sempre usará como padrão enviar uma notificação para aprovação. Essa lógica impede que os usuários em um locatário híbrido sejam direcionados ao ADFS para verificação de entrada sem que o usuário execute uma etapa adicional para clicar em "usar sua senha". Esse processo também ignorará quaisquer políticas de acesso condicional local e fluxos de autenticação de passagem. 

Se um usuário tiver uma verificação de entrada de telefone sem senha não respondida pendente e tentar entrar novamente, o usuário poderá ser levado ao ADFS para inserir uma senha.  

### <a name="azure-mfa-server"></a>Servidor do Azure MFA

Os usuários finais que estão habilitados para MFA por meio do servidor Azure MFA local de uma organização ainda podem criar e usar uma única credencial de entrada de telefone sem senha. Se o usuário tentar atualizar várias instalações (5 +) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.  

### <a name="device-registration"></a>Registo de dispositivo

Um dos pré-requisitos para criar essa nova credencial forte é que o dispositivo, em que o aplicativo Microsoft Authenticator está instalado, também deve ser registrado no locatário do Azure AD para um usuário individual. Devido às restrições de registro do dispositivo atual, um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada pelo telefone.

> [!NOTE]
> O registro do dispositivo não é o mesmo que o gerenciamento de dispositivos ou "MDM". Ele só associa uma ID de dispositivo e uma ID de usuário ao diretório do Azure AD.  

## <a name="next-steps"></a>Passos seguintes

[O que não tem senha?](concept-authentication-passwordless.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
