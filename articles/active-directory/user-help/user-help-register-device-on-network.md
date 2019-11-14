---
title: Registrar dispositivos pessoais na rede de uma organização-Azure AD
description: Saiba como registrar seu dispositivo pessoal na rede da sua organização para que você possa acessar os recursos protegidos da sua organização.
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
ms.openlocfilehash: 879fa55df422e6039c6830e25e43637fc31b8037
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028491"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrar seu dispositivo pessoal na rede da sua organização
Registre seu dispositivo pessoal (normalmente um telefone ou Tablet) na rede da sua organização. Depois que o dispositivo for registrado, ele poderá acessar os recursos restritos da sua organização.

>[!Note]
>Este artigo usa um dispositivo Windows para fins de demonstração, mas você também pode registrar dispositivos que executam iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>O que acontece quando você registra seu dispositivo
Enquanto você estiver registrando seu dispositivo na rede da sua organização, as seguintes ações ocorrerão:

- O Windows registra seu dispositivo na rede da sua organização.

- Opcionalmente, com base nas opções da sua organização, você pode ser solicitado a configurar a verificação em duas etapas por meio da [autenticação multifator](multi-factor-authentication-end-user-first-time.md) ou das [informações de segurança](user-help-security-info-overview.md).

- Opcionalmente, com base nas opções da sua organização, você pode ser registrado automaticamente no gerenciamento de dispositivo móvel, como Microsoft Intune. Para obter mais informações sobre o registro em Microsoft Intune, consulte [registrar seu dispositivo no Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Você passará pelo processo de entrada, usando o nome de usuário e a senha para sua conta corporativa ou de estudante.

## <a name="to-register-your-windows-device"></a>Para registrar seu dispositivo Windows

Siga estas etapas para registrar seu dispositivo pessoal em sua rede.

1. Abra **configurações**e, em seguida, selecione **contas**.

    ![Contas na tela de configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **acesso corporativo ou de estudante**e, em seguida, selecione **conectar** na tela **acessar o trabalho ou a escola** .

    ![Acessar a tela corporativa ou de estudante com a opção conectar realçada](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na tela **Adicionar uma conta corporativa ou de estudante** , digite seu endereço de email para sua conta corporativa ou de estudante e, em seguida, selecione **Avançar**. Por exemplo, alain@contoso.com.

4. Entre em sua conta corporativa ou de estudante e, em seguida, selecione **entrar**.

5. Conclua o restante do processo de registro, incluindo aprovar sua solicitação de verificação de identidade (se você usar a verificação em duas etapas) e configurar o Windows Hello (se necessário).

## <a name="to-verify-that-youre-registered"></a>Para verificar se você está registrado
Você pode verificar se está registrado examinando suas configurações.

1. Abra **configurações**e, em seguida, selecione **contas**.

    ![Contas na tela de configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **acesso corporativo ou de estudante**e verifique se você vê sua conta corporativa ou de estudante.

    ![Acessar a tela corporativa ou de estudante com a conta contoso conectada](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Passos seguintes
Depois de registrar seu dispositivo pessoal na rede da sua organização, você deve ser capaz de acessar a maioria dos seus recursos.

- Se sua organização quiser que você ingresse em seu dispositivo de trabalho, consulte [ingressar seu dispositivo de trabalho na rede da sua organização](user-help-join-device-on-network.md).



