---
title: Recolher dados do computador híbrido Windows com o Monitor Azure
description: Neste arranque rápido, você aprenderá a implementar o agente Log Analytics para computadores Windows que executam fora do Azure e permitir á recolha de dados com Registos do Monitor Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 11b61612a261b32e2d15b5dc70005b18aa112ed4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240346"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Recolher dados de um computador Windows em ambiente híbrido com o Monitor Azure

O [Azure Monitor](../overview.md) pode recolher dados diretamente dos seus computadores Windows físicos ou virtuais no seu ambiente para um espaço de trabalho de Log Analytics para análise detalhada e correlação. A instalação do [agente Log Analytics](../platform/log-analytics-agent.md) permite ao Monitor Azure recolher dados de um datacenter ou de outro ambiente na nuvem. Este início rápido mostra-lhe como configurar e recolher dados do seu computador Windows em alguns passos simples. Para obter informações sobre VMs Do Windows Azure, consulte [Recolher dados sobre máquinas virtuais Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Para compreender a configuração suportada, consulte [sistemas operativos Suportados Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) e [configuração](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)de firewall da rede .
 
Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em . 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

1. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

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


## <a name="get-the-workspace-id-and-key"></a>Obtenha o ID do espaço de trabalho e a chave

Antes de instalar o agente Log Analytics para windows (também designado por Microsoft Monitoring Agent (MMA)), precisa do ID do espaço de trabalho e da chave para o seu espaço de trabalho Log Analytics. O assistente de configuração precisa desta informação para configurar corretamente o agente e garantir que pode comunicar com o Monitor Azure.  

1. No canto superior esquerdo do portal Azure, selecione **Todos os serviços.** Na caixa de pesquisa, introduza **o Log Analytics**. À medida que escreve, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que criou anteriormente. (Pode ter-lhe chamado **DefaultLAWorkspace**.)

3. Selecione **definições avançadas:**

    ![Definições avançadas de Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.

5. Copie os valores à direita do Id do **Espaço de Trabalho** e da Chave **Primária**. Cola-os ao teu editor favorito.

## <a name="install-the-agent-for-windows"></a>Instalar o agente para Windows

Os seguintes passos instalam e configuram o agente para Log Analytics no Governo Azure e Azure. Utilizará o programa de configuração do agente de monitorização da Microsoft para instalar o agente no seu computador.

1. Continuando a partir do conjunto de passos anteriores, na página **do Windows Servers,** selecione a versão **Descarregamento do Windows Agent** que pretende descarregar. Selecione a versão adequada para a arquitetura do processador do seu sistema operativo Windows.

2. Execute a Configuração para instalar o agente no seu computador.

3. Na página **Bem-vindo**, selecione **Seguinte**.

4. Na página **Termos de Licenciamento**, leia a licença e selecione **Aceito**.

5. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e selecione **Seguinte**.

6. Na página Opções de Configuração do **Agente,** ligue o agente ao Azure Log Analytics e, em seguida, selecione **Next**.

7. Na página **Azure Log Analytics,** complete estes passos:

   1. Colhe na id do **espaço de trabalho** e na chave do espaço de trabalho **(Chave Primária)** que copiou anteriormente. Se o computador apresentar-se a um espaço de trabalho de Log Analytics no Governo Azure, **selecione Azure US Government** na lista **Azure Cloud.**  
   2. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy. Se o seu servidor proxy necessitar de autenticação, introduza o nome de utilizador e a palavra-passe para autenticação com o servidor proxy e, em seguida, selecione **Next**.  

8. Selecione **Seguinte** depois de adicionar as definições de configuração:

    ![Configuração do agente de monitorização da Microsoft](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Na página **Pronto para Instalar**, reveja as suas opções e selecione **Instalar**.

10. Na **página Configuração concluída com sucesso,** selecione **Terminar**.

Quando a instalação e a configuração estiverem terminadas, o Agente de Monitorização da Microsoft aparece no Painel de Controlo. Pode rever a sua configuração e verificar se o agente está ligado ao espaço de trabalho do Log Analytics. Quando ligado, no separador **Azure Log Analytics,** o agente apresenta esta mensagem: **o Microsoft Monitoring Agent ligou-se com sucesso ao serviço Microsoft Log Analytics.**<br><br> ![Estado de ligação MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

O Azure Monitor pode recolher eventos que especifica a partir dos contadores de registo e desempenho do Windows para análise e reportagem a longo prazo. Também pode tomar medidas quando detetar uma determinada condição. Siga estes passos para configurar a recolha de eventos a partir do registo de eventos do Windows e de vários contadores de desempenho comuns para começar.  

1. No canto inferior esquerdo do portal Azure, selecione **Mais serviços.** Na caixa de pesquisa, introduza **o Log Analytics**. À medida que escreve, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Selecione **definições avançadas:**

    ![Definições avançadas de Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.  

4. Adicione um registo de eventos inserindo o nome do registo. Introduza o **Sistema**e,**+** em seguida, selecione o sinal de mais ().  

5. Na tabela, selecione as severidades **de Erro** e **Aviso.**

6. Selecione **Guardar** na parte superior da página.

7. Selecione **Contadores de Desempenho do Windows** para permitir a recolha de contadores de desempenho num computador Windows.

8. Quando configura os contadores de desempenho do Windows para um novo espaço de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. Cada opção está listada, com uma caixa de verificação ao lado:

    ![Contadores de desempenho do Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecione Adicionar os contadores de **desempenho selecionados**. Os contadores são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundos.

9. Selecione **Guardar** na parte superior da página.

## <a name="view-collected-data"></a>Ver dados recolhidos

Agora que permitiu a recolha de dados, vamos fazer uma simples pesquisa de registo para ver alguns dados do computador alvo.  

1. No espaço de trabalho selecionado, a partir do painel da esquerda, selecione **Logs**.

2. Na página de consulta de `Perf` Registos, digite no editor de consulta e selecione **Executar**.
 
    ![Pesquisa de registo de Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta nesta imagem devolveu 10.000 registos de Desempenho. Os resultados serão significativamente menos.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Pode remover o agente do seu computador e eliminar o espaço de trabalho do Log Analytics se já não precisar deles.  

Para remover o agente, complete estes passos:

1. Abra o Painel de Controlo.

2. Abra **Programas e Funcionalidades**.

3. Em **Programas e Funcionalidades,** selecione **O Agente de Monitorização** da Microsoft e, em seguida, selecione **Desinstalar**.

Para eliminar o espaço de trabalho do Log Analytics que criou anteriormente, selecione-o e, na página de recursos, selecione **Delete:**

![Eliminar o espaço de trabalho do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes

Agora que está a recolher dados operacionais e de desempenho do seu computador Windows, pode facilmente começar a explorar, analisar e agir sobre os dados que recolhe, *gratuitamente.*  

Para aprender a visualizar e analisar os dados, continue para o tutorial:

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](tutorial-viewdata.md)
