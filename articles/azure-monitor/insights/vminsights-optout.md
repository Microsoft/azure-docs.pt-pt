---
title: Desativar a monitorização no Azure Monitor para VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo descreve como parar a monitorização de suas máquinas virtuais no Azure Monitor para as VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155699"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Desativar a monitorização das suas VMs no Azure Monitor para VMs (pré-visualização)

Depois de ativar a monitorização das suas máquinas virtuais (VMs), mais tarde pode optar por desativar a monitorização no Azure Monitor para as VMs. Este artigo mostra como desativar a monitorização para uma ou mais VMs.  

Atualmente o Azure Monitor para VMs não suporta seletiva a desativação da monitorização de VM. A área de trabalho do Log Analytics pode suportar o Azure Monitor para VMs e outras soluções. Ele também pode recolher outros dados de monitorização. Se a sua área de trabalho do Log Analytics fornece estes serviços, precisa entender o efeito e métodos de desativação da monitorização antes de começar.

Monitor do Azure para VMs baseia-se dos seguintes componentes para fornecer a sua experiência:

* Um trabalho do Log Analytics, que armazena dados de monitorização de VMs e de outras origens.
* Uma coleção de contadores de desempenho configurados na área de trabalho. A coleção atualiza a configuração de monitorização em todas as VMs ligadas à área de trabalho.
* `InfrastructureInsights` e `ServiceMap`, que está a monitorizar soluções configuradas na área de trabalho. Estas soluções atualizar a configuração de monitorização em todas as VMs ligadas à área de trabalho.
* `MicrosoftMonitoringAgent` e `DependencyAgent`, que são extensões de VM do Azure. Essas extensões recolhem e enviam dados para a área de trabalho.

Ao se preparar para desativar a monitorização das suas VMs, tenha em mente:

* Se avaliada com uma única VM e utilizada a área de trabalho do Log Analytics pré-selecionados predefinido, pode desativar a monitorização ao desinstalar o agente de dependência da VM e a desligar o agente Log Analytics desta área de trabalho. Essa abordagem é apropriada se pretender utilizar a VM para outros fins e decidir mais tarde voltar a ligar a uma área de trabalho diferente.
* Se tiver selecionado uma área de trabalho preexistente do Log Analytics oferece suporte a outras soluções de monitorização e recolha de dados de outras origens, pode remover componentes da solução da área de trabalho sem interromper ou afetar a sua área de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução da sua área de trabalho, poderá continuar a ver o estado de funcionamento das VMs do Azure; especificamente, irá ver o desempenho e dados do mapa quando vai para a vista no portal. Dados, eventualmente, deixará de aparecer no **desempenho** e **mapa** vistas. Mas o **estado de funcionamento** vista irá continuar a mostrar o estado de funcionamento para as suas VMs. O **Experimente agora o** opção estará disponível a partir da VM do Azure selecionada para que pode voltar a Ativar monitorização no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remover completamente o Azure Monitor para VMs

Se ainda precisar da área de trabalho do Log Analytics, siga estes passos para remover completamente o Azure Monitor para as VMs. Irá remover a `InfrastructureInsights` e `ServiceMap` soluções a partir da área de trabalho.  

>[!NOTE]
>Se tiver utilizado o mapa de serviço antes de ativado o Azure Monitor para VMs e ainda de confiar na mesma solução de monitorização, não remova essa solução conforme descrito no último passo do procedimento seguinte.  
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. Quando começar a escrever, a lista de filtros de sugestões com base na sua entrada. Selecione **Log Analytics**.
3. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho que escolheu quando ativou o Azure Monitor para as VMs.
4. No lado esquerdo, selecione **soluções**.  
5. Na lista de soluções, selecione **InfrastructureInsights (nome de área de trabalho)** . Sobre o **descrição geral** página para a solução, selecione **eliminar**. Quando lhe for pedido para confirmar, selecione **Sim**.  
6. Na lista de soluções, selecione **ServiceMap (nome de área de trabalho)** . Sobre o **descrição geral** página para a solução, selecione **eliminar**. Quando lhe for pedido para confirmar, selecione **Sim**.  

Antes de ativar o Azure Monitor para as VMs, se ainda não o tenha [recolher contadores de desempenho](vminsights-enable-overview.md#performance-counters-enabled) para as VMs baseado no Windows ou Linux na sua área de trabalho [desativar essas regras](../platform/data-sources-performance-counters.md#configuring-performance-counters) para Windows e Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desativar a monitorização e manter a área de trabalho  

Se a sua área de trabalho do Log Analytics ainda tem de suportar a monitorização a partir de outras origens, siga estes passos para desativar a monitorização na VM que utilizou para avaliar o Azure Monitor para as VMs. Para VMs do Azure, irá remover o extensão de VM do agente de dependência e o extensão de VM de agente do Log Analytics para Windows ou Linux diretamente a partir da VM. 

>[!NOTE]
>Não remova o agente de Log Analytics se: 
>
> * A automatização do Azure gere a VM para orquestrar a processos ou gerir a configuração ou atualizações. 
> * Centro de segurança do Azure gere a VM para a deteção de ameaças e segurança. 
>
> Se remover o agente Log Analytics, irá impedir que esses serviços e soluções de gerenciamento proativo da sua VM. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, selecione **máquinas virtuais**. 
3. Na lista, selecione uma VM. 
4. No lado esquerdo, selecione **extensões**. Sobre o **extensões** página, selecione **DependencyAgent**.
5. Na página de propriedades de extensão, selecione **desinstalação**.
6. Sobre o **extensões** página, selecione **MicrosoftMonitoringAgent**. Na página de propriedades de extensão, selecione **desinstalação**.  
