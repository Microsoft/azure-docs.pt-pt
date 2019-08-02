---
title: Integre suas máquinas virtuais Azure Stack ao Azure sentinela | Microsoft Docs
description: Este artigo mostra como provisionar a extensão de máquina virtual de gerenciamento de Azure Monitor, atualização e configuração em Azure Stack máquinas virtuais e começar a monitorá-las com o sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601167"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Conectar Azure Stack máquinas virtuais ao Azure Sentinel

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Com o Azure Sentinel, você pode monitorar suas VMs em execução no Azure e Azure Stack em um único local. Para integrar seus computadores Azure Stack ao Azure Sentinel, primeiro você precisa adicionar a extensão da máquina virtual às suas máquinas virtuais Azure Stack existentes. 

Depois de conectar Azure Stack computadores, escolha um de uma galeria de painéis que insights de superfície com base em seus dados. Esses painéis podem ser facilmente personalizados para suas necessidades.



## <a name="add-the-virtual-machine-extension"></a>Adicionar a extensão da máquina virtual 

Adicione a extensão de máquina virtual de **Gerenciamento de Azure monitor, atualização e configuração** para as máquinas virtuais em execução no seu Azure Stack. 

1. Em uma nova guia do navegador, faça logon em seu [portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Vá para a página **máquinas virtuais** , selecione a máquina virtual que você deseja proteger com o Azure Sentinel. Para obter informações sobre como criar uma máquina virtual em Azure Stack, consulte [criar uma VM do Windows Server com o portal do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [criar uma VM do servidor Linux usando o portal do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensions** (Extensões). A lista de extensões de máquina virtual instaladas nesta máquina virtual é mostrada.
4. Clique na guia **Adicionar** . A folha do menu **novo recurso** é aberta e mostra a lista de extensões de máquina virtual disponíveis. 
5. Selecione a extensão de **Gerenciamento de Azure monitor, atualização e configuração** e clique em **criar**. A janela instalar configuração de **extensão** é aberta.

   ![Configurações de Azure Monitor, atualização e gerenciamento de configuração](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se você não vir a extensão de **Azure monitor, atualização e gerenciamento de configuração** listada em seu Marketplace, entre em contato com seu operador de Azure Stack para disponibilizá-lo.

6. No menu do Azure Sentinel, selecione **configurações do espaço de trabalho** seguido por **avançado**e copie a **ID do espaço** de trabalho e a **chave do espaço de trabalho (chave primária)** . 
1. Na janela Azure Stack **instalar extensão** , Cole-os nos campos indicados e clique em **OK**.
1. Depois que a instalação da extensão for concluída, seu status será exibido como **provisionamento bem-sucedido**. Pode levar até uma hora para a máquina virtual aparecer no portal do Azure Sentinel.

Para obter mais informações sobre como instalar e configurar o agente para Windows, consulte [conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para a solução de problemas do agente para Linux, consulte solucionar problemas do [agente do Azure log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

No portal do Azure Sentinel no Azure, em **máquinas virtuais**, você tem uma visão geral de todas as VMs e computadores junto com seu status. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, você poderá remover a extensão da máquina virtual por meio do portal de Azure Stack.

Para remover a extensão:

1. Abra o **portal de Azure Stack**.
2. Vá para a página **máquinas virtuais** , selecione a máquina virtual da qual você deseja remover a extensão.
3. Selecione **extensões**, selecione a extensão **Microsoft. EnterpriseCloud. Monitoring**.
4. Clique em **desinstalar**e confirme sua seleção.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- Transmita dados de [dispositivos de formato de erro comuns](connect-common-event-format.md) para o Azure Sentinel.
