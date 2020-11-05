---
title: Solução monitor de desempenho de rede em Azure Microsoft Docs
description: O Monitor de Desempenho de Rede em Azure ajuda-o a monitorizar o desempenho das suas redes, em tempo quase real, para detetar e localizar estrangulamentos de desempenho da rede.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 1bb2b7ca22896ed279c8aac215109b7f7bc7854c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394276"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solução monitor de desempenho de rede em Azure

![Símbolo do Monitor de Desempenho da Rede](./media/network-performance-monitor/npm-symbol.png)


O Network Performance Monitor é uma solução de monitorização de rede híbrida baseada na nuvem que o ajuda a monitorizar o desempenho da rede entre vários pontos da sua infraestrutura de rede. Ajuda também a monitorizar a conectividade da rede ao serviço e pontos finais da aplicação, bem como o desempenho do Azure ExpressRoute. 

O Monitor de Desempenho da Rede deteta problemas de rede como o blackholing de tráfego, erros de encaminhamento e problemas que os métodos convencionais de monitorização da rede não são capazes de detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

O Monitor de Desempenho da Rede oferece três amplas capacidades: 

* [Monitor de desempenho](network-performance-monitor-performance-monitor.md): Pode monitorizar a conectividade da rede através de implementações em nuvem e locais no local, vários centros de dados e sucursais e aplicações ou microserviços críticos da missão. Com o Monitor de Desempenho, é possível detetar problemas de rede antes que os utilizadores se queixem.

* [Monitor de Conectividade](network-performance-monitor-service-connectivity.md)de Serviço : Pode monitorizar a conectividade dos seus utilizadores com os serviços de que se preocupa, determinar que infraestruturas está no caminho e identificar onde ocorrem estrangulamentos de rede. Pode saber sobre falhas antes dos seus utilizadores e ver a localização exata dos problemas ao longo do seu caminho de rede. 

    Esta capacidade ajuda-o a realizar testes com base em HTTP, HTTPS, TCP e ICMP para monitorizar em tempo real ou historicamente o tempo de disponibilidade e resposta do seu serviço. Também pode monitorizar a contribuição da rede em perda de pacotes e latência. Com um mapa de topologia de rede, pode isolar abrandamentos de rede. Pode identificar pontos problemáticos que ocorrem ao longo do caminho da rede desde o nó até ao serviço, com dados de latência em cada salto. Com testes incorporados, é possível monitorizar a conectividade da rede com o Microsoft 365 e o Dynamics CRM sem qualquer pré-confirmação. Com esta capacidade, pode monitorizar a conectividade da rede a qualquer ponto final capaz de TCP, como websites, aplicações SaaS, aplicações PaaS e bases de dados SQL.

* [ExpressRoute Monitor](network-performance-monitor-expressroute.md): Monitorize a conectividade e o desempenho de ponta a ponta entre as suas sucursais e a Azure, sobre o Azure ExpressRoute.  

Mais informações sobre as várias capacidades suportadas pelo [Network Performance Monitor](../../networking/network-monitoring-overview.md) estão disponíveis online.
 
## <a name="supported-regions"></a>Regiões apoiadas
O NPM pode monitorizar a conectividade entre redes e aplicações em qualquer parte do mundo, a partir de um espaço de trabalho que é hospedado numa das seguintes regiões:
* Europa do Norte
* Europa Ocidental
* Suíça Norte
* França Central
* Norte da África do Sul
* Canadá Central
* E.U.A. Oeste
* E.U.A. Centro-Oeste
* E.U.A. Centro-Norte
* E.U.A. Centro-Sul
* E.U.A. Central
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste 2
* Japão Oriental
* Ásia Sudeste
* Sudeste da Austrália
* Austrália Central
* Leste da Austrália
* Reino Unido do Sul
* Ásia Leste
* Coreia do Sul Central
* Índia Central
* Governo dos EUA Virgínia
* Governo dos EUA Arizona
* China Leste 2


A lista de regiões apoiadas para o ExpressRoute Monitor está disponível na [documentação.](../../expressroute/how-to-npm.md?utm_swu=8117)


