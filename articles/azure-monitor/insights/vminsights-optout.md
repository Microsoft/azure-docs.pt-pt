---
title: Desabilitar monitoramento em Azure Monitor para VMs (visualização) | Microsoft Docs
description: Este artigo descreve como parar de monitorar suas máquinas virtuais no Azure Monitor para VMs.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/05/2018
ms.openlocfilehash: 70e71688fde5aff4002c7d49b1408bcefeab1eed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555156"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Desabilitar o monitoramento de suas VMs no Azure Monitor para VMs (versão prévia)

Depois de habilitar o monitoramento de suas VMs (máquinas virtuais), você pode optar por desabilitar o monitoramento em Azure Monitor para VMs mais tarde. Este artigo mostra como desabilitar o monitoramento de uma ou mais VMs.  

Atualmente, Azure Monitor para VMs não dá suporte à desabilitação seletiva de monitoramento de VM. Seu espaço de trabalho do Log Analytics pode dar suporte a Azure Monitor para VMs e outras soluções. Ele também pode coletar outros dados de monitoramento. Se seu espaço de trabalho do Log Analytics fornece esses serviços, você precisa entender o efeito e os métodos de desabilitar o monitoramento antes de começar.

Azure Monitor para VMs se baseia nos seguintes componentes para oferecer sua experiência:

* Um espaço de trabalho Log Analytics, que armazena dados de monitoramento de VMs e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A coleção atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `InfrastructureInsights` e `ServiceMap`, que são soluções de monitoramento configuradas no espaço de trabalho. Essas soluções atualizam a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `MicrosoftMonitoringAgent` e `DependencyAgent`, que são extensões de VM do Azure. Essas extensões coletam e enviam dados para o espaço de trabalho.

Ao se preparar para desabilitar o monitoramento de suas VMs, tenha em mente estas considerações:

* Se você avaliou com uma única VM e usou o espaço de trabalho de Log Analytics padrão selecionado, você pode desabilitar o monitoramento desinstalando o agente de dependência da VM e desconectando o agente de Log Analytics desse espaço de trabalho. Essa abordagem é apropriada se você pretende usar a VM para outras finalidades e decidir mais tarde para reconectá-la a um espaço de trabalho diferente.
* Se você selecionou um espaço de trabalho de Log Analytics pré-existente que dá suporte a outras soluções de monitoramento e coleta de dados de outras fontes, você pode remover componentes da solução do espaço de trabalho sem interromper ou afetar seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do espaço de trabalho, você pode continuar a ver o estado de integridade de suas VMs do Azure; especificamente, você verá dados de desempenho e de mapa ao ir para qualquer modo de exibição no Portal. Os dados eventualmente serão interrompidos nas exibições de **desempenho** e de **mapa** . Mas a exibição de **integridade** continuará a mostrar o status de integridade para suas VMs. A opção **tentar agora** estará disponível na VM do Azure selecionada para que você possa reabilitar o monitoramento no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remover Azure Monitor para VMs completamente

Se você ainda precisar do espaço de trabalho Log Analytics, siga estas etapas para remover completamente Azure Monitor para VMs. Você removerá as soluções `InfrastructureInsights` e `ServiceMap` do espaço de trabalho.  

>[!NOTE]
>Se você usou a solução de monitoramento de Mapa do Serviço antes de habilitar Azure Monitor para VMs e ainda depender dela, não remova essa solução, conforme descrito na última etapa do procedimento a seguir.  
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione **Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você escolheu quando habilitou Azure Monitor para VMs.
4. À esquerda, selecione **soluções**.  
5. Na lista de soluções, selecione **InfrastructureInsights (nome do espaço de trabalho)** . Na página **visão geral** da solução, selecione **excluir**. Quando for solicitado a confirmar, selecione **Sim**.  
6. Na lista de soluções, selecione **ServiceMap (nome do espaço de trabalho)** . Na página **visão geral** da solução, selecione **excluir**. Quando for solicitado a confirmar, selecione **Sim**.  

Antes de habilitar a Azure Monitor para VMs, se você não [coletou os contadores de desempenho](vminsights-enable-overview.md#performance-counters-enabled) para as VMs baseadas em Windows ou Linux em seu espaço de trabalho, [desabilite essas regras](../platform/data-sources-performance-counters.md#configuring-performance-counters) para Windows e para Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desabilitar o monitoramento e manter o espaço de trabalho  

Se seu espaço de trabalho do Log Analytics ainda precisar de suporte ao monitoramento de outras fontes, seguindo estas etapas para desabilitar o monitoramento na VM que você usou para avaliar Azure Monitor para VMs. Para VMs do Azure, você removerá a extensão de VM do agente de dependência e a extensão de VM do agente de Log Analytics para Windows ou Linux diretamente da VM. 

>[!NOTE]
>Não remova o agente de Log Analytics se: 
>
> * A automação do Azure gerencia a VM para orquestrar processos ou para gerenciar a configuração ou as atualizações. 
> * A central de segurança do Azure gerencia a VM para detecção de ameaças e segurança. 
>
> Se você remover o agente de Log Analytics, impedirá que esses serviços e soluções gerenciem proativamente sua VM. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, selecione **máquinas virtuais**. 
3. Na lista, selecione uma VM. 
4. À esquerda, selecione **extensões**. Na página **extensões** , selecione **DependencyAgent**.
5. Na página Propriedades da extensão, selecione **desinstalar**.
6. Na página **extensões** , selecione **extensão microsoftmonitoringagent**. Na página Propriedades da extensão, selecione **desinstalar**.  
