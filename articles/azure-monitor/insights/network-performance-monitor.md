---
title: Monitor de Desempenho de Rede solução no Azure | Microsoft Docs
description: Monitor de Desempenho de Rede no Azure ajuda a monitorar o desempenho de suas redes, quase em tempo real, para detectar e localizar afunilamentos de desempenho de rede.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: vinigam
ms.openlocfilehash: c350ca3cd8cbfb5e550fccd0bae0df53168de178
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312082"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Monitor de Desempenho de Rede solução no Azure

![Símbolo de Monitor de Desempenho de Rede](./media/network-performance-monitor/npm-symbol.png)


O Monitor de Desempenho de Rede é uma solução de monitoramento de rede híbrida baseada em nuvem que ajuda a monitorar o desempenho de rede entre vários pontos em sua infraestrutura de rede. Ajuda também a monitorizar a conectividade da rede ao serviço e pontos finais da aplicação, bem como o desempenho do Azure ExpressRoute. 

Monitor de Desempenho de Rede detecta problemas de rede, como tráfego blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não conseguem detectar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

O Monitor de Desempenho de Rede oferece três recursos abrangentes: 

* [Monitor de desempenho](network-performance-monitor-performance-monitor.md): Você pode monitorar a conectividade de rede entre implantações de nuvem e locais, vários data centers e filiais e aplicativos multicamadas de missão crítica ou microserviços. Com o monitor de desempenho, você pode detectar problemas de rede antes que os usuários reclamarem.

* [Monitor de conectividade de serviço](network-performance-monitor-service-connectivity.md): Você pode monitorar a conectividade de seus usuários com os serviços que se preocupam, determinar qual infraestrutura está no caminho e identificar onde os afunilamentos de rede ocorrem. Você pode saber sobre interrupções antes dos usuários e ver o local exato dos problemas ao longo de seu caminho de rede. 

    Esse recurso ajuda a executar testes com base em HTTP, HTTPS, TCP e ICMP para monitorar quase em tempo real ou historicamente a disponibilidade e o tempo de resposta de seu serviço. Você também pode monitorar a contribuição da rede em perda de pacotes e latência. Com um mapa de topologia de rede, você pode isolar os gargalos de rede. Você pode identificar pontos problemáticos que ocorrem ao longo do caminho de rede do nó para o serviço, com dados de latência em cada salto. Com testes internos, você pode monitorar a conectividade de rede para o Office 365 e o Dynamics CRM sem qualquer preconfiguração. Com esse recurso, você pode monitorar a conectividade de rede para qualquer ponto de extremidade compatível com TCP, como sites, aplicativos SaaS, aplicativos PaaS e bancos de dados SQL.

* [Monitor do ExpressRoute](network-performance-monitor-expressroute.md): Monitore a conectividade e o desempenho de ponta a ponta entre suas filiais e o Azure, por meio do Azure ExpressRoute.  

