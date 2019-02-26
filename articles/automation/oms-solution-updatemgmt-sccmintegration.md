---
title: Planejar atualizações com coleções do SCCM na automatização do Azure - gestão de atualizações
description: Este artigo destina-se a ajudar a configurar o System Center Configuration Manager com esta solução para gerir atualizações de computadores geridos pelo SCCM.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddc055be45eae923be31d7d11621c9427660bf74
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818311"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integrar o System Center Configuration Manager com a gestão de atualização

Os clientes que investiram no System Center Configuration Manager para gerir PCs, servidores e dispositivos móveis também contam com a sua potência e maturidade na gestão de atualizações de software como parte do respetivo ciclo de gestão de atualizações de software (SUM).

Pode comunicar e atualizar os servidores Windows geridos através da criação e pré-configuração de implementações de atualização de software no Configuration Manager e obter o estado detalhado das implementações de atualizações foi concluída com o [solução de gestão de atualizações](automation-update-management.md). Se utilizar o Gestor de configuração para relatórios de conformidade de atualização, mas não para gerir implementações de atualizações com os seus servidores do Windows, pode continuar a reportar para o Configuration Manager enquanto as atualizações de segurança são geridas com a solução de gestão de atualizações.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter o [solução de gestão de atualizações](automation-update-management.md) adicionado à sua conta de automatização.
* Os servidores Windows atualmente geridos pelo seu ambiente do System Center Configuration Manager também precisam de reportar para a área de trabalho do Log Analytics que também tem a solução de Gestão de Atualizações ativada.
* Esta funcionalidade está ativada no System Center Configuration Manager a versão atual do ramo 1606 e superior. Para integrar o seu site de administração central do Configuration Manager ou um site primário autónomo com os registos do Azure Monitor e importar coleções, reveja [registos de ligar o Configuration Manager para o Azure Monitor](../azure-monitor/platform/collect-sccm.md).  
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

## <a name="next-steps"></a>Passos Seguintes

