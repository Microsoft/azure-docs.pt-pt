---
title: Iniciar sing-in sem palavras-passe com a app Microsoft Authenticator - Azure Ative Directory
description: Ativar o acesso sem palavras-passe ao Azure AD utilizando a aplicação Microsoft Authenticator (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964830"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Ativar o sing-in sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)

A aplicação Microsoft Authenticator pode ser usada para iniciar serção em qualquer conta AD Azure sem usar uma palavra-passe. À semelhança da tecnologia do [Windows Hello for Business,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)o Microsoft Authenticator utiliza a autenticação baseada em chaves para permitir uma credencial do utilizador que está ligada a um dispositivo e utiliza um biométrico ou PIN. Este método de autenticação pode ser utilizado em qualquer plataforma do dispositivo, incluindo mobile, e com qualquer app ou website que se integre com as Bibliotecas de Autenticação do Microsoft.

![Exemplo de um sinal de inscrição no navegador pedindo ao utilizador que aprove o sign-in](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver uma solicitação para uma palavra-passe depois de introduzir um nome de utilizador, uma pessoa que tenha ativado o acesso telefónico a partir da aplicação Microsoft Authenticator vê uma mensagem que lhes pede para tocar num número na sua aplicação. Para completar o processo de inscrição na aplicação, o utilizador deve corresponder ao número, escolher **Aprovar,** em seguida, fornecer o seu PIN ou biométrico.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o acesso telefónico sem palavras-passe com a aplicação Microsoft Authenticator, devem ser cumpridos os seguintes pré-requisitos:

- Autenticação multi-factor Azure, com notificações push permitidas como método de verificação.
- A versão mais recente do Microsoft Authenticator instalada em dispositivos com o iOS 8.0 ou superior, ou Android 6.0 ou superior.

> [!NOTE]
> Se ativou a pré-visualização de entrada sem palavras-passe da aplicação Microsoft Authenticator utilizando o Azure AD PowerShell, foi ativado para todo o seu diretório. Se ativar a utilização deste novo método, substitui a política PowerShell. Recomendamos que ative todos os utilizadores do seu inquilino através do novo menu *Métodos de Autenticação,* caso contrário os utilizadores que não constam da nova política já não possam iniciar sação sem senha.

## <a name="enable-passwordless-authentication-methods"></a>Ativar métodos de autenticação sem palavras-passe

Para utilizar a autenticação sem palavras-passe em Azure AD, primeiro ative a experiência de registo combinado e, em seguida, capacite os utilizadores para o método menos senha.

### <a name="enable-the-combined-registration-experience"></a>Permitir a experiência de registo combinado

As funcionalidades de inscrição para métodos de autenticação sem palavras-passe dependem da função de registo combinado. Para permitir que os utilizadores completem o registo combinado, siga as etapas para permitir o [registo combinado de informações de segurança.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Ativar métodos de autenticação de registo de telefone sem palavras-passe

O Azure AD permite-lhe escolher quais os métodos de autenticação que podem ser utilizados durante o processo de inscrição. Os utilizadores registam-se então para os métodos que gostariam de utilizar.

Para ativar o método de autenticação para a entrada de telefone sem palavras-passe, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta *de administrador global.*
1. Procure e selecione *O Diretório Ativo Azure,* em seguida, navegue para métodos de autenticação de **segurança**Política  >  **Authentication methods**  >  **de autenticação (Pré-visualização)**
1. No **registo telefónico sem palavra-passe,** escolha as seguintes opções:
   1. **Ativar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores selecionados
1. Para aplicar a nova política, **selecione Save**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registo e gestão do utilizador da aplicação Microsoft Authenticator

Com o método de autenticação sem palavra-passe disponível para utilização no Azure AD, os utilizadores devem agora registar-se para o método de autenticação sem palavras-passe utilizando os seguintes passos:

1. Navegue para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Iniciar sing e, em seguida, adicionar a aplicação Authenticator selecionando **o método Adicionar > app Authenticator,** em seguida, **Adicionar**.
1. Siga as instruções para instalar e configurar a aplicação Microsoft Authenticator no seu dispositivo.
1. Selecione **Feito** para completar a configuração do Autenticador.
1. Na aplicação **Microsoft Authenticator,** escolha **Ativar** o registo do telefone a partir do menu suspenso para a conta registada.
1. Siga as instruções na aplicação para terminar de registar a conta para o início de singing do telefone sem palavra-passe.

As organizações podem direcionar os seus utilizadores para [iniciar sôr-se com o seu telemóvel, não a sua palavra-passe](../user-help/user-help-auth-app-sign-in.md) para posterior assistência configurar a aplicação Microsoft Authenticator e permitir o acesso ao telefone.

> [!NOTE]
> Os utilizadores que não estejam autorizados pela política a utilizar o acesso ao telefone já não conseguem ativar o mesmo dentro da aplicação Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Iniciar sessão com credencial sem palavra-passe

Um utilizador pode começar a utilizar o início de sing in sem palavras-passe uma vez que um administrador tenha ativado o seu inquilino e o utilizador atualizou a sua aplicação Microsoft Authenticator para ativar o início de sing do telefone.

Para começar a utilizar o início de singing do telefone, depois de escrever um nome de utilizador na página de início de s início e selecionar **Next**, os utilizadores poderão ter de selecionar **Outras formas de iniciar sing, em** **seguida, aprovar um pedido na minha aplicação Microsoft Authenticator**. Os utilizadores são então apresentados com um número e são solicitados na sua aplicação Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar a sua palavra-passe. Uma vez que os utilizadores tenham usado o início de singing do telefone sem palavras-passe, são solicitados a usá-lo novamente, até que eles usem escolher outro método.

![Exemplo de um sinal de inscrição no navegador utilizando a app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas Conhecidos

As seguintes questões conhecidas existem na experiência atual de pré-visualização.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Não ver opção para o acesso telefónico sem palavra-passe

Se um utilizador tiver uma verificação de inscrição sem palavras-passe sem resposta pendente e tentar iniciar novamente o seu registo, o utilizador só poderá ver uma opção para introduzir uma palavra-passe. Abra o Autenticador microsoft e responda a quaisquer pedidos de notificação para continuar a utilizar o acesso ao telefone sem palavras-passe.

### <a name="federated-accounts"></a>Contas Federadas

Quando um utilizador tiver ativado qualquer credencial sem palavras-passe, os logins AD Azure deixarão de utilizar login_hint para acelerar o utilizador para um local de login federado. Esta lógica impede que os utilizadores de um inquilino híbrido sejam direcionados para AD FS para verificação de entrada sem que o utilizador tome um passo adicional para clicar em "Use a sua palavra-passe em vez disso".

### <a name="azure-mfa-server"></a>Servidor Azure MFA

Os utilizadores finais que estão habilitados para MFA através do servidor Azure MFA de uma organização ainda podem criar e usar uma única credencial de acesso telefónico sem palavras-passe. Se o utilizador tentar atualizar várias instalações (5+) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.  

### <a name="device-registration"></a>Registo de dispositivo

Um dos pré-requisitos para criar esta nova credencial forte, é que o dispositivo, onde a aplicação Microsoft Authenticator está instalada, também deve ser registado dentro do inquilino AZure AD para um utilizador individual. Devido às atuais restrições de registo do dispositivo, um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola na aplicação Microsoft Authenticator pode ser ativada para o início de sôm.

> [!NOTE]
> O registo do dispositivo não é o mesmo que a gestão do dispositivo ou "MDM". Apenas associa um ID do dispositivo e um ID do utilizador no diretório AD Azure.  

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autenticação AD AD e métodos sem palavras-passe, consulte os seguintes artigos:

* [Saiba como funciona a autenticação sem palavras-passe](concept-authentication-passwordless.md)
* [Saiba mais sobre o registo do dispositivo](../devices/overview.md#getting-devices-in-azure-ad)
* [Saiba mais sobre a autenticação multi-factor Azure](../authentication/howto-mfa-getstarted.md)
