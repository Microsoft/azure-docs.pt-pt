---
title: Configurar o Agente do Azure Log Analytics para o Computador Linux Híbrido | Microsoft Docs
description: Saiba como implementar o agente do Log Analytics para Linux em execução em computadores fora do Azure e ativar a recolha de dados com o Log Analytics.
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
ms.date: 06/14/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 535b96a2bb86f1996e6ff0aba19339b55a18ee50
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515864"
---
# <a name="configure-log-analytics-agent-for-linux-computers-in-a-hybrid-environment"></a>Configurar o agente do Log Analytics para computadores Linux num ambiente híbrido
O [Azure Log Analytics](../platform/agent-windows.md) pode recolher dados diretamente do seu computador Linux físico ou virtual no seu datacenter ou noutro ambiente na cloud para um único repositório, para análise e correlação detalhadas. Este início rápido mostra como configurar e recolher dados do seu computador com Linux em alguns passos simples.  Para as VMs do Linux do Azure, veja o tópico seguinte [Recolher dados sobre Máquinas Virtuais do Azure](quick-collect-azurevm.md).  

Antes de continuar, você deve primeiro examinar o artigo [visão geral](../platform/log-analytics-agent.md) do agente de log Analytics para entender quais configurações têm suporte, os [sistemas operacionais Linux](../platform/log-analytics-agent.md#supported-linux-operating-systems) e a [configuração de firewall de rede](../platform/log-analytics-agent.md#network-firewall-requirements)com suporte. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, digite **log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.

    ![Portal do Azure](media/quick-collect-linux-computer/azure-portal-01.png) 

2. Selecione **criar**e, em seguida, selecione as opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.   
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contenha uma ou mais máquinas virtuais do Azure.  
   * Selecione a **Localização** para onde as VMs devem ser implementadas.  Para obter mais informações, veja em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/).  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição já existente criada antes de 2 de abril ou para uma subscrição que estava associada a uma inscrição EA já existente, selecione o seu escalão de preço preferido.  Para obter informações adicionais sobre os escalões específicos, veja [Detalhes de Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar Log Analytics folha de recursos](media/quick-collect-linux-computer/create-loganalytics-workspace-02.png)<br>  

3. Depois de fornecer as informações necessárias no painel **log Analytics espaço de trabalho** , selecione **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho
Antes de instalar o agente do Log Analytics para Linux, precisa do ID e da chave da área de trabalho do Log Analytics.  Esta informação é necessária para o script de wrapper do agente configurar corretamente o agente e assegurar que consegue comunicar com êxito com o Log Analytics.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Na portal do Azure, selecione **todos os serviços** encontrados no canto superior esquerdo. Na lista de recursos, digite **log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. Na lista de áreas de trabalho do Log Analytics, selecione *DefaultLAWorkspace*, criada anteriormente.
3. Selecione **Definições avançadas**.

    ![Definições Avançadas do Log Analytics](media/quick-collect-linux-computer/log-analytics-advanced-settings-01.png) 
 
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Linux**.   
5. O valor à direita de **ID da Área de Trabalho** e **Chave Primária**. Copie e cole ambos no seu editor favorito.   

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux
Os passos seguintes configuram o agente do Log Analytics no Azure e na cloud do Azure Government.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics.  

Se o seu computador Linux precisar de comunicar através de um servidor Proxy com o Log Analytics, a configuração do proxy pode ser especificada na linha de comandos ao incluir `-p [protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy. 

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador com Linux para ligar ao Log Analytics, execute o seguinte comando ao fornecer o ID da área de trabalho e a chave primária que copiou anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Este comando inclui o parâmetro de proxy `-p` e sintaxe de exemplo.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador com Linux para ligar ao Log Analytics na cloud do Azure Government, execute o seguinte comando ao fornecer o ID da área de trabalho e a chave primária que copiou anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Este comando inclui o parâmetro de proxy `-p` e sintaxe de exemplo.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recolher dados de eventos e de desempenho
O Log Analytics pode recolher eventos do Linux Syslog e contadores de desempenho que especificar para análises e relatórios a longo prazo e efetuar ações quando é detetada uma determinada condição.  Siga estes passos para configurar a recolha de eventos a partir do Linux Syslog e vários contadores de desempenho comuns para começar.  

1. Selecione **Syslog**.  
2. Adicione um registo de eventos ao escrever o nome do registo. Insira **syslog** e, em seguida, selecione **+** o sinal de adição.  
3. Na tabela, desmarque as gravidades **Informações**, **Aviso** e **Depurar**. 
4. Selecione **salvar** na parte superior da página para salvar a configuração.
5. Selecione **Dados de Desempenho do Linux** para ativar a recolha de contadores de desempenho num computador Linux. 
6. Quando configurar os contadores de desempenho do Linux pela primeira vez para uma nova área de trabalho do Log Analytics, é-lhe dada a opção de criar rapidamente vários contadores comuns. Eles são listados com uma caixa de seleção ao lado de cada um. 

    ![Contadores de desempenho do Windows predefinidos](media/quick-collect-linux-computer/linux-perfcounters-default.png)
    
    Selecione **adicionar os contadores de desempenho selecionados**. Estes são adicionados e predefinidos com um intervalo de amostra de recolha de dez segundo.

7. Selecione **salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos
Agora que ativou a recolha de dados, vamos executar um exemplo de pesquisa de registo simples para ver alguns dados a partir do computador de destino.  

1. Na portal do Azure, acesse Log Analytics e selecione o espaço de trabalho criado anteriormente.
2. Selecione o bloco **pesquisa de logs** e, no painel pesquisa de logs, no campo consulta, `Perf` digite e pressione Enter ou selecione o botão Pesquisar à direita do campo de consulta.

    ![Exemplo de consulta de pesquisa de registos do Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta na imagem seguinte devolveu 735 registos de desempenho.

    ![Resultado da pesquisa de registos do Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover o agente do computador com Linux e eliminar a área de trabalho do Log Analytics.  

Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para excluir o espaço de trabalho, selecione o Log Analytics espaço de trabalho que você criou anteriormente e, na página de recursos, selecione **excluir**.

![Eliminar recurso do Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que está a recolher dados operacionais e de desempenho do computador com Linux no local, pode começar facilmente a explorar, analisar e efetuar ações nos dados recolhidos *gratuitamente*.  

Para saber como ver e analisar os dados, avance para o tutorial.   

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
