---
title: Solução de Monitor de desempenho no Azure de rede | Documentos da Microsoft
description: Monitor de desempenho de rede do Azure ajuda-o a monitorizar o desempenho das suas redes, em tempo quase real, para detetar e localizar afunilamentos de desempenho de rede.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: d35918659acb899e43f76e94168abcba080aa006
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452135"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solução de Monitor de desempenho no Azure de rede

![Símbolo de Monitor de desempenho de rede](./media/network-performance-monitor/npm-symbol.png)


Monitor de desempenho de rede é uma solução que ajuda a monitorizar o desempenho de rede entre vários pontos na sua infraestrutura de rede de monitorização de rede de com base na cloud híbrida. Ajuda também a monitorizar a conectividade da rede ao serviço e pontos finais da aplicação, bem como o desempenho do Azure ExpressRoute. 

Monitor de desempenho de rede Deteta problemas de rede, como blackholing de tráfego, erros de encaminhamento e problemas que os métodos de monitorização de rede convencionais não conseguem detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

Monitor de desempenho de rede oferece três recursos abrangentes: 

* [Monitor de desempenho](network-performance-monitor-performance-monitor.md): Pode monitorizar a conectividade de rede em implementações de nuvem e localizações no local, vários centros de dados e das filiais e aplicativos de várias camados de missão crítica ou microsserviços. Com o Monitor de desempenho, pode detectar problemas de rede antes dos usuários se queixam.

* [Monitor de conectividade do serviço](network-performance-monitor-service-endpoint.md): Pode monitorizar a conectividade dos seus utilizadores para os serviços que lhe interessam, determinar a infraestrutura estiver no caminho e identificar onde ocorrem os estrangulamentos de rede. Pode saber sobre as falhas antes dos seus utilizadores e ver a localização exata dos problemas ao longo do caminho de rede. 

    Esta capacidade ajuda-o a efetuar testes com base em HTTP, HTTPS, TCP e ICMP para monitorar em quase em tempo real ou, historicamente, a disponibilidade e o tempo de resposta do seu serviço. Também pode monitorizar a contribuição da rede na perda de pacotes e latência. Com um mapa de topologia de rede, pode isolar abrandamentos de rede. Pode identificar problemáticos que ocorrem ao longo do caminho de rede a partir do nó para o serviço, com dados de latência em cada salto. Com testes internos, pode monitorizar a conectividade de rede, ao Office 365 e Dynamics CRM, sem qualquer pré-configuração. Com esta capacidade, pode monitorizar a conectividade de rede para qualquer ponto final compatível com TCP, como Web sites, aplicações SaaS, PaaS aplicações e bases de dados SQL.

* [Monitor do ExpressRoute](network-performance-monitor-expressroute.md): Monitorizar a conectividade e desempenho ponto a ponto entre os escritórios das filiais e o Azure através do Azure ExpressRoute.  

Obter mais informações sobre os vários recursos suportados pelo [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) está disponível online.
 
## <a name="supported-regions"></a>Regiões suportadas
NPM pode monitorizar a conectividade entre redes e aplicativos em qualquer parte do mundo, a partir de uma área de trabalho que está alojado das seguintes regiões:
* Europa Ocidental
* EUA Centro-Oeste
* EUA Leste
* Leste do Japão
* Sudeste Asiático
* Centro-Sul leste da Austrália
* Reino Unido Sul
* Virgínia de administração pública dos EUA

