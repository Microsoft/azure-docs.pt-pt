---
title: Desativar a monitorização em conhecimentos VM
description: Este artigo descreve como parar de monitorizar as suas máquinas virtuais em insights VM.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 2de0dcd52745ebadb02ab8dbb563e28abf2822dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046487"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Desativar a monitorização dos seus VMs em insights VM

Depois de ativar a monitorização das suas máquinas virtuais (VMs), pode optar por desativar a monitorização em insights VM. Este artigo mostra como desativar a monitorização de um ou mais VMs.  

Atualmente, os conhecimentos em VM não suportam a desativação seletiva da monitorização de VM. O seu espaço de trabalho Log Analytics poderá suportar insights de VM e outras soluções. Pode também recolher outros dados de monitorização. Se o seu espaço de trabalho Log Analytics fornecer estes serviços, precisa entender o efeito e os métodos de desativação da monitorização antes de começar.

Os conhecimentos vM baseiam-se nos seguintes componentes para fornecer a sua experiência:

* Um espaço de trabalho Log Analytics, que armazena dados de monitorização de VMs e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A recolha atualiza a configuração de monitorização em todos os VMs ligados ao espaço de trabalho.
* `VMInsights`, que é uma solução de monitorização configurada no espaço de trabalho. Esta solução atualiza a configuração de monitorização em todos os VMs ligados ao espaço de trabalho.
* `MicrosoftMonitoringAgent` e `DependencyAgent` , que são extensões Azure VM. Estas extensões recolhem e enviam dados para o espaço de trabalho.

Enquanto se prepara para desativar a monitorização dos seus VMs, tenha em mente estas considerações:

* Se tiver avaliado com um único VM e utilizar o espaço de trabalho pré-selecionado do Log Analytics, pode desativar a monitorização desinstalando o agente Dependency do VM e desligando o agente Log Analytics deste espaço de trabalho. Esta abordagem é apropriada se pretender utilizar o VM para outros fins e decidir mais tarde ligá-lo a um espaço de trabalho diferente.
* Se selecionar um espaço de trabalho pré-existente do Log Analytics que suporte outras soluções de monitorização e recolha de dados de outras fontes, pode remover componentes de solução do espaço de trabalho sem interromper ou afetar o seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, poderá continuar a ver dados de desempenho e mapa para os seus VMs Azure. Os dados acabarão por deixar de aparecer nas vistas de **Performance** e **Map.** A opção **Enable** estará disponível a partir do Azure VM selecionado para que possa voltar a permitir a monitorização no futuro.  

## <a name="remove-vm-insights-completely"></a>Remover completamente os conhecimentos de VM

Se ainda precisar do espaço de trabalho Log Analytics, siga estes passos para remover completamente as perceções de VM. Removerá a `VMInsights` solução do espaço de trabalho.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra sugestões com base na sua entrada. Selecione o **Log Analytics**.
3. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que escolheu quando ativou insights de VM.
4. À esquerda, **selecione Soluções**.  
5. Na lista de soluções, selecione **VMInsights (nome do espaço de trabalho)**. Na página **'Visão Geral'** para a solução, selecione **Delete**. Quando for solicitado para confirmar, selecione **Sim**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desativar a monitorização e manter o espaço de trabalho  

Se o seu espaço de trabalho Log Analytics ainda precisar de suportar a monitorização de outras fontes, seguindo estes passos para desativar a monitorização no VM que usou para avaliar os conhecimentos de VM. Para os VMs Azure, removerá a extensão VM do agente de dependência e a extensão VM do agente Log Analytics para Windows ou Linux diretamente do VM. 

>[!NOTE]
>Não remova o agente Log Analytics se: 
>
> * A Azure Automation gere o VM para orquestrar processos ou para gerir configurações ou atualizações. 
> * O Azure Security Center gere o VM para deteção de segurança e ameaças. 
>
> Se remover o agente Log Analytics, evitará que esses serviços e soluções gerem proativamente o seu VM. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal Azure, selecione **Máquinas Virtuais.** 
3. Na lista, selecione uma VM. 
4. À esquerda, selecione **Extensões**. Na página **Extensões,** selecione **DependencyAgent**.
5. Na página de propriedades de extensão, selecione **Desinstalar**.
6. Na página **Extensões,** selecione **MicrosoftMonitoringAgent**. Na página de propriedades de extensão, selecione **Desinstalar**.  
