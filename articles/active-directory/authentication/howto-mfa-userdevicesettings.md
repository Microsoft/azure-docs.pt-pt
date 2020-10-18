---
title: Gerir as definições do utilizador para autenticação multi-factor Azure - Diretório Ativo Azure
description: Saiba como pode configurar as definições do utilizador do Azure Ative Directory para autenticação multi-factor Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2006422d3516aa67076233b0b4b9d3e7c58a7232
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166522"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gerir as definições do utilizador para autenticação multi-factor Azure

Para ajudar a gerir os utilizadores da Autenticação Multi-Factor Azure, pode exigir que os utilizadores reiniciem a sua palavra-passe, re-registem-se para MFA ou revoguem as sessões de MFA existentes. Para os utilizadores que tenham definido as palavras-passe da aplicação, também pode optar por eliminar estas palavras-passe, fazendo com que a autenticação antiga falhe nessas aplicações. Estas ações podem ser necessárias se precisar de prestar assistência a um utilizador ou quiser redefinir o seu estado de segurança.

## <a name="manage-user-authentication-options"></a>Gerir opções de autenticação do utilizador

Se lhe for atribuída a função *de Administrador de Autenticação,* pode exigir que os utilizadores repensem a sua palavra-passe, re-registem-se para MFA ou revoguem as sessões de MFA existentes a partir do seu objeto de utilizador. Para gerir as definições do utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **Utilizadores** > **Todos os utilizadores**.
1. Escolha o utilizador que deseja realizar uma ação e selecione **métodos de autenticação**. Na parte superior da janela, em seguida, escolha uma das seguintes opções para o utilizador:
   - **A Palavra-passe reinicializar** a palavra-passe do utilizador e atribui uma palavra-passe temporária que deve ser alterada no próximo sessão de sessão.
   - **Exigir re-registo MFA** faz com que, quando o utilizador assinar na próxima vez, seja solicitado que criem um novo método de autenticação MFA.
   
      > [!NOTE]
      > Os métodos de autenticação atualmente registados do utilizador não são eliminados quando um administrador requer re-inscrição para MFA. Depois de um utilizador voltar a registar-se para mFA, recomendamos que revejam as suas informações de segurança e apaguem quaisquer métodos de autenticação previamente registados que já não sejam utilizáveis.
   
   - **A Revogação das Sessões de MFA** limpa as sessões de MFA lembradas do utilizador e exige que realizem MFA da próxima vez que for exigida pela política do dispositivo.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Gerir métodos de autenticação a partir do portal Azure":::

## <a name="delete-users-existing-app-passwords"></a>Eliminar as palavras-passe de aplicações existentes dos utilizadores

Se necessário, pode eliminar todas as palavras-passe da aplicação que um utilizador criou. As aplicações que não foram associadas a estas palavras-passe deixam de funcionar até que seja criada uma nova senha de aplicação. As permissões *globais de administrador* são necessárias para realizar esta ação.

Para eliminar as palavras-passe da aplicação de um utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, selecione **Utilizadores do Diretório Azure Ative**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Multi-Factor Authentication**. Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu: [ ![ Selecione a autenticação multi-factor da janela do Utilizadores em Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Verifique a caixa ao lado do utilizador ou utilizadores que deseja gerir. Uma lista de opções de passo rápido aparece à direita.
1. **Selecione Gerir as definições**do utilizador, em seguida, verifique a caixa para Eliminar todas as **palavras-passe de aplicações existentes geradas pelos utilizadores selecionados**, como mostrado no exemplo seguinte: ![ Eliminar todas as palavras-passe de aplicações existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **guardar**e, em seguida, **fechar**.

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como configurar as definições individuais do utilizador. Para configurar as definições globais do serviço de autenticação multi-factor Azure, consulte as definições de [autenticação multi-factor Configure Azure](howto-mfa-mfasettings.md).

Se os seus utilizadores precisarem de ajuda, consulte o [guia do Utilizador para autenticação multi-factor Azure](../user-help/multi-factor-authentication-end-user-first-time.md).