A lista de regiões suportadas para o Monitor do ExpressRoute está disponível na [documentação](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Definir e configurar

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Utilize os processos de basic para instalar agentes em [computadores Windows ligar ao Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) e [ligar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

* **Monitor de desempenho**: Instale agentes do Log Analytics em, pelo menos, um nó ligado a cada sub-rede a partir do qual pretende monitorizar a conectividade de rede para outras sub-redes.

    Para monitorizar uma ligação de rede, instale agentes em ambos os pontos de extremidade de link. Se tiver a certeza sobre a topologia da sua rede, instale os agentes em servidores com cargas de trabalho críticas entre os quais pretende monitorizar o desempenho da rede. Por exemplo, se quiser monitorar a conexão de rede entre um servidor web e um servidor com o SQL, instale um agente em ambos os servidores. Agentes monitoram a conectividade de rede (links) entre anfitriões, não os próprios hosts. 

* **Monitor de conectividade do serviço**: Instale um agente de Log Analytics em cada nó do qual pretende monitorizar a conectividade de rede para o ponto final de serviço. Um exemplo é se pretende monitorizar a conectividade de rede para o Office 365 a partir de seus sites do office O1 e O3 2. o nome. Instale o agente do Log Analytics em pelo menos um nó em O1, 2. e O3. 

* **Monitor do ExpressRoute**: Instale, pelo menos, um agente de Log Analytics na sua rede virtual do Azure. Instale também, pelo menos, um agente na sua sub-rede local, que está conectado por meio de peering privado do ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurar agentes do Log Analytics para monitorizar 

Monitor de desempenho de rede utiliza transações sintéticas para monitorar o desempenho de rede entre os agentes de origem e de destino. Pode escolher entre TCP e ICMP como protocolo para monitorização no Monitor de desempenho e Monitor de conectividade do serviço de recursos. Apenas TCP está disponível como o protocolo de monitorização para o Monitor do ExpressRoute. Certifique-se de que a firewall permite a comunicação entre os agentes do Log Analytics para monitorizar no protocolo que escolher. 

* **Protocolo TCP**: Se optar por TCP como protocolo para monitorização, abra a porta de firewall nos agentes utilizados para o Monitor de desempenho de rede e Monitor do ExpressRoute para se certificar de que os agentes podem ligar-se entre si. Para abrir a porta, execute o [EnableRules.ps1](https://aka.ms/npmpowershellscript) script do PowerShell sem quaisquer parâmetros numa janela do PowerShell com privilégios administrativos.

    O script cria chaves de registo necessárias para a solução. Ele também cria regras de Firewall do Windows para permitir que os agentes criar conexões TCP entre si. As chaves de registo criadas pelo script Especifique se pretende registar os registos de depuração e o caminho para o ficheiro de registos. O script também define a porta TCP de agente utilizada para comunicação. Os valores para estas chaves são definidos automaticamente pelo script. Não altere manualmente essas chaves. A porta aberta por predefinição é 8084. Pode utilizar uma porta personalizada, fornecendo o portNumber de parâmetro para o script. Utilize a mesma porta em todos os computadores em que o script é executado. 

    >[!NOTE]
    > O script configura apenas Windows Firewall localmente. Se tiver uma firewall de rede, certifique-se de que ela permite que o tráfego destinado a porta TCP utilizada pelo Monitor de desempenho de rede.

    >[!NOTE]
    > Não precisa de executar o [EnableRules.ps1](https://aka.ms/npmpowershellscript ) script do PowerShell para o Monitor de ponto final de serviço.

    

* **Protocolo ICMP**: Se optar por ICMP como protocolo para monitorização, ative as seguintes regras de firewall utilizar com fiabilidade ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configurar a solução 

1. Adicionar a solução de Monitor de desempenho de rede à sua área de trabalho a partir da [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Também pode utilizar o processo descrito em [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md). 
2. Abra a área de trabalho do Log Analytics e selecione o **descrição geral** mosaico. 
3. Selecione o **Monitor de desempenho de rede** mosaico com a mensagem *solução requer configuração adicional*.

   ![Mosaico de Monitor de desempenho de rede](media/network-performance-monitor/npm-config.png)

4. Na **programa de configuração** página, verá a opção para instalar agentes do Log Analytics e configurar os agentes para monitorização no **definições comuns de** vista. Como explicado anteriormente, se instalar e configurar agentes do Log Analytics, selecione o **configuração** vista para configurar a capacidade de que pretende utilizar. 

   **Monitor de desempenho**: Selecione o protocolo a utilizar para as transações sintéticas a **predefinido** regra do Monitor de desempenho e selecione **guardar e continuar**. Apenas se aplica esta seleção de protocolo para a regra padrão gerados pelo sistema. Tem de escolher o protocolo de cada vez que criar explicitamente uma regra de Monitor de desempenho. Sempre pode mover para o **predefinido** regra definições na **Monitor de desempenho** separador (aparecer depois de concluir a configuração do seu dia-0) e o protocolo de alterações mais tarde. Se não pretender que a capacidade de Monitor de desempenho, pode desativar a regra predefinida dos **predefinição** regra definições na **Monitor de desempenho** separador.

   ![Vista de Monitor de desempenho](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor de conectividade do serviço**: O recurso fornece testes pré-configuradas incorporadas para monitorizar a conectividade de rede ao Office 365 e Dynamics 365 dos seus agentes. Escolha os serviços do Office 365 e Dynamics 365 que pretende monitorizar, selecionando as caixas de verificação ao lado. Para escolher os agentes a partir do qual pretende monitorizar, selecione **adicionar agentes**. Se não pretender utilizar esta capacidade ou que desejam configurá-la mais tarde, não escolha algo e selecione **guardar e continuar**.

   ![Modo de exibição de Monitor do ponto final de serviço](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor do ExpressRoute**: Selecione **detetar agora** para detetar todos os ExpressRoute peerings privados que estão ligados a redes virtuais na subscrição do Azure associado a esta área de trabalho do Log Analytics. 

   ![Modo de exibição de Monitor do ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Depois de concluída a deteção, os circuitos detetados e peerings estão listados numa tabela. 

   ![Página de configuração de Monitor de desempenho de rede](media/network-performance-monitor/npm-private-peerings.png)
    
A monitorização para estes peerings e circuitos está inicialmente desabilitado. Selecione cada recurso que pretende monitorizar e configurar a monitorização para os mesmos a partir da vista de detalhes à direita. Selecione **guardar** para guardar a configuração. Para obter mais informações, consulte o artigo "Configurar a monitorização de ExpressRoute". 

Depois de concluída a configuração, que demora 30 minutos para uma hora para que os dados preencher. Embora a solução agrega dados de sua rede, verá a mensagem *solução requer configuração adicional* no Monitor de desempenho de rede **descrição geral** mosaico. Depois dos dados são recolhidos e indexados, a **descrição geral** mosaico é alterado e informa-o estado de funcionamento de rede num resumo. Em seguida, pode editar a monitorização de nós no qual o Log Analytics os agentes estiverem instalados, bem como as sub-redes detetadas a partir do seu ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar definições de monitorização para sub-redes e nós 

Todas as sub-redes com, pelo menos, um agente instalado estão listadas no **sub-redes** separador na página de configuração. 


Para ativar ou desativar a monitorização de sub-redes específicos:

1. Selecione ou desmarque a caixa de verificação junto a **ID de sub-rede**. Em seguida, certifique-se de que **Utilize para monitorização** está selecionado ou desmarcada, conforme adequado. Pode selecionar ou desmarcar várias sub-redes. Quando desativada, sub-redes não são monitorizados e os agentes são atualizados para parar de enviar pings para outros agentes. 
2. Escolha os nós que pretende monitorizar numa sub-rede específica. Selecione a sub-rede a partir da lista e mover os nós necessários entre as listas que contêm nós não monitorizados e monitorizados. Pode adicionar uma descrição personalizada para a sub-rede.
3. Selecione **guardar** para guardar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolher nós para monitorizar

Todos os nós que tenha um agente instalado neles são listados os **nós** separador. 

1. Marque ou desmarque os nós que pretende monitorizar ou parar a monitorização. 
2. Selecione **Utilize para monitorização**, ou de limpeza, conforme apropriado. 
3. Selecione **Guardar**. 


Configure as funcionalidades que quer:

- [Monitor de desempenho](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor do ponto final de serviço](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor do ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes de recolha de dados
Para coletar informações de perda e latência, o Monitor de desempenho de rede utiliza pacotes de handshake TCP SYN-SYNACK-ACK ao escolher o TCP como protocolo. Monitor de desempenho de rede utiliza a resposta de eco ICMP do ICMP ECHO ao escolher ICMP como protocolo. Encaminhamento de rastreio também é usado para obter informações sobre a topologia.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Monitor de desempenho de rede.

| Plataforma | Agente direto | Agente do System Center Operations Manager | Storage do Azure | Gestor de operações necessárias? | Dados de agente do Operations Manager enviados por grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Mensagens de eco handshakes/ICMP do TCP em 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução utiliza transações sintéticas para avaliar o estado de funcionamento da rede. Agentes de análise de registo instalados em vários pontos no eco de ICMP ou pacotes de TCP de exchange de rede entre si. Se os agentes utilizam pacotes TCP ou ICMP Echo depende de protocolo que selecionou para monitorização. O processo, agentes saiba a perda de pacote e o tempo de ida e volta, se houver. Periodicamente, cada agente também realiza uma rota de rastreamento para outros agentes para encontrar as várias rotas na rede que deve ser testada. Utilizando estes dados, os agentes podem deduzir a latência de rede e as figuras de perda de pacotes. Os testes são repetidos a cada cinco segundos. Os dados são agregados durante cerca de três minutos pelos agentes do antes de ser carregado para o serviço Log Analytics.



>[!NOTE]
> Embora os agentes comunicarem entre si com freqüência, elas não geram significativo tráfego de rede ao realizar os testes. Agentes baseiam-se apenas nos pacotes de handshake TCP SYN-SYNACK-ACK para determinar a perda e latência. Não existem pacotes de dados são trocados. Durante este processo, os agentes comunicarem entre si apenas quando necessário. A topologia de comunicação do agente está otimizada para reduzir o tráfego de rede.

## <a name="use-the-solution"></a>Utilizar a solução 

### <a name="network-performance-monitor-overview-tile"></a>Mosaico de descrição geral do Monitor de desempenho de rede 

Depois de ativar a solução de Monitor de desempenho de rede, a solução de mosaico a **descrição geral** página fornece uma descrição geral rápida do Estado de funcionamento de rede. 

 ![Mosaico de descrição geral do Monitor de desempenho de rede](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Dashboard de Monitor de desempenho de rede 

* **Eventos de estado de funcionamento de rede principais**: Esta página fornece uma lista dos eventos mais recentes do Estado de funcionamento e alertas no sistema e a hora, uma vez que os eventos que estiveram ativos. Um evento de estado de funcionamento ou o alerta é gerado sempre que o valor da métrica escolhido (perda, latência, tempo de resposta ou utilização de largura de banda) para a regra de monitorização é superior ao limiar. 

* **Monitor do ExpressRoute**: Esta página fornece resumos de estado de funcionamento da solução para as ligações de peering do ExpressRoute vários monitores. O **topologia** mosaico mostra o número de caminhos de rede por meio dos circuitos do ExpressRoute que são monitorizados na sua rede. Selecione este mosaico para ir para o **topologia** vista.

* **Monitor de conectividade do serviço**: Esta página fornece resumos de estado de funcionamento para diferentes testes que criou. O **topologia** mosaico mostra o número de pontos de extremidade que são monitorizados. Selecione este mosaico para ir para o **topologia** vista.

* **Monitor de desempenho**: Esta página fornece resumos do Estado de funcionamento para o **rede** links e **sub-rede** links que monitoriza a solução. O **topologia** mosaico mostra o número de caminhos de rede que são monitorizados na sua rede. Selecione este mosaico para ir para o **topologia** vista. 

* **Consultas comuns**: Esta página contém um conjunto de consultas de pesquisa que obtêm diretamente a dados de monitorização de rede não processado. Pode utilizar estas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. 

   ![Dashboard de Monitor de desempenho de rede](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Faça uma busca detalhada para profundidade 

Pode selecionar vários links no dashboard de solução para fazer uma busca mais baixo em qualquer área de interesse. Por exemplo, quando vir um alerta ou uma ligação de rede danificadas apareça no dashboard, selecione-o para investigar mais. Uma página apresenta uma lista de todas as ligações de sub-rede para a ligação de rede específico. Pode ver a perda, a latência e o estado de funcionamento de cada ligação de subrede. Pode encontrar rapidamente qual ligação de subrede causa problemas. Selecione **ver ligações de nó** para ver todas as ligações de nó para a ligação de sub-rede mau estado de funcionamento. Em seguida, pode ver ligações de nó para nó individuais e localizar as ligações de nó de mau estado de funcionamento. 

Selecione **ver a topologia** para ver a topologia salto por salto das rotas entre os nós de origem e de destino. As rotas de mau estado de funcionamento são apresentados em vermelho. Pode ver a latência contribuída em cada salto para que pode identificar rapidamente o problema para uma parte específica da rede.

 

### <a name="network-state-recorder-control"></a>Controlo de gravação de estado de rede

Cada vista apresenta um instantâneo do seu estado de funcionamento de rede num momento específico no tempo. Por predefinição, é mostrado o estado mais recente. A barra na parte superior da página mostra o ponto no tempo para o qual o estado é apresentado. Para ver um instantâneo do seu estado de funcionamento de rede num momento anterior, selecione **ações**. Também pode ativar ou desativar atualização automática para qualquer página enquanto ver o estado mais recente. 

 ![Gravador de estado de rede](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendências 

Em cada nível que faça uma busca detalhada, pode ver a tendência da métrica aplicável. Pode ser perda, latência, tempo de resposta ou utilização de largura de banda. Para alterar o intervalo de tempo da tendência, use o controle de tempo na parte superior do gráfico. 

Gráficos de tendências mostram-lhe uma perspectiva histórica do desempenho de uma métrica de desempenho. Alguns problemas de rede são de natureza transitórios e são difíceis de detectar ao observar o estado atual da rede. Problemas podem surgem rapidamente e desaparecer antes que alguém perceba, apenas a reaparecer posteriormente no tempo. Tais problemas transitórios também podem ser difícil para os administradores do aplicativo. Os problemas, muitas vezes, são exibidos como unexplained aumentos no tempo de resposta do aplicativo, até mesmo quando todos os componentes da aplicação aparecem a funcionar sem problemas. 

Pode facilmente detectar estes tipos de problemas ao ver um gráfico de tendência. O problema é apresentado como um pico na latência de rede ou perda de pacotes. Para investigar o problema, utilize o controlo de gravação de estado de rede para ver o instantâneo de rede e a topologia para esse ponto no tempo em que ocorreu o problema.

 
![Gráficos de tendências](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

Monitor de desempenho de rede mostra a topologia salto por salto de rotas entre o ponto final de origem e de destino num mapa de topologia interativo. Para ver o mapa de topologia, selecione o **topologia** mosaico no dashboard de solução. Também pode selecionar o **ver a topologia** ligação nas páginas de desagregação. 

O mapa de topologia apresenta o número de rotas é entre a origem e destino e o que caminhos levar os pacotes de dados. A latência contribuída cada salto de rede também está visível. Todos os caminhos para os quais a latência total do caminho é superior ao limiar (definido na regra de monitorização de correspondente) são exibidos em vermelho. 

Quando seleciona um nó ou coloque o cursor sobre ele no mapa de topologia, pode ver as propriedades de nó, como o FQDN e o endereço IP. Selecione um salto para ver o respetivo endereço IP. É possível identificar o salto de rede problemáticas observando a latência contribui. Para filtrar rotas específicas, utilize os filtros no painel de ação minimizável. Para simplificar as topologias de rede, oculta os saltos intermediários, utilizando o controlo de deslize no painel de ações. Pode aumentar o zoom ou fora do mapa de topologia de zoom com sua roda do mouse. 

A topologia mostrada no mapa é a topologia de camada 3 e não contém dispositivos de camada 2 e ligações. 

 
![Mapa de topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Pesquisa do log Analytics 

Todos os dados que é páginas graficamente expostas através do dashboard de Monitor de desempenho de rede e a desagregação também está disponível nativamente no [de pesquisa do Log Analytics](../../azure-monitor/log-query/log-query-overview.md). Pode efetuar análises interativas de dados no repositório e correlacione dados de origens diferentes. Também pode criar vistas e alertas personalizados e exportar os dados para Excel, Power BI ou uma ligação partilhável. O **consultas comuns** área no dashboard tem algumas consultas útil que pode utilizar como ponto de partida para criar suas próprias consultas e relatórios. 

## <a name="alerts"></a>Alertas

Monitor de desempenho de rede utiliza as capacidades de alertas do [do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Isso significa que todas as notificações são geridas através do [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview).  

Se for um utilizador NPM, criação de um alerta através do Log Analytics: 
1. Verá um link que irá redirecioná-lo para o Portal do Azure. Clique nele para aceder ao portal.
2. Clique no mosaico de solução de Monitor de desempenho de rede. 
3. Navegue para o configurar.  
4. Selecione o teste que pretende criar um alerta e siga o abaixo mencionado passos.

Se for um utilizador NPM, criação de um alerta através do Portal do Azure:  
1. Pode optar por introduzir o seu e-mail diretamente ou pode optar por criar alertas através de grupos de ação.
2. Se optar por introduzir o seu e-mail diretamente, uma ação de grupo com o nome **NPM Email ActionGroup** é criado e o id de e-mail é adicionado a esse grupo de ação.
3. Se optar por utilizar grupos de ação, terá de selecionar um grupo de ação criado anteriormente. Pode saber como criar um grupo de ação [aqui.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Depois do alerta for criado com êxito, pode utilizar o link de gerir alertas para gerir os alertas. 

Sempre que cria um alerta, o NPM cria uma regra de alerta de registo de consulta com base no Azure Monitor. Esta consulta é acionada cada 5 minutos por predefinição. Monitor do Azure não cobra para a 250 primeira registar regras de alerta criadas e quaisquer regras de alerta acima limitam de 250 regras de alerta de registo serão cobradas como [preço na página de preços do Azure Monitor de alertas](https://azure.microsoft.com/en-us/pricing/details/monitor/).
Notificações são cobradas em separado, de acordo [notificações de preço na página de preços do Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/).


## <a name="pricing"></a>Preços

Informações sobre os preços estão disponíveis [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Enviar comentários 

* **UserVoice:** Pode postar suas ideias para funcionalidades de Monitor de desempenho de rede que pretende que trabalhemos no. Visite o [página do UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Junte-se a nossa coorte:** Estamos sempre que novos clientes, Junte-se a nossa coorte. Como parte do mesmo, obtenha acesso antecipado a novos recursos e uma oportunidade para ajudar-na melhorar o Monitor de desempenho de rede. Se tiver interesse em aderir ao, preencha este [inquérito Rápido](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Passos Seguintes 
Saiba mais sobre [Monitor de desempenho](network-performance-monitor-performance-monitor.md), [Monitor de conectividade do serviço](network-performance-monitor-performance-monitor.md), e [Monitor do ExpressRoute](network-performance-monitor-expressroute.md). 
