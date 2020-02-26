---
title: A bordo das suas máquinas virtuais Azure Stack para O Sentinela Azul  Microsoft Docs
description: Este artigo mostra-lhe como fornecer a extensão virtual da máquina virtual Do Monitor, Atualização e Configuração do Azure Monitor, Update e Configuration Management nas máquinas virtuais do Azure Stack e começar a monitorizá-las com o Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588523"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Conecte máquinas virtuais Azure Stack a Azure Sentinel




Com o Azure Sentinel, pode monitorizar os seus VMs em funcionamento em Azure e Azure Stack num só local. Para embarcar nas suas máquinas Azure Stack ao Azure Sentinel, primeiro precisa de adicionar a extensão virtual da máquina às suas máquinas virtuais existentes no Azure Stack. 

Depois de ligar as máquinas Azure Stack, escolha entre uma galeria de dashboards que superam os insights com base nos seus dados. Estes dashboards podem ser facilmente personalizados às suas necessidades.



## <a name="add-the-virtual-machine-extension"></a>Adicione a extensão da máquina virtual 

Adicione a extensão virtual do **Monitor Azure, Atualização e Gestão** de Configuração às máquinas virtuais em funcionamento na sua Stack Azure. 

1. Num novo separador de navegador, inicie sessão no portal [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Vá à página de **máquinas Virtuais,** selecione a máquina virtual que pretende proteger com o Azure Sentinel. Para obter informações sobre como criar uma máquina virtual no Azure Stack, consulte Criar um VM de [servidor Windows com o portal Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou criar um VM de servidor [Linux utilizando o portal Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensions** (Extensões). A lista de extensões de máquinas virtuais instaladas nesta máquina virtual é mostrada.
4. Clique no separador **Adicionar.** A lâmina do menu **New Resource** abre e mostra a lista de extensões de máquinavirtual disponíveis. 
5. Selecione a extensão de **Monitor, Atualização e Gestão** de Configuração e clique em **Criar**. A janela de configuração de **extensão de instalação** abre-se.

   ![Definições de monitorização, atualização e configuração do Monitor, Atualização e Configuração](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se não vir a extensão de **Monitorização, Atualização e Gestão** de Configurações e Monitorização de Configuração do Azure listada no seu mercado, contacte o operador do Azure Stack para o disponibilizar.

6. No menu Azure Sentinel, selecione **as definições** do Espaço de Trabalho seguidas por **Advanced**, e copie o ID do **espaço de trabalho** e a chave **workspace (Chave Primária)** . 
1. Na janela de extensão de **instalação** de pilhas azure, cola-as nos campos indicados e clica **EM OK**.
1. Após o fim da instalação da extensão, o seu estado mostra como **o provisionamento foi bem sucedido**. Pode levar até uma hora para a máquina virtual aparecer no portal Azure Sentinel.

Para obter mais informações sobre a instalação e configuração do agente para windows, consulte [Connect Windows computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para problemas de resolução de problemas com o Linux, consulte [Troubleshoot Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

No portal Azure Sentinel em Azure, em **Máquinas Virtuais,** tem uma visão geral de todos os VMs e computadores, juntamente com o seu estado. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover a extensão da máquina virtual através do portal Azure Stack.

Para remover a extensão:

1. Abra o **Portal da Pilha Azure.**
2. Vá à página de **máquinas virtuais,** selecione a máquina virtual a partir da qual pretende remover a extensão.
3. **Selecione Extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **Desinstalar**e confirmar a sua seleção.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Transmita dados dos [aparelhos de formato](connect-common-event-format.md) de erro comum para o Azure Sentinel.
