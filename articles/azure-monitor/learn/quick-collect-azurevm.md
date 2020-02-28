---
title: Recolha dados de uma máquina virtual Azure com o Monitor Azure Microsoft Docs
description: Saiba como ativar a Extensão de VM do agente do Log Analytics e a recolha de dados das VMs do Azure com o Log Analytics.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2e63b2ca0ee7246009068e6a2875795a14aeddb3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660194"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Recolher dados de uma máquina virtual Azure com o Monitor Azure

O [Azure Monitor](../overview.md) pode recolher dados diretamente das suas máquinas virtuais Azure para um espaço de trabalho de Log Analytics para análise detalhada e correlação. A instalação da extensão VM De Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) permite ao Monitor Azure recolher dados dos seus VMs Azure. Este quickstart mostra-lhe como configurar e recolher dados dos seus VMs Azure Linux ou Windows utilizando a extensão VM com alguns passos fáceis.  
 
Este início rápido pressupõe que tem uma máquina virtual do Azure. Se não for o caso, pode [criar uma VM do Windows](../../virtual-machines/windows/quick-create-portal.md) ou [criar uma VM do Linux](../../virtual-machines/linux/quick-create-cli.md) ao seguir os nossos inícios rápidos de VM.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

    ![Portal do Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **Criar**e, em seguida, selecione escolhas para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criar lâmina de recursos Log Analytics](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel de espaço de **trabalho Log Analytics,** selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Ativar a Extensão de VM do Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Para máquinas virtuais do Windows e Linux já implementadas no Azure, instale o agente do Log Analytics com a Extensão de VM do Log Analytics. A utilização da extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho do Log Analytics que especificar. O agente também é atualizado automaticamente quando uma versão mais recente é lançada, garantindo que tem as mais recentes funcionalidades e correções. Antes de prosseguir, verifique se a VM está a ser executado caso contrário, o processo falhará concluir com êxito.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics. 

1. No portal Azure, selecione **Todos os serviços encontrados** no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Na lista de áreas de trabalho do Log Analytics, selecione *DefaultLAWorkspace*, criada anteriormente.

3. No menu à esquerda, em Fontes de Dados do Workspace, selecione **máquinas Virtuais**.  

4. Na lista de **Máquinas virtuais**, selecione uma máquina virtual na qual quer instalar o agente. Tenha em atenção que o **Estado de ligação do Log Analytics** da VM indica **Não ligado**.

5. Nos detalhes da máquina virtual, selecione **Ligar**. O agente é instalado e configurado automaticamente para a área de trabalho do Log Analytics. Este processo demora alguns minutos, durante o qual o **Estado** mostra **A Ligação**.

6. Depois de instalar e ligar o agente, o **Estado de ligação do Log Analytics** será atualizado para **Esta área de trabalho**.

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

O Azure Monitor pode recolher eventos a partir dos registos de eventos do Windows ou dos contadores de desempenho do Linux Syslog e do desempenho que especifica para análise e reportagem a longo prazo, e tomar medidas quando uma determinada condição é detetada. Siga estes passos para configurar a recolha de eventos do registo do sistema do Windows e do Linux Syslog e vários contadores de desempenho comuns para começar.  

### <a name="data-collection-from-windows-vm"></a>Recolha de dados da VM do Windows

1. Selecione **Definições avançadas**.

    ![Definições Avançadas do Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.

3. Adicione um registo de eventos ao escrever o nome do registo.  Digite **o Sistema** e, em seguida, selecione o sinal de mais **+** .

4. Na tabela, verifique as gravidades **Erro** e **Aviso**.

5. Selecione **Guardar** na parte superior da página para guardar a configuração.

6. Selecione **Dados de Desempenho do Windows** para ativar a recolha de contadores de desempenho num computador Windows.

7. Quando configurar os contadores de desempenho do Windows pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho do Windows predefinidos](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Selecione Adicionar os contadores de **desempenho selecionados**.  Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.
  
8. Selecione **Guardar** na parte superior da página para guardar a configuração.

### <a name="data-collection-from-linux-vm"></a>Recolha de dados da VM do Linux

1. Selecione **Syslog**.  

2. Adicione um registo de eventos ao escrever o nome do registo.  Digite **syslog** e, em seguida, selecione o sinal de mais **+** .  

3. Na tabela, desmarque as severidades **Info,** **Notice** e **Debug**. 

4. Selecione **Guardar** na parte superior da página para guardar a configuração.

5. Selecione **Dados de Desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Linux. 

6. Quando configurar os contadores de desempenho do Linux pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho do Windows predefinidos](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecione **Aplicar abaixo a configuração para as minhas máquinas** e, em seguida, selecione Adicionar os contadores de **desempenho selecionados**.  Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.  

7. Selecione **Guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Agora que ativou a recolha de dados, vamos executar um exemplo de pesquisa de registo simples para ver alguns dados a partir das VMs de destino.  

1. No espaço de trabalho selecionado, a partir do painel da esquerda, selecione **Logs**.

2. Na página de consulta de Registos, digite `Perf` no editor de consulta e selecione **Executar**.

    ![Exemplo de consulta de pesquisa de registos do Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Por exemplo, a consulta na imagem seguinte devolveu 10.000 registos de desempenho. Os resultados serão significativamente menos.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessária, elimine a área de trabalho do Log Analytics. Para tal, selecione o espaço de trabalho log Analytics que criou anteriormente e na página de recursos selecione **Delete**.


![Eliminar recurso do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes

Agora que está a recolher dados operacionais e de desempenho das máquinas virtuais do Windows e Linux, pode começar facilmente a explorar, analisar e efetuar ações nos dados recolhidos *gratuitamente*.  

Para saber como ver e analisar os dados, avance para o tutorial.

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
