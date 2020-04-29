---
title: 'Quickstart: Recolher dados de um computador linux híbrido com monitor Azure'
description: Neste arranque rápido, você aprenderá a implementar o agente Log Analytics para computadores Linux que executam fora do Azure e permitir á recolha de dados com Registos do Monitor Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1da862a7994d7db5ee8c9f090119e14f19c6b13a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80637898"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Quickstart: Recolher dados de um computador Linux em um ambiente híbrido com o Monitor Azure

O [Azure Monitor](../overview.md) pode recolher dados diretamente dos seus computadores Linux físicos ou virtuais no seu ambiente para um espaço de trabalho de Log Analytics para análise detalhada e correlação. A instalação do [agente Log Analytics](../platform/log-analytics-agent.md) permite ao Monitor Azure recolher dados de um datacenter ou de outro ambiente na nuvem. Este quickstart mostra-lhe como configurar e recolher dados do seu servidor Linux com alguns passos fáceis. Para obter informações sobre Os VMs Azure Linux, consulte [Recolher dados sobre máquinas virtuais Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Para compreender a configuração suportada, consulte [sistemas operativos Suportados Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) e [configuração](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)de firewall da rede .
 
Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

1. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

    ![Encontrar espaço de trabalho log Analytics no portal Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **Criar**e, em seguida, selecione escolhas para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criação de um espaço de trabalho log Analytics no portal Azure](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel de espaço de **trabalho Log Analytics,** selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho

Antes de instalar o agente do Log Analytics para Linux, precisa do ID e da chave da área de trabalho do Log Analytics. Esta informação é exigida pelo script de invólucro do agente para configurar corretamente o agente e garantir que pode comunicar com sucesso com o Monitor Azure.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo do portal Azure, selecione **Todos os serviços.** Na caixa de pesquisa, introduza **o Log Analytics**. À medida que escreve, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que criou anteriormente. (Pode ter-lhe chamado **DefaultLAWorkspace**.)

3. Selecione **definições avançadas:**

    ![Menu de Definições Avançadas para Log Analytics no portal Azure](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Linux**.

5. O valor à direita de **ID da Área de Trabalho** e **Chave Primária**. Copie e cole ambos no seu editor favorito.

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux

Os passos seguintes configuram o agente do Log Analytics no Azure e na cloud do Azure Government.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics.  

Se o seu computador Linux precisar de comunicar através de um servidor Proxy com o Log Analytics, a configuração do proxy pode ser especificada na linha de comandos ao incluir `-p [protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy. 

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se ligar a um espaço de trabalho do Log Analytics, execute o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária copiada anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando `-p` inclui o parâmetro proxy e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para ligar ao espaço de trabalho log Analytics na nuvem do Governo de Azure, executar o seguinte comando fornecendo o ID do espaço de trabalho e chave primária copiado anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando `-p` inclui o parâmetro proxy e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

O Azure Monitor pode recolher eventos a partir dos contadores linux Syslog e performance que especifica para análise e reportagem a longo prazo. Também pode tomar medidas quando detetar uma determinada condição. Siga estes passos para configurar a recolha de eventos a partir do Linux Syslog e vários contadores de desempenho comuns para começar.  

1. No canto inferior esquerdo do portal Azure, selecione **Mais serviços.** Na caixa de pesquisa, introduza **o Log Analytics**. À medida que escreve, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Selecione **Dados,** e, em seguida, selecione **Syslog**.  

3. Adicione syslog digitando em nome do diário. Introduza **o Syslog** e, em seguida, selecione o sinal **+** de mais .  

4. Na tabela, desmarque as gravidades **Informações**, **Aviso** e **Depurar**. 

5. Selecione **Guardar** na parte superior da página para guardar a configuração.

6. Selecione **Dados de Desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Linux. 

7. Quando configurar os contadores de desempenho do Linux pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho do Linux predefinidos selecionados no Monitor Azure](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecione **Aplicar abaixo a configuração para as minhas máquinas** e, em seguida, selecione Adicionar os contadores de **desempenho selecionados**. Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.  

8. Selecione **Guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Agora que ativou a recolha de dados, vamos executar um exemplo de pesquisa de registo simples para ver alguns dados a partir do computador de destino.  

1. No espaço de trabalho selecionado, a partir do painel da esquerda, selecione **Logs**.

2. Na página de consulta de `Perf` Registos, digite no editor de consulta e selecione **Executar**.
 
    ![Pesquisa de registo de Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta na imagem seguinte devolveu 10.000 registos de desempenho. Os resultados serão significativamente menos.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode remover o agente do computador com Linux e eliminar a área de trabalho do Log Analytics.  

Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para eliminar o espaço de trabalho, selecione o espaço de trabalho do Log Analytics que criou anteriormente e na página de recursos selecione **Delete**.

![Eliminar recurso do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes

Agora que está a recolher dados operacionais e de desempenho do computador com Linux no local, pode começar facilmente a explorar, analisar e efetuar ações nos dados recolhidos *gratuitamente*.  

Para saber como ver e analisar os dados, avance para o tutorial.

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
