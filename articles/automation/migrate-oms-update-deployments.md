---
title: Migrate Azure Monitor regista atualizações para portal Azure
description: Este artigo diz como migrar o Azure Monitor atualiza ções de atualização para o portal Azure.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 9bd6a7ff943b5f3750ce8aaeada32010b88272c2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745632"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Migrate Azure Monitor regista atualizações para portal Azure

O portal De Gestão de Operações (OMS) está a ser [depreciado.](../azure-monitor/platform/oms-portal-transition.md) Toda a funcionalidade que estava disponível no portal OMS para Gestão de Atualizações está disponível no portal Azure, através de registos do Monitor Azure. Este artigo fornece a informação que precisa para migrar para o portal Azure.

## <a name="key-information"></a>Informação chave

* As missões existentes continuarão a funcionar. Depois de recriar a implantação em Azure, pode eliminar a sua antiga implantação.
* Todas as funcionalidades existentes que tinha em OMS estão disponíveis no Azure. Para saber mais sobre a Gestão de Atualizações, consulte a [visão geral da Gestão de Atualizações.](automation-update-management.md)

## <a name="access-the-azure-portal"></a>Aceder ao portal do Azure

1. A partir do seu espaço de trabalho, clique **em Open in Azure**. 

    ![Aberto em Azure - Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. No portal Azure, clique na **Conta de Automação**

    ![Registos do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

3. Na sua conta De automação, clique em **Gestão de Atualização**.

    ![Gestão de Atualizações](media/migrate-oms-update-deployments/azure-automation.png)

4. No portal Azure, selecione **Contas de Automação** em **todos os serviços.** 

5. Em **Ferramentas de Gestão,** selecione a conta de Automação apropriada e clique em **Gestão de Atualização**.

## <a name="recreate-existing-deployments"></a>Recriar as implementações existentes

Todas as implementações de atualizações criadas no portal OMS têm uma [pesquisa guardada](../azure-monitor/platform/computer-groups.md) também conhecida como um grupo de computador, com o mesmo nome que a implementação da atualização que existe. A pesquisa guardada contém a lista de máquinas que estavam programadas na implementação da atualização.

![Gestão de Atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para utilizar esta pesquisa guardada existente, siga estes passos:

1. Para criar uma nova implementação de atualização, vá ao portal Azure, selecione a conta Automation que é utilizada e clique em Gestão de **Atualização**. Clique na **implementação da atualização de agenda**.

    ![Programar atualização de implementação](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Abre-se o painel de implementação da nova atualização. Introduza valores para as propriedades descritas na tabela a seguir e clique em **Criar:**

3. Para que as **Máquinas atualizem,** selecione a procura guardada utilizada pela implementação oMS.

    | Propriedade | Descrição |
    | --- | --- |
    |Name |O nome exclusivo para identificar a implementação de atualizações. |
    |Sistema Operativo| Selecione **Linux** ou **Windows**.|
    |Máquinas para atualizar |Selecione uma pesquisa Saved, grupo importado ou escolha máquina a partir do dropdown e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos em registos do Monitor Azure](../azure-monitor/platform/computer-groups.md) |
    |Classificações de atualizações|Selecione todas as classificações de atualização de que necessita. O CentOS não suporta isto fora da caixa.|
    |Atualizações para excluir|Introduza as atualizações para excluir. Para Windows, introduza o artigo KB sem o prefixo **KB.** Para linux, introduza o nome do pacote ou use um personagem wildcard.  |
    |Configurações de agenda|Selecione a hora de iniciar e, em seguida, selecione **uma vez** ou **recorrente** para a recorrência. | 
    | Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos ou mais de 6 horas. |
    | Controlo de reiniciar| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

4. Clique em **atualizações agendadas** para ver o estado da implementação da atualização recém-criada.

    ![nova implementação de atualização](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Como mencionado anteriormente, uma vez configuradas as suas novas implementações através do portal Azure, pode remover as implementações existentes do portal Azure.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a Gestão de Atualizações na Automação Azure, consulte a [visão geral da Gestão de Atualizações.](automation-update-management.md)
