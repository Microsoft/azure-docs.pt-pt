---
title: Como gerir palavras-passe de aplicação - Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre as palavras-passe de aplicação e o que são utilizados para em relação à verificação de dois passos.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee21080fd95815289f73654cc5bc650296c8e8dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433370"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerir palavras-passe de aplicação para a verificação de dois passos

Determinadas aplicações não baseadas no browser, tais como o Outlook 2010, não suporta a verificação de dois passos. Essa falta de suporte significa que, se estiver usando a verificação de dois passos, a aplicação não funcionará. Para resolver este problema, pode criar uma palavra-passe gerada automaticamente para utilizar com cada aplicação de fora do browser separada da sua palavra-passe normal.

Ao utilizar as palavras-passe de aplicação, é importante lembrar-se:

- As palavras-passe de aplicação são gerados automaticamente e apenas introduziu uma vez por aplicação.

- Existe um limite de 40 palavras-passe por utilizador. Se tentar criar uma após esse limite, será solicitado para eliminar uma palavra-passe existente antes de poder criar novo.

- Utilize uma palavra-passe aplicação por dispositivo, não por aplicação. Por exemplo, crie uma única palavra-passe para todas as aplicações no computador portátil e, em seguida, outra palavra-passe única para todas as aplicações no ambiente de trabalho.

    >[!Note]
    >Novos protocolos de autenticação de suporte de clientes do Office 2013 (incluindo Outlook) e pode ser utilizado com verificação de dois passos. Esse suporte significa que após a verificação de dois passos está ativada, já não terá de palavras-passe de aplicação para clientes do Office 2013. Para obter mais informações, consulte a [como a autenticação moderna funciona para aplicações de cliente do Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artigo.

## <a name="where-to-create-and-delete-your-app-passwords"></a>Onde pretende criar e eliminar as palavras-passe de aplicação

É-lhe fornecido uma palavra-passe de aplicação durante o registo de verificação inicial de dois passos. Se precisar de mais do que uma senha, pode criar palavras-passe adicionais, com base na forma como utiliza a verificação de dois passos:

- **Verificação de dois passos é usar com seu trabalho ou conta da instituição de ensino e o portal MyApps.** Criar e eliminar as palavras-passe de aplicação com as instruções no [criar e eliminar aplicações palavras-passe com o portal MyApps](#create-and-delete-app-passwords-using-the-myapps-portal) seção deste artigo. Para obter mais informações sobre o portal MyApps e como usá-lo, consulte [o que é o portal MyApps no Azure Active Directory?](active-directory-saas-access-panel-introduction.md).

- **Verificação de dois passos é usar com seu trabalho ou conta da instituição de ensino e o portal do Office 365.** Criar e eliminar as palavras-passe de aplicação com as instruções no [criar e eliminar aplicações palavras-passe através do portal do Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) seção deste artigo.

- **Verificação de dois passos é usar com sua conta Microsoft pessoal.** Criar e eliminar as palavras-passe de aplicação com o [Noções básicas de segurança](https://account.microsoft.com/account/) página com a sua conta Microsoft pessoal. Para obter mais informações, consulte a [palavras-passe de aplicação e verificação de dois passos](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artigo.

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Criar e eliminar as palavras-passe de aplicação com o portal MyApps
Pode criar e eliminar as palavras-passe de aplicação através do portal MyApps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma palavra-passe de aplicação com o portal MyApps

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Selecione o nome no canto superior direito e escolher **perfil**.

3. Selecione **verificação adicional de segurança**.

   ![Selecione a verificação de segurança adicional - captura de ecrã](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Selecione **palavras-passe de aplicação**.

   ![Selecione palavras-passe de aplicação - captura de ecrã](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Clique em **Criar**.

6. Escreva um nome para a palavra-passe de aplicação e, em seguida, selecione **seguinte**.

7. Copie a palavra-passe de aplicação para a área de transferência e cole-o na sua aplicação.
   
    ![Criar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para eliminar uma palavra-passe de aplicação com o portal MyApps

1. Aceda ao seu perfil e, em seguida, selecione **verificação de segurança adicional**.

2. Selecione **palavras-passe de aplicação**e, em seguida, selecione **eliminar** junto a palavra-passe de aplicação que pretende eliminar.

   ![Eliminar uma palavra-passe de aplicação](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Selecione **Sim** para confirmar que pretende eliminar a palavra-passe e, em seguida, selecione **fechar**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e eliminar as palavras-passe de aplicação com o portal do Office 365

Se utilizar a verificação de dois passos com o seu trabalho ou a conta escolar e a aplicações do Office 365, pode criar e eliminar as palavras-passe de aplicação com o portal do Office 365. Pode ter um máximo de 40 palavras-passe de aplicação ao mesmo tempo. Se precisar de outra palavra-passe de aplicações após esse limite, terá de eliminar uma das suas palavras-passe de aplicação existentes.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar palavras-passe de aplicação com o portal do Office 365

1. Inicie sessão na sua conta escolar ou profissional.

2. Aceda a https://portal.office.com, selecione o **configurações** ícone no canto superior direito do **portal do Office 365** página e, em seguida, expanda **verificação adicional de segurança**.

    ![Mostrar portal do Office expandido a área de verificação de segurança adicionais](media/security-info/security-info-o365password.png)

3. Selecione o texto que indica **criar e gerir palavras-passe de aplicação** para abrir o **palavras-passe de aplicação** página.

4. Selecione **Create**, escreva um nome amigável para a aplicação que tem da palavra-passe de aplicação e, em seguida, selecione **próxima**.

5. Selecione **copiar palavra-passe para a área de transferência**e, em seguida, selecione **fechar**.

6. Utilize a palavra-passe de aplicação copiado para iniciar sessão na sua aplicação não baseadas no browser. Só tem de introduzir esta palavra-passe de uma vez e ele é memorizado para o futuro.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para eliminar as palavras-passe de aplicação com o portal do Office 365

1. Inicie sessão na sua conta escolar ou profissional.

2. Aceda a https://portal.office.com, selecione o **configurações** ícone no canto superior direito do **portal do Office 365** página e, em seguida, selecione **verificação adicional de segurança**.

3. Selecione o texto que indica **criar e gerir palavras-passe de aplicação** para abrir o **palavras-passe de aplicação** página.

4. Selecione **elimine** a palavra-passe de aplicação eliminar, selecione **Sim** na caixa de confirmação e, em seguida, selecione **fechar**.

    A palavra-passe de aplicação foi eliminada com êxito.

5. Siga os passos para criar uma palavra-passe de aplicação para criar a sua nova palavra-passe de aplicação.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as palavras-passe de aplicação não estão funcionando corretamente

Certifique-se de que escreveu corretamente a palavra-passe. Se tiver a certeza de que introduziu corretamente a palavra-passe, pode tentar iniciar sessão novamente e criar uma nova palavra-passe de aplicação. Se nenhuma dessas opções corrigir o problema, contacte o suporte da empresa, de modo que podem eliminar a sua aplicação palavras-passe existentes, permitindo-lhe criar, de novo em folha. 

## <a name="next-steps"></a>Passos Seguintes

- [Gerir as definições da verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para verificar os inícios de sessão com as notificações de aplicações, em vez de textos ou chamadas a receber.
