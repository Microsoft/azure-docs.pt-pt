---
title: Registar dispositivos pessoais na rede da organização - Azure Active Directory | Documentos da Microsoft
description: Saiba como registar o seu dispositivo pessoal na rede da sua organização para que possa aceder a recursos protegidos da sua organização.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 041c8bb6b4de2bbe2cbeb4c1a89e452239ae57bd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439745"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registar o seu dispositivo pessoal na rede da sua organização
Registe o seu dispositivo pessoal (normalmente, um telefone ou tablet) na rede da sua organização. Depois do dispositivo estiver registado, será capaz de acessar recursos restritos da sua organização.

>[!Note]
>Este artigo utiliza um dispositivo Windows para fins de demonstração, mas também pode registar dispositivos com iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>O que acontece quando registar o seu dispositivo
Enquanto estiver a registar o dispositivo na rede da sua organização, irão ocorrer as seguintes ações:

- Windows regista o dispositivo na rede da sua organização.

- Opcionalmente, com base nas opções da sua organização, poderá ser-lhe pedido para configurar a verificação de dois passos através de um [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) ou [informações de segurança](user-help-security-info-overview.md).

- Opcionalmente, com base nas opções da sua organização, poderá ser automaticamente inscritos na gestão de dispositivos móveis, como o Microsoft Intune. Para mais informações sobre como inscrever-se no Microsoft Intune, veja [inscrever o seu dispositivo no Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Vai passar pelo processo de início de sessão, com o nome de utilizador e palavra-passe para a sua conta escolar ou profissional.

## <a name="to-register-your-windows-device"></a>Para registar o seu dispositivo Windows

Siga estes passos para registar o seu dispositivo pessoal na sua rede.

1. Open **configurações**e, em seguida, selecione **contas**.

    ![Contas no ecrã de definições](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **acesso profissional ou escolar**e, em seguida, selecione **Connect** partir os **acesso profissional ou escolar** ecrã.

    ![Aceder ao ecrã escolar ou profissional com a opção Connect realçada](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Sobre o **adicionar uma conta escolar ou profissional** ecrã, escreva o endereço de e-mail para a sua conta escolar ou profissional e, em seguida, selecione **próxima**. Por exemplo, alain@contoso.com.

4. Inicie sessão na sua conta profissional ou escolar e, em seguida, selecione **iniciar sessão**.

5. Conclua o processo de registo, incluindo aprovar o pedido de verificação de identidade (se usar a verificação de dois passos) e configurar o Windows Hello (se necessário).

## <a name="to-verify-that-youre-registered"></a>Para verificar que está registado
Pode tornar-se de que é registrado ao observar as suas definições.

1. Open **configurações**e, em seguida, selecione **contas**.

    ![Contas no ecrã de definições](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **acesso profissional ou escolar**e certifique-se vir a sua conta escolar ou profissional.

    ![Aceder ao ecrã escolar ou profissional com a conta de contoso ligados](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Passos Seguintes
Depois de registar o seu dispositivo pessoal à rede da sua organização, deverá conseguir aceder à maioria dos seus recursos.

- Se a organização pretende a associar o seu dispositivo de trabalho, consulte [associar o seu dispositivo de trabalho para a rede da sua organização](user-help-join-device-on-network.md).



