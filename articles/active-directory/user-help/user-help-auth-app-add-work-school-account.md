---
title: Adicionar a sua conta escolar ou profissional para a aplicação Microsoft Authenticator - Azure Active Directory | Documentos da Microsoft
description: Como adicionar a sua conta escolar ou profissional para a aplicação Microsoft Authenticator para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960630"
---
# <a name="add-your-work-or-school-account"></a>Adicionar a sua conta escolar ou profissional
Se a sua organização utiliza a verificação de dois fatores, pode configurar a sua conta escolar ou profissional para utilizar a aplicação Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Antes de poder adicionar a sua conta, tem de transferir e instalar a aplicação Microsoft Authenticator. Se ainda não tiver feito, siga os passos a [transferir e instalar a aplicação](user-help-auth-app-download-install.md) artigo.

## <a name="add-your-work-or-school-account"></a>Adicionar a sua conta escolar ou profissional

1. No seu computador, vá para o [verificação adicional de segurança](https://aka.ms/mfasetup) página.

    >[!Note]
    >Se não vir a **verificação adicional de segurança** página, é possível que o administrador tiver ativado a experiência de informações (pré-visualização) de segurança. Se for esse o caso, deve seguir as instruções a [definir informações de segurança para utilizar uma aplicação de autenticação](security-info-setup-auth-app.md) secção. Se não for esse o caso, terá de contactar o suporte técnico da sua organização para obter assistência. Para obter mais informações sobre as informações de segurança, consulte [descrição geral de informações (pré-visualização) de segurança](user-help-security-info-overview.md).

2. A caixa de verificação junto a **aplicação de autenticador**e, em seguida, selecione **configurar**.

    O **aplicação móvel de configurar** é apresentada a página.
    
    ![Ecrã que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho conta escolar ou profissional**.

4. Utilizar a câmara do dispositivo para analisar o código QR a partir do **configurar de aplicação móvel** ecrã no seu computador e, em seguida, escolha **feito**.

    >[!Note]
    >Se a câmara não consegue capturar o código QR, pode adicionar manualmente as informações da sua conta para a aplicação Microsoft Authenticator para verificação de dois fatores. Para obter mais informações e saber como fazê-lo, consulte [adicione manualmente a sua conta](user-help-auth-app-add-account-manual.md).

5. Reveja os **contas** ecrã da aplicação no seu dispositivo, para certificar-se de que sua conta é a direita e de que existe um código de verificação de seis dígitos associados. Para segurança adicional, as alterações de código de verificação a cada 30 segundos a impedir que alguém usando um código várias vezes.

    ![ecrã de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Passos Seguintes

- Depois de adicionar as suas contas para a aplicação, pode iniciar sessão com a aplicação de autenticador no seu dispositivo. Para obter mais informações, consulte [iniciar sessão com a aplicação](user-help-auth-app-sign-in.md).

- Para dispositivos iOS, também pode criar cópias de segurança as credenciais da conta e relacionados com as definições de aplicação, como a ordem das suas contas, a nuvem. Para obter mais informações, consulte [cópia de segurança e recuperação com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
