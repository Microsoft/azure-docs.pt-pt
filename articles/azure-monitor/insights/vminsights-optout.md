---
title: Desativar a monitorização no Monitor Azure para VMs
description: Este artigo descreve como parar de monitorizar as suas máquinas virtuais no Monitor Azure para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480526"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Desativar a monitorização dos seus VMs no Monitor Azure para VMs

Depois de ativar a monitorização das suas máquinas virtuais (VMs), poderá optar por desativar a monitorização no Monitor Azure para VMs. Este artigo mostra como desativar a monitorização de um ou mais VMs.  

Atualmente, o Monitor Azure para VMs não suporta a desativação seletiva da monitorização vm. O seu espaço de trabalho Log Analytics poderá suportar o Monitor Azure para VMs e outras soluções. Também pode recolher outros dados de monitorização. Se o seu espaço de trabalho Log Analytics fornecer estes serviços, precisa entender o efeito e os métodos de desativação da monitorização antes de começar.

O Monitor Azure para VMs baseia-se nos seguintes componentes para proporcionar a sua experiência:

* Um espaço de trabalho log Analytics, que armazena dados de monitorização de VMs e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A coleção atualiza a configuração de monitorização em todos os VMs ligados ao espaço de trabalho.
* `VMInsights`, que é uma solução de monitorização configurada no espaço de trabalho. Esta solução atualiza a configuração de monitorização em todos os VMs ligados ao espaço de trabalho.
* `MicrosoftMonitoringAgent`e, `DependencyAgent`que são extensões VM Azure. Estas extensões recolhem e enviam dados para o espaço de trabalho.

Enquanto se prepara para desativar a monitorização dos seus VMs, tenha em mente estas considerações:

* Se tiver avaliado com um único VM e utilizado o espaço de trabalho pre-seleccionado de Log Analytics, pode desativar a monitorização desinstalando o agente dependency do VM e desligando o agente Log Analytics deste espaço de trabalho. Esta abordagem é apropriada se pretender utilizar o VM para outros fins e decidir mais tarde reconectá-lo a um espaço de trabalho diferente.
* Se tiver selecionado um espaço de trabalho pré-existente no Log Analytics que suporte outras soluções de monitorização e recolha de dados de outras fontes, pode remover componentes de soluções do espaço de trabalho sem interromper ou afetar o seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, poderá continuar a ver dados de desempenho e mapas para os seus VMs Azure. Os dados acabarão por deixar de aparecer nas vistas **de Desempenho** e **Mapa.** A opção **Enable** estará disponível a partir do Azure VM selecionado para que possa reativar a monitorização no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remova completamente o Monitor Azure para VMs

Se ainda necessitar do espaço de trabalho Log Analytics, siga estes passos para remover completamente o Monitor Azure para VMs. Removerá a `VMInsights` solução do espaço de trabalho.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra sugestões com base na sua entrada. Selecione **Log Analytics**.
3. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que escolheu quando ativou o Monitor Azure para VMs.
4. À esquerda, selecione **Soluções**.  
5. Na lista de soluções, selecione **VMInsights (nome do espaço**de trabalho) . Na página **'Visão Geral'** para a solução, selecione **Eliminar**. Quando for solicitado a confirmar, selecione **Sim**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desative a monitorização e mantenha o espaço de trabalho  

Se o seu espaço de trabalho Log Analytics ainda necessitar de suportar a monitorização de outras fontes, seguindo estas medidas para desativar a monitorização no VM que usou para avaliar o Monitor Azure para VMs. Para Os VMs Azure, removerá a extensão VM do agente de dependência e a extensão VM do agente Log Analytics para Windows ou Linux diretamente do VM. 

>[!NOTE]
>Não remova o agente Log Analytics se: 
>
> * A Azure Automation gere o VM para orquestrar processos ou para gerir configurações ou atualizações. 
> * O Azure Security Center gere o VM para deteção de segurança e ameaças. 
>
> Se remover o agente Log Analytics, evitará que esses serviços e soluções gerem proativamente o seu VM. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal Azure, selecione **Máquinas Virtuais**. 
3. Na lista, selecione uma VM. 
4. À esquerda, selecione **Extensões**. Na página **Extensões,** selecione **DependencyAgent**.
5. Na página de propriedades de extensão, selecione **Desinstalar**.
6. Na página **Extensions,** selecione **MicrosoftMonitoringAgent**. Na página de propriedades de extensão, selecione **Desinstalar**.  
