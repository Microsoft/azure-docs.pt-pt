---
title: Junte o seu dispositivo de trabalho à rede da sua organização - AD
description: Saiba como juntar o seu dispositivo de trabalho à rede da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536975"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Junte-se ao seu dispositivo de trabalho na rede da sua organização
Junte-se ao seu dispositivo windows 10 de propriedade laboral na rede da sua organização para que possa aceder a recursos potencialmente restritos.

## <a name="what-happens-when-you-join-your-device"></a>O que acontece quando se junta ao seu dispositivo
Enquanto se junta ao seu dispositivo Windows 10 na rede da sua organização, as seguintes ações irão acontecer:

- O Windows regista o seu dispositivo na rede da sua organização, permitindo-lhe aceder aos seus recursos utilizando a sua conta pessoal. Depois de o seu dispositivo estar registado, o Windows junta-se então ao seu dispositivo à rede, para que possa utilizar o nome de utilizador e a palavra-passe da sua organização para iniciar sôm e aceder a recursos restritos.

- Opcionalmente, com base nas escolhas da sua organização, poderá ser-lhe solicitada a configuração de uma verificação em duas etapas através de [uma autenticação multi-factor](multi-factor-authentication-end-user-first-time.md) ou [de informações de segurança.](./security-info-setup-signin.md)

- Opcionalmente, com base nas escolhas da sua organização, poderá ser automaticamente inscrito na gestão de dispositivos móveis, como o Microsoft Intune. Para obter mais informações sobre a inscrição no Microsoft Intune, consulte [Inscrever o seu dispositivo no Intune.](/intune-user-help/enroll-your-device-in-intune-all)

- Você vai passar pelo processo de inscrição, usando o sign-in automático com a sua conta organizacional.

## <a name="to-join-a-brand-new-windows-10-device"></a>Para aderir a um novo dispositivo Windows 10
Se o seu dispositivo for novo e ainda não tiver sido configurado, pode passar pelo processo Windows out of Box Experience (OOBE) para se juntar ao seu dispositivo na rede.

1. Inicie o seu novo dispositivo e inicie o processo OOBE.

2. No **'Iniciar s-in' com** o ecrã da Microsoft, digite o seu endereço de e-mail de trabalho ou escola.

    ![Inscreva-se no ecrã com endereço de e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. No ecrã de inserir a **sua palavra-passe,** digite a sua palavra-passe.

    ![Insira o seu ecrã de senha](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. No seu dispositivo móvel, aprove o seu dispositivo para que possa aceder à sua conta. 

    ![Screenshot que mostra o ecrã de notificação "Insira a sua palavra-passe".](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Preencha o processo OOBE, incluindo a definição das suas definições de privacidade e a configuração do Windows Hello (se necessário).

    O seu dispositivo está agora unido à rede da sua organização.

## <a name="to-make-sure-youre-joined-new-device"></a>Para ter a certeza de que está associado (novo dispositivo)
Pode certificar-se de que está unido olhando para as suas definições.

1. Abrir **Definições** e, em seguida, selecionar **Contas**.

    ![Contas no ecrã De Definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **Access work ou escola**, e certifique-se de ver texto que diz algo como, Ligado a ***\<your_organization>* AD AZure**.

    ![Screenshot que mostra a janela "Access work or school" com a conta "Connected to (your organization) AD" selecionada.](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Para aderir a um dispositivo já configurado Do Windows 10
Se já tem o seu dispositivo há algum tempo e já foi configurado, pode seguir estes passos para se juntar ao seu dispositivo na rede.

1. Abrir **Definições** e, em seguida, selecionar **Contas**.

2. Selecione **Access work ou escola**, e, em seguida, selecione **Connect**.

    ![Acesso ao trabalho ou escola e links de ligação](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. No conjunto de um ecrã **de conta de trabalho ou escola,** selecione Junte este dispositivo ao **Diretório Ativo Azure**.

    ![Configurar um ecrã de conta de trabalho ou escola](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. No **Let's get you signed in** screen, type your email address (por exemplo, ) e, em alain@contoso.com seguida, selecione **Next**.

    ![Vamos assinar no ecrã.](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. No ecrã **de senha de entrada,** digite a sua palavra-passe e, em seguida, selecione **Iniciar sôm.**

    ![Introduzir palavra-passe](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. No seu dispositivo móvel, aprove o seu dispositivo para que possa aceder à sua conta. 

    ![Tela de notificação móvel](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. No the **Certifie-se de que este é o ecrã** da sua organização, reveja as informações para se certificar de que está certo e, em seguida, selecione **'Juntar-se'.**

    ![Certifique-se de que este é o seu ecrã de verificação da organização](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. No ecrã **do Seu conjunto,** clique em **'Fazer'.**

    ![Você está tudo definido ecrã](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Para ter certeza de que está associado
Pode certificar-se de que está unido olhando para as suas definições.

1. Abrir **Definições** e, em seguida, selecionar **Contas**.

    ![Contas no ecrã De Definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **Access work ou escola**, e certifique-se de ver texto que diz algo como, Ligado a ***\<your_organization>* AD AZure**.

    ![Trabalho de acesso ou tela escolar com conta contoso conectada](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Passos seguintes
Depois de se juntar ao seu dispositivo na rede da sua organização, deverá poder aceder a todos os seus recursos utilizando a informação da sua conta de trabalho ou da sua escola.

- Se a sua organização quiser que registe o seu dispositivo pessoal, como o seu telemóvel, consulte [registar o seu dispositivo pessoal na rede da sua organização.](user-help-register-device-on-network.md)

- Se a sua organização for gerida utilizando o Microsoft Intune e tiver dúvidas sobre a inscrição, o sôm-in ou qualquer outro problema relacionado com o Intune, consulte o conteúdo de ajuda do [utilizador Intune.](/intune-user-help/use-managed-devices-to-get-work-done)