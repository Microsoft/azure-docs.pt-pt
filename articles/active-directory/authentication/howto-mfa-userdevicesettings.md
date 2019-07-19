---
title: Administradores gerenciam usuários e dispositivos – Azure MFA-Azure Active Directory
description: Como os administradores podem alterar as configurações do usuário, como forçar os usuários a fazer o processo de verificação novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3152dead04510078dd475b611afbfc30264e58f7
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297637"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a autenticação multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Gerenciar métodos de autenticação

Como um administrador atribuiu a função de administrador de autenticação, você pode exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revogar sessões de MFA existentes de seu objeto de usuário.

![Gerenciar métodos de autenticação do portal do Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

## <a name="require-users-to-provide-contact-methods-again"></a>Exigir que os usuários forneçam métodos de contato novamente

Essa configuração força o usuário a concluir o processo de registro novamente. Aplicativos sem navegador continuam a funcionar se o usuário tem senhas de aplicativo para eles.  Você pode excluir as senhas de aplicativo dos usuários selecionando também **excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os usuários forneçam métodos de contato novamente

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
3. À direita, selecione **autenticação** multifator na barra de ferramentas. A página autenticação multifator é aberta.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **gerenciar configurações do usuário**.
6. Marque a caixa **exigir que os usuários selecionados forneçam métodos de contato novamente**.
   ![Exigir que os usuários forneçam métodos de contato novamente](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

As organizações podem concluir essas etapas com o PowerShell usando o seguinte como um guia para `StrongAuthenticationMethods` limpar o atributo:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo param de funcionar até que uma nova senha de aplicativo seja criada.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
3. À direita, selecione **autenticação** multifator na barra de ferramentas. A página autenticação multifator é aberta.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **gerenciar configurações do usuário**.
6. Marque a caixa **excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![Excluir todas as senhas de aplicativo existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar MFA em todos os dispositivos lembrados de um usuário

Um dos recursos configuráveis da autenticação multifator do Azure é dar aos usuários a opção de marcar dispositivos como confiáveis. Para obter mais informações, consulte [definir as configurações de autenticação multifator do Azure](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Os usuários podem recusar a verificação em duas etapas para um número configurável de dias em seus dispositivos regulares. Se uma conta for comprometida ou um dispositivo confiável for perdido, você precisará ser capaz de remover o status confiável e exigir a verificação em duas etapas novamente.

Quando marcada, **restaure a autenticação multifator em todos os dispositivos lembrados** os usuários devem executar a verificação em duas etapas na próxima vez que entrarem, mesmo que tenham marcado seu dispositivo como confiável.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como restaurar o MFA em todos os dispositivos suspensos para um usuário

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
3. À direita, selecione **autenticação** multifator na barra de ferramentas. A página autenticação multifator é aberta.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **gerenciar configurações do usuário**.
6. Marque a caixa para **restaurar a autenticação multifator em todos os dispositivos**
   ![lembrados restaurar a autenticação multifator em todos os dispositivos lembrados](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="next-steps"></a>Passos Seguintes

- Obter mais informações sobre como [definir as configurações de autenticação multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, aponte [para o guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
