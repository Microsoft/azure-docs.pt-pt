---
title: Gerir as definições de utilizador para autenticação de multi-factores Azure - Diretório Ativo Azure
description: Saiba como pode configurar as definições de utilizador do Diretório Ativo Do Azure para autenticação de multi-factors Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309760"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gerir as definições do utilizador para autenticação de multifactor azure

Para ajudar a gerir os utilizadores da Autenticação Multi-Factor Azure, pode exigir que os utilizadores redefinirem a sua palavra-passe, rereginciem para mFA ou revoguem as sessões de MFA existentes. Para os utilizadores que tenham definido as palavras-passe das aplicações, também pode optar por eliminar estas palavras-passe, fazendo com que a autenticação do legado falhe nessas aplicações. Estas ações podem ser necessárias se necessitar de prestar assistência a um utilizador, ou se pretender redefinir o seu estado de segurança.

## <a name="manage-user-authentication-options"></a>Gerir as opções de autenticação do utilizador

Se lhe for atribuída a função de *Administrador de Autenticação,* poderá exigir que os utilizadores redefinirem a sua palavra-passe, reregiquem para o MFA ou revoguem as sessões de MFA existentes a partir do seu objeto de utilizador. Para gerir as definições do utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.
1. Escolha o utilizador que deseja realizar uma ação e **selecione métodos**de autenticação . Na parte superior da janela, escolha uma das seguintes opções para o utilizador:
   - **Redefinir a palavra-passe** repõe a palavra-passe do utilizador e atribui uma senha temporária que deve ser alterada no próximo início de sessão.
   - **Exigir re-registar o MFA** faz com que, quando o utilizador assinar na próxima vez, seja solicitado que estabeleça um novo método de autenticação MFA.
   - **A Revogação das Sessões de MFA** iliba as sessões de MFA lembradas do utilizador e exige que realizem MFA da próxima vez que for exigido pela apólice no dispositivo.

   ![Gerir métodos de autenticação a partir do portal Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Eliminar as senhas de aplicação existentes dos utilizadores

Se necessário, pode eliminar todas as palavras-passe da aplicação que um utilizador criou. As aplicações não-navegadoras que estavam associadas a estas palavras-passe da aplicação deixam de funcionar até que seja criada uma nova senha de aplicação. *São* necessárias permissões globais de administradores para realizar esta ação.

Para eliminar as palavras-passe de uma aplicação de um utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Do lado esquerdo,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.
1. **Selecione a autenticação de vários fatores.** Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione autenticação multi-factor da janela utilizadores em Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Verifique a caixa ao lado do utilizador ou utilizadores que deseja gerir. Uma lista de opções rápidas aparece à direita.
1. **Selecione Gerir as definições**do utilizador , em seguida, verifique a caixa para eliminar ![todas as **palavras-passe de aplicação existentes geradas pelos utilizadores selecionados**, como mostra o seguinte exemplo: Eliminar todas as palavras-passe de aplicação existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **guardar**e, em seguida, **fechar.**

## <a name="next-steps"></a>Passos seguintes

Este artigo ajudou a configurar as definições individuais do utilizador. Para configurar as definições do serviço de autenticação de vários fatores Azure, consulte as definições de [autenticação multi-factor do Azure Azure, consulte as definições de autenticação de multi-factors do Azure, consulte as definições de autenticação de multi-factors do Azure](howto-mfa-mfasettings.md)

Se os seus utilizadores precisarem de ajuda, consulte o guia do [Utilizador para autenticação multi-factor Azure](../user-help/multi-factor-authentication-end-user.md).
