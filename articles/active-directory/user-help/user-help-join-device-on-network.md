---
title: Ingresse seu dispositivo de trabalho na rede-AD da sua organização
description: Saiba como unir seu dispositivo de trabalho à rede da sua organização.
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
ms.openlocfilehash: b122d70179f22c91028c8343b23d74cc616b1f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028533"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Ingresse seu dispositivo de trabalho na rede da sua organização
Ingresse seu dispositivo Windows 10 de Propriedade do seu trabalho na rede da sua organização para que você possa acessar recursos potencialmente restritos.

## <a name="what-happens-when-you-join-your-device"></a>O que acontece quando você ingressa em seu dispositivo
Enquanto você estiver ingressando seu dispositivo Windows 10 na rede da sua organização, as seguintes ações ocorrerão:

- O Windows registra seu dispositivo na rede da sua organização, permitindo que você acesse seus recursos usando sua conta pessoal. Depois que o dispositivo é registrado, o Windows, em seguida, une seu dispositivo à rede, para que você possa usar o nome de usuário e a senha da sua organização para entrar e acessar recursos restritos.

- Opcionalmente, com base nas opções da sua organização, você pode ser solicitado a configurar a verificação em duas etapas por meio da [autenticação multifator](multi-factor-authentication-end-user-first-time.md) ou das [informações de segurança](user-help-security-info-overview.md).

- Opcionalmente, com base nas opções da sua organização, você pode ser registrado automaticamente no gerenciamento de dispositivo móvel, como Microsoft Intune. Para obter mais informações sobre o registro em Microsoft Intune, consulte [registrar seu dispositivo no Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Você passará pelo processo de entrada, usando a entrada automática com sua conta institucional.

## <a name="to-join-a-brand-new-windows-10-device"></a>Para ingressar em uma marca-novo dispositivo Windows 10
Se seu dispositivo tiver uma marca nova e ainda não tiver sido configurado, você poderá passar pelo processo OOBE (Windows out of Box Experience) para ingressar seu dispositivo na rede.

1. Inicie o novo dispositivo e inicie o processo OOBE.

2. Na tela **entrar com a conta da Microsoft** , digite seu endereço de email corporativo ou de estudante.

    ![Tela de entrada com endereço de email](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na tela **Insira sua senha** , digite sua senha.

    ![Insira sua tela de senha](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Em seu dispositivo móvel, aprove seu dispositivo para que ele possa acessar sua conta. 

    ![Tela de notificação móvel](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Conclua o processo OOBE, incluindo a definição de suas configurações de privacidade e a configuração do Windows Hello (se necessário).

    Seu dispositivo agora está ingressado na rede da sua organização.

## <a name="to-make-sure-youre-joined"></a>Para certificar-se de que você está associado
Você pode certificar-se de que você está associado examinando suas configurações.

1. Abra **configurações**e, em seguida, selecione **contas**.

    ![Contas na tela de configurações](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **acesso corporativo ou de estudante**e verifique se você vê texto que diz algo como, **conectado a *\<your_organization >* Azure ad**.

    ![Acessar a tela corporativa ou de estudante com a conta contoso conectada](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Para ingressar em um dispositivo do Windows 10 já configurado
Se você tiver seu dispositivo por algum tempo e ele já tiver sido configurado, você poderá seguir estas etapas para ingressar seu dispositivo na rede.

1. Abra **configurações**e, em seguida, selecione **contas**.

2. Selecione **acesso corporativo ou de estudante**e, em seguida, selecione **conectar**.

    ![Acessar links corporativos ou de estudante e conectar](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na tela **Configurar uma conta corporativa ou de estudante** , selecione **Adicionar este dispositivo a Azure Active Directory**.

    ![Configurar uma tela de conta corporativa ou de estudante](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na tela **vamos** entrar, digite seu endereço de email (por exemplo, alain@contoso.com) e, em seguida, selecione **Avançar**.

    ![Vamos entrar na tela](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na tela **digitar senha** , digite sua senha e, em seguida, selecione **entrar**.

    ![Inserir senha](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Em seu dispositivo móvel, aprove seu dispositivo para que ele possa acessar sua conta. 

    ![Tela de notificação móvel](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na tela Verifique **se esta é a sua organização** , examine as informações para verificar se ela está correta e, em seguida, selecione **ingressar**.

    ![Verifique se esta é a tela de verificação da sua organização](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na tela **você está** pronto, clique em **concluído**.

    ![Você está pronto para todas as telas](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Para certificar-se de que você está associado
Você pode certificar-se de que você está associado examinando suas configurações.

1. Abra **configurações**e, em seguida, selecione **contas**.

    ![Contas na tela de configurações](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **acesso corporativo ou de estudante**e verifique se você vê texto que diz algo como, **conectado a *\<your_organization >* Azure ad**.

    ![Acessar a tela corporativa ou de estudante com a conta contoso conectada](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Passos seguintes
Depois de ingressar seu dispositivo na rede da sua organização, você deve ser capaz de acessar todos os seus recursos usando suas informações de conta corporativa ou de estudante.

- Se sua organização quiser que você registre seu dispositivo pessoal, como seu telefone, consulte [registrar seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

- Se sua organização for gerenciada usando Microsoft Intune e você tiver dúvidas sobre o registro, a entrada ou qualquer outro problema relacionado ao Intune, consulte o [conteúdo da ajuda do usuário do Intune](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).