---
title: Associar o seu dispositivo de trabalho à rede da sua organização - Azure Active Directory | Documentos da Microsoft
description: Saiba como associar o seu dispositivo de trabalho para a rede da sua organização.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ba6b2d33c3fb5d9fda6821718ac61513a958b7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369144"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Associar o seu dispositivo de trabalho para a rede da sua organização
Associe o seu dispositivo de Windows 10 pertencentes à trabalho à rede da sua organização para que possa aceder a recursos potencialmente restritos.

## <a name="what-happens-when-you-join-your-device"></a>O que acontece quando associar o seu dispositivo
Enquanto estiver ingressando em seu dispositivo Windows 10 para a rede da sua organização, irão ocorrer as seguintes ações:

- Windows regista o dispositivo à rede da sua organização, permitindo-lhe aceder aos recursos com a sua conta pessoal. Depois do dispositivo estiver registado, Windows, em seguida, junta-se o dispositivo à rede, pelo que pode utilizar a sua organização nome de utilizador e palavra-passe para iniciar sessão e aceder aos recursos restritos.

- Opcionalmente, com base nas opções da sua organização, poderá ser-lhe pedido para configurar a verificação de dois passos através de um [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) ou [informações de segurança](user-help-security-info-overview.md).

- Opcionalmente, com base nas opções da sua organização, poderá ser automaticamente inscritos na gestão de dispositivos móveis, como o Microsoft Intune. Para mais informações sobre como inscrever-se no Microsoft Intune, veja [inscrever o seu dispositivo no Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Vai passar pelo processo de início de sessão, com o início de sessão automático, com a sua conta organizacional.

## <a name="to-join-a-brand-new-windows-10-device"></a>Para associar um dispositivo Windows 10 novo em folha
Se o dispositivo está totalmente novo e não foi configurado ainda, pode percorrer o processo do Windows de caixa experiência original (OOBE) para associar o seu dispositivo à rede.

1. Iniciar o seu dispositivo novo e iniciar o processo OOBE.

2. Sobre o **iniciar sessão com a Microsoft** ecrã, escreva o seu e-mail profissional ou escolar.

    ![Inicie sessão no ecrã com endereço de e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Sobre o **a palavra-passe** ecrã, escreva a palavra-passe.

    ![Introduza o seu ecrã de palavra-passe](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. No seu dispositivo móvel, aprove o seu dispositivo para que ele pode aceder à sua conta. 

    ![Ecrã de notificação por telemóvel](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Conclua o processo OOBE, incluindo as definições de privacidade de configuração e configurar o Windows Hello (se necessário).

    O dispositivo está agora associado à rede da sua organização.

## <a name="to-make-sure-youre-joined"></a>Para garantir que são associadas
Pode tornar-se de que está associado ao observar as suas definições.

1. Open **configurações**e, em seguida, selecione **contas**.

    ![Contas no ecrã de definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **acesso profissional ou escolar**e certifique-se de ver o texto que diz algo como: **ligado *< your_organization >* do Azure AD**.

    ![Aceder ao ecrã escolar ou profissional com a conta de contoso ligados](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Para associar um dispositivo Windows 10 já configurado
Se já teve o seu dispositivo por um tempo e ele já está foi configurado, pode seguir estes passos para associar o seu dispositivo à rede.

1. Open **configurações**e, em seguida, selecione **contas**.

2. Selecione **acesso profissional ou escolar**e, em seguida, selecione **Connect**.

    ![Acesso profissional ou escolar e ligações do Connect](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Sobre o **configurar uma conta escolar ou profissional** ecrã, selecione **associar este dispositivo ao Azure Active Directory**.

    ![Configurar um trabalho ou escolar ecrã de conta](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Sobre o **vamos ajudá-lo a entrar** ecrã, escreva o seu endereço de e-mail (por exemplo, alain@contoso.com) e, em seguida, selecione **seguinte**.

    ![Vamos ajudá-lo a sessão iniciada no ecrã](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Sobre o **introduzir palavra-passe** ecrã, escreva a palavra-passe e, em seguida, selecione **iniciar sessão**.

    ![Introduzir palavra-passe](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. No seu dispositivo móvel, aprove o seu dispositivo para que ele pode aceder à sua conta. 

    ![Ecrã de notificação por telemóvel](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Sobre o **certificar-se de que esta é a sua organização** ecrã, reveja as informações para se certificar de que é adequada e, em seguida, selecione **associar**.

    ![Certifique-se de que este é o ecrã de verificação da organização](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Sobre o **tudo pronto** ecrã, clique em **feito**.

    ![É o ecrã de conjunto de todos os](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Para garantir que são associadas
Pode tornar-se de que está associado ao observar as suas definições.

1. Open **configurações**e, em seguida, selecione **contas**.

    ![Contas no ecrã de definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **acesso profissional ou escolar**e certifique-se de ver o texto que diz algo como: **ligado *< your_organization >* do Azure AD**.

    ![Aceder ao ecrã escolar ou profissional com a conta de contoso ligados](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Passos Seguintes
Depois de associar o seu dispositivo à rede da sua organização, deve ser capaz de aceder a todos os seus recursos através do seu trabalho ou escolar informações da conta.

- Se a organização quer que Registre seu dispositivo pessoal, como o seu telemóvel, veja [registar o seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

