---
title: Ligue as suas máquinas não-Azure ao Centro de Segurança Azure
description: Saiba como ligar as suas máquinas não-Azure ao Centro de Segurança
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 88c34be45a832f8944217630568927bc7d52fd88
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660207"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Ligue as suas máquinas não-Azure ao Centro de Segurança

O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não-Azure, mas primeiro tem de os ligar ao Azure.

Pode ligar os seus computadores não-Azure de qualquer das seguintes formas:

- Utilização de servidores ativados aZure Arc **(recomendado)**
- Das páginas do Security Center no portal Azure (**Começar** e **Inventar)**

Cada um destes é descrito nesta página.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Adicione máquinas não-Azure com Arco Azure

Os servidores ativados Azure Arc são a forma preferida de adicionar as suas máquinas não-Azure ao Centro de Segurança Azure.

Uma máquina com servidores ativados Azure Arc torna-se um recurso Azure e aparece no Centro de Segurança com recomendações como os seus outros recursos Azure.

Além disso, os servidores ativados pelo Azure Arc fornecem capacidades melhoradas, tais como a opção de ativar as políticas de configuração dos hóspedes na máquina, implementar o agente Log Analytics como uma extensão, simplificar a implementação com outros serviços Azure, e muito mais. Para uma visão geral dos benefícios, consulte [cenários suportados](../azure-arc/servers/overview.md#supported-scenarios).

Saiba mais sobre [os servidores ativados do Azure Arc](../azure-arc/servers/overview.md).

**Para implantar o Arco azul:**

- Para uma máquina, siga as instruções em [Quickstart: Ligue a máquina híbrida com servidores ativados Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Para ligar várias máquinas à escala aos servidores ativados pelo Arc, consulte [as máquinas híbridas Connect para Azure à escala](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Se estiver a bordo de máquinas em funcionamento em AWS, o conector do Security Center para a AWS lida de forma transparente com a implantação do Arco Azure para si. Saiba mais em [Ligar as suas contas AWS ao Azure Security Center](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Adicione máquinas não-Azure do portal Azure

1. A partir do menu do Security Center, abra a página **'Iniciar'.**
1. Selecione o separador **Introdução**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Começar a fazer o separador iniciar na página 'Começar'" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Abaixo **Adicione servidores não-Azure**, selecione **Configurar** .

    > [!TIP]
    > Também pode abrir máquinas adicionais a partir do botão **de adicionar servidores não-Azure** da página de **inventário.**
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Adicionar máquinas não-Azure da página de inventário de ativos":::

    É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que queira utilizar.

    Pode adicionar computadores a um espaço de trabalho existente ou criar um novo espaço de trabalho.

1. Opcionalmente, para criar um novo espaço de trabalho, **selecione Criar um novo espaço de trabalho.**

1. A partir da lista de espaços de trabalho, **selecione Add Servers** para o espaço de trabalho relevante.
    A página **de gestão de agentes** aparece.

    A partir daqui, escolha o procedimento relevante abaixo, dependendo do tipo de máquinas que está a bordo:

    - [A bordo dos seus VMs Azure Stack](#onboard-your-azure-stack-vms)
    - [A bordo das suas máquinas Linux](#onboard-your-linux-machines)
    - [A bordo das suas máquinas Windows](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-vms"></a>A bordo dos seus VMs Azure Stack

Para adicionar VMs Azure Stack, precisa das informações na página **de gestão** de Agentes e para configurar a extensão virtual da máquina virtual **Azure Monitor, Update and Configuration Management** nas máquinas virtuais em execução na sua Stack Azure.

1. A partir da página **de gestão** de agentes, copie o **ID** do Espaço de Trabalho e **a Chave Primária** no Bloco de Notas.
1. Inicie sessão no seu portal **Azure Stack** e abra a página **de máquinas Virtuais.**
1. Selecione a máquina virtual que pretende proteger com o Centro de Segurança.
    >[!TIP]
    > Para obter informações sobre como criar uma máquina virtual no Azure Stack, consulte [este arranque rápido para máquinas virtuais windows](/azure-stack/user/azure-stack-quick-windows-portal) ou este arranque rápido para [máquinas virtuais Linux](/azure-stack/user/azure-stack-quick-linux-portal).
1. Selecione **Extensões**. É apresentada a lista de extensões de máquinas virtuais instaladas nesta máquina virtual.
1. Selecione o **separador Adicionar.** O menu **New Resource** mostra a lista de extensões de máquinas virtuais disponíveis.
1. Selecione a extensão de Gestão de **Azure Monitor, Atualização e Configuração** e selecione **Criar**. A página **de configuração de extensão de instalação** abre.
    >[!NOTE]
    > Se não vir a extensão de Gestão de **Azure Monitor, Atualização e Configuração** listada no seu mercado, contacte o operador Azure Stack para o disponibilizar.
1. Na página de configuração de **extensão de instalação,** cole a chave **de ID** do espaço de trabalho e **do espaço de trabalho (chave primária)** que copiou no Bloco de Notas no passo anterior.
1. Quando completar a configuração, selecione **OK**. O estado da extensão mostrará como **Provisioning Succeeded**. Pode levar até uma hora para a máquina virtual aparecer no Centro de Segurança.

### <a name="onboard-your-linux-machines"></a>A bordo das suas máquinas Linux

Para adicionar máquinas Linux, precisa do comando WGET da página de gestão de **agentes.**

1. A partir da página **de gestão** de agentes, copie o comando **WGET** no Bloco de Notas. Guarde este ficheiro numa localização acessível a partir do seu computador Linux.
1. No seu computador Linux, abra o ficheiro com o comando WGET. Selecione todo o conteúdo e copie e cole-o numa consola terminal.
1. Quando a instalação estiver concluída, pode validar que o *omsagent* está instalado executando o comando *pgrep.* O comando devolverá o *PID omsagent.*
    Os registos do Agente podem ser encontrados em: */var/opt/microsoft/omsagent/ \<workspace id> /log/* Pode demorar até 30 minutos para que a nova máquina Linux apareça no Centro de Segurança.

### <a name="onboard-your-windows-machines"></a>A bordo das suas máquinas Windows

Para adicionar máquinas Windows, precisa de informações na página **de gestão** de Agentes e de descarregar o ficheiro de agente apropriado (32/64 bits).
1. Selecione a ligação **Transferir o Agente do Windows** aplicável ao seu tipo de processador do computador para transferir o ficheiro de configuração.
1. A partir da página **de gestão** de agentes, copie o **ID** do Espaço de Trabalho e **a Chave Primária** no Bloco de Notas.
1. Copie o ficheiro de configuração descarregado para o computador alvo e execute-o.
1. Siga o assistente de instalação **(Seguinte**, **Concordo**, **Seguinte**, **Seguinte**).
    1. Na página **Azure Log Analytics,** cole a chave **de ID** do espaço de trabalho e **do espaço de trabalho (chave primária)** que copiou para o Bloco de Notas.
    1. Se o computador deve reportar-se a um espaço de trabalho log analytics na nuvem do Governo Azure, selecione O **Governo dos EUA Azure** da lista de dropdown da **Azure Cloud.**
    1. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy.
    1. Quando introduzir todas as definições de configuração, selecione **Seguinte**.
    1. A partir da página **Pronto para Instalar,** reveja as definições a aplicar e selecione **Instalar**.
    1. Na **página de Configuração concluída com sucesso,** selecione **Terminar**.

Quando concluído, o **agente de monitorização** da Microsoft aparece no **Painel de Controlo**. Pode rever a configuração e confirmar que o agente está ligado.

Para obter mais informações sobre a instalação e configuração do agente, consulte [as máquinas Connect Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Verificação

Parabéns! Agora podem ver as suas máquinas Azure e não-Azure juntas num só lugar. Abra a página de inventário do [ativo](asset-inventory.md) e filtre para os tipos de recursos relevantes. Estes ícones distinguem os tipos:

  ![Ícone ASC para máquina não-Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Máquina não-Azure

  ![Ícone ASC para máquina Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure

  ![Ícone ASC para servidor Azure Arc](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Servidor ativado Azure Arc

## <a name="next-steps"></a>Próximos passos

Esta página mostrou-lhe como adicionar as suas máquinas não-Azure ao Centro de Segurança Azure. Para monitorizar o seu estado, utilize as ferramentas de inventário como explicado na página seguinte:

- [Explore e gere os seus recursos com ferramentas de inventário e gestão de ativos](asset-inventory.md)