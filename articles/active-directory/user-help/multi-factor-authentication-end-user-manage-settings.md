---
title: Gerir definições de verificação de dois passos - Azure Active Directory | Documentos da Microsoft
description: Gerir como utilizar o Azure multi-factor Authentication, incluindo alterar as informações de contacto ou configurar seus dispositivos.
services: active-directory
keywords: cliente de autenticação multifator, problema de autenticação, ID de correlação
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334493"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Gerir as definições de verificação de dois passos
Este artigo responde a perguntas sobre como atualizar as definições de verificação ou multifator a autenticação de dois passos. Se estiver a ter problemas ao iniciar sessão para a sua conta, consulte [dificuldades com verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md) para resolução de problemas de ajuda.

## <a name="where-to-find-the-settings-page"></a>Onde encontrar a página de definições
Dependendo de como sua empresa configurar o multi-factor Authentication do Azure, existem alguns lugares onde pode alterar as definições, como o seu número de telefone.

Se o suporte da empresa enviados uma URL específica ou passos para gerir a verificação de dois passos, siga as instruções. Caso contrário, as instruções seguintes devem funcionar para todas as outras pessoas. Se siga estes passos, mas não vê as mesmas opções, que significa que a sua empresa ou escola personalizada seu próprio portal. Peça ao seu administrador para a ligação ao seu portal do Azure multi-factor Authentication.

**Ir para a página de verificação de segurança adicionais**

- Aceda a https://aka.ms/MFASetup.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Se clicar no link não funcionar para, também pode obter para o **verificação adicional de segurança** página através dos seguintes passos:

1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. Selecione o nome da sua conta no canto superior direito, em seguida, selecione **perfil**.

3. Selecione **verificação adicional de segurança**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. Carrega a página de verificação de segurança adicionais com as suas definições.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Quero alterar meu número de telefone ou adicionar um número secundário
É importante configurar um número de telefone de autenticação secundária.  Uma vez que o número de telefone principal e a sua aplicação móvel são provavelmente no telefone mesmo, o número de telefone secundário é a forma mais rápida, poderá voltar à sua conta se o seu telefone for perdido ou roubado.

> [!NOTE]
> Se não tiver acesso para o seu número de telefone principal e precisar de ajuda para obter sua conta, consulte a [dificuldades com verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md) artigo para obter mais ajuda.  

**Para alterar o número de telefone principal:**  

1. Sobre o **verificação adicional de segurança** página, selecione a caixa de texto com o atual número de telefone e editá-lo com o novo número de telefone.  
2. Selecione **Guardar**.  
3. Se este número de telefone é o número que utiliza para a sua opção de verificação preferida, tem de verificar o número de novos antes de pode guardá-lo.  

**Para adicionar um número de telefone secundário:**  

1. Na página de verificação de segurança adicionais, selecione a caixa junto a **telefone de autenticação alternativo.**  
2. Introduza o seu número de telefone secundário na caixa de texto.  
3. Selecione **guardar** e terminar as suas alterações.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Exigir a verificação de dois passos novamente um dispositivo que tiver marcado como fidedigna

Dependendo das suas definições de organização, talvez tenha uma caixa de verificação que diz "não perguntar novamente durante **X** dias" ao efetuar a verificação de dois passos no seu navegador. Se esta caixa de verificação e, em seguida, perder o dispositivo ou considerar que a sua conta for comprometida, deve restaurar a verificação de dois passos para todos os seus dispositivos.

1. Na página de verificação adicional de segurança, selecione **restauro a autenticação multifator nos dispositivos anteriormente fidedignos**.
2. Da próxima vez que iniciar sessão em qualquer dispositivo, será solicitado a efetuar a verificação de dois passos.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Como limpar o Microsoft Authenticator do meu dispositivo antigo e mover para uma nova?
Quando desinstalar a aplicação a partir do seu dispositivo ou repor o dispositivo, não remove a ativação no back-end. Para obter mais informações, consulte [Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="next-steps"></a>Passos Seguintes
* Obter sugestões de resolução de problemas e ajudar no [a ter problemas com verificação de dois passos](multi-factor-authentication-end-user-troubleshoot.md)
* Configurar [palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) para todas as aplicações que não suportam a verificação de dois passos.
