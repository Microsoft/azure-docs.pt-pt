---
title: Coletar dados de um computador Windows híbrido com Azure Monitor | Microsoft Docs
description: Neste guia de início rápido, você aprenderá a implantar o agente de Log Analytics para computadores Windows em execução fora do Azure e habilitar a coleta de dados com logs de Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 1b8888c2c8aaedce181d19c015206323ccf2b0ba
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677245"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Coletar dados de um computador Windows em um ambiente híbrido com Azure Monitor

[Azure monitor](../overview.md) pode coletar dados diretamente de você são seus computadores Windows físicos ou virtuais em seu ambiente em um espaço de trabalho log Analytics para análise e correlação detalhadas. A instalação do [agente de log Analytics](../platform/log-analytics-agent.md) permite Azure monitor coletar dados de um datacenter ou de outro ambiente de nuvem. Este início rápido mostra-lhe como configurar e recolher dados do seu computador Windows em alguns passos simples. Para obter informações sobre VMs do Windows do Azure, consulte [coletar dados sobre máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Para entender a configuração com suporte, consulte [sistemas operacionais Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)com suporte.
 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

    ![Portal do Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criar Log Analytics folha de recursos](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel **log Analytics espaço de trabalho** , selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 


## <a name="get-the-workspace-id-and-key"></a>Obter a ID e a chave do espaço de trabalho

Antes de instalar o agente de Log Analytics para Windows (também conhecido como Microsoft Monitoring Agent (MMA)), você precisa da ID e da chave do espaço de trabalho para seu espaço de trabalho Log Analytics. O assistente de instalação precisa dessas informações para configurar corretamente o agente e garantir que ele possa se comunicar com Azure Monitor.  

1. No canto superior esquerdo da portal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite **log Analytics**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você criou anteriormente. (Você pode ter nomeado **DefaultLAWorkspace**.)

3. Selecione **Configurações avançadas**:

    ![Log Analytics configurações avançadas](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.

5. Copie os valores à direita da **ID do espaço de trabalho** e da **chave primária**. Cole-os em seu editor favorito.

## <a name="install-the-agent-for-windows"></a>Instalar o agente para Windows

As etapas a seguir instalam e configuram o agente para Log Analytics no Azure e no Azure governamental. Você usará o programa de instalação Microsoft Monitoring Agent para instalar o agente em seu computador.

1. Continuando do conjunto de etapas anterior, na página **servidores do Windows** , selecione a versão de **download do agente do Windows** que você deseja baixar. Selecione a versão apropriada para a arquitetura do processador do seu sistema operacional Windows.

2. Execute a Configuração para instalar o agente no seu computador.

3. Na página **Bem-vindo**, selecione **Seguinte**.

4. Na página **Termos de Licenciamento**, leia a licença e selecione **Aceito**.

5. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e selecione **Seguinte**.

6. Na página **Opções de instalação do agente** , conecte o agente ao log Analytics do Azure e, em seguida, selecione **Avançar**.

7. Na página **log Analytics do Azure** , conclua estas etapas:

   1. Cole a **ID do espaço de trabalho** e a **chave do espaço de trabalho (chave primária)** que você copiou anteriormente. Se o computador deve se reportar a um espaço de trabalho Log Analytics no Azure governamental, selecione **Azure EUA governamental** na lista de **nuvem do Azure** .  
   2. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy. Se o servidor proxy exigir autenticação, insira o nome de usuário e a senha para autenticação com o servidor proxy e, em seguida, selecione **Avançar**.  

8. Selecione **Avançar** depois de adicionar as definições de configuração:

    ![Microsoft Monitoring Agent configuração](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Na página **Pronto para Instalar**, reveja as suas opções e selecione **Instalar**.

10. Na página **configuração concluída com êxito** , selecione **concluir**.

Quando a instalação e a instalação forem concluídas, Microsoft Monitoring Agent aparecerá no painel de controle. Você pode examinar sua configuração e verificar se o agente está conectado ao espaço de trabalho Log Analytics. Quando conectado, na guia **log Analytics do Azure** , o agente exibe esta mensagem: **a Microsoft Monitoring Agent se conectou com êxito ao serviço Microsoft log Analytics.**<br><br> ![MMA ](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png) de status da conexão

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

Azure Monitor pode coletar eventos que você especificar no log de eventos do Windows e contadores de desempenho para análise e relatórios de longo prazo. Ele também pode tomar medidas quando detecta uma condição específica. Siga estes passos para configurar a recolha de eventos a partir do registo de eventos do Windows e de vários contadores de desempenho comuns para começar.  

1. No canto inferior esquerdo da portal do Azure, selecione **mais serviços**. Na caixa de pesquisa, digite **log Analytics**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Selecione **Configurações avançadas**:

    ![Log Analytics configurações avançadas](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.  

4. Você adiciona um log de eventos digitando o nome do log. Insira **sistema**e, em seguida, selecione o sinal de adição ( **+** ).  

5. Na tabela, selecione as severidades de **erro** e **aviso** .

6. Selecione **salvar** na parte superior da página.

7. Selecione **contadores de desempenho do Windows** para habilitar a coleta de contadores de desempenho em um computador Windows.

8. Ao configurar os contadores de desempenho do Windows pela primeira vez para um novo Log Analytics espaço de trabalho, você tem a opção de criar rapidamente vários contadores comuns. Cada opção é listada, com uma caixa de seleção ao lado dela:

    ![Contadores de desempenho do Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecione **adicionar os contadores de desempenho selecionados**. Os contadores são adicionados e predefinidos com um intervalo de amostra de coleção de dez segundos.

9. Selecione **salvar** na parte superior da página.

## <a name="view-collected-data"></a>Exibir dados coletados

Agora que você habilitou a coleta de dados, vamos executar uma pesquisa de log simples para ver alguns dados do computador de destino.  

1. No espaço de trabalho selecionado, no painel esquerdo, selecione **logs**.

2. Na página consulta de logs, digite `Perf` no editor de consultas e selecione **executar**.
 
    ![Log Analytics pesquisa de log](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta nesta imagem retornou 10.000 registros de desempenho. Os resultados serão significativamente menos.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você pode remover o agente do seu computador e excluir o Log Analytics espaço de trabalho se não precisar mais deles.  

Para remover o agente, conclua estas etapas:

1. Abra o painel de controle.

2. Abra **Programas e Funcionalidades**.

3. Em **programas e recursos**, selecione **Microsoft Monitoring Agent** e, em seguida, selecione **desinstalar**.

Para excluir o espaço de trabalho Log Analytics criado anteriormente, selecione-o e, na página recurso, selecione **excluir**:

![Excluir Log Analytics espaço de trabalho](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você está coletando dados operacionais e de desempenho do seu computador Windows, você pode começar a explorar, analisar e agir com facilidade nos dados coletados *gratuitamente*.  

Para saber como exibir e analisar os dados, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](tutorial-viewdata.md)
