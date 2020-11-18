---
title: Iniciar sing-in sem palavras-passe com a app Microsoft Authenticator - Azure Ative Directory
description: Ativar o acesso sem palavras-passe ao Azure AD utilizando a aplicação Microsoft Authenticator (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0c00bd8498e3f43d5f8258308fbc010a6a274
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839527"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Ativar o sing-in sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)

A aplicação Microsoft Authenticator pode ser usada para iniciar serção em qualquer conta AD Azure sem usar uma palavra-passe. O Microsoft Authenticator utiliza a autenticação baseada em chaves para ativar uma credencial do utilizador que está ligada a um dispositivo, onde o dispositivo utiliza um PIN ou um biométrico. [O Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) utiliza uma tecnologia semelhante.

Esta tecnologia de autenticação pode ser utilizada em qualquer plataforma do dispositivo, incluindo mobile. Esta tecnologia também pode ser usada com qualquer app ou website que se integre com as Bibliotecas de Autenticação do Microsoft.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Exemplo de um sinal de inscrição no navegador pedindo ao utilizador que aprove a inscrição.":::

As pessoas que ativaram o registo telefónico a partir da aplicação Microsoft Authenticator vêem uma mensagem que lhes pede para tocarem num número na sua aplicação. Não é pedido nome de utilizador ou palavra-passe. Para completar o processo de inscrição na aplicação, o utilizador deve seguir tomar as seguintes ações:

1. Corresponde ao número.
2. Selecione **Aprovar**.
3. Forneça o pin ou biométrico.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o acesso telefónico sem palavras-passe com a aplicação Microsoft Authenticator, devem ser cumpridos os seguintes pré-requisitos:

- Autenticação multi-factor Azure AD, com notificações push permitidas como método de verificação.
- A versão mais recente do Microsoft Authenticator instalada em dispositivos com o iOS 8.0 ou superior, ou Android 6.0 ou superior.

> [!NOTE]
> Se ativou a pré-visualização de entrada sem palavras-passe do Microsoft Authenticator utilizando o Azure AD PowerShell, foi ativado para todo o seu diretório. Se ativar a utilização deste novo método, substitui a política PowerShell. Recomendamos que ative todos os utilizadores do seu inquilino através do novo menu *Métodos de Autenticação,* caso contrário os utilizadores que não constam da nova política já não possam iniciar sação sem senha.

## <a name="enable-passwordless-authentication-methods"></a>Ativar métodos de autenticação sem palavras-passe

Para utilizar a autenticação sem palavras-passe em Azure AD, primeiro ative a experiência de registo combinado e, em seguida, capacite os utilizadores para o método menos senha.

### <a name="enable-the-combined-registration-experience"></a>Permitir a experiência de registo combinado

As funcionalidades de inscrição para métodos de autenticação sem palavras-passe dependem da função de registo combinado. Para permitir que os utilizadores completem o registo combinado, siga as etapas para permitir o [registo combinado de informações de segurança.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Ativar métodos de autenticação de registo de telefone sem palavras-passe

O Azure AD permite-lhe escolher quais os métodos de autenticação que podem ser utilizados durante o processo de inscrição. Os utilizadores registam-se então para os métodos que gostariam de utilizar.

Para ativar o método de autenticação para a entrada de telefone sem palavras-passe, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta *de administrador global.*
1. Procure e selecione *O Diretório Ativo Azure,* em seguida, navegue para métodos de autenticação de **segurança** Política  >  **Authentication methods**  >  **de autenticação (Pré-visualização)**
1. No **registo telefónico sem palavra-passe,** escolha as seguintes opções:
   1. **Ativar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores selecionados
1. Para aplicar a nova política, **selecione Save**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Registo e gestão do Microsoft Authenticator

Os utilizadores registam-se para o método de autenticação sem palavras-passe do Azure AD utilizando os seguintes passos:

1. Navegue para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Iniciar sing e, em seguida, adicionar a aplicação Authenticator selecionando **o método Adicionar > app Authenticator,** em seguida, **Adicionar**.
1. Siga as instruções para instalar e configurar a aplicação Microsoft Authenticator no seu dispositivo.
1. Selecione **Feito** para completar a configuração do Autenticador.
1. No **Microsoft Authenticator,** escolha **Ativar** o registo telefónico do menu suspenso para a conta registada.
1. Siga as instruções na aplicação para terminar de registar a conta para o início de singing do telefone sem palavra-passe.

Uma organização pode direcionar os seus utilizadores a iniciar sôr entrada com os seus telemóveis, sem usar uma senha. Para obter mais assistência que configura a aplicação Microsoft Authenticator e habilitar o acesso ao telefone, consulte [iniciar sôm nas suas contas utilizando a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Os utilizadores que não estejam autorizados pela política a utilizar o acesso ao telefone já não conseguem ativar o mesmo dentro da aplicação Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Iniciar sessão com credencial sem palavra-passe

Um utilizador pode começar a utilizar o início de singing sem palavras-passe depois de todas as seguintes ações estarem concluídas:

- Um administrador permitiu o inquilino do utilizador.
- O utilizador atualizou a sua aplicação Microsoft Authenticator para permitir o sôm-in do telefone.

A primeira vez que um utilizador inicia o processo de início de sação do telefone, o utilizador executa os seguintes passos:

1. Insira o nome dela na página de inscrição.
2. Seleciona **Seguinte**.
3. Se necessário, seleciona **Outras formas de iniciar sinsuposições**.
4. **Selecione Aprovar um pedido na minha aplicação Microsoft Authenticator**.

O utilizador é então apresentado com um número. A aplicação solicita ao utilizador que autentica selecione o número apropriado, em vez de introduzir uma palavra-passe.

Depois de o utilizador ter utilizado o início de surgiu no registo do telefone sem palavras-passe, a aplicação continua a orientar o utilizador através deste método. No entanto, o utilizador verá a opção de escolher outro método.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Exemplo de um teste de inscrição no navegador utilizando a aplicação Microsoft Authenticator.":::

## <a name="known-issues"></a>Problemas Conhecidos

As seguintes questões conhecidas existem na experiência atual de pré-visualização.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Não ver opção para o acesso telefónico sem palavra-passe

Em um cenário, um utilizador pode ter uma verificação de acesso telefónico sem palavras-passe sem resposta que está pendente. No entanto, o utilizador pode tentar entrar novamente. Quando isso acontece, o utilizador pode ver apenas a opção de introduzir uma palavra-passe.

Para resolver este cenário, podem ser utilizados os seguintes passos:

1. Abra a aplicação Microsoft Authenticator.
2. Responda a quaisquer pedidos de notificação.

Em seguida, o utilizador pode continuar a utilizar o registo telefónico sem palavras-passe.

### <a name="federated-accounts"></a>Contas Federadas

Quando um utilizador ativou qualquer credencial sem palavras-passe, o processo de login Azure AD deixa de utilizar a sugestão de \_ login. Portanto, o processo já não acelera o utilizador em direção a um local de login federado.

Esta lógica geralmente impede que um utilizador num inquilino híbrido seja direcionado para os Serviços Federados de Diretório Ativo (AD FS) para verificação de entrada. No entanto, o utilizador mantém a opção de clicar em utilizar a **sua palavra-passe.**

### <a name="azure-mfa-server"></a>Servidor Azure MFA

Um utilizador final pode ser ativado para a autenticação de vários fatores (MFA), através de um servidor Azure MFA no local. O utilizador ainda pode criar e utilizar uma única credencial de acesso telefónico sem palavras-passe.

Se o utilizador tentar atualizar várias instalações (5+) da aplicação Microsoft Authenticator com a credencial de acesso ao telefone sem palavras-passe, esta alteração pode resultar num erro.

### <a name="device-registration"></a>Registo de dispositivo

Antes de criar esta nova credencial forte, há pré-requisitos. Um dos pré-requisitos é que o dispositivo em que a aplicação Microsoft Authenticator está instalada deve ser registado dentro do inquilino AZure AD para um utilizador individual.

Atualmente, um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola na aplicação Microsoft Authenticator pode ser ativada para o início de sôm.

> [!NOTE]
> O registo do dispositivo não é o mesmo que a gestão do dispositivo ou a gestão de dispositivos móveis (MDM). O registo do dispositivo apenas associa um ID do dispositivo e um ID do utilizador, no diretório AD Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autenticação AD AD e métodos sem palavras-passe, consulte os seguintes artigos:

- [Saiba como funciona a autenticação sem palavras-passe](concept-authentication-passwordless.md)
- [Saiba mais sobre o registo do dispositivo](../devices/overview.md#getting-devices-in-azure-ad)
- [Saiba mais sobre a autenticação multi-factor Azure AD](../authentication/howto-mfa-getstarted.md)
