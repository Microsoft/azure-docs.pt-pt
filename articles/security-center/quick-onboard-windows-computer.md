---
title: Carregar computadores Windows para o Centro de Segurança do Azure
description: Este quickstart mostra-lhe como providenciar o agente Log Analytics num computador Windows.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2cec55b7c9450070d2e692fae50eff9ea880c54e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013746"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Início Rápido: Incluir computadores Windows no Centro de Segurança do Azure
Depois de embarcar nas suas subscrições Azure, pode ativar o Security Center para recursos que correm fora de Azure, por exemplo, no local ou noutras nuvens, fornecendo o agente Log Analytics.

Este arranque rápido mostra-lhe como instalar o agente Log Analytics num computador Windows.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Deve estar no nível de preços padrão do Security Center antes de iniciar este arranque rápido. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Pode tentar o Standard do Centro de Segurança sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Adicionar novo computador Windows

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure,** selecione **Security Center**. **Centro de Segurança - A visão geral** abre.

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
7. Caso o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione **Azure US Government**, na lista pendente **Azure Cloud**. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy.
8. Selecione **Seguinte**, depois de indicar as definições de configuração necessárias.

   ![Instalar o agente][5]

9. Na página **Pronto para Instalar**, reveja as suas opções e selecione **Instalar**.
10. Na página **Configuração concluída com êxito**, selecione **Concluir**

Quando concluído, o **agente Log Analytics** aparece no Painel de **Controlo**. Pode rever a configuração e confirmar que o agente está ligado.

Para obter mais informações sobre como instalar e configurar o agente, veja [Ligar computadores Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. Em **Computação**, tem uma descrição geral de todas as VMs e computadores, juntamente com recomendações. Cada coluna representa um conjunto de recomendações. A cor representa o estado de segurança atual da VM ou do computador para essa recomendação. O Centro de Segurança também apresenta quaisquer deteções destes computadores nos alertas de Segurança.

  ![Painel Computação][6]

Estão representados dois tipos de ícone no painel **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) VM do Azure

## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não for necessário, pode remover o agente do computador Windows.

Para remover o agente:

1. **Painel de controlo aberto**.
2. Abra **Programas e Funcionalidades**.
3. Em **Programas e Funcionalidades**, selecione **o agente Log Analytics** e clique em **Desinstalar**.

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, ateou o agente Log Analytics num computador Windows. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: definir e avaliar as políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
