---
title: Solução de Monitor de Desempenho de Rede em Azure Microsoft Docs
description: O Network Performance Monitor em Azure ajuda-o a monitorizar o desempenho das suas redes, em tempo real, para detetar e localizar estrangulamentos de desempenho da rede.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480883"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solução de Monitor de Desempenho de Rede em Azure

![Símbolo do Monitor de Desempenho da Rede](./media/network-performance-monitor/npm-symbol.png)


O Network Performance Monitor é uma solução de monitorização de rede híbrida baseada na nuvem que o ajuda a monitorizar o desempenho da rede entre vários pontos da sua infraestrutura de rede. Ajuda também a monitorizar a conectividade da rede ao serviço e pontos finais da aplicação, bem como o desempenho do Azure ExpressRoute. 

O Network Performance Monitor deteta problemas de rede como blackholing de tráfego, erros de encaminhamento e problemas que os métodos convencionais de monitorização da rede não são capazes de detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

O Network Performance Monitor oferece três amplas capacidades: 

* [Monitor de Desempenho:](network-performance-monitor-performance-monitor.md)Pode monitorizar a conectividade da rede através de implementações em nuvem e locais no local, múltiplos centros de dados e filiais e aplicações ou microserviços multimais críticos da missão. Com o Monitor de Desempenho, pode detetar problemas de rede antes que os utilizadores se queixem.

* [Monitor](network-performance-monitor-service-connectivity.md)de Conectividade de Serviço : Pode monitorizar a conectividade dos seus utilizadores aos serviços de que se preocupa, determinar que infraestrutura está no caminho e identificar onde ocorrem estrangulamentos de rede. Pode saber sobre falhas antes dos seus utilizadores e ver a localização exata dos problemas ao longo da sua trajetória de rede. 

    Esta capacidade ajuda-o a realizar testes com base em HTTP, HTTPS, TCP e ICMP para monitorizar em tempo real ou historicamente o tempo de disponibilidade e resposta do seu serviço. Também pode monitorizar a contribuição da rede na perda de pacotes e latência. Com um mapa de topologia de rede, pode isolar os abrandamentos da rede. Pode identificar pontos problemáticos que ocorrem ao longo do caminho da rede desde o nó até ao serviço, com dados de latência em cada salto. Com testes incorporados, pode monitorizar a conectividade da rede com o Office 365 e o Dynamics CRM sem qualquer preconfiguração. Com esta capacidade, pode monitorizar a conectividade da rede com qualquer ponto final capaz de TCP, tais como websites, aplicações SaaS, aplicações PaaS e bases de dados SQL.

* [Monitor ExpressRoute](network-performance-monitor-expressroute.md): Monitor de conectividade e desempenho de ponta a ponta entre as suas filiais e o Azure, sobre o Azure ExpressRoute.  

