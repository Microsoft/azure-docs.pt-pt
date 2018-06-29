---
title: Administradores de gerir utilizadores e dispositivos - MFA do Azure | Microsoft Docs
description: Descreve como alterar as definições de utilizador, tais como forçar os utilizadores para repetir o processo de prova.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 363106421e75fa2e1b220d03a7d7cbed25447bcc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098401"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerir definições de utilizador com o Azure multi-factor Authentication na nuvem

Como administrador, pode gerir as seguintes definições de utilizador e dispositivo:

* Exigir que os utilizadores forneçam novamente os métodos de contacto
* Eliminar palavras-passe de aplicação
* Exigir a MFA em todos os dispositivos fidedignos 

## <a name="require-users-to-provide-contact-methods-again"></a>Exigir que os utilizadores forneçam novamente os métodos de contacto
Esta definição força o utilizador para concluir o processo de registo novamente. Aplicações não baseadas no browser continuem a funcionar se o utilizador tiver palavras-passe de aplicação para os mesmos.  Pode eliminar as palavras-passe de aplicação de utilizadores selecionando também **eliminar todas as aplicações palavras-passe existentes geradas pelos utilizadores selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como requerer que os utilizadores forneçam novamente os métodos de contacto
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**. Abre a página do multi-factor authentication. 
4. Selecione a caixa junto ao utilizador ou utilizadores que pretende gerir. Uma lista das opções do passo rápida aparecem à direita. 
5. Selecione **gerir definições de utilizador**.
6. Marque a caixa de **exigir que os utilizadores selecionados forneçam novamente os métodos de contacto**.
   ![Forneça métodos de contacto](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="delete-users-existing-app-passwords"></a>Eliminar utilizadores existentes de palavras-passe de aplicação
Esta definição elimina todas as palavras-passe de aplicação que criou um utilizador. Aplicações não baseadas no browser que se encontravam associadas estas palavras-passe de aplicação parar de funcionar até que seja criada uma nova palavra-passe de aplicação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como eliminar os utilizadores existentes de palavras-passe de aplicação
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**. Abre a página do multi-factor authentication. 
6. Selecione a caixa junto ao utilizador ou utilizadores que pretende gerir. Uma lista das opções do passo rápida aparecem à direita. 
7. Selecione **gerir definições de utilizador**.
8. Marque a caixa de **eliminar todas as aplicações palavras-passe existentes geradas pelos utilizadores selecionados**.
   ![Eliminar palavras-passe de aplicação](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Clique em **Guardar**.
10. Clique em **fechar**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar o MFA em todos os dispositivos memorizados para um utilizador
Uma das funcionalidades configuráveis de Azure multi-factor Authentication está a dar aos utilizadores a opção para marcar dispositivos como fidedigna. Para obter mais informações, consulte [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Os utilizadores podem ativamente por verificação de dois passos para um número de dias nos respetivos dispositivos regulares configurável. Se uma conta ser comprometida ou um dispositivo fidedigno é perdido, terá de ser capaz de remover o estado fidedigno e exigir verificação de dois passos novamente.

O **restauro a autenticação multifator em todos os dispositivos memorizados** definição significa que o utilizador serão solicitados para efetuar a verificação da próxima vez que iniciar sessão, independentemente se escolheu marcar o respetivo dispositivo como fidedigna. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como restaurar o MFA em todos os dispositivos suspensos para um utilizador
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**. Abre a página do multi-factor authentication. 
6. Selecione a caixa junto ao utilizador ou utilizadores que pretende gerir. Uma lista das opções do passo rápida aparecem à direita. 
7. Selecione **gerir definições de utilizador**.
8. Marque a caixa de **restauro a autenticação multifator em todos os dispositivos memorizados**
   ![eliminar palavras-passe de aplicação](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Clique em **Guardar**.
10. Clique em **fechar**.

## <a name="next-steps"></a>Passos Seguintes

- Obter mais informações sobre como [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md)

- Se os seus utilizadores precisarem de ajuda, apontar para o [Guia do utilizador para a verificação de dois passos](end-user/current/multi-factor-authentication-end-user.md)
