---
title: Máquinas virtuais de onboard Azure Stack para o Centro de Segurança Azure
description: Este quickstart mostra-lhe como fornecer a extensão virtual de máquina seletiva, atualização e configuração do Monitor, Atualização e Configuração numa máquina virtual Azure Stack.
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
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73686519"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Quickstart: A bordo das suas máquinas virtuais Azure Stack para o Centro de Segurança
Depois de embarcar na subscrição do Azure, pode ativar o Security Center para proteger as suas máquinas virtuais em funcionamento no Azure Stack, adicionando a extensão virtual do **Monitor de Atualização, Atualização e Gestão** de Configuração do mercado Azure Stack.

Este quickstart mostra-lhe como adicionar a extensão virtual de máquina seletiva, atualização **e gestão** de configurações do Azure numa máquina virtual (Linux e Windows são ambos suportados) em funcionamento no Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Deve ter uma assinatura Azure no nível Standard do Security Center antes de iniciar este arranque rápido. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Pode experimentar o nível Padrão do Centro de Segurança sem custos durante 30 dias. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Selecione o seu espaço de trabalho no Centro de Segurança Azure

1. Assine no [portal Azure.](https://azure.microsoft.com/features/azure-portal/)
2. No menu **Microsoft Azure,** selecione **Security Center**. **Centro de Segurança - Descrição Geral** é aberto. 

   ![Descrição geral do Centro de Segurança][2]

3. No menu principal do Centro de Segurança, selecione **Introdução**.
4. Selecione o separador **Introdução**.

   ![Introdução][3]

5. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione este espaço de trabalho ou outro espaço de trabalho para o VM azure Stack reportar dados de segurança.

    ![Adicionar computador não pertencente ao Azure](./media/quick-onboard-windows-computer/non-azure.png)

   A lâmina **do Agente Direto** abre com um link para descarregar o agente e as chaves para o seu ID do espaço de trabalho para usar na configuração do agente.

   >[!NOTE]
   > Não é necessário descarregar manualmente o agente. O agente será instalado como uma extensão VM nos degraus abaixo.

6. À direita de **ID da Área de Trabalho**, selecione o ícone de copiar e cole o ID no Bloco de Notas.

7. À direita de **Chave Primária**, selecione o ícone de copiar e cole a chave no Bloco de Notas.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Adicione a extensão da máquina virtual às suas máquinas virtuais existentes do Azure Stack
Tem agora de adicionar a extensão virtual da máquina de **Monitorização, Atualização e Configuração do Monitor, Atualização e Configuração** às máquinas virtuais em funcionamento no seu Azure Stack.

1. Num novo separador de navegador, inicie sessão no portal **Azure Stack.**
2. Vá à página de **máquinas Virtuais,** selecione a máquina virtual que pretende proteger com o Centro de Segurança. Para obter informações sobre como criar uma máquina virtual no Azure Stack, consulte [este arranque rápido para máquinas virtuais windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou este arranque rápido para [máquinas virtuais Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensions** (Extensões). A lista de extensões de máquinas virtuais instaladas nesta máquina virtual é mostrada.
4. Clique no separador **Adicionar.** A lâmina do menu **New Resource** abre e mostra a lista de extensões de máquinavirtual disponíveis. 
5. Selecione a extensão de Gestão de **Monitorização, Atualização e Configuração do Azure** e clique em **Criar**. A lâmina de configuração de **extensão de instalação** abre-se.

>[!NOTE]
> Se não vir a extensão de **Monitorização, Atualização e Gestão** de Configurações e Monitorização de Configuração do Azure listada no seu mercado, contacte o operador do Azure Stack para o disponibilizar.

6. Na lâmina de configuração de **extensão De instalação,** cola o ID do **espaço de trabalho** e a chave do espaço de trabalho **(Chave Primária)** que copiou no Bloco de Notas no procedimento anterior.
7. Quando terminar, fornecendo as definições de configuração necessárias, clique em **OK**.
8. Uma vez concluída a instalação da extensão, o seu estatuto mostrar-se-á como **o provisionamento bem sucedido**. Pode levar até uma hora para a máquina virtual aparecer no portal do Centro de Segurança.

Para mais informações sobre a instalação e configuração do agente para windows, consulte [Connect Windows computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para problemas de resolução de problemas com o Linux, consulte [Troubleshoot Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. No portal Do Centro de Segurança em Azure, no âmbito da **Compute,** tem uma visão geral de todos os VMs e computadores, juntamente com as suas recomendações. O Centro de Segurança também mostra qualquer deteção para estes computadores em alertas de Segurança.

  ![Painel Computação][6]

Estão representados dois tipos de ícone no painel **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack VMs vai mostrar neste grupo)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover a extensão da máquina virtual através do portal Azure Stack.

Para remover a extensão:

1. Abra o **Portal da Pilha Azure.**
2. Vá à página de **máquinas virtuais,** selecione a máquina virtual a partir da qual pretende remover a extensão.
3. **Selecione Extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **Desinstalar**e confirmar a sua seleção clicando **em Sim**.

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, disponibilizou a extensão de Monitorização, Atualização e Gestão de Configurações e Monitorização de Configuração de Uma máquina virtual em funcionamento no Azure Stack. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: definir e avaliar as políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