Mais informações sobre as várias capacidades suportadas pelo [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.
 
## <a name="supported-regions"></a>Regiões Apoiadas
O NPM pode monitorizar a conectividade entre redes e aplicações em qualquer parte do mundo, a partir de um espaço de trabalho que está alojado numa das seguintes regiões:
* Europa do Norte
* Europa ocidental
* França Central
* Canadá Central
* E.U.A. Oeste
* E.U.A. Centro-Oeste
* E.U.A. Centro-Norte
* E.U.A. Centro-Sul
* E.U.A. Central
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A.Oeste 2
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
* China Leste 2


A lista de regiões apoiadas para o Monitor ExpressRoute está disponível na [documentação.](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117)


## <a name="set-up-and-configure"></a>Configurar e configurar

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Utilize os processos básicos para instalar agentes em [Connect Windows computadores para O Monitor Azure](../platform/agent-windows.md) e [Connect Operations Manager para o Monitor Azure](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

* **Monitor de Desempenho**: Instale os agentes De log Analytics em pelo menos um nó ligado a cada subrede a partir do qual pretende monitorizar a conectividade da rede a outras subredes.

    Para monitorizar uma ligação de rede, instale agentes em ambos os pontos finais desse link. Se não tiver a certeza sobre a topoologia da sua rede, instale os agentes nos servidores com cargas de trabalho críticas entre as quais pretende monitorizar o desempenho da rede. Por exemplo, se pretender monitorizar a ligação de rede entre um servidor web e um servidor em execução SQL, instale um agente em ambos os servidores. Os agentes monitorizam a conectividade da rede (ligações) entre os anfitriões, e não os próprios anfitriões. 

* **Monitor de Conectividade**de Serviço : Instale um agente Log Analytics em cada nó a partir do qual pretende monitorizar a conectividade da rede até ao ponto final do serviço. Um exemplo é se quiser monitorizar a conectividade da rede com o Office 365 a partir dos seus sites de escritórios com a marca O1, O2 e O3. Instale o agente Log Analytics em pelo menos um nó cada um em O1, O2 e O3. 

* **Monitor ExpressRoute**: Instale pelo menos um agente Log Analytics na sua rede virtual Azure. Instale também pelo menos um agente na sua subrede no local, que está ligada através de um peering privado ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configure os agentes de Log Analytics para monitorização 

O Network Performance Monitor utiliza transações sintéticas para monitorizar o desempenho da rede entre os agentes de origem e destino. Pode escolher entre tCP e ICMP como o protocolo de monitorização nas capacidades do Monitor de Desempenho e do Monitor de Conectividade do Serviço. Apenas o TCP está disponível como o protocolo de monitorização para o Monitor ExpressRoute. Certifique-se de que a firewall permite a comunicação entre os agentes Log Analytics utilizados para monitorizar o protocolo que escolher. 

* **Protocolo TCP**: Se escolher o TCP como protocolo de monitorização, abra a porta de firewall dos agentes utilizados para o Monitor de Desempenho da Rede e o Monitor ExpressRoute para se certificar de que os agentes podem ligar-se entre si. Para abrir a porta, execute o script [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell sem quaisquer parâmetros numa janela PowerShell com privilégios administrativos.

    O script cria chaves de registo exigidas pela solução. Também cria regras do Windows Firewall para permitir que os agentes criem ligações TCP entre si. As teclas de registo criadas pelo script especificam se devem registar os registos de depuração e o caminho para o ficheiro de registos. O script também define a porta TCP do agente utilizada para a comunicação. Os valores destas teclas são automaticamente definidos pelo script. Não mude manualmente estas chaves. A porta aberta por defeito é 8084. Pode utilizar uma porta personalizada fornecendo a porta de parâmetroNúmero para o script. Utilize a mesma porta em todos os computadores onde o script é executado. 

    >[!NOTE]
    > O script configura apenas o Windows Firewall localmente. Se tiver uma firewall de rede, certifique-se de que permite o tráfego destinado à porta TCP utilizada pelo Monitor de Desempenho da Rede.

    >[!NOTE]
    > Não é necessário executar o script [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell para o Monitor de Conectividade do Serviço.

    

* **Protocolo ICMP**: Se escolher o ICMP como protocolo de monitorização, ative as seguintes regras de firewall para utilizar de forma fiável o ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configure a solução 

1. Adicione a solução de Monitor de Desempenho da Rede ao seu espaço de trabalho a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md). 
2. Abra o seu espaço de trabalho Log Analytics e selecione o azulejo **de visão geral.** 
3. Selecione o azulejo do Monitor de Desempenho da **Rede** com a *mensagem A solução requer uma configuração adicional*.

   ![Azulejo do Monitor de Desempenho da Rede](media/network-performance-monitor/npm-config.png)

4. Na página **'Configuração',** vê a opção de instalar agentes Log Analytics e configurar os agentes para monitorização na vista **Definições Comuns.** Como anteriormente explicado, se instalou e configurar os agentes log analytics, selecione a vista **Configuração** para configurar a capacidade que pretende utilizar. 

   **Monitor de Desempenho**: Escolha o protocolo a utilizar para transações sintéticas na regra do Monitor de Desempenho **Predefinido** e selecione **Save & Continue**. Esta seleção de protocolo seleção apenas se mantém para a regra de padrão gerada pelo sistema. Você precisa escolher o protocolo cada vez que criar uma regra de Monitor de Desempenho explicitamente. Pode sempre mover-se para as definições de regra **Predefinido** no separador Monitor de **Desempenho** (aparece depois de completar a configuração do dia-0) e alterar o protocolo mais tarde. Se não quiser a capacidade do Monitor de Desempenho, pode desativar a regra predefinida a partir das definições de regra **Predefinida** **sem** condutor.

   ![Vista do Monitor de Desempenho](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor**de Conectividade de Serviço : A capacidade fornece testes pré-configurados incorporados para monitorizar a conectividade da rede com o Office 365 e a Dynamics 365 dos seus agentes. Escolha os serviços Office 365 e Dynamics 365 que pretende monitorizar selecionando as caixas de verificação ao seu lado. Para escolher os agentes a partir dos quais pretende monitorizar, selecione **Adicionar Agentes**. Se não quiser utilizar esta capacidade ou quiser instalá-la mais tarde, não escolha nada e selecione **Save & Continue**.

   ![Vista monitor de conectividade de serviço](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute Monitor**: Selecione **Discover Now** para descobrir todos os pares privados ExpressRoute que estão ligados às redes virtuais da subscrição Azure ligadas a este espaço de trabalho Log Analytics. 

   ![Visão do Monitor ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Após a descoberta, os circuitos descobertos e os olhares estão listados numa mesa. 

   ![Página de configuração do monitor de desempenho da rede](media/network-performance-monitor/npm-private-peerings.png)
    
A monitorização destes circuitos e observações encontra-se inicialmente em estado de desativação. Selecione cada recurso que pretende monitorizar e configure a monitorização dos mesmos a partir da vista de detalhes à direita. Selecione **Guardar** para salvar a configuração. Para saber mais, consulte o artigo "Configure ExpressRoute monitoring". 

Depois de terminar a configuração, leva 30 minutos a uma hora para os dados povoarem. Enquanto a solução agrega dados da sua rede, vê que a solução de mensagem *requer uma configuração adicional* no azulejo de visão **geral** do Monitor de Desempenho da Rede. Depois de recolhidos e indexados os dados, o azulejo de **visão geral** muda e informa-o da sua saúde da rede num resumo. Em seguida, pode editar a monitorização dos nós em que os agentes log Analytics estão instalados, bem como as subredes descobertas do seu ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar definições de monitorização para subredes e nódosos 

Todas as subredes com pelo menos um agente instalado estão listadas no separador **Subredes** na página de configuração. 


Para permitir ou desativar a monitorização de determinadas subredes:

1. Selecione ou limpe a caixa de verificação ao lado do ID da **sub-rede**. Em seguida, certifique-se de que a **utilização para monitorização** é selecionada ou apurada, conforme apropriado. Pode selecionar ou limpar várias subredes. Quando desativadas, as subredes não são monitorizadas, e os agentes são atualizados para parar de pingar outros agentes. 
2. Escolha os nódosos que pretende monitorizar numa determinada subrede. Selecione a subrede da lista e mova os nós necessários entre as listas que contenham nós não monitorizados e monitorizados. Pode adicionar uma descrição personalizada à subrede.
3. Selecione **Guardar** para salvar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolha os nódosos para monitorizar

Todos os nós que têm um agente instalado neles estão listados no separador **Nós.** 

1. Selecione ou limpe os nódosos que pretende monitorizar ou parar de monitorizar. 
2. Selecione **Use para monitorização,** ou limpe-a, conforme apropriado. 
3. Selecione **Guardar**. 


Configure as capacidades que deseja:

- [Monitorização de Desempenho](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor de Conectividade do Serviço](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes da recolha de dados
Para recolher informações sobre perdas e latências, o Network Performance Monitor utiliza pacotes de aperto de mão TCP SYN-SYNACK-ACK quando escolhe o TCP como protocolo. O Monitor de Desempenho da Rede utiliza o ICMP ECHO ECHO ANSWER quando escolhe o ICMP como protocolo. A rota de rastreio também é usada para obter informações de topologia.

O quadro seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Monitor de Desempenho da Rede.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistema | Storage do Azure | Gestor de Operações necessário? | Dados do agente de operações enviados através de grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Apertos de mão/Mensagens Echo do ICMP a cada 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução utiliza transações sintéticas para avaliar a saúde da rede. Agentes de Log Analytics instalados em vários pontos dos pacotes TCP de troca de rede ou DoICMP Echo uns com os outros. Se os agentes usam pacotes TCP ou ICMP Echo depende do protocolo selecionado para monitorização. No processo, os agentes aprendem o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também realiza uma rota de rastreio para outros agentes para encontrar todas as várias rotas da rede que devem ser testadas. Utilizando estes dados, os agentes podem deduzir os números de latência da rede e perda de pacotes. Os testes são repetidos a cada cinco segundos. Os dados são agregados durante cerca de três minutos pelos agentes antes de ser enviado para o espaço de trabalho log Analytics no Monitor Azure.



>[!NOTE]
> Embora os agentes comuniquem-se frequentemente, não geram tráfego de rede significativo enquanto realizam os testes. Os agentes dependem apenas de pacotes de aperto de mão Da TCP SYN-SYNACK-ACK para determinar a perda e a latência. Não são trocados pacotes de dados. Durante este processo, os agentes comunicam-se uns com os outros apenas quando necessário. A topologia de comunicação do agente está otimizada para reduzir o tráfego de rede.

## <a name="use-the-solution"></a>Use a solução 

### <a name="network-performance-monitor-overview-tile"></a>Azulejo de visão geral do monitor de desempenho da rede 

Depois de ativar a solução Network Performance Monitor, o azulejo de solução na página **Overview** fornece uma visão geral rápida da saúde da rede. 

 ![Azulejo de visão geral do monitor de desempenho da rede](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Painel de monitor de desempenho da rede 

* **Principais Eventos**de Saúde da Rede : Esta página fornece uma lista dos mais recentes eventos de saúde e alertas no sistema e o tempo desde que os eventos estão ativos. Um evento de saúde ou alerta é gerado sempre que o valor da métrica escolhida (perda, latência, tempo de resposta ou utilização da largura de banda) para a regra de monitorização exceder o limiar. 

* **ExpressRoute Monitor**: Esta página fornece resumos de saúde para as várias ligações de peering ExpressRoute os monitores de solução. O azulejo **topologia** mostra o número de percursos de rede através dos circuitos ExpressRoute que são monitorizados na sua rede. Selecione este azulejo para ir à vista **Topologia.**

* **Monitor de Conectividade**de Serviço : Esta página fornece resumos de saúde para os diferentes testes que criou. O azulejo **topologia** mostra o número de pontos finais que são monitorizados. Selecione este azulejo para ir à vista **Topologia.**

* **Monitor de Desempenho**: Esta página fornece resumos de saúde para as ligações de **rede** e **ligações de subrede** que a solução monitoriza. O azulejo **topologia** mostra o número de caminhos de rede que são monitorizados na sua rede. Selecione este azulejo para ir à vista **Topologia.** 

* **Consultas comuns**: Esta página contém um conjunto de consultas de pesquisa que rebuscam dados de monitorização de rede bruta diretamente. Pode usar estas consultas como ponto de partida para criar as suas próprias consultas para reportagens personalizadas. 

   ![Painel de monitor de desempenho da rede](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Perfurar para baixo para profundidade 

Pode selecionar várias ligações no painel de instrumentos de solução para aprofundar mais em qualquer área de interesse. Por exemplo, quando vir um alerta ou uma ligação de rede pouco saudável aparecer no painel de instrumentos, selecione-o para investigar mais. Uma página lista todos os links de subrede para o link de rede específico. Pode ver a perda, latência e estado de saúde de cada ligação de subrede. Pode descobrir rapidamente qual a ligação de subrede que causa problemas. Selecione **Ver ligações** do nó para ver todas as ligações do nó para a ligação de sub-rede pouco saudável. Em seguida, você pode ver ligações individuais de nó ao nó e encontrar as ligações do nó insalubre. 

Selecione **Ver topologia** para ver a topologia hop-by-hop das rotas entre a fonte e os nós de destino. As rotas pouco saudáveis aparecem a vermelho. Pode ver a latência contribuída por cada salto para que possa identificar rapidamente o problema para uma determinada parte da rede.

 

### <a name="network-state-recorder-control"></a>Controlo de gravador de estado de rede

Cada vista apresenta uma imagem instantânea da sua saúde de rede num determinado momento. Por padrão, o estado mais recente é mostrado. A barra no topo da página mostra o ponto de tempo para o qual o estado é exibido. Para ver uma imagem da sua saúde de rede em um momento anterior, selecione **Ações**. Também pode ativar ou desativar a atualização automática para qualquer página enquanto vê o estado mais recente. 

 ![Gravador de Estado de Rede](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendências 

Em cada nível que você perfura, você pode ver a tendência da métrica aplicável. Pode ser perda, latência, tempo de resposta ou utilização da largura de banda. Para alterar o intervalo de tempo para a tendência, utilize o controlo de tempo no topo da tabela. 

Os gráficos de tendências mostram-lhe uma perspetiva histórica do desempenho de uma métrica de desempenho. Algumas questões de rede são de natureza transitória e são difíceis de apanhar olhando apenas para o estado atual da rede. As questões podem surgir rapidamente e desaparecer antes que alguém perceba, apenas para reaparecer mais tarde no tempo. Estas questões transitórias também podem ser difíceis para os administradores de aplicações. As questões aparecem frequentemente como aumentos inexplicáveis no tempo de resposta à aplicação, mesmo quando todos os componentes da aplicação parecem funcionar sem problemas. 

Você pode facilmente detetar este tipo de problemas olhando para um gráfico de tendências. O problema aparece como um aumento repentino na latência da rede ou perda de pacotes. Para investigar o problema, utilize o controlo do Gravador de Estado da Rede para visualizar o instantâneo da rede e a topologia para esse momento em que o problema ocorreu.

 
![Gráficos de tendências](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

O Network Performance Monitor mostra-lhe a topologia hop-by-hop de rotas entre a fonte e o ponto final do destino num mapa interativo de topologia. Para ver o mapa de topologia, selecione o azulejo **topologia** no painel de solução. Também pode selecionar o link **'Ver topologia'** nas páginas de perfuração. 

O mapa de topologia mostra quantas rotas existem entre a fonte e o destino e que caminhos os pacotes de dados tomam. A latência contribuida por cada rede de lúpulo também é visível. Todos os caminhos para os quais a latência total do caminho está acima do limiar (definido na regra de monitorização correspondente) são mostrados a vermelho. 

Quando seleciona um nó ou paira sobre ele no mapa de topologia, vê as propriedades do nó, como fQDN e endereço IP. Selecione um salto para ver o seu endereço IP. Pode identificar o salto de rede problemático, notando a latência que contribui. Para filtrar determinadas rotas, utilize os filtros no painel de ação desmontável. Para simplificar as topoologias da rede, esconda o lúpulo intermédio utilizando o slider no painel de ação. Pode ampliar ou ampliar para fora do mapa de topologia utilizando a roda do rato. 

A topologia mostrada no mapa é topologia de camada 3 e não contém dispositivos e ligações de camada 2. 

 
![Mapa de topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Consultas de log no Monitor Azure

Todos os dados expostos graficamente através do painel de instrumentos do Monitor de Desempenho da Rede e das páginas de perfuração também estão disponíveis de forma nativa em consultas de [log](../log-query/log-query-overview.md). Pode efetuar análiseinterativa de dados no repositório e correlacionar dados de diferentes fontes. Também pode criar alertas e vistas personalizados e exportar os dados para Excel, Power BI ou um link compartilhável. A área **de Consultas Comuns** no painel de instrumentos tem algumas consultas úteis que pode usar como ponto de partida para criar as suas próprias consultas e relatórios. 

## <a name="alerts"></a>Alertas

O Monitor de Desempenho da Rede utiliza as capacidades de alerta do [Monitor Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Isto significa que todas as notificações são geridas através de grupos de [ação.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)  

Se for um utilizador npm criando um alerta via Log Analytics: 
1. Verá um link que irá redirecioná-lo para o portal Azure. Clique nele para aceder ao portal.
2. Clique no azulejo da solução Network Performance Monitor. 
3. Navegue para Configurar.  
4. Selecione o teste em que pretende criar um alerta e siga os passos abaixo mencionados.

Se for um utilizador npm criando um alerta através do portal Azure:  
1. Pode optar por inserir o seu email diretamente ou pode optar por criar alertas através de grupos de ação.
2. Se optar por inserir o seu e-mail diretamente, é criado um grupo de ação com o nome **NPM Email ActionGroup** e o id de e-mail é adicionado a esse grupo de ação.
3. Se optar por utilizar grupos de ação, terá de selecionar um grupo de ação previamente criado. Pode aprender a criar um grupo de ação [aqui.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Assim que o alerta for criado com sucesso, pode utilizar a ligação 'Gerir alertas' para gerir os seus alertas. 

Cada vez que cria um alerta, o NPM cria uma regra de alerta de registo baseada em consulta no Monitor Azure. Esta consulta é desencadeada a cada 5 minutos por padrão. O monitor Azure não cobra as primeiras 250 regras de alerta de log criadas, e quaisquer regras de alerta acima do limite de 250 regras de alerta de log serão faturadas de acordo com os preços dos Alertas na página de [preços do Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/).
As notificações são cobradas separadamente de acordo com os preços das [Notificações na página de preços do Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Preços

A informação sobre os preços está disponível [online.](network-performance-monitor-pricing-faq.md)

## <a name="provide-feedback"></a>Enviar comentários 

* **UserVoice:** Pode publicar as suas ideias para funcionalidades do Network Performance Monitor em que pretende que trabalhemos. Visite a [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Junte-se à nossa coorte:** Estamos sempre interessados em ter novos clientes a juntarem-se à nossa coorte. Como parte dele, você tem acesso antecipado a novas funcionalidades e uma oportunidade para nos ajudar a melhorar o Monitor de Desempenho da Rede. Se estiver interessado em aderir, preencha esta [pesquisa rápida.](https://aka.ms/npmcohort) 

## <a name="next-steps"></a>Passos seguintes 
Saiba mais sobre o Monitor de [Desempenho,](network-performance-monitor-performance-monitor.md) [monitor de conectividade de serviço](network-performance-monitor-performance-monitor.md)e monitor [expressroute.](network-performance-monitor-expressroute.md) 
