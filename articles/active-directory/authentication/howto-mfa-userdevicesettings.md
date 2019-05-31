---
title: Os administradores de gerir utilizadores e dispositivos - MFA do Azure - Azure Active Directory
description: Como é que os administradores podem alterar as definições de utilizador, como forçar os utilizadores para repetir o processo de verificação de segurança.
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
ms.openlocfilehash: 04d4848a00fd645bcf23342f27fe820ccf034a8b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298845"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerir definições de utilizador com o Azure multi-factor Authentication na cloud

Como administrador, pode gerir as seguintes definições de utilizador e dispositivo:

* Exigir que os utilizadores forneçam novamente os métodos de contacto
* Eliminar palavras-passe de aplicação
* Exigir a MFA em todos os dispositivos fidedignos

## <a name="require-users-to-provide-contact-methods-again"></a>Exigir que os utilizadores forneçam novamente os métodos de contacto

Esta definição força o utilizador para concluir o processo de registo novamente. Aplicações não baseadas no browser continuam a funcionar se o utilizador tem as palavras-passe de aplicação para os mesmos.  Pode eliminar as palavras-passe de aplicação de utilizadores ao selecionar também **eliminar todas as senhas de aplicação existentes geradas pelos utilizadores selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os utilizadores forneçam novamente os métodos de contacto

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.
3. À direita, selecione **multi-factor Authentication** na barra de ferramentas. É aberta a página do multi-factor authentication.
4. Selecione a caixa ao lado do utilizador ou utilizadores que pretende gerir. É apresentada uma lista de opções do passo rápido à direita.
5. Selecione **gerir definições de utilizador**.
6. Marque a caixa **exigir que os utilizadores selecionados forneçam novamente os métodos de contacto**.
   ![Exigir que os utilizadores forneçam novamente os métodos de contacto](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

As organizações podem realizar essas etapas com o PowerShell com o seguinte como guia para limpar o `StrongAuthenticationMethods` atributo:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Eliminar utilizadores existentes de palavras-passe de aplicação

Esta definição elimina todas as palavras-passe de aplicação que criou um utilizador. Aplicações não baseadas no browser que foram associadas estas palavras-passe de aplicação para de funcionar até que seja criada uma nova palavra-passe de aplicação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como eliminar os utilizadores existentes de palavras-passe de aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.
3. À direita, selecione **multi-factor Authentication** na barra de ferramentas. É aberta a página do multi-factor authentication.
4. Selecione a caixa ao lado do utilizador ou utilizadores que pretende gerir. É apresentada uma lista de opções do passo rápido à direita.
5. Selecione **gerir definições de utilizador**.
6. Marque a caixa **eliminar todas as senhas de aplicação existentes geradas pelos utilizadores selecionados**.
   ![Eliminar todas as senhas de aplicação existente](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar o MFA em todos os dispositivos memorizados para um utilizador

Um dos recursos configuráveis do multi-factor Authentication é fornecer aos utilizadores a opção para marcar dispositivos como fidedigna. Para obter mais informações, consulte [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Os utilizadores podem optar por verificação de dois passos para um número configurável de dias nos respetivos dispositivos regulares. Se uma conta for comprometida ou um dispositivo fidedigno é perdido, terá de ser capaz de remover o estado fidedigno e exigir a verificação novamente.

Quando selecionado, **restauro a autenticação multifator em todos os dispositivos memorizados** os utilizadores são necessários para efetuar a verificação de dois passos da próxima vez que iniciarem sessão, mesmo se eles marcado o respetivo dispositivo como fidedigna.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como restaurar o MFA em todos os dispositivos suspensos para um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.
3. À direita, selecione **multi-factor Authentication** na barra de ferramentas. É aberta a página do multi-factor authentication.
4. Selecione a caixa ao lado do utilizador ou utilizadores que pretende gerir. É apresentada uma lista de opções do passo rápido à direita.
5. Selecione **gerir definições de utilizador**.
6. Marque a caixa **restauro a autenticação multifator em todos os dispositivos memorizados**
   ![restauro a autenticação multifator em todos os dispositivos memorizados](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="next-steps"></a>Passos Seguintes

- Obter mais informações sobre como [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md)
- Se os utilizadores precisam de ajuda, aponte-los em direção a [manual do utilizador de verificação de dois passos](../user-help/multi-factor-authentication-end-user.md)
