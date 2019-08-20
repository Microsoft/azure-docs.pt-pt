---
title: Recolher dados sobre Máquinas Virtuais do Azure | Microsoft Docs
description: Saiba como ativar a Extensão de VM do agente do Log Analytics e a recolha de dados das VMs do Azure com o Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 2ace0b2a9ec24932ddc7d2660666e647b56e53e8
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624510"
---
# <a name="collect-data-about-azure-virtual-machines"></a>Recolher dados sobre Máquinas Virtuais do Azure

O [Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) pode recolher dados diretamente das máquinas virtuais do Azure e de outros recursos no seu ambiente para um único repositório para análise e correlação detalhadas. Este início rápido mostra como configurar e recolher dados das VMs do Linux e Windows do Azure em alguns passos simples.  
 
Este início rápido pressupõe que tem uma máquina virtual do Azure. Se não for o caso, pode [criar uma VM do Windows](../../virtual-machines/windows/quick-create-portal.md) ou [criar uma VM do Linux](../../virtual-machines/linux/quick-create-cli.md) ao seguir os nossos inícios rápidos de VM.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.

    ![Portal do Azure](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criar Log Analytics folha de recursos](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Depois de fornecer as informações necessárias no painel **log Analytics espaço de trabalho** , selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Ativar a Extensão de VM do Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Para máquinas virtuais do Windows e Linux já implementadas no Azure, instale o agente do Log Analytics com a Extensão de VM do Log Analytics. A utilização da extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho do Log Analytics que especificar. O agente é também atualizado automaticamente e assegura que tem as funcionalidades e correções mais recentes. Antes de prosseguir, verifique se a VM está a ser executado caso contrário, o processo falhará concluir com êxito.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics. 

1. Na portal do Azure, selecione **todos os serviços** encontrados no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Na lista de áreas de trabalho do Log Analytics, selecione *DefaultLAWorkspace*, criada anteriormente.

3. No menu à esquerda, em fontes de dados do espaço de trabalho, selecione **máquinas virtuais**.  

4. Na lista de **Máquinas virtuais**, selecione uma máquina virtual na qual quer instalar o agente. Tenha em atenção que o **Estado de ligação do Log Analytics** da VM indica **Não ligado**.

5. Nos detalhes da máquina virtual, selecione **Ligar**. O agente é instalado e configurado automaticamente para a área de trabalho do Log Analytics. Esse processo leva alguns minutos, durante o qual o **status** mostra a **conexão**.

6. Depois de instalar e ligar o agente, o **Estado de ligação do Log Analytics** será atualizado para **Esta área de trabalho**.

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

O Log Analytics pode recolher eventos de registos do Windows ou do Linux Syslog e contadores de desempenho que especificar para análises e relatórios a longo prazo e efetuar ações quando é detetada uma determinada condição. Siga estes passos para configurar a recolha de eventos do registo do sistema do Windows e do Linux Syslog e vários contadores de desempenho comuns para começar.  

### <a name="data-collection-from-windows-vm"></a>Recolha de dados da VM do Windows

1. Selecione **Definições avançadas**.

    ![Definições Avançadas do Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png)

2. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.

3. Adicione um registo de eventos ao escrever o nome do registo.  Digite **System** e, em seguida, selecione **+** o sinal de adição.

4. Na tabela, verifique as gravidades **Erro** e **Aviso**.

5. Selecione **salvar** na parte superior da página para salvar a configuração.

6. Selecione **Dados de Desempenho do Windows** para ativar a recolha de contadores de desempenho num computador Windows.

7. Quando configurar os contadores de desempenho do Windows pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho do Windows predefinidos](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Selecione **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.
  
8. Selecione **salvar** na parte superior da página para salvar a configuração.

### <a name="data-collection-from-linux-vm"></a>Recolha de dados da VM do Linux

1. Selecione **Syslog**.  

2. Adicione um registo de eventos ao escrever o nome do registo.  Digite **syslog** e, em seguida, selecione **+** o sinal de adição.  

3. Na tabela, desmarque as **informações**de severidades, **aviso** e **depuração**. 

4. Selecione **salvar** na parte superior da página para salvar a configuração.

5. Selecione **Dados de Desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Linux. 

6. Quando configurar os contadores de desempenho do Linux pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho do Windows predefinidos](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Selecione **aplicar configuração abaixo ao em meus computadores** e, em seguida, selecione **adicionar os contadores de desempenho selecionados**.  Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.  

7. Selecione **salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Agora que ativou a recolha de dados, vamos executar um exemplo de pesquisa de registo simples para ver alguns dados a partir das VMs de destino.  

1. No portal do Azure, navegue até Log Analytics e selecione a área de trabalho criada anteriormente.

2. Selecione o bloco **pesquisa de logs** e, no painel pesquisa de logs, no tipo `Perf` de campo de consulta e pressione Enter ou selecione o botão Pesquisar à direita do campo de consulta.

    ![Exemplo de consulta de pesquisa de registos do Log Analytics](./media/quick-collect-azurevm/log-analytics-portal-perf-query.png) 

Por exemplo, a consulta na imagem seguinte devolveu 735 registos de desempenho.  Os resultados serão significativamente menos.

![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessária, elimine a área de trabalho do Log Analytics. Para fazer isso, selecione o espaço de trabalho Log Analytics que você criou anteriormente e, na página de recursos, selecione **excluir**.


![Eliminar recurso do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que está a recolher dados operacionais e de desempenho das máquinas virtuais do Windows e Linux, pode começar facilmente a explorar, analisar e efetuar ações nos dados recolhidos *gratuitamente*.  

Para saber como ver e analisar os dados, avance para o tutorial.

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
