---
title: Início Rápido do Centro de Segurança do Azure - Incluir os computadores Windows no Centro de Segurança | Microsoft Docs
description: Este guia de introdução mostra-lhe como aprovisionar o MMA (Microsoft Monitoring Agent) num computador Windows.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: bee4618ff08c89bbdab7413ca7f7f74a266d96dd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077909"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Início rápido: Integrar computadores do Windows ao centro de segurança do Azure
Depois de incluir as subscrições do Azure, pode ativar o Centro de Segurança para os recursos em execução fora do Azure, por exemplo, no local ou em outras clouds, ao aprovisionar o MMA.

Este início rápido mostra-lhe como instalar o MMA num computador Windows.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Tem de estar no escalão de preço Standard do Centro de Segurança antes de começar este início rápido. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Pode experimentar o escalão Standard do Centro de segurança sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Adicionar novo computador Windows

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

   ![Descrição geral do Centro de Segurança][2]

3. No menu principal do Centro de Segurança, selecione **Introdução**.
4. Selecione o separador **Introdução**.

   ![Introdução][3]

5. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que queira utilizar.

    ![Adicionar computador não pertencente ao Azure](./media/quick-onboard-windows-computer/non-azure.png)

   O painel **Agente Direto** abre-se, com uma ligação para transferir o agente do Windows e as chaves para o seu ID da área de trabalho a utilizar na configuração do agente.

6. Selecione a ligação **Transferir o Agente do Windows** aplicável ao seu tipo de processador do computador para transferir o ficheiro de configuração.

7. À direita de **ID da Área de Trabalho**, selecione o ícone de copiar e cole o ID no Bloco de Notas.

8. À direita de **Chave Primária**, selecione o ícone de copiar e cole a chave no Bloco de Notas.

## <a name="install-the-agent"></a>Instalar o agente
Agora tem de instalar o ficheiro transferido no computador de destino.

1. Copie o ficheiro para o computador de destino e Execute a Configuração.
2. Na página **Bem-vindo**, selecione **Seguinte**.
3. Na página **Termos de Licenciamento**, leia a licença e selecione **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e selecione **Seguinte**.
5. Na página **Opções de Configuração do Agente**, escolha ligar o agente ao Azure Log Analytics e selecione **Seguinte**.
6. Na página **Azure Log Analytics**, cole o **ID da Área de Trabalho** e a **Chave da Área de Trabalho (Chave Primária)** que copiou para o Bloco de Notas no procedimento anterior.
7. Caso o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione **Azure US Government**, na lista pendente **Azure Cloud**.  Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy.
8. Selecione **Seguinte**, depois de indicar as definições de configuração necessárias.

   ![Instalar o agente][5]

9. Na página **Pronto para Instalar**, reveja as suas opções e selecione **Instalar**.
10. Na página **Configuração concluída com êxito**, selecione **Concluir**

Quando terminar, o **Microsoft Monitoring Agent** aparece no **Painel de Controlo**. Pode rever a configuração e confirmar que o agente está ligado.

Para obter mais informações sobre como instalar e configurar o agente, veja [Ligar computadores Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. Em **Computação**, tem uma descrição geral de todas as VMs e computadores, juntamente com recomendações. Cada coluna representa um conjunto de recomendações. A cor representa o estado de segurança atual da VM ou do computador para essa recomendação. O Centro de Segurança também apresenta quaisquer deteções destes computadores nos alertas de Segurança.

  ![Painel Computação][6]

Estão representados dois tipos de ícone no painel **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) VM do Azure

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover o agente do computador Windows.

Para remover o agente:

1. Abra o **Painel de Controlo**.
2. Abra **Programas e Funcionalidades**.
3. Em **Programas e Funcionalidades**, selecione **Microsoft Monitoring Agent** e clique em **Desinstalar**.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, aprovisionou o MMA num computador Windows. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
