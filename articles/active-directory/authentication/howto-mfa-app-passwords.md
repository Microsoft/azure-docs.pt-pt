---
title: Configurar palavras-passe de aplicativos para autenticação multi-factor Azure AD - Diretório Ativo Azure
description: Saiba como configurar e utilizar palavras-passe de aplicativos para aplicações antigas na Autenticação Multi-Factor AD Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743110"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Ativar e utilizar autenticação multi-factor Azure AD com aplicações antigas usando senhas de aplicação

Algumas aplicações mais antigas, não-navegadores, como o Office 2010 ou mais cedo e o Apple Mail antes do iOS 11 não entendem pausas ou quebras no processo de autenticação. Se um utilizador estiver habilitado para autenticação multi-factor Azure AD e tentar utilizar uma destas aplicações mais antigas e não-navegadores, não conseguirá autenticar com sucesso. Para utilizar estas aplicações de forma segura com a autenticação multi-factor Azure AD ativada para contas de utilizador, pode utilizar as palavras-passe da aplicação. Estas palavras-passe da aplicação substituíram a sua senha tradicional para permitir que uma aplicação contornasse a autenticação de vários fatores e funcionasse corretamente.

A autenticação moderna é suportada para os clientes do Microsoft Office 2013 e mais tarde. Os clientes do Office 2013, incluindo o Outlook, suportam protocolos de autenticação modernos e podem ser habilitados a trabalhar com verificação em duas etapas. Após a ativação do cliente, as palavras-passe da aplicação não são necessárias para o cliente.

Este artigo mostra-lhe como ativar e usar senhas de aplicações para aplicações antigas que não suportam pedidos de autenticação de vários fatores.

>[!NOTE]
> As palavras-passe da aplicação não funcionam com políticas de autenticação de vários fatores baseadas no Acesso Condicional e na autenticação moderna.

## <a name="overview-and-considerations"></a>Visão geral e considerações

Quando uma conta de utilizador é ativada para autenticação multi-factor Azure AD, o pedido regular de inscrição é interrompido por um pedido de verificação adicional. Algumas aplicações mais antigas não entendem esta quebra no processo de inscrição, por isso a autenticação falha. Para manter a segurança da conta de utilizador e deixar a autenticação multi-factor Azure AD ativada, as palavras-passe da aplicação podem ser utilizadas em vez do nome de utilizador e palavra-passe regulares do utilizador. Quando uma palavra-passe de aplicação usada durante o sent-in, não há nenhuma solicitação de verificação adicional, por isso a autenticação é bem sucedida.

As palavras-passe da aplicação são geradas automaticamente, não especificadas pelo utilizador. Esta palavra-passe gerada automaticamente dificulta a adivinhação de um intruso, por isso é mais seguro. Os utilizadores não têm de acompanhar as palavras-passe ou introduzi-las sempre que as palavras-passe da aplicação são inseridas apenas uma vez por aplicação.

Quando utiliza palavras-passe de aplicações, aplicam-se as seguintes considerações:

* Há um limite de 40 palavras-passe de aplicações por utilizador.
* As aplicações que cache palavras-passe e as usam em cenários no local podem falhar porque a palavra-passe da aplicação não é conhecida fora do trabalho ou da conta escolar. Um exemplo deste cenário são os e-mails da Exchange que estão no local, mas o correio arquivado está na nuvem. Neste cenário, a mesma palavra-passe não funciona.
* Depois de a Azure AD Multi-Factor Authentication ser ativada na conta de um utilizador, as palavras-passe da aplicação podem ser usadas com a maioria dos clientes não-navegador, como o Outlook e o Microsoft Skype para negócios. No entanto, as ações administrativas não podem ser realizadas utilizando senhas de aplicação através de aplicações não-navegador, como o Windows PowerShell. As ações não podem ser executadas mesmo quando o utilizador tem uma conta administrativa.
    * Para executar scripts PowerShell, crie uma conta de serviço com uma senha forte e não ative a conta para verificação em duas etapas.
