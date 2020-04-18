---
title: Utilize a Gestão de Atualizações Azure com clientes do Gestor de Configuração
description: Este artigo destina-se a ajudá-lo a configurar o Microsoft Endpoint Configuration Manager com esta solução para implementar atualizações de software para clientes ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618727"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Implementar atualizações para clientes do Microsoft Endpoint Configuration Manager com Gestão de Atualizações

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerir Computadores, servidores e dispositivos móveis também dependem da sua força e maturidade na gestão de atualizações de software como parte do seu ciclo de gestão de atualizações de software (SUM).

Pode reportar e atualizar servidores do Windows geridos através da criação e pré-realização de implementações de atualizações de software no Gestor de Configuração e obter o estado detalhado das implementações de atualizações concluídas utilizando a [solução de Gestão](automation-update-management.md)de Atualizações . Se utilizar o Gestor de Configuração para o relatório de conformidade da atualização, mas não para gerir as implementações de atualizações com os seus servidores Windows, pode continuar a reportar ao Gestor de Configuração enquanto as atualizações de segurança são geridas com a solução 'Gestão de Actualizações'.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de adicionar a [solução De Gestão](automation-update-management.md) de Atualização à sua conta Desmótica.
* Os servidores Windows atualmente geridos pelo ambiente do Gestor de Configuração também precisam de reportar ao espaço de trabalho do Log Analytics que também tem a solução de Gestão de Atualizações ativada.
* Esta funcionalidade está ativada na versão atual do 'Gestor de Configuração' 1606 e superior. Para integrar o site da administração central do Gestor de Configuração ou um site primário autónomo com registos do Monitor Azure e coleções de importação, reveja o [Connect Configuration Manager para os registos do Monitor Azure](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows têm de ser configurados para comunicar com um servidor de Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update, caso não recebam atualizações de segurança do Configuration Manager.   

A forma de gerir os clientes alojados no IaaS do Azure com o seu ambiente existente do Configuration Manager depende principalmente da ligação que tem entre os datacenters do Azure e a sua infraestrutura. Esta ligação afeta quaisquer alterações de estrutura que possa ter de fazer na infraestrutura do Configuration Manager e o custo relacionado para suportar essas alterações necessárias. Para compreender quais as considerações de planeamento que precisa de avaliar antes de continuar, reveja [Configuration Manager no Azure - Perguntas mais frequentes](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerir atualizações de software a partir do Configuration Manager 

Se pretender continuar a gerir implementações de atualizações a partir do Configuration Manager, execute os passos seguintes. A Azure Automation liga-se ao Gestor de Configuração para aplicar atualizações aos computadores clientes ligados ao seu espaço de trabalho Log Analytics. O conteúdo da atualização está disponível na cache do computador cliente como se a implementação fosse gerida pelo Configuration Manager.

1. Crie uma implementação de atualização de software a partir do site de alto nível na hierarquia do Gestor de Configuração utilizando o processo descrito nas atualizações de [software de implementação](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). A única definição que tem de ser configurada de forma diferente de uma implementação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de transferência do pacote de implementação. Este comportamento é gerido pela solução Update Management criando uma implementação de atualização programada no passo seguinte.

1. Na Automatização Azure, selecione Gestão de **Atualizações.** Crie uma nova implementação seguindo os passos descritos na Criação de [uma Implementação](automation-tutorial-update-management.md#schedule-an-update-deployment) de Atualizações e selecione **grupos importados** no **drop-down do Tipo** para selecionar a coleção de Gestor de Configuração apropriada. Tenha em mente os seguintes pontos importantes: a. Se for definida uma janela de manutenção na coleção de dispositivos Select Configuration Manager, os membros da coleção honram-na em vez da definição de **Duração** definida na implementação programada.
    b. Os membros da recolha do alvo devem ter uma ligação à Internet (diretamente, através de um servidor proxy ou através do gateway Log Analytics).

Após completar a implementação da atualização através do Azure Automation, os computadores-alvo que são membros do grupo de computador selecionado sairá atualizações no momento programado a partir da sua cache de cliente local. Pode [ver o estado de implementação da atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorizar os resultados da implementação.

### <a name="manage-software-updates-from-azure-automation"></a>Gerir atualizações de software da Azure Automation

Para gerir atualizações para as VMs do Windows Server que são clientes do Configuration Manager, precisa de configurar a política de cliente para desativar a funcionalidade de Gestão de Atualizações de Software para todos os clientes geridos por esta solução. Por predefinição, as definições de cliente visam todos os dispositivos na hierarquia. Para obter mais informações sobre esta definição de política e como configurá-la, reveja [como configurar as definições](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)do cliente no Gestor de Configuração .

Depois de realizar esta alteração de configuração, cria uma nova implementação seguindo os passos descritos na Criação de [uma Implementação](automation-tutorial-update-management.md#schedule-an-update-deployment) de Atualização e selecione **grupos importados** no **drop-down do Tipo** para selecionar a coleção de Gestor de Configuração apropriada.

## <a name="next-steps"></a>Passos seguintes

