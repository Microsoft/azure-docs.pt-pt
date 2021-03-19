---
title: Registar dispositivos pessoais na rede de uma organização - Azure AD
description: Saiba como registar o seu dispositivo pessoal na rede da sua organização para que possa aceder aos recursos protegidos da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90033052"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registe o seu dispositivo pessoal na rede da sua organização
Registe o seu dispositivo pessoal (normalmente um telefone ou tablet) na rede da sua organização. Depois de o seu dispositivo estar registado, poderá aceder aos recursos restritos da sua organização.

>[!Note]
>Este artigo utiliza um dispositivo Windows para fins de demonstração, mas também pode registar dispositivos com iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>O que acontece quando regista o seu dispositivo
Enquanto regista o seu dispositivo na rede da sua organização, as seguintes ações irão acontecer:

- O Windows regista o seu dispositivo na rede da sua organização.

- Opcionalmente, com base nas escolhas da sua organização, poderá ser-lhe solicitada a configuração de uma verificação em duas etapas através [de uma autenticação de dois fatores](multi-factor-authentication-end-user-first-time.md) ou [de informações](./security-info-setup-signin.md)de segurança.

- Opcionalmente, com base nas escolhas da sua organização, poderá ser automaticamente inscrito na gestão de dispositivos móveis, como o Microsoft Intune. Para obter mais informações sobre a inscrição no Microsoft Intune, consulte [Inscrever o seu dispositivo no Intune.](/intune-user-help/enroll-your-device-in-intune-all)

- Você vai passar pelo processo de inscrição, usando o nome de utilizador e senha para o seu trabalho ou conta escolar.

## <a name="to-register-your-windows-device"></a>Para registar o seu dispositivo Windows

Siga estes passos para registar o seu dispositivo pessoal na sua rede.

1. Abrir **Definições** e, em seguida, selecionar **Contas**.

    ![Contas no ecrã De Definições](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Access work ou escola**, e, em seguida, selecione **Connect** a partir do trabalho de Acesso ou ecrã **escolar.**

    ![Trabalho de acesso ou ecrã escolar com opção Connect em destaque](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. No ecrã de **conta de trabalho ou escola, digite** o seu endereço de e-mail para o seu trabalho ou conta escolar e, em seguida, selecione **Next**. Por exemplo, alain@contoso.com.

4. Inscreva-se na sua conta de trabalho ou escola e, em seguida, **selecione Iniciar sôm.**

5. Complete o resto do processo de registo, incluindo a aprovação do seu pedido de verificação de identidade (se utilizar a verificação em duas etapas) e a configuração do Windows Hello (se necessário).

## <a name="to-verify-that-youre-registered"></a>Para verificar se está registado
Pode certificar-se de que está registado olhando para as suas definições.

1. Abrir **Definições** e, em seguida, selecionar **Contas**.

    ![Contas no ecrã De Definições](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Access work ou escola**, e certifique-se de ver o seu trabalho ou conta escolar.

    ![Trabalho de acesso ou tela escolar com conta contoso conectada](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Passos seguintes
Depois de registar o seu dispositivo pessoal na rede da sua organização, deverá poder aceder à maioria dos seus recursos.

- Se a sua organização quiser que se junte ao seu dispositivo de trabalho, consulte [junte-se ao seu dispositivo de trabalho na rede da sua organização.](user-help-join-device-on-network.md)