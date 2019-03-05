---
title: Configurar palavras-passe de aplicação a partir da página de informações (pré-visualização) de segurança - Azure Active Directory | Documentos da Microsoft
description: Configure gerado automaticamente palavras-passe (palavras-passe de aplicação) para utilizar com todas as aplicações não baseadas no browser ou qualquer aplicação que não suporta a verificação de dois fatores, na sua organização. Esta palavra-passe de aplicação está separado de uma palavra-passe normal e pode ser definida partir da página de informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341108"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Gerir palavras-passe de aplicação a partir da sua página de informações (pré-visualização) de segurança
Certas aplicações, como o Outlook 2010, não oferecem suporte a verificação de dois passos. Essa falta de suporte significa que, se estiver usando a verificação de dois passos na sua organização, a aplicação não funcionará. Para resolver este problema, pode criar uma palavra-passe gerada automaticamente para utilizar com cada aplicação de fora do browser separada da sua palavra-passe normal.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>O administrador pode não permitir a utilização de palavras-passe de aplicação. Se não vir **palavras-passe de aplicação** como opção, eles não estão disponíveis na sua organização.

Ao utilizar as palavras-passe de aplicação, é importante lembrar-se:

- As palavras-passe de aplicação são gerados automaticamente e apenas introduziu uma vez por aplicação.

- Existe um limite de 40 palavras-passe por utilizador. Se tentar criar uma após esse limite, será solicitado para eliminar uma palavra-passe existente antes de poder criar novo.

- Utilize uma palavra-passe aplicação por dispositivo, não por aplicação. Por exemplo, crie uma única palavra-passe para todas as aplicações no computador portátil e, em seguida, outra palavra-passe única para todas as aplicações no ambiente de trabalho.

    >[!Note]
    >Novos protocolos de autenticação de suporte de clientes do Office 2013 (incluindo Outlook) e pode ser utilizado com verificação de dois passos. Esse suporte significa que após a verificação de dois passos está ativada, já não terá de palavras-passe de aplicação para clientes do Office 2013. Para obter mais informações, consulte a [como a autenticação moderna funciona para aplicações de cliente do Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artigo.

## <a name="create-new-app-passwords"></a>Criar novas palavras-passe de aplicação
Se usar a verificação de dois passos com a sua conta escolar ou profissional e o administrador tiver ativado a experiência de informações de segurança, pode criar e eliminar as palavras-passe de aplicação com o **informações de segurança** página.

>[!Note]
>Se o administrador não tenha ativado a experiência de informações de segurança, tem de seguir as instruções e informações da [gerir palavras-passe de aplicação para a verificação de](multi-factor-authentication-end-user-app-passwords.md) secção.

### <a name="to-create-a-new-app-password"></a>Para criar uma nova palavra-passe de aplicação
1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **palavra-passe de aplicação** na lista pendente e, em seguida, selecione **Add**.

    ![Adicionar a caixa de método, com a palavra-passe de aplicações selecionado](media/security-info/securityinfo-myprofile-addpassword.png)

4. Escreva o nome da aplicação que requer a palavra-passe de aplicação e, em seguida, selecione **seguinte**.

    ![Página de palavra-passe de aplicação, com o nome da aplicação](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copie o texto dos **palavra-passe** caixa, cole a palavra-passe na área de palavra-passe da aplicação (neste exemplo, o Outlook 2010) e, em seguida, selecione **feito**.

    ![Página de palavra-passe de aplicação, com o nome da aplicação](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    A palavra-passe é adicionada e pode iniciar sessão com êxito à sua aplicação no futuro.

## <a name="delete-your-app-passwords"></a>Eliminar as palavras-passe de aplicação
Se já não precisar de utilizar uma aplicação que requer uma palavra-passe de aplicação, pode eliminar a palavra-passe de aplicação associada. A eliminar a palavra-passe de aplicação liberta a um dos pontos de palavra-passe de aplicação disponível para utilização no futuro.

>[!Important]
>Se eliminar uma palavra-passe de aplicação por engano, não é possível-lo. Terá que criar uma nova palavra-passe de aplicação e volte a introduzir na aplicação, seguindo os passos a [criar novas palavras-passe de aplicação](#create-new-app-passwords) seção deste artigo.

### <a name="to-delete-an-app-password"></a>Para eliminar uma palavra-passe de aplicação

1. Sobre o **informações de segurança** página, selecione a **eliminar** ligação junto ao **palavra-passe de aplicação** opção para a aplicação específica.

    ![Ligação para eliminar o método de palavra-passe de aplicação a partir de informações de segurança](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar a **palavra-passe de aplicação**. Depois da palavra-passe de aplicação é eliminada, este é removido do suas informações de segurança e, este desaparece dos **informações de segurança** página.

## <a name="for-more-information"></a>Para obter mais informações:
- Para obter mais informações sobre o **informações de segurança** página e como configurá-lo, consulte [descrição geral de informações de segurança](user-help-security-info-overview.md)