## <a name="set-up-and-configure"></a>Configurar e configurar

> [!NOTE]
> Também pode ver o agente Log Analytics referido como o Agente de Monitorização da Microsoft (MMA) ou o agente OMS Linux.

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Utilize os processos básicos para instalar agentes nos [computadores Connect Windows ao Azure Monitor](../platform/agent-windows.md), [Ligue os computadores Linux ao Azure Monitor (Preview)](../../virtual-machines/extensions/oms-linux.md) e Connect Operations Manager ao [Azure Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

* **Monitor de desempenho** : Instale agentes do Log Analytics em pelo menos um nó ligado a cada sub-rede a partir da qual pretende monitorizar a conectividade da rede a outras sub-redes.

    Para monitorizar uma ligação de rede, instale agentes em ambos os pontos finais dessa ligação. Se não tiver a certeza sobre a topologia da sua rede, instale os agentes em servidores com cargas de trabalho críticas entre as quais pretende monitorizar o desempenho da rede. Por exemplo, se pretender monitorizar a ligação de rede entre um servidor web e um servidor em execução SQL, instale um agente em ambos os servidores. Os agentes monitorizam a conectividade da rede (ligações) entre os anfitriões, e não os próprios anfitriões. 

* **Monitor de Conectividade de Serviço** : Instale um agente Log Analytics em cada nó a partir do qual pretende monitorizar a conectividade da rede até ao ponto final de serviço. Um exemplo é se pretender monitorizar a conectividade da rede com o Microsoft 365 a partir dos seus sites de escritórios com a etiqueta O1, O2 e O3. Instale o agente Log Analytics em pelo menos um nó cada em O1, O2 e O3. 

* **Monitor ExpressRoute** : Instale pelo menos um agente Log Analytics na sua rede virtual Azure. Instale também pelo menos um agente na sua sub-rede no local, que está ligada através do espreitamento privado ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configure os agentes do Log Analytics para monitorização 

O Network Performance Monitor utiliza transações sintéticas para monitorizar o desempenho da rede entre agentes de origem e de destino. Pode escolher entre TCP e ICMP como protocolo para monitorização nas capacidades do Monitor de Desempenho e do Monitor de Conectividade de Serviço. Apenas o TCP está disponível como protocolo de monitorização do ExpressRoute Monitor. Certifique-se de que a firewall permite a comunicação entre os agentes do Log Analytics utilizados para monitorizar o protocolo que escolher. 

* **Protocolo TCP** : Se escolher o TCP como protocolo de monitorização, abra a porta de firewall dos agentes utilizados para o Monitor de Desempenho da Rede e o Monitor ExpressRoute para se certificar de que os agentes podem ligar-se uns aos outros. Para as máquinas Windows, para abrir a porta, executar o script [ powerShellEnableRules.ps1](https://aka.ms/npmpowershellscript) sem parâmetros numa janela PowerShell com privilégios administrativos.
Para as máquinas Linux, os portNumbers devem ser utilizados manualmente. 
* Navegue pelo caminho: /var/opt/microsoft/omsagent/npm_state . 
* Arquivo aberto: npmdregistry
* Alterar o valor para número de porta ```“PortNumber:<port of your choice>”```

 Note que os números de porta utilizados devem ser os mesmos em todos os agentes utilizados num espaço de trabalho. 

O script cria chaves de registo necessárias pela solução. Também cria regras do Windows Firewall para permitir que os agentes criem ligações TCP entre si. As teclas de registo criadas pelo script especificam se registam os registos de depurg e o caminho para o ficheiro de registos. O script também define a porta TCP do agente utilizada para a comunicação. Os valores destas teclas são automaticamente definidos pelo script. Não mude manualmente estas chaves. A porta aberta por defeito é 8084. Pode utilizar uma porta personalizada fornecendo o parâmetro portNumber ao script. Use a mesma porta em todos os computadores onde o script é executado. 

   >[!NOTE]
   > O script configura apenas o Windows Firewall localmente. Se tiver uma firewall de rede, certifique-se de que permite o tráfego destinado à porta TCP utilizada pelo Network Performance Monitor.

   >[!NOTE]
   > Não é necessário executar o [EnableRules.ps1](https://aka.ms/npmpowershellscript ) script PowerShell para o Monitor de Conectividade de Serviço.

    

* **Protocolo ICMP** : Se escolher o ICMP como protocolo de monitorização, permita que as seguintes regras de firewall utilizem de forma fiável o ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configure a solução 

1. Adicione a solução Monitor de Desempenho da Rede ao seu espaço de trabalho a partir do [mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria soluções.](./solutions.md) 
2. Abra o seu espaço de trabalho Log Analytics e selecione o **azulejo de visão geral.** 
3. Selecione o azulejo **do Monitor de Desempenho** da Rede com a solução de mensagem requer uma *configuração adicional*.

   ![Azulejo do Monitor de Desempenho da Rede](media/network-performance-monitor/npm-config.png)

4. Na página **Configuração,** vê a opção de instalar agentes do Log Analytics e configurar os agentes para monitorização na vista **Configurações Comuns.** Como explicado anteriormente, se instalou e configurar agentes do Log Analytics, selecione a vista **de Configuração** para configurar a capacidade que pretende utilizar. 

   **Monitor de Desempenho** : Escolha o protocolo a utilizar para transações sintéticas na regra do Monitor de Desempenho **Predefinido** e selecione **Save & Continue**. Esta seleção de protocolos só é guarda para a regra de incumprimento gerada pelo sistema. Tem de escolher o protocolo sempre que criar uma regra do Monitor de Desempenho explicitamente. Pode sempre mover-se para as definições de regra **padrão** no **separador Monitor de Desempenho** (aparece depois de completar a configuração do dia-0) e alterar o protocolo mais tarde. Se não quiser a capacidade do Monitor de Desempenho, pode desativar a regra predefinida a partir das definições de regra **padrão** no **separador Monitor de Desempenho.**

   ![Vista do Monitor de Desempenho](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor de Conectividade de Serviço** : A capacidade fornece testes pré-configurados incorporados para monitorizar a conectividade da rede com o Microsoft 365 e o Dynamics 365 dos seus agentes. Escolha os serviços Microsoft 365 e Dynamics 365 que pretende monitorizar selecionando as caixas de verificação ao seu lado. Para escolher os agentes a partir dos quais pretende monitorizar, **selecione Adicionar Agentes**. Se não quiser utilizar esta capacidade ou quiser instalá-la mais tarde, não escolha nada e **selecione Save & Continue**.

   ![Vista do Monitor de Conectividade de Serviço](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute Monitor** : Selecione **Discover Now** para descobrir todos os persperos privados ExpressRoute que estão ligados às redes virtuais na subscrição Azure ligada a este espaço de trabalho Log Analytics. 

   ![Vista do Monitor ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Após a descoberta estar terminada, os circuitos descobertos e os seus pares estão listados numa mesa. 

   ![Página de configuração do monitor de desempenho da rede](media/network-performance-monitor/npm-private-peerings.png)
    
A monitorização destes circuitos e dos seus pares encontra-se inicialmente num estado de desativação. Selecione cada recurso que pretende monitorizar e configuure a monitorização para eles a partir da vista de detalhes à direita. **Selecione Guardar** para guardar a configuração. Para saber mais, consulte o artigo "Configurar a monitorização ExpressRoute". 

Depois de terminada a configuração, os dados demoram 30 minutos a uma hora para os dados se preencherem. Enquanto a solução agrega dados da sua rede, vê a solução de mensagem *requer uma configuração adicional* no azulejo de **visão geral** do Monitor de Rede. Após a recolha e indexação dos dados, o azulejo **geral** altera-o e informa-o da saúde da sua rede num resumo. Em seguida, pode editar a monitorização dos nós nos quais estão instalados os agentes do Log Analytics, bem como as sub-redes descobertas a partir do seu ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar as definições de monitorização para sub-redes e nosdes 

Todas as sub-redes com pelo menos um agente instalado estão listadas no separador **Subnetworks** na página de configuração. 


Para permitir ou desativar o controlo de determinadas sub-redes:

1. Selecione ou limpe a caixa de verificação ao lado do **ID da sub-rede**. Em seguida, certifique-se de que **a utilização para monitorização** é selecionada ou limpa, conforme apropriado. Pode selecionar ou limpar várias sub-redes. Quando desativados, as sub-redes não são monitorizadas, e os agentes são atualizados para parar de pingar outros agentes. 
2. Escolha os nós que pretende monitorizar numa determinada sub-rede. Selecione a sub-rede da lista e mova os nós necessários entre as listas que contêm nós não monitorizados e monitorizados. Pode adicionar uma descrição personalizada à sub-rede.
3. **Selecione Guardar** para guardar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolha os nódes para monitorizar

Todos os nós que têm um agente instalado neles estão listados no **separador Nodes.** 

1. Selecione ou limpe os nós que pretende monitorizar ou pare de monitorizar. 
2. Selecione **Utilização para Monitorização,** ou limpe-o, conforme apropriado. 
3. Selecione **Save** (Guardar). 


Configure as capacidades que deseja:

- [Monitorização de Desempenho](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor de Conectividade do Serviço](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes da recolha de dados
Para recolher informações sobre perdas e latência, o Network Performance Monitor utiliza pacotes de aperto de mão TCP SYN-SYNACK-ACK quando escolhe o TCP como protocolo. O Monitor de Desempenho da Rede utiliza resposta ICMP ECHO ECHO ECHO quando escolhe o ICMP como protocolo. A rota do rastreio também é usada para obter informações de topologia.

A tabela seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Monitor de Desempenho da Rede.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistema | Armazenamento do Azure | Diretor de Operações necessário? | Dados de agente do Gestor de Operações enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Apertos de mão TCP/ICMP MENSAGEns ECHO a cada 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução utiliza transações sintéticas para avaliar a saúde da rede. Log Analytics agentes instalados em vários pontos da rede trocam pacotes TCP ou ICMP Echo uns com os outros. Se os agentes utilizam pacotes TCP ou ICMP Echo depende do protocolo selecionado para monitorização. No processo, os agentes aprendem o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também realiza uma rota de rastreio para outros agentes para encontrar todas as várias rotas da rede que devem ser testadas. Utilizando estes dados, os agentes podem deduzir os valores de latência da rede e perda de pacotes. Os testes são repetidos a cada cinco segundos. Os dados são agregados durante cerca de três minutos pelos agentes antes de serem enviados para o espaço de trabalho do Log Analytics no Azure Monitor.



>[!NOTE]
> Embora os agentes se comuniquem frequentemente, não geram tráfego significativo de rede durante a realização dos testes. Os agentes confiam apenas nos pacotes de aperto de mão TCP SYN-SYNACK-ACK para determinar a perda e a latência. Não são trocados pacotes de dados. Durante este processo, os agentes comunicam uns com os outros apenas quando necessário. A topologia de comunicação do agente está otimizada para reduzir o tráfego de rede.

## <a name="use-the-solution"></a>Use a solução 

### <a name="network-performance-monitor-overview-tile"></a>Azulejo geral do monitor de desempenho da rede 

Depois de ativar a solução Network Performance Monitor, o azulejo da solução na página **'Vista Geral'** fornece uma visão geral rápida da saúde da rede. 

 ![Azulejo geral do monitor de desempenho da rede](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Painel de monitorização de desempenho da rede 

* **Principais Eventos de Saúde da Rede** : Esta página fornece uma lista dos mais recentes eventos de saúde e alertas no sistema e no tempo desde que os eventos têm estado ativos. Um evento ou alerta de saúde é gerado sempre que o valor da métrica escolhida (perda, latência, tempo de resposta ou utilização da largura de banda) para a regra de monitorização excede o limiar. 

* **ExpressRoute Monitor** : Esta página fornece resumos de saúde para as várias ligações de observação ExpressRoute que a solução monitoriza. O **azulejo topologia** mostra o número de caminhos de rede através dos circuitos ExpressRoute que são monitorizados na sua rede. Selecione este azulejo para ir à vista **topologia.**

* **Monitor de Conectividade de Serviço** : Esta página fornece resumos de saúde para os diferentes testes que criou. O **azulejo topologia** mostra o número de pontos finais que são monitorizados. Selecione este azulejo para ir à vista **topologia.**

* **Monitor de Desempenho** : Esta página fornece resumos de saúde para as ligações **de rede** e ligações **de subneteta** que a solução monitoriza. O **azulejo topologia** mostra o número de caminhos de rede que são monitorizados na sua rede. Selecione este azulejo para ir à vista **topologia.** 

* **Consultas Comuns** : Esta página contém um conjunto de consultas de pesquisa que procuram dados de monitorização da rede bruta diretamente. Pode utilizar estas consultas como ponto de partida para criar as suas próprias consultas para relatórios personalizados. 

   ![Painel de monitorização de desempenho da rede](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Perfurar para baixo para a profundidade 

Pode selecionar vários links no painel de instrumentos de solução para aprofundar qualquer área de interesse. Por exemplo, quando vir um alerta ou uma ligação de rede pouco saudável aparecer no painel de instrumentos, selecione-o para investigar mais. Uma página lista todos os links de sub-rede para o link de rede específico. Pode ver a perda, latência e estado de saúde de cada ligação de sub-rede. Pode descobrir rapidamente qual a ligação de sub-rede que causa problemas. Selecione **Ver links de nó** para ver todos os links do nó para a ligação de sub-redes pouco saudável. Em seguida, você pode ver ligações individuais nó-a-nó e encontrar os links de nó pouco saudável. 

Selecione **Ver topologia** para ver a topologia hop-by-hop das rotas entre os nós de origem e destino. As rotas pouco saudáveis aparecem no vermelho. Você pode ver a latência contribuida por cada salto para que você possa identificar rapidamente o problema para uma determinada parte da rede.

 

### <a name="network-state-recorder-control"></a>Controlo do gravador de estado de rede

Cada visualização exibe uma imagem instantânea da sua saúde da rede num determinado momento. Por defeito, o estado mais recente é mostrado. A barra no topo da página mostra o ponto no tempo para o qual o estado é exibido. Para visualizar uma imagem instantânea da sua saúde em rede em tempo anterior, selecione **Actions**. Também pode ativar ou desativar a atualização automática para qualquer página enquanto vê o estado mais recente. 

 ![Gravador de Estado da Rede](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendências 

Em cada nível que se perfura, pode-se ver a tendência da métrica aplicável. Pode ser perda, latência, tempo de resposta ou utilização de largura de banda. Para alterar o intervalo de tempo para a tendência, utilize o controlo de tempo no topo da tabela. 

Os gráficos de tendências mostram-lhe uma perspetiva histórica do desempenho de uma métrica de desempenho. Algumas questões de rede são transitórias de natureza e são difíceis de apanhar, analisando apenas o estado atual da rede. Os problemas podem surgir rapidamente e desaparecer antes que alguém note, apenas para reaparecer mais tarde no tempo. Estas questões transitórias também podem ser difíceis para os administradores de aplicações. Os problemas aparecem frequentemente como aumentos inexplicáveis no tempo de resposta da aplicação, mesmo quando todos os componentes da aplicação parecem funcionar sem problemas. 

Você pode facilmente detetar este tipo de problemas olhando para um gráfico de tendências. A questão aparece como um aumento repentino na latência da rede ou perda de pacotes. Para investigar o problema, utilize o controlo do Gravador estatal de rede para visualizar o instantâneo da rede e a topologia para aquele momento em que o problema ocorreu.

 
![Gráficos de tendências](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

O Monitor de Desempenho da Rede mostra-lhe a topologia hop-by-hop das rotas entre a fonte e o ponto final de destino num mapa interativo de topologia. Para ver o mapa de topologia, selecione o azulejo **topologia** no painel de instrumentos de solução. Também pode selecionar o link **de topologia Ver** nas páginas de perfuração. 

O mapa de topologia mostra quantas rotas estão entre a fonte e o destino e que caminhos os pacotes de dados tomam. A latência contribuida por cada lúpulo de rede também é visível. Todos os caminhos para os quais a latência total do caminho está acima do limiar (definido na regra de monitorização correspondente) são mostrados a vermelho. 

Quando seleciona um nó ou paira sobre ele no mapa da topologia, vê as propriedades do nó, como o endereço FQDN e IP. Selecione um salto para ver o seu endereço IP. Pode identificar o salto de rede problemático notando a latência que contribui. Para filtrar determinadas vias, utilize os filtros no painel de ação desmontável. Para simplificar as topologias da rede, esconda os lúpulos intermédios utilizando o slider no painel de ação. Pode ampliar ou ampliar o mapa da topologia utilizando a roda do rato. 

A topologia mostrada no mapa é topologia da camada 3 e não contém dispositivos e conexões de camada 2. 

 
![Mapa de topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Consultas de registo no Azure Monitor

Todos os dados expostos graficamente através do painel de controlo de desempenho da rede e páginas de perfuração também estão disponíveis nativamente em [consultas de registo](../log-query/log-query-overview.md). Pode realizar análises interativas de dados no repositório e correlacionar dados de diferentes fontes. Também pode criar alertas e vistas personalizadas e exportar os dados para Excel, Power BI ou um link partilhável. A área **de Consultas Comuns** no painel de instrumentos tem algumas consultas úteis que você pode usar como ponto de partida para criar suas próprias consultas e relatórios. 

## <a name="alerts"></a>Alertas

O Monitor de Desempenho da Rede utiliza as capacidades de alerta do [Monitor Azure](../platform/alerts-overview.md).

Isto significa que todas as notificações são geridas através de [grupos de ação.](../platform/action-groups.md)  

Se for um utilizador NPM a criar um alerta via Log Analytics: 
1. Verá um link que o redirecionará para o portal Azure. Clique nele para aceder ao portal.
2. Clique no azulejo da solução Monitor de desempenho da rede. 
3. Navegue para Configure.  
4. Selecione o teste em que pretende criar um alerta e siga os passos abaixo mencionados.

Se for um utilizador NPM a criar um alerta através do portal Azure:  
1. Pode optar por inserir o seu e-mail diretamente ou pode optar por criar alertas através de grupos de ação.
2. Se optar por inserir o seu e-mail diretamente, é criado um grupo de ação com o nome **NPM Email ActionGroup** e o id de e-mail é adicionado a esse grupo de ação.
3. Se optar por utilizar grupos de ação, terá de selecionar um grupo de ação previamente criado. Pode aprender a criar um grupo de ação [aqui.](../platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. Uma vez criado o alerta com sucesso, pode utilizar o link 'Gerir alertas' para gerir os seus alertas. 

Cada vez que cria um alerta, o NPM cria uma regra de alerta de registo baseada em consulta no Azure Monitor. Esta consulta é desencadeada a cada 5 minutos por defeito. O monitor Azure não cobra as primeiras 250 regras de alerta de registo criadas, e quaisquer regras de alerta acima do limite de 250 regras de alerta de registo serão faturadas de acordo com [os preços dos Alertas na página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
As notificações são cobradas separadamente de acordo com [o preço das notificações na página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Preços

A informação sobre os preços está disponível [online.](network-performance-monitor-pricing-faq.md)

## <a name="provide-feedback"></a>Enviar comentários 

* **UserVoice:** Pode publicar as suas ideias para funcionalidades do Monitor de Desempenho da Rede em que deseja que trabalhemos. Visite a [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Junte-se ao nosso grupo:** Estamos sempre interessados em que novos clientes se juntem à nossa coorte. Como parte dele, obtém acesso antecipado a novas funcionalidades e uma oportunidade para nos ajudar a melhorar o Network Performance Monitor. Se estiver interessado em aderir, preencha esta [pesquisa rápida.](https://aka.ms/npmcohort) 

## <a name="next-steps"></a>Passos seguintes 
Saiba mais sobre [o Monitor de Desempenho,](network-performance-monitor-performance-monitor.md) [Monitor de Conectividade de Serviços](network-performance-monitor-performance-monitor.md)e [Monitor ExpressRoute](network-performance-monitor-expressroute.md). 

