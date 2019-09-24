---
title: Guia de início rápido da central de segurança do Azure-integração de suas máquinas virtuais Azure Stack à central de segurança | Microsoft Docs
description: Este guia de início rápido mostra como provisionar a extensão da máquina virtual de gerenciamento de Azure Monitor, atualização e configuração em uma Azure Stack máquinas virtuais.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 1772fd34a2d79b725b2b5ccaa66adb0b251b7e1d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202863"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Início rápido:  Integre suas máquinas virtuais Azure Stack à central de segurança
Depois de integrar sua assinatura do Azure, você pode habilitar a central de segurança para proteger suas máquinas virtuais em execução no Azure Stack adicionando a extensão de máquina virtual de **Gerenciamento de Azure monitor, atualização e configuração** do Azure Stack Comunidade.

Este guia de início rápido mostra como adicionar a extensão de máquina virtual de **Gerenciamento de Azure monitor, atualização e configuração** em uma máquina virtual (com suporte do Linux e do Windows) em Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Você deve ter uma assinatura do Azure na camada Standard da central de segurança antes de iniciar este guia de início rápido. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Você pode experimentar a camada Standard da central de segurança sem nenhum custo por 30 dias. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Selecione seu espaço de trabalho na central de segurança do Azure

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto. 

   ![Descrição geral do Centro de Segurança][2]

3. No menu principal do Centro de Segurança, selecione **Introdução**.
4. Selecione o separador **Introdução**.

   ![Introdução][3]

5. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione este espaço de trabalho ou outro espaço de trabalho para o qual você deseja que a VM de Azure Stack relate dados de segurança.

    ![Adicionar computador não pertencente ao Azure](./media/quick-onboard-windows-computer/non-azure.png)

   A folha **agente direto** é aberta com um link para baixar o agente e as chaves para a sua ID do espaço de trabalho a ser usada na configuração do agente.

   >[!NOTE]
   > Você não precisa baixar o agente manualmente. O agente será instalado como uma extensão de VM nas etapas abaixo.

6. À direita de **ID da Área de Trabalho**, selecione o ícone de copiar e cole o ID no Bloco de Notas.

7. À direita de **Chave Primária**, selecione o ícone de copiar e cole a chave no Bloco de Notas.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Adicionar a extensão da máquina virtual às suas máquinas virtuais Azure Stack existentes
Agora você deve adicionar a extensão de máquina virtual de **Gerenciamento de Azure monitor, atualização e configuração** para as máquinas virtuais em execução no seu Azure Stack.

1. Em uma nova guia do navegador, faça logon em seu portal de **Azure Stack** .
2. Vá para a página **máquinas virtuais** , selecione a máquina virtual que você deseja proteger com a central de segurança. Para obter informações sobre como criar uma máquina virtual em Azure Stack, consulte [este guia de início rápido para máquinas virtuais do Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [este guia de início rápido para máquinas virtuais do Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensions** (Extensões). A lista de extensões de máquina virtual instaladas nesta máquina virtual é mostrada.
4. Clique na guia **Adicionar** . A folha do menu **novo recurso** é aberta e mostra a lista de extensões de máquina virtual disponíveis. 
5. Selecione a **Azure monitor, atualização e** extensão de gerenciamento de configuração e clique em **criar**. A folha instalar configuração da **extensão** é aberta.

>[!NOTE]
> Se você não vir a extensão de **Azure monitor, atualização e gerenciamento de configuração** listada em seu Marketplace, entre em contato com seu operador de Azure Stack para disponibilizá-lo.

6. Na folha **instalar** configuração de extensão, Cole a **ID do espaço de trabalho** e a **chave do espaço de trabalho (chave primária)** que você copiou no bloco de notas no procedimento anterior.
7.  Quando terminar de fornecer as definições de configuração necessárias, clique em **OK**.
8. Depois que a instalação da extensão for concluída, seu status será exibido como **provisionamento bem-sucedido**. Pode levar até uma hora para que a máquina virtual apareça no portal da central de segurança.

Para obter mais informações sobre como instalar e configurar o agente para Windows, consulte [conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para a solução de problemas do agente para Linux, consulte [solucionar problemas do agente do Azure log Analytics Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. No portal da central de segurança no Azure, em **computação**, você tem uma visão geral de todas as VMs e computadores junto com suas recomendações. A central de segurança também revela qualquer detecção desses computadores em alertas de segurança.

  ![Painel Computação][6]

Estão representados dois tipos de ícone no painel **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) VM do Azure (Azure Stack VMs serão mostradas neste grupo)

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, você poderá remover a extensão da máquina virtual por meio do portal de Azure Stack.

Para remover a extensão:

1. Abra o **portal de Azure Stack**.
2. Vá para a página **máquinas virtuais** , selecione a máquina virtual da qual você deseja remover a extensão.
3. Selecione **extensões**, selecione a extensão **Microsoft. EnterpriseCloud. Monitoring**.
4. Clique em **desinstalar**e confirme sua seleção clicando em **Sim**.

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você provisionou a extensão de Azure Monitor, atualização e gerenciamento de configuração em uma máquina virtual em execução no Azure Stack. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
