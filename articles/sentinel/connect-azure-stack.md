---
title: A bordo das suas máquinas virtuais Azure Stack Hub para Azure Sentinel Microsoft Docs
description: Este artigo mostra-lhe como providenciar a extensão da máquina virtual Azure Monitor, Update e Configuration Management nas máquinas virtuais Azure Stack Hub e começar a monitorizá-las com o Sentinel.
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
ms.openlocfilehash: 9ff70e7c05ca8de49f560fba3d59f0609785b8c4
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636780"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Ligue as máquinas virtuais do Azure Stack Hub ao Azure Sentinel




Com o Azure Sentinel, pode monitorizar os seus VMs em execução no Azure e no Azure Stack Hub num só local. Para embarcar as suas máquinas Azure Stack ao Azure Sentinel, primeiro tem de adicionar a extensão da máquina virtual às máquinas virtuais existentes do Azure Stack Hub. 

Depois de ligar as máquinas Azure Stack Hub, escolha entre uma galeria de painéis de instrumentos que superem insights com base nos seus dados. Estes dashboards podem ser facilmente personalizados às suas necessidades.



## <a name="add-the-virtual-machine-extension"></a>Adicione a extensão da máquina virtual 

Adicione a extensão da máquina virtual **Azure Monitor, Update e Configuration Management** às máquinas virtuais que executam no seu Azure Stack Hub. 

1. Num novo separador de navegador, inicie sessão no [seu portal Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Vá à página **de máquinas Virtuais,** selecione a máquina virtual que pretende proteger com o Azure Sentinel. Para obter informações sobre como criar uma máquina virtual no Azure Stack Hub, consulte [criar um VM servidor windows com o portal Azure Stack Hub](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou Criar um [VM servidor Linux utilizando o portal Azure Stack Hub](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensões**. É apresentada a lista de extensões de máquinas virtuais instaladas nesta máquina virtual.
4. Clique no **separador Adicionar.** A lâmina do menu **New Resource** abre e mostra a lista de extensões de máquinas virtuais disponíveis. 
5. Selecione a extensão de Gestão de **Azure Monitor, Atualização e Configuração** e clique em **Criar**. A janela **de configuração de extensão de instalação** abre-se.

   ![Definições de gestão de monitores, atualizações e configurações de Azure Monitor, Atualização e Configuração](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se não vir a extensão de Gestão de **Azure Monitor, Atualização e Configuração** listada no seu mercado, contacte o operador do Azure Stack Hub para o disponibilizar.

6. No menu Azure Sentinel, selecione **as definições do Espaço de Trabalho seguidas** de **Advanced** , e copie a chave **de ID** e **espaço de trabalho (Chave Primária)**. 
1. Na janela de **extensão** Azure Stack Hub Instale-as, cole-as nos campos indicados e clique em **OK**.
1. Após a conclusão da instalação de extensão, o seu estado mostra como **Provisioning Succeeded**. Pode levar até uma hora para a máquina virtual aparecer no portal Azure Sentinel.

Para obter mais informações sobre a instalação e configuração do agente para o Windows, consulte [os computadores Connect Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Para a resolução de problemas do Linux sobre problemas de agentes, consulte o Agente Linux do [Log Analytics de Troubleshoot Azure Log.](../azure-monitor/platform/agent-linux-troubleshoot.md)

No portal Azure Sentinel em Azure, em **Máquinas Virtuais,** tem uma visão geral de todos os VMs e computadores, juntamente com o seu estado. 

## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não é necessário, pode remover a extensão da máquina virtual através do portal Azure Stack Hub.

Para remover a extensão:

1. Abra o **Portal Azure Stack Hub**.
2. Aceda à página **de máquinas Virtuais,** selecione a máquina virtual a partir da qual pretende remover a extensão.
3. Selecione **Extensões** , selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **Desinstalar** e confirmar a sua seleção.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Transmita os dados dos [aparelhos Common Error Format](connect-common-event-format.md) para o Azure Sentinel.
