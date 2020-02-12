---
title: Definir tempo limite de inatividade ao nível do diretório para os utilizadores do portal Azure  Microsoft Docs
description: Os administradores podem impor o tempo máximo de inatividade antes de uma sessão ser assinada. A política de tempo limite de inatividade é definida ao nível do diretório.
services: azure-portal
keywords: configurações, intervalo
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 950580048f0496fd8436901938a5b6768c61bab6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132081"
---
# <a name="set-directory-level-inactivity-timeout"></a>Definir tempo limite de inatividade ao nível do diretório

A definição de tempo limite de inatividade ajuda a proteger os seus recursos de acesso não autorizado se os utilizadores se esquecerem de garantir a sua estação de trabalho. Quando um utilizador está inativo há algum tempo, a sua sessão do portal Azure é automaticamente assinada. Os administradores no papel de [Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) podem impor o tempo máximo de inatividade antes de uma sessão ser assinada. A definição de tempo limite de inatividade aplica-se ao nível do diretório. Para obter mais informações sobre diretórios, consulte a visão geral dos serviços de domínio do [diretório ativo.](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

## <a name="configure-the-inactive-timeout-setting"></a>Configure a definição inativa do tempo de descanso

Se é administrador global e pretende impor um intervalo de tempo inativo para todos os utilizadores do portal Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Definições** a partir do cabeçalho de página global.
3. Selecione o intervalo de tempo de nível de **diretório configurar**o texto de link .

    ![Screenshot mostrando definições do portal com texto de ligação realçado](./media/admin-timeout/settings.png)

4. Uma nova página é aberta. Na página de tempo limite de **inatividade do nível do diretório Configure,** selecione Ativar o nível de tempo **inativo para o portal Azure** ligar a definição.
5. Em seguida, insira as **Horas** e **Minutos** pelo tempo máximo que um utilizador pode ficar inativo antes de a sua sessão ser automaticamente assinada.
6. Selecione **Aplicar**.

    ![Screenshot mostrando página para definir tempo limite de inatividade nível de diretório](./media/admin-timeout/configure.png)

Para confirmar que a política de tempo limite de inatividade é definida corretamente, selecione **Notificações** do cabeçalho da página global. Verifique se está listada uma notificação de sucesso.

  ![Screenshot mostrando mensagem de notificação bem sucedida para timeout de inatividade de nível de diretório](./media/admin-timeout/confirmation.png)

A definição entra em vigor para novas sessões. Não se aplica imediatamente a nenhum utilizador que já tenha assinado.

> [!NOTE]
> Se um administrador tiver configurado uma definição de tempo de tempo de nível de diretório, os utilizadores podem anular a apólice e definir a sua própria duração inativa de inscrição. No entanto, o utilizador deve escolher um intervalo de tempo inferior ao definido ao nível do diretório.
>

## <a name="next-steps"></a>Passos seguintes

* [Detete as suas preferências do portal Azure](set-preferences.md)
* [Exportar ou elimine as definições de utilizador](azure-portal-export-delete-settings.md)
* [Ativar o alto contraste ou a alteração de tema](azure-portal-change-theme-high-contrast.md)
