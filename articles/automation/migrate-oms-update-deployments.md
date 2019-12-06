---
title: Migrar suas implantações de atualização do OMS para o Azure
description: Este artigo descreve como migrar suas implantações de atualização do OMS existentes para o Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 08b3f11f3e44c6580df9942aab2a890115c79ba3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849501"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrar suas implantações de atualização do OMS para o Azure

O portal do OMS (Operations Management Suite) está sendo [preterido](../azure-monitor/platform/oms-portal-transition.md). Todas as funcionalidades disponíveis no portal do OMS para Gerenciamento de Atualizações estão disponíveis na portal do Azure. Este artigo fornece as informações de que você precisa para migrar para o portal do Azure.

## <a name="key-information"></a>Informação importante

* As implantações existentes continuarão a funcionar. Depois de recriar a implantação no Azure, você poderá excluir a implantação antiga do OMS.
* Todos os recursos existentes que você tinha no OMS estão disponíveis no Azure, para saber mais sobre Gerenciamento de Atualizações, consulte [Gerenciamento de atualizações visão geral](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Acessar o portal do Azure

No espaço de trabalho do OMS, clique em **abrir no Azure**. Isso navega para o espaço de trabalho Log Analytics usado pelo OMS.

![Abrir no Azure – portal do OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Na portal do Azure, clique em **conta de automação**

![Registos do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na sua conta de automação, clique em **Gerenciamento de atualizações** para abrir gerenciamento de atualizações.

![Gestão de Atualizações](media/migrate-oms-update-deployments/azure-automation.png)

No futuro, você pode ir diretamente para o portal do Azure, em **todos os serviços**, selecionar **contas de automação** em ferramentas de **Gerenciamento**, selecionar a conta de automação apropriada e clicar em **Gerenciamento de atualizações**.

## <a name="recreate-existing-deployments"></a>Recriar implantações existentes

Todas as implantações de atualização criadas no portal do OMS têm uma [pesquisa salva](../azure-monitor/platform/computer-groups.md) também conhecida como um grupo de computadores, com o mesmo nome da implantação de atualização que existe. A pesquisa salva contém a lista de computadores que foram agendados na implantação da atualização.

![Gestão de Atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para usar essa pesquisa salva existente, siga estas etapas:

Para criar uma nova implantação de atualização, vá para o portal do Azure, selecione a conta de automação que é usada e clique em **Gerenciamento de atualizações**. Clique em **agendar implantação de atualização**.

![Agendar a implementação da atualização](media/migrate-oms-update-deployments/schedule-update-deployment.png)

O **novo painel implantação de atualizações** é aberto. Insira valores para as propriedades descritas na tabela a seguir e clique em **criar**:

Para computadores a serem atualizados, selecione a pesquisa salva usada pela implantação do OMS existente.

| Propriedade | Descrição |
| --- | --- |
|Nome |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Selecione **Linux** ou **Windows**.|
|Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou escolha a máquina na lista suspensa e selecione computadores individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização de que você precisa. O CentOS não dá suporte a isso pronto para uso.|
|Atualizações a serem excluídas|Insira as atualizações a serem excluídas. Para o Windows, insira o artigo da base de conhecimento sem o prefixo **KB** . Para o Linux, insira o nome do pacote ou use um caractere curinga.  |
|Definições da agenda|Selecione a hora para iniciar e, em seguida, selecione **uma vez** ou **recorrente** para a recorrência. | 
| Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos ou maior que 6 horas. |
| Controle de reinicialização| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

Clique em **implantações de atualização agendada** para exibir o status da implantação de atualização criada recentemente.

![nova implantação de atualizações](media/migrate-oms-update-deployments/new-update-deployment.png)

Conforme mencionado anteriormente, depois que as novas implantações são configuradas por meio do portal do Azure, as implantações existentes podem ser removidas do portal do OMS.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Gerenciamento de Atualizações no Azure, consulte [Gerenciamento de atualizações](automation-update-management.md)
