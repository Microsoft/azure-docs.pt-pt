---
title: Centro de segurança do Azure início rápido do-carregar suas máquinas virtuais do Azure Stack ao centro de segurança | Documentos da Microsoft
description: Este guia de introdução mostra-lhe como aprovisionar a extensão de máquina virtual do Azure Monitor, atualização e gestão de configuração em máquinas de virtuais do Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
editor: ''
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 7a630acee079301b95e7e05f5c5333dd116abb68
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563799"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Início rápido:  Carregar suas máquinas virtuais do Azure Stack ao centro de segurança
Depois de carregar a subscrição do Azure, pode ativar o Centro de segurança proteger as suas máquinas virtuais em execução no Azure Stack, adicionando a **Azure Monitor, atualização e gestão de configuração** extensão da máquina virtual dos O Azure marketplace de pilha.

Este início rápido mostra como adicionar a **do Azure Monitor, atualização e gestão de configuração** extensão da máquina virtual numa máquina virtual (Linux e Windows são ambas suportadas) em execução no Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Tem de ter uma subscrição do Azure no escalão Standard do Centro de segurança antes de iniciar este guia de introdução. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Pode experimentar o escalão Standard do Centro de segurança sem custos durante 30 dias. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Selecione a área de trabalho no Centro de segurança do Azure

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto. 

   ![Descrição geral do Centro de Segurança][2]

3. No menu principal do Centro de Segurança, selecione **Introdução**.
4. Selecione o separador **Introdução**.

   ![Introdução][3]

5. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou pretende que a VM de pilha do Azure para dados de segurança do relatório para outra área de trabalho.

    ![Adicionar computador não pertencente ao Azure](./media/quick-onboard-windows-computer/non-azure.png)

   O **agente direto** painel abre-se com uma ligação para transferir o agente e as chaves para o seu ID de área de trabalho para utilizar na configuração do agente.

   >[!NOTE]
   > Não é necessário transferir o agente manualmente. O agente será instalado como uma extensão de VM nos passos abaixo.

6. À direita de **ID da Área de Trabalho**, selecione o ícone de copiar e cole o ID no Bloco de Notas.

7. À direita de **Chave Primária**, selecione o ícone de copiar e cole a chave no Bloco de Notas.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Adicione a extensão de máquina virtual para suas máquinas de virtuais existentes do Azure Stack
Agora tem de adicionar o **do Azure Monitor, atualização e gestão de configuração** extensão da máquina virtual para as máquinas virtuais em execução no seu Azure Stack.

1. Num novo separador do browser, inicie sessão no seu **do Azure Stack** portal.
2. Vá para o **máquinas virtuais** página, selecione a máquina virtual que pretende proteger com o Centro de segurança. Para obter informações sobre como criar uma máquina virtual no Azure Stack, veja [neste início rápido para as máquinas virtuais do Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [neste início rápido para máquinas virtuais do Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensions** (Extensões). É apresentada a lista de extensões de máquina virtual instalada nesta máquina virtual.
4. Clique nas **adicionar** separador. O **novo recurso** painel de menu é aberto e mostra a lista de extensões de máquina virtual de disponibilidade. 
5. Selecione o **do Azure Monitor, atualização e gestão de configuração** extensão e clique em **criar**. O **instalar a extensão** abre o painel de configuração.

>[!NOTE]
> Se não vir a **do Azure Monitor, atualização e gestão de configuração** extensão listado no seu marketplace, contacte o seu operador do Azure Stack para disponibilizá-lo.

6. Na **instalar a extensão** painel de configuração, colar a **ID de área de trabalho** e **chave da área de trabalho (chave primária)** que copiou no bloco de notas no procedimento anterior.
7.  Quando tiver terminado desde que as definições de configuração necessárias, clique em **OK**.
8. Depois de concluída a instalação da extensão, o respetivo estado será mostrado como **aprovisionamento concluído com êxito**. Poderá demorar até uma hora para a máquina virtual para que sejam apresentadas no portal do Centro de segurança.

Para obter mais informações sobre como instalar e configurar o agente para Windows, consulte [computadores Windows ligar](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para Linux resolução de problemas relacionados do agente, consulte [resolver problemas relacionados com o Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. O Security Center em portal no Azure, **computação**, tem uma descrição geral de todas as VMs e computadores, juntamente com suas recomendações. Centro de segurança também apresenta qualquer deteção destes computadores nos alertas de segurança.

  ![Painel Computação][6]

Estão representados dois tipos de ícone no painel **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) VM do Azure (VMs do Azure Stack irá mostrar neste grupo)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover a extensão da máquina virtual através do portal do Azure Stack.

Para remover a extensão:

1. Abra o **Portal do Azure Stack**.
2. Aceda a **máquinas virtuais** página, selecione a máquina virtual a partir do qual pretende remover a extensão.
3. Selecione **extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **desinstalação**e confirme a seleção clicando **Sim**.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, aprovisionou a extensão do Azure Monitor, atualização e gestão de configuração numa máquina virtual em execução no Azure Stack. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
