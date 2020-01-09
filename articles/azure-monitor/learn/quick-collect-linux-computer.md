---
title: 'Início rápido: coletar dados de um computador Linux híbrido com Azure Monitor'
description: Neste guia de início rápido, você aprenderá a implantar o agente de Log Analytics para computadores Linux em execução fora do Azure e habilitar a coleta de dados com logs de Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: f494702166fc3c018aba9b1356a6806384ae4673
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530022"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Início rápido: coletar dados de um computador Linux em um ambiente híbrido com Azure Monitor

[Azure monitor](../overview.md) pode coletar dados diretamente de seus computadores Linux físicos ou virtuais em seu ambiente em um espaço de trabalho log Analytics para análise e correlação detalhadas. A instalação do [agente de log Analytics](../platform/log-analytics-agent.md) permite Azure monitor coletar dados de um datacenter ou de outro ambiente de nuvem. Este guia de início rápido mostra como configurar e coletar dados do seu servidor Linux com algumas etapas fáceis. Para obter informações sobre VMs Linux do Azure, consulte [coletar dados sobre máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Para entender a configuração com suporte, consulte [sistemas operacionais Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) e [configuração de firewall de rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)com suporte.
 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

    ![Localizando Log Analytics espaço de trabalho no portal do Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criando um espaço de trabalho Log Analytics no portal do Azure](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel **log Analytics espaço de trabalho** , selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho

Antes de instalar o agente do Log Analytics para Linux, precisa do ID e da chave da área de trabalho do Log Analytics. Essas informações são exigidas pelo script de wrapper de agente para configurar corretamente o agente e garantir que ele possa se comunicar com êxito com Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo da portal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite **log Analytics**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você criou anteriormente. (Você pode ter nomeado **DefaultLAWorkspace**.)

3. Selecione **Configurações avançadas**:

    ![Menu de configurações avançadas para Log Analytics no portal do Azure](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Linux**.

5. O valor à direita de **ID da Área de Trabalho** e **Chave Primária**. Copie e cole ambos no seu editor favorito.

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux

Os passos seguintes configuram o agente do Log Analytics no Azure e na cloud do Azure Government.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics.  

Se o seu computador Linux precisar de comunicar através de um servidor Proxy com o Log Analytics, a configuração do proxy pode ser especificada na linha de comandos ao incluir `-p [protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy. 

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar a um Log Analytics espaço de trabalho, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O comando a seguir inclui o `-p` parâmetro de proxy e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao Log Analytics espaço de trabalho na nuvem do Azure governamental, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O comando a seguir inclui o `-p` parâmetro de proxy e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho

Azure Monitor pode coletar eventos dos contadores de desempenho e syslog do Linux que você especificar para análise e relatórios de longo prazo. Ele também pode tomar medidas quando detecta uma condição específica. Siga estes passos para configurar a recolha de eventos a partir do Linux Syslog e vários contadores de desempenho comuns para começar.  

1. No canto inferior esquerdo da portal do Azure, selecione **mais serviços**. Na caixa de pesquisa, digite **log Analytics**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Selecione **dados**e, em seguida, selecione **syslog**.  

3. Você adiciona o syslog digitando o nome do log. Insira o **syslog** e, em seguida, selecione o sinal de adição **+** .  

4. Na tabela, desmarque as gravidades **Informações**, **Aviso** e **Depurar**. 

5. Selecione **salvar** na parte superior da página para salvar a configuração.

6. Selecione **Dados de Desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Linux. 

7. Quando configurar os contadores de desempenho do Linux pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. São listados com uma caixa de verificação junto a cada um.

    ![Contadores de desempenho padrão do Linux selecionados em Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecione **aplicar configuração abaixo ao em meus computadores** e, em seguida, selecione **adicionar os contadores de desempenho selecionados**. Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.  

8. Selecione **salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Agora que ativou a recolha de dados, vamos executar um exemplo de pesquisa de registo simples para ver alguns dados a partir do computador de destino.  

1. No espaço de trabalho selecionado, no painel esquerdo, selecione **logs**.

2. Na página consulta de logs, digite `Perf` no editor de consultas e selecione **executar**.
 
    ![Log Analytics pesquisa de log](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta na imagem a seguir retornou 10.000 registros de desempenho. Os resultados serão significativamente menos.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode remover o agente do computador com Linux e eliminar a área de trabalho do Log Analytics.  

Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para excluir o espaço de trabalho, selecione o Log Analytics espaço de trabalho que você criou anteriormente e, na página de recursos, selecione **excluir**.

![Eliminar recurso do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos seguintes

Agora que está a recolher dados operacionais e de desempenho do computador com Linux no local, pode começar facilmente a explorar, analisar e efetuar ações nos dados recolhidos *gratuitamente*.  

Para saber como ver e analisar os dados, avance para o tutorial.

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
