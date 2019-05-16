---
title: Configurar o agente do Log Analytics do Azure para computadores de Windows híbrida | Documentos da Microsoft
description: Neste início rápido, irá aprender como implantar o agente do Log Analytics para computadores Windows em execução fora do Azure e ativar a recolha de dados com o Log Analytics.
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
ms.date: 04/09/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 15885137b9559bf34fb2b985398401af09caa629
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602991"
---
# <a name="configure-the-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Configurar o agente do Log Analytics para computadores Windows num ambiente híbrido
[O Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) pode recolher dados diretamente a partir de computadores Windows físicos ou virtuais para um único repositório para análise e correlação detalhadas. Log Analytics pode recolher dados de um datacenter ou outro ambiente de cloud. Este início rápido mostra-lhe como configurar e recolher dados do seu computador Windows em alguns passos simples.  Para obter informações sobre as VMs do Windows Azure, consulte [recolher dados sobre máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Para compreender a configuração suportada, veja [sistemas de operativos Windows suportados](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, introduza **do Log Analytics**. À medida que escreve, os filtros de lista com base na sua entrada. Selecione **do Log Analytics**:

    ![Portal do Azure](media/quick-collect-windows-computer/azure-portal-01.png)
  
2. Selecione **criar**e, em seguida, forneça estes detalhes:

   * Introduza um nome para a nova **área de trabalho do Log Analytics**. Algo como **DefaultLAWorkspace**.
   * Selecione um **subscrição** para ligar a. Se a predefinição não for aquele que pretende utilizar, selecione outra lista.
   * Para o **grupo de recursos**, selecione um grupo de recursos existente que contém um ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas. Aqui está uma lista dos [regiões em que está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).  
   * Se estiver a criar uma área de trabalho numa subscrição que criou depois de 2 de Abril de 2018, a área de trabalho irá utilizar automaticamente o **por GB** plano de preços. Não será possível selecionar um escalão de preço. Se estiver a criar uma área de trabalho numa subscrição que criou antes de 2 de Abril de 2018, ou numa subscrição que estava associada a uma inscrição EA já existente, selecione o escalão de preço que pretende utilizar. Consulte a [detalhes de preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para obter informações sobre as camadas.

        ![Criar recurso do Log Analytics](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Depois de fornecer as informações necessárias na **área de trabalho do Log Analytics** painel, selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho está a ser criada, pode acompanhar o progresso em **notificações** no menu.

## <a name="get-the-workspace-id-and-key"></a>Obter o ID de área de trabalho e a chave
Antes de instalar o Microsoft Monitoring Agent para Windows, terá do ID de área de trabalho e a chave para a área de trabalho do Log Analytics. O Assistente de configuração precisa dessas informações para configurar corretamente o agente e assegurar que consegue comunicar com o Log Analytics.  

1. No canto superior esquerdo do portal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza **do Log Analytics**. À medida que escreve, os filtros de lista com base na sua entrada. Selecione **Log Analytics**.
2. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho que criou anteriormente. (Pode ter-lhe o nome **DefaultLAWorkspace**.)
3. Selecione **definições avançadas**:

    ![Definições avançadas do log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.
5. Copie os valores para a direita da **ID da área de trabalho** e **chave primária**. Cole-os no seu editor favorito.

## <a name="install-the-agent-for-windows"></a>Instalar o agente para Windows
Os seguintes passos instale e configure o agente do Log Analytics no Azure e o Azure Government. Usará o programa de configuração do Microsoft Monitoring Agent para instalar o agente no seu computador.

1. Continuando do conjunto anterior de passos, o **servidores do Windows** página, selecione a **transferir o agente do Windows** versão que pretende transferir. Selecione a versão adequada para a arquitetura de processador do sistema operativo Windows.
2. Execute a Configuração para instalar o agente no seu computador.
2. Na página **Bem-vindo**, selecione **Seguinte**.
3. Na página **Termos de Licenciamento**, leia a licença e selecione **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e selecione **Seguinte**.
5. Sobre o **opções de configuração do agente** página, ligar o agente ao Azure Log Analytics e, em seguida, selecione **próxima**.
6. Sobre o **do Azure Log Analytics** página, conclua estes passos:
   1. Cole o **ID da área de trabalho** e **chave da área de trabalho (chave primária)** que copiou anteriormente. Se o computador deva reportar a uma área de trabalho do Log Analytics no Azure Government, selecione **do Azure US Government** no **na Cloud do Azure** lista.  
   2. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, selecione **Avançadas** e forneça o URL e o número da porta do servidor proxy. Se o servidor proxy requer autenticação, introduza o nome de utilizador e palavra-passe para a autenticação com o servidor proxy e, em seguida, selecione **seguinte**.  
7. Selecione **seguinte** depois de adicionar as definições de configuração:

    ![Configuração do Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

8. Na página **Pronto para Instalar**, reveja as suas opções e selecione **Instalar**.
9. Sobre o **configuração concluída com êxito** página, selecione **concluir**.

Quando a instalação e configuração estiver concluída, o Microsoft Monitoring Agent é apresentada no painel de controlo. Pode rever a configuração e confirmar que o agente está ligado ao Log Analytics. Quando estiver ligado, no **do Azure Log Analytics** guia, o agente apresenta esta mensagem: **O Microsoft Monitoring Agent foi ligado com êxito para o serviço Microsoft Log Analytics.**<br><br> ![Estado de ligação do MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho
Log Analytics pode recolher eventos que especifique do registo de eventos do Windows e contadores de desempenho para análises mais tempo do prazo e relatórios. Também pode demorar ação quando detetar uma determinada condição. Siga estes passos para configurar a recolha de eventos a partir do registo de eventos do Windows e de vários contadores de desempenho comuns para começar.  

1. No canto inferior esquerdo do portal do Azure, selecione **mais serviços**. Na caixa de pesquisa, introduza **do Log Analytics**. À medida que escreve, os filtros de lista com base na sua entrada. Selecione **Log Analytics**.
2. Selecione **definições avançadas**:

    ![Definições avançadas do log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
 
3. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.  
4. Adicione um registo de eventos ao introduzir o nome do registo. Introduza **System**e, em seguida, selecione o sinal de adição (**+**).  
5. Na tabela, selecione o **erro** e **aviso** gravidades.
6. Selecione **guardar** na parte superior da página.
7. Selecione **contadores de desempenho do Windows** para ativar a recolha de contadores de desempenho num computador Windows.
8. Quando configura pela primeira vez contadores de desempenho do Windows para uma nova área de trabalho do Log Analytics, é-lhe fornecido a opção para criar rapidamente vários contadores comuns. Cada opção está listada, com uma caixa de verificação junto ao mesmo:

    ![Contadores de desempenho do Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecione **adicionar os contadores de desempenho selecionados**. Os contadores são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.

9. Selecione **guardar** na parte superior da página.

## <a name="view-collected-data"></a>Ver dados recolhidos
Agora que ativou a recolha de dados, vamos executar uma pesquisa de registos simples para ver alguns dados do computador de destino.  

1. No portal do Azure, na área de trabalho selecionada, selecione o **registos** mosaico.  
2. Sobre o **pesquisa de registos** painel, na caixa de consulta, introduza **desempenho** e clique em **executar** no topo da caixa de consulta:
 
    ![Pesquisa de registos do log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta nesta imagem devolveu 735 registos de desempenho:

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Pode remover o agente do computador e eliminar a área de trabalho do Log Analytics, se já não precisar deles.  

Para remover o agente, conclua estes passos:

1. Abra o painel de controle.
2. Abra **Programas e Funcionalidades**.
3. Na **programas e funcionalidades**, selecione **Microsoft Monitoring Agent** e, em seguida, selecione **desinstalação**.

Para eliminar a área de trabalho do Log Analytics que criou anteriormente, selecione-o e, na página de recursos, selecione **eliminar**:

![Eliminar área de trabalho de Log Analyticss](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que está a recolher operacionais e dados de desempenho do seu computador Windows, pode começar facilmente a explorar, analisar e atuar sobre os dados recolhidos, para *gratuita*.  

Para saber como ver e analisar os dados, avance para o tutorial:

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](tutorial-viewdata.md)
