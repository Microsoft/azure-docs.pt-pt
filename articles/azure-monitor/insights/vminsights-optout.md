---
title: Como desativar a monitorização com o Azure Monitor para VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo descreve como interromper a monitorização das suas máquinas virtuais com o Azure Monitor para as VMs.
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
ms.openlocfilehash: 0f35ea3e35277ee7f1afd8278a31f45ed20c6995
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522126"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Como desativar a monitorização das suas máquinas virtuais com o Azure Monitor para VMs (pré-visualização)

Se depois de ativar a monitorização das suas máquinas virtuais, decidir que já não pretende monitorizá-las com o Azure Monitor para as VMs, pode desativar a monitorização. Este artigo mostra como fazer isso para uma única ou várias VMs.  

Atualmente, o Azure Monitor para VMs não suporta a seletivamente a desativação da monitorização das suas VMs. Se a sua área de trabalho do Log Analytics é configurada para suportar esta solução e outras soluções, bem como recolher outros dados de monitorização, é importante que compreender o impacto e os métodos descritos abaixo antes de continuar.

Monitor do Azure para VMs baseia-se dos seguintes componentes para fornecer a sua experiência:

* Uma área de trabalho do Log Analytics, que armazena dados de monitorização recolhidos a partir de VMs e de outras origens.
* Coleção de contadores de desempenho configurados na área de trabalho, o que atualiza a configuração de monitorização em todas as VMs ligadas à área de trabalho.
* Duas soluções de monitorização configuradas na área de trabalho - **InfrastructureInsights** e **ServiceMap**, que configuração de monitorização de atualização em todas as VMs ligadas à área de trabalho.
* Duas extensões de máquina virtual do Azure, o **MicrosoftMonitoringAgent** e o **DependencyAgent**, que recolhem e enviam dados para a área de trabalho.

Ao se preparar para desativar a monitorização das suas máquinas virtuais com o Azure Monitor para as VMs, considere o seguinte:

* Se estiver a avaliar com uma única VM, e aceitou a área de trabalho do Log Analytics pré-selecionadas predefinido, pode desativar a monitorização ao desinstalar o agente de dependência da VM e a desligar o agente Log Analytics desta área de trabalho. Essa abordagem é apropriada se pretende usar a VM para outros fins e decidir mais tarde voltar a ligar a uma área de trabalho diferente.
* Se estiver a utilizar a área de trabalho do Log Analytics para oferecer suporte a outras soluções de monitorização e recolha de dados de outras origens, pode remover o Azure Monitor para componentes de solução de VMs da área de trabalho sem interrupção ou impacto para a área de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução da sua área de trabalho, pode continuar a ver o estado de funcionamento das VMs do Azure; especificamente mapa de dados de desempenho e ao navegar para a vista no portal. Dados, eventualmente, deixará de aparecer a partir da vista de desempenho e mapa após algum tempo; No entanto, a vista de estado de funcionamento irá continuar Mostrar o estado de funcionamento para as suas VMs. O **Experimente agora o** opção estará disponível a partir da VM do Azure selecionada para que possa reative a monitorização no futuro.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Concluir a remoção do Azure Monitor para VMs

Os passos seguintes descrevem como remover completamente o Azure Monitor para VMs se ainda precisar da área de trabalho do Log Analytics. Vai remover o **InfrastructureInsights** e **ServiceMap** soluções a partir da área de trabalho.  

>[!NOTE]
>Se estivesse usando o mapa de serviço solução anterior ao ativar o Azure Monitor para VMs de monitorização e ainda conta com ele, não remova essa solução conforme descrito no passo 6 abaixo.  
>

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva o Log Analytics. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
3. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho que escolheu quando a integração do Azure Monitor para as VMs.
4. No painel esquerdo, selecione **soluções**.  
5. Na lista de soluções, selecione **InfrastructureInsights (nome de área de trabalho)** e, em seguida, no **descrição geral** para a solução, clique em **eliminar**.  Quando lhe for pedido para confirmar, clique em **Sim**.  
6. Na lista de soluções, selecione **ServiceMap (nome de área de trabalho)** e, em seguida, no **descrição geral** para a solução, clique em **eliminar**.  Quando lhe for pedido para confirmar, clique em **Sim**.  

Se antes de integração do Azure Monitor para as VMs, não tivesse [recolher os contadores de desempenho ativados](vminsights-enable-overview.md#performance-counters-enabled) para o Windows ou baseado em Linux VMs na sua área de trabalho, terá de desativar essas regras, seguindo os passos descritos [aqui](../platform/data-sources-performance-counters.md#configuring-performance-counters) para Windows e Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Desativar a monitorização para uma VM do Azure e reter a área de trabalho  

Os passos seguintes descrevem como desativar a monitorização para uma máquina virtual que foi ativada para avaliar o Azure Monitor para as VMs, mas a área de trabalho do Log Analytics ainda é necessária para suportar a monitorização de outras origens. Se se tratar de uma VM do Azure, vai remover o agente de dependência da extensão de VM e o agente de Log Analytics a extensão de VM para Windows/Linux diretamente a partir da VM. 

>[!NOTE]
>Se a máquina virtual for gerenciada pelo automatização do Azure para orquestrar a processos, gerir a configuração, ou gerir as atualizações ou geridos pelo centro de segurança do Azure para segurança gestão e deteção de ameaças, o agente não deve ser removido do Log Analytics. Caso contrário, evitará que esses serviços e soluções de gerenciamento proativo da sua VM. 

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, selecione **máquinas virtuais**. 
3. Na lista, selecione uma VM. 
4. No painel esquerdo, selecione **extensões** e, no **extensões** página, selecione **DependencyAgent**.
5. Na página de propriedades de extensão, clique em **desinstalação**.
6. Sobre o **extensões** página, selecione **MicrosoftMonitoringAgent** e na página de propriedades de extensão, clique em **desinstalação**.  
