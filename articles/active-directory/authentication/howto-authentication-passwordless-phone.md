---
title: Iniciar sing-in sem palavras-passe com a app Microsoft Authenticator - Azure Ative Directory
description: Ativar o acesso sem palavras-passe ao Azure AD utilizando a aplicação Microsoft Authenticator (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5022dfc5811f3db59774a46a7034ff14c4dcf4a8
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428029"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Ativar o sing-in sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)

A aplicação Microsoft Authenticator pode ser usada para iniciar serção em qualquer conta AD Azure sem usar uma palavra-passe. À semelhança da tecnologia do [Windows Hello for Business,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)o Microsoft Authenticator utiliza a autenticação baseada em chaves para permitir uma credencial do utilizador que está ligada a um dispositivo e utiliza um biométrico ou PIN. Este método de autenticação pode ser utilizado em qualquer plataforma do dispositivo, incluindo mobile, e com qualquer app ou website que se integre com bibliotecas de autenticação da Microsoft. 

![Exemplo de um sinal de inscrição no navegador pedindo ao utilizador que aprove o sign-in](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver uma solicitação para uma palavra-passe depois de introduzir um nome de utilizador, uma pessoa que tenha ativado o acesso ao telefone a partir da aplicação Microsoft Authenticator verá uma mensagem a dizer-lhes para tocarem num número na sua aplicação. Na aplicação, o utilizador deve corresponder ao número, escolher Aprovar, depois fornecer o seu PIN ou biométrico, então a autenticação ficará completa.

> [!NOTE]
> Esta capacidade encontra-se na aplicação microsoft Authenticator desde março de 2017, pelo que existe a possibilidade de, quando a política estiver ativada para um diretório, os utilizadores encontrarem este fluxo imediatamente, e ver uma mensagem de erro se não tiverem sido ativadas pela política. Esteja atento e prepare os seus utilizadores para esta mudança.

## <a name="prerequisites"></a>Pré-requisitos

- Autenticação multi-factor Azure, com notificações push permitidas como método de verificação 
- A versão mais recente do Microsoft Authenticator instalada em dispositivos com o iOS 8.0 ou superior, ou Android 6.0 ou superior.

> [!NOTE]
> Se ativou a pré-visualização de inscrição sem palavras-passe da aplicação Microsoft Authenticator anterior utilizando o Azure AD PowerShell, foi ativado para todo o seu diretório. Se ativar a utilização deste novo método, irá sobreceder na política PowerShell. Recomendamos que seja ativado para todos os utilizadores do seu inquilino através dos novos Métodos de Autenticação, caso contrário os utilizadores que não constam da nova política deixarão de poder iniciar sessão sem palavras-passe. 

## <a name="enable-passwordless-authentication-methods"></a>Ativar métodos de autenticação sem palavras-passe

### <a name="enable-the-combined-registration-experience"></a>Permitir a experiência de registo combinado

As funcionalidades de inscrição para métodos de autenticação sem palavras-passe dependem da função de registo combinado. Siga as etapas do artigo Habilitar o [registo combinado de informações de segurança,](howto-registration-mfa-sspr-combined.md)para permitir o registo combinado.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Ativar métodos de autenticação de registo de telefone sem palavras-passe

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta **de administração global.**
1. Procure e selecione *Azure Active Directory*. Selecione **Security**  >  **métodos de autenticação de segurança**  >  **Política do método de autenticação (visualização)**
1. No **registo telefónico sem palavra-passe,** escolha as seguintes opções
   1. **Ativar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores selecionados
1. **Poupe** para definir a nova política

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registo e gestão do utilizador da aplicação Microsoft Authenticator

1. Navegue para[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Inscreva-se se não já não
1. Adicione uma aplicação autenticadora clicando no **método Adicionar,** escolhendo a **aplicação Authenticator**e clicando **em Adicionar**
1. Siga as instruções para instalar e configurar a aplicação Microsoft Authenticator no seu dispositivo
1. Clique **em Fazer** para completar o fluxo de configuração da aplicação Do Autenticador MFA. 
1. No **Microsoft Authenticator**, escolha **Ativar** o registo do telefone a partir do menu suspenso da conta
1. Siga as instruções na aplicação para terminar o registo para o início de sing-in telefónico sem palavra-passe. 

As organizações podem apontar os seus utilizadores para o artigo [Iniciar sing com o seu telefone, não a sua palavra-passe](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para posterior assistência configurando na aplicação Microsoft Authenticator e ativando o acesso ao telefone. Para aplicar estas definições, poderá ter de fazer login e voltar a entrar no arrendatário. 

## <a name="sign-in-with-passwordless-credential"></a>Iniciar sessão com credencial sem palavra-passe

Para visualização pública, não há forma de impor aos utilizadores a criação ou utilização desta nova credencial. Um utilizador só encontrará o sign-in sem palavra-passe uma vez que um administrador tenha ativado o seu inquilino **e** o utilizador tenha atualizado a sua aplicação Microsoft Authenticator para ativar o acesso ao telefone.

Depois de digitar o seu nome de utilizador na web e selecionar **Next**, os utilizadores são apresentados com um número e são solicitados na sua aplicação Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar a sua palavra-passe. 

![Exemplo de um sinal de inscrição no navegador utilizando a app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>O utilizador não está ativado por política, mas ainda tem o método de acesso telefónico sem palavras-passe no Microsoft Authenticator

É possível que um utilizador tenha criado, em algum momento, uma credencial de acesso ao telefone sem palavras-passe na sua atual aplicação Microsoft Authenticator ou num dispositivo anterior. Uma vez que um administrador permita a política do método de autenticação para a entrada de telefone sem palavra-passe, qualquer utilizador com uma credencial registada, começará a experimentar o novo pedido de entrada, independentemente de terem sido ou não habilitados a utilizar a apólice. Se o utilizador não tiver sido autorizado a utilizar a credencial por política, verá um erro após completar o fluxo de autenticação. 

O administrador pode optar por permitir que o utilizador utilize o acesso ao telefone sem palavras-passe, ou o utilizador deve remover o método. Se o utilizador deixar de ter o dispositivo registado, pode ir [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) buscá-lo e removê-lo. Se ainda estiverem a utilizar o Autenticador para MFA, podem escolher **iniciar sôms desativação** do telefone dentro do Autenticador Microsoft.  

### <a name="ad-fs-integration"></a>Integração AD FS

Quando um utilizador tiver ativado a credencial sem palavras-passe do Microsoft Authenticator, a autenticação para esse utilizador será sempre por defeito ao envio de uma notificação para aprovação. Esta lógica impede que os utilizadores de um inquilino híbrido sejam direcionados para AD FS para verificação de entrada sem que o utilizador tome um passo adicional para clicar em "Use a sua palavra-passe em vez disso". Este processo também irá contornar quaisquer políticas de acesso condicional no local e fluxos de autenticação pass-through. 

Se um utilizador tiver uma verificação de inscrição sem palavra-passe sem resposta pendente e tentar iniciar novamente o s.  

### <a name="azure-mfa-server"></a>Servidor Azure MFA

Os utilizadores finais que estão habilitados para MFA através do servidor Azure MFA de uma organização ainda podem criar e usar um único sinal de telefone sem palavra-passe na credencial. Se o utilizador tentar atualizar várias instalações (5+) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.  

### <a name="device-registration"></a>Registo de dispositivo

Um dos pré-requisitos para criar esta nova credencial forte, é que o dispositivo, onde a aplicação Microsoft Authenticator está instalada, também deve ser registado dentro do inquilino AZure AD para um utilizador individual. Devido às atuais restrições de registo do dispositivo, um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola na aplicação Microsoft Authenticator pode ser ativada para o início de sôm.

### <a name="intune-mobile-application-management"></a>Gestão de aplicações móveis do Intune 

Os utilizadores finais que estão sujeitos a uma política que requer gestão de aplicações móveis (MAM) não podem registar a credencial sem palavra-passe na aplicação Microsoft Authenticator. 

> [!NOTE]
> O registo do dispositivo não é o mesmo que a gestão do dispositivo ou "MDM". Apenas associa um ID do dispositivo e um ID do utilizador no diretório AD Azure.  

## <a name="next-steps"></a>Próximos passos

[Saiba como funciona a autenticação sem palavras-passe](concept-authentication-passwordless.md)

[Saiba mais sobre o registo do dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre a autenticação multi-factor Azure](../authentication/howto-mfa-getstarted.md)
