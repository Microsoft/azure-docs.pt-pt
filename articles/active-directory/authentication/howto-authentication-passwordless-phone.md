---
title: Sem palavras-passe com a aplicação Microsoft Authenticator - Diretório Ativo Azure
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
ms.openlocfilehash: 853e7143834a340b870b71ef1a287dab136e2783
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654059"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Ativar o acesso sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)

A aplicação Microsoft Authenticator pode ser usada para iniciar sessão em qualquer conta DaD Azure sem utilizar uma palavra-passe. À semelhança da tecnologia do [Windows Hello for Business,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)o Microsoft Authenticator utiliza a autenticação baseada em chaves para permitir uma credencial do utilizador que está ligada a um dispositivo e utiliza um biométrico ou PIN. Este método de autenticação pode ser utilizado em qualquer plataforma de dispositivos, incluindo móvel, e com qualquer aplicação ou website que se integre com bibliotecas de autenticação da Microsoft. 

![Exemplo de um sessão de navegador pedindo ao utilizador para aprovar o sessão](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um pedido de senha depois de introduzir um nome de utilizador, uma pessoa que tenha ativado o registo telefónico a partir da aplicação Microsoft Authenticator verá uma mensagem a dizer-lhes para tocarem num número na sua aplicação. Na aplicação, o utilizador deve corresponder ao número, escolher Aprovar, em seguida, fornecer o seu PIN ou biométrico, então a autenticação estará completa.

> [!NOTE]
> Esta capacidade está na aplicação Microsoft Authenticator desde março de 2017, pelo que existe a possibilidade de, quando a política estiver ativada para um diretório, os utilizadores encontrarem este fluxo imediatamente, e verem uma mensagem de erro se não tiverem sido ativadas pela política. Esteja atento e prepare os seus utilizadores para esta alteração.

## <a name="prerequisites"></a>Pré-requisitos

- Autenticação Azure Multi-Factor, com notificações push permitidas como um método de verificação 
- Versão mais recente do Microsoft Authenticator instalado em dispositivos com iOS 8.0 ou maior, ou Android 6.0 ou maior.

> [!NOTE]
> Se ativou a pré-visualização sem palavras-passe da aplicação Microsoft Authenticator utilizando o Azure AD PowerShell, foi ativado para todo o seu diretório. Se permitir utilizar este novo método, irá substituir a política PowerShell. Recomendamos que permita a todos os utilizadores do seu inquilino através dos novos Métodos de Autenticação, caso contrário os utilizadores que não estejam na nova política deixarão de poder fazer login sem palavras-de-semana. 

## <a name="enable-passwordless-authentication-methods"></a>Ativar métodos de autenticação sem palavras-passe

### <a name="enable-the-combined-registration-experience"></a>Ativar a experiência de registo combinado

As funcionalidades de registo dos métodos de autenticação sem palavras-passe baseiam-se na pré-visualização combinada do registo. Siga os passos do artigo Enable registo combinado de informações de [segurança (pré-visualização)](howto-registration-mfa-sspr-combined.md), para permitir a pré-visualização do registo combinado.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Ativar métodos de autenticação de acesso telefónico sem palavras-passe

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Procure e selecione *Azure Active Directory*. Selecione**métodos** > de autenticação de **segurança** > Política do método de**autenticação (Pré-visualização)**
1. Em **'Sessão de acesso ao telefone Passwordless',** escolha as seguintes opções
   1. **Habilitar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores Selecionados
1. **Poupe** para definir a nova política

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registo e gestão de utilizadores da aplicação Microsoft Authenticator

1. Navegue para[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Inscreva-se se ainda não
1. Adicione uma aplicação autenticadorclicando o **método Adicionar,** escolhendo a **aplicação Authenticator,** e clicando em **Adicionar**
1. Siga as instruções para instalar e configurar a aplicação Autenticadora Microsoft no seu dispositivo
1. Clique em **Feito** para completar o fluxo de configuração da aplicação Authenticator MFA. 
1. No **Microsoft Authenticator,** escolha ativar o **registo do telefone** no menu de entrega da conta
1. Siga as instruções na aplicação para terminar a inscrição para o acesso ao telefone sem palavras-passe. 

As organizações podem apontar os seus utilizadores para o artigo [Iniciar sessão com o seu telemóvel, e não a sua palavra-passe](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para mais assistência na aplicação Microsoft Authenticator e ativar o registo do telefone. Para aplicar estas definições, poderá ter de iniciar sessão e voltar a entrar no inquilino. 

## <a name="sign-in-with-passwordless-credential"></a>Inscreva-se com credenciais sem palavras-passe

Para pré-visualização pública, não há forma de impor aos utilizadores a criação ou utilização desta nova credencial. Um utilizador só encontrará um registo sem palavras-passe uma vez que um administrador tenha ativado o seu inquilino **e** o utilizador atualizou a sua aplicação Microsoft Authenticator para ativar o registo do telefone.

Depois de digitar o seu nome de utilizador na web e selecionar **Seguinte,** os utilizadores são apresentados com um número e são solicitados na sua aplicação Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar a sua palavra-passe. 

![Exemplo de um sessão de navegador usando a aplicação Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>O utilizador não está ativado pela política, mas ainda tem um método de acesso ao telefone sem palavras-passe no Microsoft Authenticator

É possível que um utilizador tenha criado, a dada altura, uma credencial de entrada de telefone sem palavras-passe na sua aplicação atual do Microsoft Authenticator ou num dispositivo anterior. Uma vez que um administrador permita a política do método de autenticação para o registo telefónico sem palavras, qualquer utilizador com uma credencial registada, começará a experimentar a nova solicitação de entrada, independentemente de terem sido ou não habilitadas a utilizar a apólice. Se o utilizador não tiver sido autorizado a utilizar a credencial por política, verá um erro após completar o fluxo de autenticação. 

O administrador pode optar por ativar o utilizador a utilizar um sinal de acesso ao telefone sem palavras-passe, ou o utilizador deve remover o método. Se o utilizador deixar de ter o [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) dispositivo registado, pode ir e removê-lo. Se ainda estiverem a utilizar o Autenticador para MFA, podem escolher **desativar** o registo do telefone dentro do Autenticador Microsoft.  

### <a name="ad-fs-integration"></a>Integração AD FS

Quando um utilizador tiver ativado a credencial sem palavras-passe do Microsoft Autenticador, a autenticação para esse utilizador irá sempre descontar no envio de uma notificação para aprovação. Esta lógica impede que os utilizadores de um inquilino híbrido sejam direcionados para a ADFS para verificação de sessão sem que o utilizador tenha dado um passo adicional para clicar em "Use a sua palavra-passe em vez disso". Este processo também irá contornar quaisquer políticas de Acesso Condicional no local e fluxos de autenticação pass-through. 

Se um utilizador tiver uma verificação de acesso de telefone sem palavras-passe sem resposta pendente e tentar iniciar sessão novamente, o utilizador poderá ser levado à ADFS para introduzir uma palavra-passe.  

### <a name="azure-mfa-server"></a>Servidor Azure MFA

Os utilizadores finais que estão habilitados para o MFA através do servidor Azure MFA de uma organização ainda podem criar e usar um único sinal de telefone sem palavras-passe na credencial. Se o utilizador tentar atualizar várias instalações (5+) do Autenticador Microsoft com a credencial, esta alteração pode resultar num erro.  

### <a name="device-registration"></a>Registo de dispositivo

Um dos pré-requisitos para criar esta nova credencial forte, é que o dispositivo, onde a aplicação Microsoft Authenticator está instalada, também deve ser registado dentro do inquilino da AD Azure a um utilizador individual. Devido às restrições de registo do dispositivo, um dispositivo só pode ser registado num único inquilino. Este limite significa que apenas uma conta de trabalho ou escola na aplicação Microsoft Authenticator pode ser ativada para o início de sessão telefónica.

### <a name="intune-mobile-application-management"></a>Gestão de aplicações móveis do Intune 

Os utilizadores finais que estejam sujeitos a uma política que exija a gestão de aplicações móveis (MAM) não podem registar a credencial sem palavras-passe na aplicação Microsoft Authenticator. 

> [!NOTE]
> O registo do dispositivo não é o mesmo que a gestão do dispositivo ou "MDM". Apenas associa um ID do dispositivo e um ID do utilizador juntos no diretório Azure AD.  

## <a name="next-steps"></a>Passos seguintes

[O que é o modo sem palavra-passe?](concept-authentication-passwordless.md)

[Conheça o registo do dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Conheça a autenticação de multi-factores azure](../authentication/howto-mfa-getstarted.md)
