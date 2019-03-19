---
title: Migrar as implementações de atualização do OMS para o Azure
description: Este artigo descreve como migrar as implementações de atualização do OMS existentes para o Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4d11dfcb66a545cbecc80b6bdad558ca6d328ed2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999671"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrar as implementações de atualização do OMS para o Azure

O portal do Operations Management Suite (OMS) está sendo [preterido](../azure-monitor/platform/oms-portal-transition.md). Todas as funcionalidades que estava disponível no portal do OMS para gerenciamento de atualizações estão disponível no portal do Azure. Este artigo fornece as informações que necessárias para migrar para o portal do Azure.

## <a name="key-information"></a>Informações da chave

* As implementações existentes continuarão a funcionar. Depois de ter recriado a implementação no Azure, pode eliminar a implementação antiga do OMS.
* Todas as funcionalidades existentes, tinha no OMS estão disponíveis no Azure, para saber mais sobre a gestão de atualizações, consulte [descrição geral da gestão de atualizações](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Aceder ao portal do Azure

A partir da sua área de trabalho do OMS, clique em **aberto no Azure**. Navega para a área de trabalho do Log Analytics OMS utilizado.

![Abrir no Azure - portal do OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

No portal do Azure, clique em **conta de automatização**

![Registos do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na sua conta de automatização, clique em **gestão de atualizações** para abrir a gestão de atualizações.

![Gestão de Atualizações](media/migrate-oms-update-deployments/azure-automation.png)

No futuro pode ir diretamente para o portal do Azure, em **todos os serviços**, selecione **contas de automatização** sob **ferramentas de gestão**, selecione a automação apropriadas Conta e clique em **gestão de atualizações**.

## <a name="recreate-existing-deployments"></a>Recriar as implementações existentes

Todas as implementações de atualização criadas no portal do OMS têm um [pesquisa guardada](../azure-monitor/platform/computer-groups.md) também conhecido como um grupo de computadores, com o mesmo nome que a implementação da atualização que existe. A pesquisa guardada contém a lista de máquinas que eram agendadas na implementação de atualização.

![Gestão de Atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para utilizar esta pesquisa guardada existente, siga estes passos:

Para criar uma nova atualização de implementação, aceda ao portal do Azure, selecione a conta de automatização que é utilizado e clique em **gestão de atualizações**. Clique em **agendar a implementação da atualização**.

![Agendar a implementação da atualização](media/migrate-oms-update-deployments/schedule-update-deployment.png)

O **nova implementação de atualização** painel abre-se. Introduza valores para as propriedades descritas na tabela seguinte e, em seguida, clique em **criar**:

Para as máquinas atualizar, selecione a pesquisa guardada utilizada pela implementação de OMS existente.

| Propriedade | Descrição |
| --- | --- |
|Name |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Selecione **Linux** ou **Windows**.|
|Computadores a atualizar |Selecione uma pesquisa guardada, grupo importada, ou escolher máquina da lista pendente e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos registos do Azure Monitor, consulte o artigo [grupos de computadores nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização que precisa. CentOS não suporta esta prontos a utilizar.|
|Atualizações a excluir|Introduza as atualizações a excluir. Para Windows, introduza o artigo KB sem o **KB** prefixo. Para o Linux, introduza o nome do pacote ou utilizar um caráter universal.  |
|Definições da agenda|Selecione a hora de começar e, em seguida, selecione **uma vez** ou **periódico** da periodicidade. | 
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos ou mais de seis horas. |
| Controlo de reinício| Determina como devem ser tratadas reinicializações.</br>As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

Clique em **implementações de atualização agendada** para ver o estado da implementação da atualização recém-criado.

![nova implementação de atualização](media/migrate-oms-update-deployments/new-update-deployment.png)

Como mencionado anteriormente, uma vez configuradas as novas implementações através do portal do Azure, as implementações existentes podem ser removidas do portal do OMS.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a gestão de atualizações no Azure, veja [gestão de atualizações](automation-update-management.md)
