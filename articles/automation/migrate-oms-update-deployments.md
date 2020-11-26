---
title: Migrar Azure Monitor regista atualização de implementações para o portal Azure
description: Este artigo diz como migrar o Azure Monitor regista a atualização das implementações para o portal Azure.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: a2226f55c829afa4316a92888d16f6dc68e1f931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183605"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Migrar Azure Monitor regista atualização de implementações para o portal Azure

O portal Da Suite de Gestão de Operações (OMS) está a ser [depreciado.](../azure-monitor/platform/oms-portal-transition.md) Todas as funcionalidades que estavam disponíveis no portal OMS para Gestão de Atualização estão disponíveis no portal Azure, através dos registos do Azure Monitor. Este artigo fornece a informação necessária para migrar para o portal Azure.

## <a name="key-information"></a>Informações-chave

* As missões existentes continuarão a funcionar. Uma vez recriado a implantação em Azure, pode eliminar a sua antiga implantação.
* Todas as funcionalidades existentes que tinha no OMS estão disponíveis no Azure. Para saber mais sobre a Gestão de Atualização, consulte [a visão geral da Gestão de Atualização](./update-management/overview.md).

## <a name="access-the-azure-portal"></a>Aceder ao portal do Azure

1. A partir do seu espaço de trabalho, clique **em Abrir em Azure.** 

    ![Aberto em Azure - Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. No portal Azure, clique na **Conta Dem automação**

    ![Registos do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

3. Na sua conta de Automação, clique em **Gestão de Atualização.**

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Screenshot da página de gestão de atualização.":::

4. No portal Azure, selecione **Contas de Automação** em Todos os **serviços**. 

5. Em **Ferramentas de Gestão,** selecione a conta de Automação adequada e clique em **Gestão de Atualização.**

## <a name="recreate-existing-deployments"></a>Recriar as implementações existentes

Todas as implementações de atualização criadas no portal OMS têm uma [pesquisa guardada](../azure-monitor/platform/computer-groups.md) também conhecida como um grupo de computador, com o mesmo nome que a implementação da atualização que existe. A pesquisa guardada contém a lista de máquinas que estavam programadas na implementação da atualização.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Screenshot da página 'Actualização' com os campos Nome e Servidores destacados.":::

Para utilizar esta pesquisa guardada existente, siga estes passos:

1. Para criar uma nova implementação de atualização, vá ao portal Azure, selecione a conta Automation that is used e clique em **Update Management**. Clique **na implementação da atualização do calendário**.

    ![Implementação da atualização de horários](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. O painel de implementação da nova atualização abre. Introduza os valores para as propriedades descritas na tabela seguinte e, em seguida, clique em **Criar**:

3. Para **que as Máquinas se atualizem,** selecione a procura guardada utilizada pela implementação OMS.

    | Propriedade | Descrição |
    | --- | --- |
    |Nome |O nome exclusivo para identificar a implementação de atualizações. |
    |Sistema Operativo| Selecione **Linux** ou **Windows**.|
    |Máquinas a atualizar |Selecione uma pesquisa guardada, grupo importado ou escolha a Máquina a partir do dropdown e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para conhecer os diferentes métodos de criação de grupos informáticos em registos do Monitor Azure, consulte [grupos de computador em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md) |
    |Classificações de atualizações|Selecione todas as classificações de atualização que necessita. O CentOS não suporta isto fora da caixa.|
    |Atualizações para excluir|Introduza as atualizações para excluir. Para windows, introduza o artigo KB sem o prefixo **KB.** Para Linux, introduza o nome do pacote ou use um caractere wildcard.  |
    |Definições de agenda|Selecione a hora de partida e, em seguida, selecione **uma vez** ou **recorrente** para a recorrência. | 
    | Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos ou mais de 6 horas. |
    | Reiniciar o controlo| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

4. Clique **em implementações de atualização programadas** para ver o estado da implementação da atualização recentemente criada.

    ![nova implementação de atualização](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Como mencionado anteriormente, uma vez configuradas as suas novas implementações através do portal Azure, pode remover as implementações existentes do portal Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Gestão de Atualização na Azure Automation, consulte [a visão geral da Gestão de Atualização](./update-management/overview.md).