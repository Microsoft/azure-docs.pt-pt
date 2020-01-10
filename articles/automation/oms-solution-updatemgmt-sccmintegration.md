---
title: Usar o Gerenciamento de Atualizações do Azure com clientes do Configuration Manager
description: Este artigo destina-se a ajudá-lo a configurar o Microsoft Endpoint Configuration Manager com essa solução para implantar atualizações de software em clientes do ConfigMgr.
services: automation
ms.subservice: update-management
ms.date: 03/19/2018
ms.topic: conceptual
ms.openlocfilehash: 9df401ec9c6d11bfef5d1d60833c855029f8ca01
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769953"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Implantar atualizações para clientes do Microsoft Endpoint Configuration Manager com Gerenciamento de Atualizações

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem de sua força e maturidade no gerenciamento de atualizações de software como parte do ciclo de SUM (gerenciamento de atualização de software).

Pode comunicar e atualizar os servidores Windows geridos através da criação e pré-configuração de implementações de atualização de software no Configuration Manager e obter o estado detalhado das implementações de atualizações foi concluída com o [solução de gestão de atualizações](automation-update-management.md). Se você usar Configuration Manager para relatórios de conformidade de atualização, mas não para gerenciar implantações de atualização com seus servidores Windows, poderá continuar relatando para Configuration Manager enquanto as atualizações de segurança são gerenciadas com a solução de Gerenciamento de Atualizações.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter o [solução de gestão de atualizações](automation-update-management.md) adicionado à sua conta de automatização.
* Os servidores do Windows atualmente gerenciados pelo ambiente de Configuration Manager também precisam ser relatados para o espaço de trabalho Log Analytics que também tem a solução Gerenciamento de Atualizações habilitada.
* Esse recurso está habilitado no Configuration Manager Branch atual versão 1606 e superior. Para integrar seu site de administração central do Configuration Manager ou um site primário autônomo com Azure Monitor logs e importar coleções, examine [conectar Configuration Manager para Azure monitor logs](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows têm de ser configurados para comunicar com um servidor de Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update, caso não recebam atualizações de segurança do Configuration Manager.   

A forma de gerir os clientes alojados no IaaS do Azure com o seu ambiente existente do Configuration Manager depende principalmente da ligação que tem entre os datacenters do Azure e a sua infraestrutura. Esta ligação afeta quaisquer alterações de estrutura que possa ter de fazer na infraestrutura do Configuration Manager e o custo relacionado para suportar essas alterações necessárias. Para compreender quais as considerações de planeamento que precisa de avaliar antes de continuar, reveja [Configuration Manager no Azure - Perguntas mais frequentes](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerir atualizações de software a partir do Configuration Manager 

Se pretender continuar a gerir implementações de atualizações a partir do Configuration Manager, execute os passos seguintes. A automatização do Azure liga-se para o Configuration Manager para aplicar atualizações nos computadores cliente ligados à sua área de trabalho do Log Analytics. O conteúdo da atualização está disponível na cache do computador cliente como se a implementação fosse gerida pelo Configuration Manager.

1. Crie uma implementação de atualização de software a partir do site de nível superior na hierarquia do Configuration Manager com o processo descrito em [Implementar o processo de atualização de software](/sccm/sum/deploy-use/deploy-software-updates). A única definição que tem de ser configurada de forma diferente de uma implementação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de transferência do pacote de implementação. Este comportamento é gerido pela solução de gestão de atualizações através da criação de uma implementação de atualização agendada no próximo passo.

1. Na automatização do Azure, selecione **gestão de atualizações**. Criar uma nova implementação, seguindo os passos descritos na [criar uma implementação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **importados grupos** no **tipo** pendente para selecionar a adequada Coleção do Configuration Manager. Tenha em atenção os seguintes pontos importantes: um. Se uma janela de manutenção é definida na coleção de dispositivos selecionada do Configuration Manager, da coleção honram-lo em vez do **duração** definição definida na implementação agendada.
    b. Os membros da coleção de destino tem de ter uma ligação à Internet (direta, através de um servidor proxy ou através do gateway do Log Analytics).

Depois de concluir a implementação da atualização por meio da automatização do Azure, os computadores de destino que são membros do grupo do computador selecionado instalará atualizações à hora agendada da respetiva cache do cliente local. Pode [ver o estado de implementação da atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorizar os resultados da implementação.

### <a name="manage-software-updates-from-azure-automation"></a>Gerir atualizações de software a partir da automatização do Azure

Para gerir atualizações para as VMs do Windows Server que são clientes do Configuration Manager, precisa de configurar a política de cliente para desativar a funcionalidade de Gestão de Atualizações de Software para todos os clientes geridos por esta solução. Por predefinição, as definições de cliente visam todos os dispositivos na hierarquia. Para obter mais informações sobre esta definição de política e sobre como configurá-la, reveja [Como configurar as definições de cliente no System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Depois de efetuar esta alteração de configuração, crie uma nova implementação, seguindo os passos descritos na [criar uma implementação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **importados grupos** no **tipo** pendente para selecionar a coleção apropriada do Configuration Manager.

## <a name="next-steps"></a>Passos seguintes