Mais informações sobre os vários recursos com suporte do [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.
 
## <a name="supported-regions"></a>Regiões com suporte
O NPM pode monitorar a conectividade entre redes e aplicativos em qualquer parte do mundo, de um espaço de trabalho hospedado em uma das seguintes regiões:
* Europa Ocidental
* EUA Centro-Oeste
* East US
* Leste do Japão
* Sudeste Asiático
* Sudeste da Austrália
* Sul do Reino Unido
* Índia Central
* Virgínia do governo dos EUA


A lista de regiões com suporte para o monitor do ExpressRoute está disponível na [documentação](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Configurar e configurar

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Use os processos básicos para instalar agentes em [conectar computadores Windows para Azure monitor](../platform/agent-windows.md) e [conectar Operations Manager ao Azure monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

* **Monitor de desempenho**: Instale os agentes de Log Analytics em pelo menos um nó conectado a cada sub-rede da qual você deseja monitorar a conectividade de rede com outras sub-redes.

    Para monitorar um link de rede, instale agentes em ambos os pontos de extremidade do link. Se você não tiver certeza sobre a topologia da sua rede, instale os agentes em servidores com cargas de trabalho críticas entre as quais você deseja monitorar o desempenho da rede. Por exemplo, se você quiser monitorar a conexão de rede entre um servidor Web e um servidor que executa o SQL, instale um agente em ambos os servidores. Os agentes monitoram a conectividade de rede (links) entre hosts, e não os próprios hosts. 

* **Monitor de conectividade de serviço**: Instale um agente de Log Analytics em cada nó do qual você deseja monitorar a conectividade de rede com o ponto de extremidade de serviço. Um exemplo é se você deseja monitorar a conectividade de rede para o Office 365 de seus sites do Office rotulados como O1, O2 e O3. Instale o agente de Log Analytics em pelo menos um nó em O1, O2 e O3. 

* **Monitor do ExpressRoute**: Instale pelo menos um agente de Log Analytics em sua rede virtual do Azure. Instale também pelo menos um agente em sua sub-rede local, que é conectada por meio do emparelhamento privado do ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurar agentes de Log Analytics para monitoramento 

Monitor de Desempenho de Rede usa transações sintéticas para monitorar o desempenho de rede entre os agentes de origem e de destino. Você pode escolher entre TCP e ICMP como o protocolo para monitoramento no monitor de desempenho e recursos do monitor de conectividade de serviço. Somente o TCP está disponível como o protocolo de monitoramento para o monitor do ExpressRoute. Certifique-se de que o firewall permite a comunicação entre os agentes de Log Analytics usados para monitoramento no protocolo que você escolher. 

* **Protocolo TCP**: Se você escolher TCP como o protocolo para monitoramento, abra a porta do firewall nos agentes usados para Monitor de Desempenho de Rede e monitor do ExpressRoute para garantir que os agentes possam se conectar entre si. Para abrir a porta, execute o script do PowerShell [EnableRules. ps1](https://aka.ms/npmpowershellscript) sem parâmetros em uma janela do PowerShell com privilégios administrativos.

    O script cria chaves do registro exigidas pela solução. Ele também cria regras de firewall do Windows para permitir que os agentes criem conexões TCP entre si. As chaves do registro criadas pelo script especificam se devem ser registradas em log os logs de depuração e o caminho para o arquivo de logs. O script também define a porta TCP do agente usada para comunicação. Os valores para estas chaves são definidos automaticamente pelo script. Não altere manualmente essas chaves. A porta aberta por padrão é 8084. Você pode usar uma porta personalizada fornecendo o parâmetro portNumber para o script. Use a mesma porta em todos os computadores em que o script é executado. 

    >[!NOTE]
    > O script configura apenas o Firewall do Windows localmente. Se você tiver um firewall de rede, verifique se ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.

    >[!NOTE]
    > Você não precisa executar o script do PowerShell [EnableRules. ps1](https://aka.ms/npmpowershellscript ) para o monitor de conectividade de serviço.

    

* **Protocolo ICMP**: Se você escolher ICMP como o protocolo para monitoramento, habilite as seguintes regras de firewall para utilizar o ICMP de forma confiável:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configurar a solução 

1. Adicione a solução Monitor de Desempenho de Rede ao seu espaço de trabalho do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Você também pode usar o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md). 
2. Abra seu espaço de trabalho Log Analytics e selecione o bloco **visão geral** . 
3. Selecione o bloco **Monitor de desempenho de rede** com a mensagem a *solução requer configuração adicional*.

   ![Bloco Monitor de Desempenho de Rede](media/network-performance-monitor/npm-config.png)

4. Na página **instalação** , você verá a opção para instalar agentes de log Analytics e configurar os agentes para monitoramento no modo de exibição **configurações comuns** . Conforme explicado anteriormente, se você instalou e configurou agentes de Log Analytics, selecione a exibição de **instalação** para configurar a funcionalidade que você deseja usar. 

   **Monitor de desempenho**: Escolha o protocolo a ser usado para transações sintéticas na regra **padrão** do monitor de desempenho e selecione **Salvar & continuar**. Essa seleção de protocolo só contém a regra padrão gerada pelo sistema. Você precisa escolher o protocolo sempre que criar uma regra de monitor de desempenho explicitamente. Você sempre pode mover para as configurações de regra **padrão** na guia **Monitor de desempenho** (ela aparece depois que você conclui a configuração do dia 0) e altera o protocolo mais tarde. Se você não quiser o recurso de monitor de desempenho, poderá desabilitar a regra padrão nas configurações de regra **padrão** na guia **Monitor de desempenho** .

   ![Exibição do monitor de desempenho](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor de conectividade de serviço**: A funcionalidade fornece testes pré-configurados predefinidos para monitorar a conectividade de rede para o Office 365 e o Dynamics 365 de seus agentes. Escolha os serviços do Office 365 e do Dynamics 365 que você deseja monitorar marcando as caixas de seleção ao lado deles. Para escolher os agentes dos quais você deseja monitorar, selecione **adicionar agentes**. Se você não quiser usar esse recurso ou desejar configurá-lo mais tarde, não escolha nada e selecione **salvar & continuar**.

   ![Exibição do monitor de conectividade de serviço](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor do ExpressRoute**: Selecione **descobrir agora** para descobrir todos os emparelhamentos privados do ExpressRoute que estão conectados às redes virtuais na assinatura do Azure vinculada a este espaço de trabalho log Analytics. 

   ![Exibição do monitor do ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Após a conclusão da descoberta, os circuitos e emparelhamentos descobertos são listados em uma tabela. 

   ![Página de configuração do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-private-peerings.png)
    
O monitoramento para esses circuitos e emparelhamentos está inicialmente em um estado desabilitado. Selecione cada recurso que você deseja monitorar e configure o monitoramento para eles no modo de exibição de detalhes à direita. Selecione **salvar** para salvar a configuração. Para saber mais, consulte o artigo "configurar monitoramento do ExpressRoute". 

Após a conclusão da instalação, levará 30 minutos a uma hora para que os dados sejam preenchidos. Embora a solução agregue dados de sua rede, você vê a mensagem a *solução requer configuração adicional* no bloco de **visão geral** monitor de desempenho de rede. Depois que os dados são coletados e indexados, o bloco de **visão geral** é alterado e informa a sua integridade de rede em um resumo. Em seguida, você pode editar o monitoramento dos nós nos quais os agentes do Log Analytics estão instalados, bem como as sub-redes descobertas do seu ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar configurações de monitoramento para sub-redes e nós 

Todas as sub-redes com pelo menos um agente instalado são listadas na guia  **sub-redes**  na página configuração. 


Para habilitar ou desabilitar o monitoramento de sub-redes específicas:

1. Marque ou desmarque a caixa de seleção ao lado da **ID da sub-rede**. Em seguida, verifique se **usar para monitoramento** está marcado ou desmarcado, conforme apropriado. Você pode selecionar ou desmarcar várias sub-redes. Quando desabilitadas, as sub-redes não são monitoradas e os agentes são atualizados para interromper o ping de outros agentes. 
2. Escolha os nós que você deseja monitorar em uma sub-rede específica. Selecione a sub-rede na lista e mova os nós necessários entre as listas que contêm nós não monitorados e monitorados. Você pode adicionar uma descrição personalizada à sub-rede.
3. Selecione **salvar** para salvar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolher os nós a serem monitorados

Todos os nós que têm um agente instalado neles são listados na guia **nós** . 

1. Marque ou desmarque os nós que você deseja monitorar ou parar o monitoramento. 
2. Selecione **usar para monitoramento**ou desmarque-o, conforme apropriado. 
3. Selecione **Guardar**. 


Configure os recursos desejados:

- [Monitor de desempenho](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor de conectividade de serviço](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor do ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes de recolha de dados
Para coletar informações de perda e latência, Monitor de Desempenho de Rede usa pacotes de handshake TCP SYN-SYNACK-ACK quando você escolhe TCP como o protocolo. Monitor de Desempenho de Rede usa ICMP ECHO ICMP ECHO REPLY quando você escolhe ICMP como o protocolo. A rota de rastreamento também é usada para obter informações de topologia.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para Monitor de Desempenho de Rede.

| Plataforma | Agente direto | Agente de System Center Operations Manager | Storage do Azure | Operations Manager necessário? | Operations Manager dados do agente enviados por meio do grupo de gerenciamento | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Handshakes TCP/mensagens de eco ICMP a cada 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução usa transações sintéticas para avaliar a integridade da rede. Log Analytics agentes instalados em vários pontos na rede trocam pacotes TCP ou eco ICMP entre si. Se os agentes usam pacotes TCP ou o eco ICMP depende do protocolo selecionado para monitoramento. No processo, os agentes aprendem o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também executa uma rota de rastreamento para outros agentes para localizar todas as várias rotas na rede que devem ser testadas. Usando esses dados, os agentes podem deduzir os números de latência de rede e perda de pacotes. Os testes são repetidos a cada cinco segundos. Os dados são agregados por cerca de três minutos pelos agentes antes de serem carregados no espaço de trabalho Log Analytics em Azure Monitor.



>[!NOTE]
> Embora os agentes se comuniquem entre si com frequência, eles não geram tráfego de rede significativo durante a condução dos testes. Os agentes dependem apenas dos pacotes de handshake TCP SYN-SYNACK-ACK para determinar a perda e a latência. Nenhum pacote de dados é trocado. Durante esse processo, os agentes se comunicam entre si somente quando necessário. A topologia de comunicação do agente é otimizada para reduzir o tráfego de rede.

## <a name="use-the-solution"></a>Utilizar a solução 

### <a name="network-performance-monitor-overview-tile"></a>Bloco de visão geral Monitor de Desempenho de Rede 

Depois de habilitar a solução Monitor de Desempenho de Rede, o bloco solução na página **visão geral** fornece uma rápida visão geral da integridade da rede. 

 ![Bloco de visão geral Monitor de Desempenho de Rede](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Painel do Monitor de Desempenho de Rede 

* **Principais eventos de integridade da rede**: Esta página fornece uma lista dos eventos de integridade e alertas mais recentes no sistema e a hora em que os eventos estão ativos. Um alerta ou evento de integridade é gerado sempre que o valor da métrica escolhida (perda, latência, tempo de resposta ou utilização de largura de banda) para a regra de monitoramento excede o limite. 

* **Monitor do ExpressRoute**: Esta página fornece resumos de integridade para as várias conexões de emparelhamento de ExpressRoute que a solução monitora. O bloco **topologia** mostra o número de caminhos de rede por meio dos circuitos do ExpressRoute que são monitorados em sua rede. Selecione este bloco para ir para a exibição de **topologia** .

* **Monitor de conectividade de serviço**: Esta página fornece resumos de integridade para os diferentes testes que você criou. O bloco **topologia** mostra o número de pontos de extremidade que são monitorados. Selecione este bloco para ir para a exibição de **topologia** .

* **Monitor de desempenho**: Esta página fornece resumos de integridade para os links de **rede** **e links** de sub-rede que a solução monitora. O bloco **topologia** mostra o número de caminhos de rede que são monitorados em sua rede. Selecione este bloco para ir para a exibição de **topologia** . 

* **Consultas comuns**: Esta página contém um conjunto de consultas de pesquisa que buscam dados brutos de monitoramento de rede diretamente. Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. 

   ![Painel do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Detalhar para profundidade 

Você pode selecionar vários links no painel de solução para fazer uma busca detalhada em qualquer área de interesse. Por exemplo, quando você vir um alerta ou um link de rede não íntegro aparecer no painel, selecione-o para investigar ainda mais. Uma página lista todos os links de sub-rede para o link de rede específico. Você pode ver a perda, a latência e o status de integridade de cada link de sub-rede. Você pode descobrir rapidamente qual link de sub-rede causa problemas. Selecione **exibir links** de nó para ver todos os links de nó para o link de sub-rede não íntegro. Em seguida, você pode ver links individuais de nó para nó e encontrar os links de nó não íntegros. 

Selecione **Exibir topologia** para exibir a topologia de salto a salto das rotas entre os nós de origem e de destino. As rotas não íntegras aparecem em vermelho. Você pode exibir a latência contribuída por cada salto para que possa identificar rapidamente o problema para uma parte específica da rede.

 

### <a name="network-state-recorder-control"></a>Controle do gravador de estado de rede

Cada exibição exibe um instantâneo da integridade da sua rede em um determinado momento. Por padrão, o estado mais recente é mostrado. A barra na parte superior da página mostra o ponto no tempo para o qual o estado é exibido. Para exibir um instantâneo de sua integridade de rede em um momento anterior, selecione **ações**. Você também pode habilitar ou desabilitar a atualização automática para qualquer página enquanto exibe o estado mais recente. 

 ![Gravador de estado de rede](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendência 

Em cada nível de detalhamento, você pode ver a tendência da métrica aplicável. Pode ser perda, latência, tempo de resposta ou utilização de largura de banda. Para alterar o intervalo de tempo para a tendência, use o controle de tempo na parte superior do gráfico. 

Os gráficos de tendência mostram uma perspectiva histórica do desempenho de uma métrica de desempenho. Alguns problemas de rede são transitórios por natureza e são difíceis de capturar observando apenas o estado atual da rede. Os problemas podem surgir rapidamente e desaparecer antes que alguém perceba, apenas para reaparecer em um momento posterior. Esses problemas transitórios também podem ser difíceis para os administradores de aplicativos. Os problemas geralmente aparecem como aumentos não explicados no tempo de resposta do aplicativo, mesmo quando todos os componentes do aplicativo parecem ser executados sem problemas. 

Você pode facilmente detectar esses tipos de problemas examinando um gráfico de tendência. O problema aparece como um pico repentino na latência de rede ou perda de pacotes. Para investigar o problema, use o controle de gravador de estado de rede para exibir o instantâneo de rede e a topologia para esse ponto no tempo em que o problema ocorreu.

 
![Gráficos de tendência](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

Monitor de Desempenho de Rede mostra a topologia de salto a salto de rotas entre o ponto de extremidade de origem e de destino em um mapa de topologia interativo. Para exibir o mapa de topologia, selecione o bloco **topologia** no painel da solução. Você também pode selecionar o link **Exibir topologia** nas páginas de busca detalhada. 

O mapa de topologia exibe quantas rotas estão entre a origem e o destino e quais caminhos são adotados pelos pacotes de dados. A latência contribuída por cada salto de rede também é visível. Todos os caminhos para os quais a latência total do caminho está acima do limite (definido na regra de monitoramento correspondente) são mostrados em vermelho. 

Ao selecionar um nó ou passar o mouse sobre ele no mapa de topologia, você vê as propriedades do nó, como o FQDN e o endereço IP. Selecione um salto para ver seu endereço IP. Você pode identificar o salto de rede problemático observando a latência que ele contribui. Para filtrar rotas específicas, use os filtros no painel Ação recolhível. Para simplificar as topologias de rede, oculte os saltos intermediários usando o controle deslizante no painel ação. Você pode ampliar ou reduzir o mapa de topologia usando a roda do mouse. 

A topologia mostrada no mapa é a topologia de camada 3 e não contém dispositivos e conexões de camada 2. 

 
![Mapa de topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Consultas de log em Azure Monitor

Todos os dados expostos graficamente por meio do painel de Monitor de Desempenho de Rede e das páginas de busca detalhada também estão disponíveis nativamente em [consultas de log](../log-query/log-query-overview.md). Você pode executar a análise interativa de dados no repositório e correlacionar dados de diferentes fontes. Você também pode criar alertas e exibições personalizados e exportar os dados para o Excel, Power BI ou um link compartilhável. A área **consultas** comuns no painel tem algumas consultas úteis que você pode usar como ponto de partida para criar suas próprias consultas e relatórios. 

## <a name="alerts"></a>Alertas

Monitor de Desempenho de Rede usa os recursos de alerta do [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Isso significa que todas as notificações são gerenciadas usando [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Se você for um usuário do NPM criando um alerta por meio do Log Analytics: 
1. Você verá um link que o redirecionará para portal do Azure. Clique para acessar o Portal.
2. Clique no bloco Monitor de Desempenho de Rede solução. 
3. Navegue até configurar.  
4. Selecione o teste no qual você deseja criar um alerta e siga as etapas mencionadas abaixo.

Se você for um usuário do NPM criando um alerta por meio do portal do Azure:  
1. Você pode optar por inserir seu email diretamente ou pode optar por criar alertas por meio de grupos de ação.
2. Se você optar por inserir seu email diretamente, um grupo de ações com o nome **NPM email** Group será criado e a ID de email será adicionada a esse grupo de ações.
3. Se você optar por usar grupos de ações, precisará selecionar um grupo de ações criado anteriormente. Você pode aprender a criar um grupo de ações [aqui.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Depois que o alerta for criado com êxito, você poderá usar o link Gerenciar alertas para gerenciar seus alertas. 

Cada vez que você cria um alerta, o NPM cria uma regra de alerta de log baseada em consulta no Azure Monitor. Essa consulta é disparada a cada 5 minutos por padrão. O Azure monitor não cobra pelas primeiras regras de alerta de log de 250 criadas e quaisquer regras de alerta acima do limite de regras de alerta de log 250 serão cobradas de acordo com os [preços dos alertas na página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
As notificações são cobradas separadamente de acordo com os [preços das notificações na página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Preços

Informações sobre preços estão disponíveis [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Enviar comentários 

* **UserVoice** Você pode postar suas ideias para Monitor de Desempenho de Rede recursos nos quais você deseja trabalhar. Visite a [página uservoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Junte-se a nosso coorte:** Estamos sempre interessados em fazer novos clientes ingressarem em nosso coorte. Como parte dele, você obtém acesso antecipado a novos recursos e uma oportunidade para nos ajudar a melhorar o Monitor de Desempenho de Rede. Se você estiver interessado em ingressar, preencha esta [pesquisa rápida](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Passos Seguintes 
Saiba mais sobre o monitor de [desempenho](network-performance-monitor-performance-monitor.md), o [Monitor de conectividade de serviço](network-performance-monitor-performance-monitor.md)e o monitor do [ExpressRoute](network-performance-monitor-expressroute.md). 
