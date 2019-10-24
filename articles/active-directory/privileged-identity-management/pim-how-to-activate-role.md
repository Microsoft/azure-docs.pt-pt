---
title: Ativar minhas funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como ativar as funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756294"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar minhas funções do Azure AD no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifica o modo como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.  

Se você se tornou qualificado para uma função administrativa, isso significa que você pode ativar essa função quando precisar executar ações privilegiadas. Por exemplo, se você ocasionalmente gerencia recursos do Office 365, os administradores de função com privilégios de sua organização podem não torná-lo um administrador global permanente, pois essa função também afeta outros serviços. Em vez disso, eles o tornam qualificado para funções do Azure AD, como administrador do Exchange Online. Você pode solicitar para ativar essa função quando precisar de seus privilégios e, em seguida, terá controle de administrador para um período de tempo predeterminado.

Este artigo é para os administradores que precisam ativar sua função do Azure AD no Privileged Identity Management.

## <a name="activate-a-role"></a>Ativar uma função

Quando precisar tomar uma função do Azure AD, você poderá solicitar a ativação usando a opção de navegação **minhas funções** no Privileged Identity Management.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**. Para obter informações sobre como adicionar o bloco Privileged Identity Management ao seu painel, consulte [começar a usar o Privileged Identity Management](pim-getting-started.md).

1. Clique em **funções do Azure ad**.

1. Clique em **minhas funções** para ver uma lista de suas funções qualificadas do Azure AD.

    ![Funções do Azure AD – minhas funções mostrando a lista de funções qualificadas ou ativas](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que você deseja ativar.

    ![Funções do Azure AD – minha lista de funções qualificadas mostrando o link ativar](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Ativar** para abrir o painel detalhes de ativação de função.

1. Se sua função exigir a autenticação multifator (MFA), clique em **verificar sua identidade antes de continuar**. Você só precisa autenticar uma vez por sessão.

    ![Verificar meu painel de identidade com MFA antes da ativação de função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **verificar minha identidade** e siga as instruções para fornecer verificação de segurança adicional.

    ![Página de verificação de segurança adicional perguntando como entrar em contato com você](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Ativar** para abrir o painel ativação.

    ![Painel ativação para especificar a hora de início, a duração, o tíquete e o motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa **motivo da ativação** , insira o motivo para a solicitação de ativação. Algumas funções exigem que você forneça um número de tíquete de problema.

    ![O painel de ativação foi concluído com uma hora de início personalizada, a duração, o tíquete e o motivo](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **Ativar**.

    Se a função não exigir aprovação, um painel **status de ativação** será exibido exibindo o status da ativação.

    ![Página status da ativação mostrando os três estágios da ativação](./media/pim-how-to-activate-role/activation-status.png)

    Depois que todos os estágios forem concluídos, clique **no link sair para sair do** portal do Azure. Quando você entra novamente no portal, agora você pode usar a função.

    Se a [função exigir aprovação](./azure-ad-pim-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com aprovação pendente.

    ![Notificação de aprovação pendente da solicitação de ativação](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status de suas solicitações pendentes para ativar.

1. Abra Azure AD Privileged Identity Management.

1. Clique em **funções do Azure ad**.

1. Clique em **minhas solicitações** para ver uma lista de suas solicitações.

    ![Funções do Azure AD – lista de minhas solicitações](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Depois que uma função é ativada, ela é desativada automaticamente quando seu limite de tempo (duração qualificada) é atingido.

Se você concluir as tarefas do administrador antecipadamente, também poderá desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra Azure AD Privileged Identity Management.

1. Clique em **funções do Azure ad**.

1. Clique em **minhas funções**.

1. Clique em **funções ativas** para ver sua lista de funções ativas.

1. Localize a função que você concluiu usando e clique em **desativar**.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Se você não precisar de ativação de uma função que exija aprovação, poderá cancelar uma solicitação pendente a qualquer momento.

1. Abra Azure AD Privileged Identity Management.

1. Clique em **funções do Azure ad**.

1. Clique em **minhas solicitações**.

1. Para a função que você deseja cancelar, clique no botão **Cancelar** .

    Quando você clicar em cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de solicitações com o botão Cancelar realçado](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando você ativa uma função no Privileged Identity Management, a ativação pode não ser propagada instantaneamente para todos os portais que exigem a função privilegiada. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a ativação estiver atrasada, aqui está o que você deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.

    Quando você ativar uma função do Azure AD, verá os estágios da ativação. Quando todas as fases estiverem concluídas, verá a ligação **Terminar sessão**. Você pode usar este link para sair. Isso irá resolver a maioria dos casos de atraso de ativação.

1. Em Privileged Identity Management, verifique se você está listado como o membro da função.

## <a name="next-steps"></a>Passos seguintes

- [Ativar minhas funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-activate-your-roles.md)
