---
title: Junte-se ao seu dispositivo de trabalho na rede da sua organização - AD
description: Aprenda a aderir ao seu dispositivo de trabalho na rede da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266315"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Junte-se ao seu dispositivo de trabalho na rede da sua organização
Junte-se ao seu dispositivo Windows 10 de propriedade própria na rede da sua organização para que possa aceder a recursos potencialmente restritos.

## <a name="what-happens-when-you-join-your-device"></a>O que acontece quando se junta ao seu dispositivo
Enquanto está a juntar o seu dispositivo Windows 10 à rede da sua organização, as seguintes ações irão acontecer:

- O Windows regista o seu dispositivo na rede da sua organização, permitindo-lhe aceder aos seus recursos através da sua conta pessoal. Depois de registado o seu dispositivo, o Windows junta-se ao seu dispositivo na rede, para que possa utilizar o nome de utilizador e a palavra-passe da sua organização para iniciar sessão e aceder a recursos restritos.

- Opcionalmente, com base nas escolhas da sua organização, pode ser-lhe pedido que efetue uma verificação em duas etapas através da [autenticação multi-factor](multi-factor-authentication-end-user-first-time.md) ou da informação de [segurança.](user-help-security-info-overview.md)

- Opcionalmente, com base nas escolhas da sua organização, poderá estar automaticamente inscrito na gestão de dispositivos móveis, como o Microsoft Intune. Para obter mais informações sobre a inscrição no Microsoft Intune, consulte [Matricular o seu dispositivo em Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Você vai passar pelo processo de inscrição, usando o sessão automático com a sua conta organizacional.

## <a name="to-join-a-brand-new-windows-10-device"></a>Para se juntar a um novo dispositivo Windows 10
Se o seu dispositivo for novo e ainda não tiver sido configurado, pode passar pelo processo Windows out of Box Experience (OOBE) para se juntar ao seu dispositivo na rede.

1. Inicie o seu novo dispositivo e inicie o processo OOBE.

2. No **signine com** o ecrã da Microsoft, escreva o seu endereço de e-mail ou de trabalho escolar.

    ![Inscreva-se no ecrã com endereço de e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. No **ecrã de inserir a sua palavra-passe,** escreva a sua palavra-passe.

    ![Introduza o seu ecrã de senha](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. No seu dispositivo móvel, aprove o seu dispositivo para que possa aceder à sua conta. 

    ![Tela de notificação móvel](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Complete o processo OOBE, incluindo a definição das definições de privacidade e a configuração do Windows Hello (se necessário).

    O seu dispositivo está agora unido à rede da sua organização.

## <a name="to-make-sure-youre-joined"></a>Para ter certeza de que está se juntando
Pode certificar-se de que está acompanhado olhando para as suas definições.

1. Abra **as Definições**e, em seguida, selecione **Contas**.

    ![Contas no ecrã Definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **Access work ou school**, e certifique-se de ver texto que diga algo como, Ligado a ** * \<your_organization>* AD Azure**.

    ![Acesso ao trabalho ou tela escolar com conta de contoso conectado](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Para se juntar a um dispositivo já configurado do Windows 10
Se já tem o seu dispositivo há algum tempo e já foi configurado, pode seguir estes passos para se juntar ao seu dispositivo à rede.

1. Abra **as Definições**e, em seguida, selecione **Contas**.

2. Selecione **O trabalho de acesso ou escola**e, em seguida, selecione **Connect**.

    ![Trabalho de acesso ou ligações escolares e ligações de ligação](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. No Conjunto de um ecrã de conta de **trabalho ou de escola,** selecione Juntar este dispositivo ao **Diretório Ativo Azure**.

    ![Configurar um ecrã de conta de trabalho ou escolar](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. No **Let's get you signed in** screen, type alain@contoso.comyour email address (por exemplo, ) e, em seguida, selecione **Next**.

    ![Vamos assinar no ecrã.](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. No ecrã de **palavra-passe Introduza,** escreva a sua palavra-passe e, em seguida, selecione **Iniciar sessão**.

    ![Introduzir palavra-passe](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. No seu dispositivo móvel, aprove o seu dispositivo para que possa aceder à sua conta. 

    ![Tela de notificação móvel](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. No **ecrã certifique-se** de que este é o ecrã da sua organização, reveja as informações para se certificar de que está certo e, em seguida, selecione **Join**.

    ![Certifique-se de que este é o seu ecrã de verificação de organização](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. No ecrã De **Conjunto You're All,** clique **em Done**.

    ![Você é todo o set screen](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Para ter certeza de que está se juntando
Pode certificar-se de que está acompanhado olhando para as suas definições.

1. Abra **as Definições**e, em seguida, selecione **Contas**.

    ![Contas no ecrã Definições](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecione **Access work ou school**, e certifique-se de ver texto que diga algo como, Ligado a ** * \<your_organization>* AD Azure**.

    ![Acesso ao trabalho ou tela escolar com conta de contoso conectado](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Passos seguintes
Depois de se juntar ao seu dispositivo na rede da sua organização, deverá poder aceder a todos os seus recursos utilizando o seu trabalho ou informação sobre contas escolares.

- Se a sua organização quiser que registe o seu dispositivo pessoal, como o seu telemóvel, consulte O Registo do [seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

- Se a sua organização for gerida usando o Microsoft Intune e tiver dúvidas sobre inscrição, registo ou qualquer outro problema relacionado com o [Intune,](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done)consulte o conteúdo de ajuda do utilizador Intune .