---
title: Gerir VMs de início/paragem v2 (pré-visualização)
description: Este artigo diz como monitorizar o estado dos seus VMs Azure geridos pela função Iniciar/Parar VMs v2 (pré-visualização) e executar outras tarefas de gestão.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112184"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Como gerir VMs de início/paragem v2 (pré-visualização)

## <a name="azure-dashboard"></a>Dashboard do Azure

Start/Stop VMs v2 (pré-visualização) inclui um [dashboard](../../azure-monitor/visualizations.md#azure-dashboards) para ajudá-lo a entender o âmbito de gestão e as operações recentes contra os seus VMs. É uma forma rápida e fácil de verificar o estado de cada operação que é realizada nos seus VMs Azure. A visualização em cada azulejo baseia-se numa consulta de Log e para ver a consulta, selecione a opção **Abrir na lâmina de troncos** no canto direito do azulejo. Isto abre a ferramenta [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) no portal Azure, e a partir daqui pode avaliar a consulta e modificar para suportar as suas necessidades, tais como alertas de [registo personalizados,](../../azure-monitor/alerts/alerts-log.md)um [livro](../../azure-monitor/visualize/workbooks-overview.md)personalizado, etc.

Os dados de registo de cada azulejo nos ecrãs do painel de instrumentos são atualizados a cada hora, com uma opção de atualização manual a pedido clicando no ícone **Refresh** numa determinada visualização, ou refrescando o painel completo.

Para aprender a trabalhar com um dashboard baseado em log, consulte o seguinte [tutorial](../../azure-monitor/visualize/tutorial-logs-dashboards.md).

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar notificações de e-mail após o início/paragem de VMs v2 (pré-visualização) é implementado, pode modificar o grupo de ação criado durante a implementação.

1. No portal Azure, navegue para **Monitor,** depois **Alertas**. Selecione **Gerir ações**.

1. Na página **'Gerir acções',** selecione o grupo de ação chamado **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Screenshot da página grupos de ação.":::

1. Na página **StartStopV2_VM_Notification,** pode modificar as opções de notificação por e-mail/SMS/Push/Voice. Adicione outras ações ou atualize a configuração existente neste grupo de ação e, em seguida, clique em **OK** para guardar as suas alterações.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Screenshot da página email/SMS/Push/Voice mostrando um endereço de e-mail de exemplo atualizado.":::

    Para saber mais sobre grupos de ação, consulte [grupos de ação](../../azure-monitor/alerts/action-groups.md)

A imagem a seguir é um e-mail de exemplo que é enviado quando a funcionalidade desliga as máquinas virtuais.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Screenshot de um e-mail de exemplo enviado quando a funcionalidade desliga máquinas virtuais.":::

## <a name="next-steps"></a>Passos seguintes

Para lidar com problemas durante a gestão de VM, consulte [problemas de Início/Paragem de VMs v2](troubleshoot.md) (pré-visualização).