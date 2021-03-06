---
title: Remover VMs de arranque/paragem da automatização do Azure durante a visão geral fora de horas
description: Este artigo descreve como remover os VMs iniciar/parar durante o período de folga e desvincular uma conta de Automação do espaço de trabalho Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533953"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Remover VMs de início/paragem durante as horas fora da conta de Automação

Depois de ativar os VMs iniciar/parar durante o período de folga para gerir o estado de funcionamento dos seus VMs Azure, poderá decidir parar de o utilizar. A remoção desta função pode ser feita utilizando um dos seguintes métodos baseados nos modelos de implantação suportados:

* Elimine o grupo de recursos que contém a conta Automation e linked Azure Monitor Log Analytics, cada um dedicado a suportar esta funcionalidade.
* Desvincula o espaço de trabalho do Log Analytics da conta Automation e elimina a conta Automation dedicada a esta funcionalidade.
* Elimine a funcionalidade de uma conta De automação e espaço de trabalho ligado que suporte outros objetivos de gestão e monitorização.

A eliminação desta funcionalidade apenas remove os runbooks associados, não elimina os horários ou variáveis que foram criados durante a implementação ou quaisquer livros personalizados criados posteriormente.

> [!NOTE]
> Antes de prosseguir, verifique se não existem [quaisquer bloqueios do Gestor de Recursos](../azure-resource-manager/management/lock-resources.md) aplicados na subscrição, grupo de recursos ou recursos que impeçam a eliminação acidental ou modificação de recursos críticos. Quando implementa os VMs iniciar/parar durante a solução fora de horas, define o nível de bloqueio para **CanNotDelete** contra vários recursos dependentes na conta Automation (especificamente os seus runbooks e variáveis). Todas as fechaduras têm de ser removidas antes de poder eliminar a conta Automação.

## <a name="delete-the-dedicated-resource-group"></a>Eliminar o grupo de recursos dedicado

Para eliminar o grupo de recursos, siga os passos descritos no grupo de [recursos Azure Resource Manager e](../azure-resource-manager/management/delete-resource-group.md) no artigo de eliminação de recursos.

## <a name="delete-the-automation-account"></a>Eliminar a conta De automação

Para eliminar a sua conta Dems automatização dedicada a VMs de início/paragem durante o horário de folga, execute os seguintes passos.

1. Inscreva-se no Azure at [https://portal.azure.com](https://portal.azure.com) .

2. Navegue para a sua conta de Automação e selecione **espaço de trabalho linked** under Related **resources**.

3. Selecione **Vá para o espaço de trabalho**.

4. Clique em **Soluções** em **Geral**.

5. Na página Soluções, selecione **Start-Stop-VM[Workspace]**.

6. Na página **VMManagementSolution[Workspace],** **selecione Delete** do menu.

7. Enquanto a informação é verificada e a funcionalidade é eliminada, pode acompanhar o progresso em Notificações , **escolhidas** a partir do menu. É devolvido à página Soluções após o processo de remoção.

### <a name="unlink-workspace-from-automation-account"></a>Desassociar uma área de trabalho da conta de Automatização

Existem duas opções para desvincular o espaço de trabalho Log Analytics da sua conta Automation. Pode realizar este processo a partir da conta Automation ou do espaço de trabalho ligado.

Para desvincular-se da sua conta Automation, execute os seguintes passos.

1. No portal Azure, selecione **Contas de Automação.**

2. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

3. Na página do **espaço de trabalho Unlink,** selecione **o espaço de trabalho Unlink** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Enquanto tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Para desvincular-se do espaço de trabalho, execute os seguintes passos.

1. No portal Azure, selecione **Log Analytics workspaces**.

2. A partir do espaço de trabalho, **selecione Conta de Automação** em **Recursos Relacionados.**

3. Na página 'Conta Dem automação', selecione **'Desvincular's** e responda às solicitações.

Enquanto tenta desvincular a conta Automation, pode acompanhar o progresso no menu das **Notificações.**

### <a name="delete-automation-account"></a>Eliminar conta de Automação

1. No portal Azure, selecione **Contas de Automação.**

2. Abra a sua conta Dem automação e **selecione Eliminar** do menu.

Enquanto a informação é verificada e a conta é eliminada, pode acompanhar o progresso nas Notificações , **escolhidas** a partir do menu.

## <a name="delete-the-feature"></a>Eliminar a funcionalidade

Para eliminar VMs de início/paragem durante o horário de folga da sua conta Demôm automação, execute os seguintes passos. A conta de automação e o espaço de trabalho Log Analytics não são eliminados como parte deste processo. Se não quiser manter o espaço de trabalho do Log Analytics, deve eliminá-lo manualmente. Para obter mais informações sobre a eliminação do seu espaço de trabalho, consulte [Delete e recupere o espaço de trabalho do Azure Log Analytics.](../azure-monitor/logs/delete-workspace.md)

1. Navegue para a sua conta de Automação e selecione **espaço de trabalho linked** under Related **resources**.

2. Selecione **Vá para o espaço de trabalho**.

3. Clique em **Soluções** em **Geral**.

4. Na página Soluções, selecione **Start-Stop-VM[Workspace]**.

5. Na página **VMManagementSolution[Workspace],** **selecione Delete** do menu.

    ![Excluir recurso de gestão VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela Solução Eliminar, confirme que pretende eliminar a função.

7. Enquanto a informação é verificada e a funcionalidade é eliminada, pode acompanhar o progresso em Notificações , **escolhidas** a partir do menu. É devolvido à página Soluções após o processo de remoção.

8. Se não quiser manter os [recursos](automation-solution-vm-management.md#components) criados pela funcionalidade ou por si posteriormente (tais como variáveis, horários, etc.), tem de os eliminar manualmente da conta.

## <a name="next-steps"></a>Passos seguintes

Para voltar a ativar esta função, consulte [Ativar Iniciar/Parar durante as horas de folga](automation-solution-vm-management-enable.md).