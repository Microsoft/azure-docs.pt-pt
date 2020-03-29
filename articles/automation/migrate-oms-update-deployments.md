---
title: Migrar as suas implementações de atualização OMS para o Azure
description: Este artigo descreve como migrar as suas implementações de Atualização OMS existentes para o Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417772"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrar as suas implementações de atualização OMS para o Azure

O portal De Gestão de Operações (OMS) está a ser [depreciado.](../azure-monitor/platform/oms-portal-transition.md) Toda a funcionalidade que estava disponível no portal OMS para Gestão de Atualizações está disponível no portal Azure. Este artigo fornece as informações de que necessita para migrar para o portal Azure.

## <a name="key-information"></a>Informação chave

* As missões existentes continuarão a funcionar. Uma vez recriada a implantação em Azure, pode eliminar a sua antiga implantação a partir de OMS.
* Todas as funcionalidades existentes que teve no OMS estão disponíveis no Azure, para saber mais sobre a Gestão de Atualizações, consulte a [visão geral da Atualização .](automation-update-management.md)

## <a name="access-the-azure-portal"></a>Aceder ao portal do Azure

A partir do seu espaço de trabalho OMS, clique **em Open in Azure**. Isto navega para o espaço de trabalho log Analytics que o OMS usou.

![Aberto em Portal Azure - OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

No portal Azure, clique na **Conta de Automação**

![Registos do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na sua Conta de Automação, clique em **Gestão de Atualização** para abrir a Gestão de Atualizações.

![Gestão de Atualizações](media/migrate-oms-update-deployments/azure-automation.png)

No futuro poderá ir diretamente ao portal Azure, sob **todos os serviços,** selecionar **Contas de Automação** em Ferramentas de **Gestão,** selecionar a Conta de Automação apropriada e clicar em Gestão de **Atualização.**

## <a name="recreate-existing-deployments"></a>Recriar as implementações existentes

Todas as implementações de atualizações criadas no portal OMS têm uma [pesquisa guardada](../azure-monitor/platform/computer-groups.md) também conhecida como um grupo de computador, com o mesmo nome que a implementação da atualização que existe. A pesquisa guardada contém a lista de máquinas que estavam programadas na implementação da atualização.

![Gestão de Atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para utilizar esta pesquisa guardada existente, siga estes passos:

Para criar uma nova implementação de atualização, vá ao portal Azure, selecione a Conta de Automação que é utilizada e clique em Gestão de **Atualização**. Clique na **implementação da atualização de agenda**.

![Programar atualização de implementação](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Abre-se o painel de implementação da **nova atualização.** Introduza valores para as propriedades descritas na tabela a seguir e clique em **Criar:**

Para que as Máquinas atualizem, selecione a procura guardada utilizada pela implementação oMS existente.

| Propriedade | Descrição |
| --- | --- |
|Nome |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Selecione **Linux** ou **Windows**.|
|Máquinas para atualizar |Selecione uma pesquisa Saved, grupo importado ou escolha máquina a partir da gota-para-baixo e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização de que necessita. O CentOS não suporta isto fora da caixa.|
|Atualizações para excluir|Introduza as atualizações para excluir. Para Windows, introduza o artigo KB sem o prefixo **KB.** Para linux, introduza o nome do pacote ou use um personagem wildcard.  |
|Configurações de agenda|Selecione a hora de iniciar e, em seguida, selecione **uma vez** ou **recorrente** para a recorrência. | 
| Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos ou mais de 6 horas. |
| Controlo de reiniciar| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

Clique em **atualizações agendadas** para ver o estado da implementação da atualização recém-criada.

![nova implementação de atualização](media/migrate-oms-update-deployments/new-update-deployment.png)

Como mencionado anteriormente, uma vez configuradas as suas novas implementações através do portal Azure, as implementações existentes podem ser removidas do portal OMS.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Gestão de Atualizações no Azure, consulte [Gestão de Atualizações](automation-update-management.md)
