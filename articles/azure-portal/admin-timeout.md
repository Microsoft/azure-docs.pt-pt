---
title: Definir tempo de inatividade ao nível do diretório para os utilizadores do portal Azure Microsoft Docs
description: Os administradores podem impor o tempo máximo de marcha lenta antes de uma sessão ser assinada. A política de tempo de inatividade é definida ao nível do diretório.
services: azure-portal
keywords: definições, tempo limite
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2efee29ba709607e1e7935215ca7689a39966de1
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764167"
---
# <a name="set-directory-level-inactivity-timeout"></a>Definir tempo de inatividade ao nível do diretório

A definição de tempo de inatividade ajuda a proteger os seus recursos de acesso não autorizado se os utilizadores se esquecerem de proteger a sua estação de trabalho. Quando um utilizador está inativo há algum tempo, a sessão do portal Azure é automaticamente assinada. Os administradores na [função de Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) podem impor o tempo máximo de inatividade antes de uma sessão ser assinada. A definição de tempo de inatividade aplica-se ao nível do diretório. Para obter mais informações sobre diretórios, consulte a [Visão geral dos Serviços de Domínio do Diretório Ativo.](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

## <a name="configure-the-inactive-timeout-setting"></a>Configure a definição de tempo limite inativo

Se você é um Administrador Global, e deseja impor uma definição de tempo de tempo ocioso para todos os utilizadores do portal Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Definições** do cabeçalho de página global.
3. Selecione o tempo limite **de tempo do diretório de configuração do**texto de ligação .

    ![Screenshot mostrando as definições do portal com texto de ligação realçado](./media/admin-timeout/settings.png)

4. Uma nova página é aberta. Na página de **tempo limite de tempo de inatividade do nível de diretório configurar,** selecione Ativar o tempo limite de marcha lenta para o portal **Azure** para ativar a definição.
5. Em seguida, insira as **Horas** e **Minutos** pelo tempo máximo que um utilizador pode ficar inativo antes da sua sessão ser automaticamente assinada.
6. Selecione **Aplicar**.

    ![Screenshot mostrando página para definir tempo de inatividade de nível de diretório](./media/admin-timeout/configure.png)

Para confirmar que a política de tempo limite de inatividade está corretamente definida, selecione **Notificações** do cabeçalho da página global. Verifique se está listada uma notificação de sucesso.

  ![Screenshot mostrando mensagem de notificação bem sucedida para tempo de inatividade ao nível do diretório](./media/admin-timeout/confirmation.png)

A definição entra em vigor para novas sessões. Não se aplicará imediatamente a utilizadores que já estejam inscritos.

> [!NOTE]
> Se um Administrador Global tiver configurado uma definição de tempo limite de nível de diretório, os utilizadores podem anular a política e definir a sua própria duração de inscrição inativa. No entanto, o utilizador deve escolher um intervalo de tempo inferior ao definido ao nível do diretório pelo Administrador Global.
>

## <a name="next-steps"></a>Passos seguintes

* [Definir as preferências do portal do Azure](set-preferences.md)
* [Exportar ou eliminar as definições de utilizador](azure-portal-export-delete-settings.md)
* [Ativar o alto contraste ou a alteração de tema](azure-portal-change-theme-high-contrast.md)