* Se suspeitar que uma conta de utilizador está comprometida e revogar/redefinir a palavra-passe da conta, as palavras-passe da aplicação também devem ser atualizadas. As palavras-passe da aplicação não são revogadas automaticamente quando uma palavra-passe da conta de utilizador é revogada/reiniciada. O utilizador deve eliminar as palavras-passe de aplicações existentes e criar novas.
   * Para obter mais informações, consulte [Criar e eliminar palavras-passe de aplicações da página adicional de verificação de segurança.](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

>[!WARNING]
> As palavras-passe das aplicações não funcionam em ambientes híbridos onde os clientes comunicam tanto no local como com pontos finais de descoberta automática em nuvem. As palavras-passe de domínio são necessárias para autenticar no local. As palavras-passe da aplicação são necessárias para autenticar com a nuvem.

### <a name="app-password-names"></a>Nomes de senha de aplicação

Os nomes da palavra-passe da aplicação devem refletir o dispositivo em que são utilizados. Se tiver um portátil que tenha aplicações não-navegador como Outlook, Word e Excel, crie uma palavra-passe de aplicação chamada **Laptop** para estas aplicações. Crie outra palavra-passe de aplicação chamada **Desktop** para as mesmas aplicações que executam no seu computador de secretária.

Recomenda-se criar uma palavra-passe de aplicação por dispositivo, em vez de uma palavra-passe de aplicação por aplicação.

## <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicação federadas ou únicas

A Azure AD suporta a federação, ou um único sign-on (SSO), com serviços de domínio de diretório ativo no local (DS AD). Se a sua organização for federada com Azure AD e estiver a utilizar a autenticação multi-factor Azure AD, aplicam-se as seguintes considerações de senha de aplicação:

>[!NOTE]
> Os seguintes pontos aplicam-se apenas aos clientes federados (SSO).

* As palavras-passe da aplicação são verificadas pela Azure AD e, portanto, pela federação de bypass. A Federação só é utilizada ativamente na configuração de senhas de aplicação.
* O Fornecedor de Identidade (IdP) não é contactado para utilizadores federados (SSO), ao contrário do fluxo passivo. As palavras-passe da aplicação são armazenadas no trabalho ou na conta escolar. Se um utilizador deixar a empresa, a informação do utilizador flui para o trabalho ou conta escolar utilizando o **DirSync** em tempo real. O desativamento/eliminação da conta pode demorar até três horas para sincronizar, o que pode atrasar o desativamento/eliminação da palavra-passe da aplicação em Azure AD.
* As definições de Controlo de Acesso do cliente no local não são honradas pela funcionalidade de palavras-passe da aplicação.
* Não existe qualquer capacidade de autenticação ou auditoria no local com a funcionalidade de palavras-passe da aplicação.

Algumas arquiteturas avançadas requerem uma combinação de credenciais para a autenticação de vários fatores com os clientes. Estas credenciais podem incluir um nome de utilizador de conta de trabalho ou de uma conta escolar e senhas, e senhas de aplicação. Os requisitos dependem da forma como a autenticação é realizada. Para clientes que autenticam contra uma infraestrutura no local, um nome de utilizador de conta de trabalho ou de uma conta escolar é necessário. Para clientes que autenticam contra a Azure AD, é necessária uma senha de aplicação.

Por exemplo, suponha que tenha a seguinte arquitetura:

* O seu caso no local do Ative Directory é federado com Azure AD.
* Usas o Exchange online.
* Usas o Skype para negócios no local.
* Utiliza a autenticação multi-factor Azure AD.

Neste cenário, utiliza as seguintes credenciais:

* Para iniciar sôm no Skype for Business, use o nome de utilizador e a palavra-passe da sua conta de trabalho ou da sua conta escolar.
* Para aceder ao livro de endereços de um cliente do Outlook que se conecta ao Exchange online, utilize uma senha de aplicação.

## <a name="allow-users-to-create-app-passwords"></a>Permitir que os utilizadores criem senhas de aplicação

Por padrão, os utilizadores não podem criar senhas de aplicação. A funcionalidade de palavras-passe da aplicação deve ser ativada antes que os utilizadores possam usá-las. Para dar aos utilizadores a capacidade de criar palavras-passe de aplicações, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure e selecione **O Diretório Ativo Azure,** em seguida, escolha **Utilizadores**.
3. Selecione **a autenticação multi-factor** a partir da barra de navegação na parte superior da janela do *Utilizadores.*
4. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
5. Na página Definições de **Serviço,** selecione o **Permitir que os utilizadores criem senhas de aplicação para iniciar sessão na opção de aplicações não-navegador.**

    ![Screenshot do portal Azure que mostra as definições de serviço para autenticação de vários fatores para permitir ao utilizador de senhas de aplicação](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Quando desativa a capacidade de os utilizadores criarem senhas de aplicação, as palavras-passe existentes continuam a funcionar. No entanto, os utilizadores não conseguem gerir ou eliminar as palavras-passe existentes da aplicação uma vez que desativam esta capacidade.
>
> Quando desativa a capacidade de criar palavras-passe de aplicações, recomenda-se também [criar uma política de Acesso Condicional para desativar o uso da autenticação antiga.](../conditional-access/block-legacy-authentication.md) Esta abordagem impede que as palavras-passe existentes da aplicação funcionem e força o uso de métodos modernos de autenticação.

## <a name="create-an-app-password"></a>Criar uma senha de aplicação

Quando os utilizadores completam o seu registo inicial de Autenticação Multi-Factor AZure AD, existe a opção de criar senhas de aplicação no final do processo de registo.

Os utilizadores também podem criar senhas de aplicação após o registo. Para obter mais informações e passos detalhados para os seus utilizadores, veja [o que são as palavras-passe da aplicação na Autenticação Multi-Factor AD Azure?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como permitir que os utilizadores se registem rapidamente para autenticação multi-factor Azure AD, consulte [a visão geral do registo de informações](concept-registration-mfa-sspr-combined.md)de segurança combinadas .
