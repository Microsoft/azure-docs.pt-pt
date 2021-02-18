---
title: 'Azure ExpressRoute: Configure NPM para circuitos'
description: Configure a monitorização da rede baseada em nuvem (NPM) para os circuitos Azure ExpressRoute. Isto cobre a monitorização sobre o peering privado ExpressRoute e o espreitamento da Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 907c03bd15463368def316e72f55ce214cb3e617
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571043"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurar o Monitor de Desempenho de Rede para o ExpressRoute

Este artigo ajuda-o a configurar uma extensão do Monitor de Desempenho da Rede para monitorizar o ExpressRoute. O Monitor de Desempenho de Rede (NPM) é uma solução de monitorização de rede com base na cloud que monitoriza a conectividade entre implementações na cloud do Azure e as localizações no local (sucursais, etc.). O NPM faz parte dos registos do Azure Monitor. O NPM oferece uma extensão para o ExpressRoute que permite monitorizar o desempenho da rede através dos circuitos do ExpressRoute que estão configurados para recorrer ao peering privado ou ao peering da Microsoft. Quando configurar o NPM para o ExpressRoute, poderá detetar problemas de rede relacionados com a identificação e eliminação. Este serviço também está disponível para a Cloud do Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode:

* Monitorizar a perda e a latência em vários VNets e definir alertas

* Monitorize todos os caminhos (incluindo caminhos redundantes) na rede

* Problemas de resolução de problemas transitórios e problemas pontuais de rede que são difíceis de replicar

* Ajude a determinar um segmento específico na rede responsável pelo desempenho degradado

* Obtenha a produção por rede virtual (Se tiver agentes instalados em cada VNet)

* Consulte o estado do sistema ExpressRoute a partir de um ponto anterior no tempo

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Os agentes de monitorização são instalados em vários servidores, tanto no local como em Azure. Os agentes comunicam entre si, mas não enviam dados, enviam pacotes de aperto de mão TCP. A comunicação entre os agentes permite ao Azure mapear a topologia da rede e o caminho que o tráfego poderia tomar.

1. Criar um espaço de trabalho NPM. Isto é o mesmo que um espaço de trabalho log analytics.
2. Instale e configuure agentes de software. (Se quiser apenas monitorizar o Microsoft Peering, não precisa de instalar e configurar agentes de software.): 
    * Instale agentes de monitorização nos servidores do local e nos VMs Azure (para espreitar privadamente).
    * Configurar as definições nos servidores do agente de monitorização para permitir que os agentes de monitorização se comuniquem. (Portas de firewall abertas, etc.)
3. Configure as regras do grupo de segurança da rede (NSG) para permitir que o agente de monitorização instalado em VMs Azure comunique com os agentes de monitorização no local.
4. Configurar a monitorização: Descobrir e gerir quais as redes visíveis em NPM.

Se já estiver a utilizar o Monitor de Desempenho da Rede para monitorizar outros objetos ou serviços, e já tiver espaço de trabalho numa das regiões suportadas, pode saltar o Passo 1 e o Passo 2 e iniciar a sua configuração com o Passo 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Passo 1: Criar um espaço de trabalho

Crie um espaço de trabalho na subscrição que tenha a ligação VNets ao circuito ExpressRoute.

1. No [portal Azure,](https://portal.azure.com)selecione a Subscrição que tem os VNETs espreitados para o seu circuito ExpressRoute. Em seguida, procure a lista de serviços no **Marketplace** para 'Network Performance Monitor'. Na devolução, clique para abrir a página do Monitor de Desempenho da **Rede.**

   >[!NOTE]
   >Pode criar um novo espaço de trabalho ou utilizar um espaço de trabalho existente. Se quiser utilizar um espaço de trabalho existente, deve certificar-se de que o espaço de trabalho foi migrado para a nova linguagem de consulta. [Mais informações...](../azure-monitor/logs/log-query-overview.md)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Na parte inferior da página principal do Monitor de Desempenho da **Rede,** clique em **Criar** para abrir o Monitor de Desempenho da Rede - Criar nova página **de solução.** Clique **em Log Analytics Workspace - selecione um espaço de trabalho** para abrir a página workspaces. Clique **+ Crie um novo espaço de trabalho** para abrir a página workspace.
3. Na página do **espaço de trabalho Log Analytics,** selecione Create **New** e, em seguida, configure as seguintes definições:

   * Log Analytics Workspace - Digite um nome para o seu espaço de trabalho.
   * Subscrição - Se tiver várias subscrições, escolha a que pretende associar-se ao novo Espaço de Trabalho.
   * Grupo de recursos - Crie um grupo de recursos ou utilize um existente.
   * Localização - Esta localização é usada para especificar a localização da conta de armazenamento que é usada para os registos de ligação do agente.
   * Nível de preços - Selecione o nível de preços.
  
     >[!NOTE]
     >O circuito ExpressRoute pode estar em qualquer lugar do mundo. Não tem de ser na mesma região que o Espaço de Trabalho.
     >
  
     ![área de trabalho](./media/how-to-npm/4.png)<br><br>
4. Clique **em OK** para guardar e implementar o modelo de definições. Assim que o modelo validar, clique em **Criar** para implementar o espaço de trabalho.
5. Depois de implantar o Espaço de Trabalho, navegue para o recurso **NetworkMonitoring (nome)** que criou. Validar as definições e clicar **em Solução requer uma configuração adicional.**

   ![configuração adicional](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Passo 2: Instalar e configurar agentes

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Descarregue o ficheiro de configuração do agente

1. Aceda ao separador **Definições Comuns** da página de **Configuração do Monitor de Desempenho da Rede** para o seu recurso. Clique no agente que corresponde ao processador do seu servidor a partir da secção **'Instalar Agentes de Análise de Registos'** e descarregue o ficheiro de configuração.
2. Em seguida, copie o **ID** do Espaço de Trabalho e **a Chave Primária** para o Bloco de Notas.
3. A partir dos **Agentes de Análise de Registo configurado para monitorização utilizando a secção de protocolo TCP,** descarregue o Script Powershell. O script PowerShell ajuda-o a abrir a porta de firewall relevante para as transações TCP.

   ![Script do PowerShell](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Instale um agente de monitorização em cada servidor de monitorização (em cada VNET que pretende monitorizar)

Recomendamos que instale pelo menos dois agentes em cada lado da ligação ExpressRoute para redundância (por exemplo, no local, VNETs Azure). O agente deve ser instalado num Servidor do Windows (2008 SP1 ou posterior). A monitorização dos circuitos ExpressRoute utilizando o Windows Desktop OS e o Linux OS não é suportado. Utilize os seguintes passos para instalar agentes:
   
  >[!NOTE]
  >Os agentes pressionados pela SCOM (inclui [MMA)](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))podem não ser capazes de detetar consistentemente a sua localização se estiverem hospedados em Azure. Recomendamos que não utilize estes agentes em VNETs Azure para monitorizar o ExpressRoute.
  >

1. Executar **configuração** para instalar o agente em cada servidor que pretende utilizar para monitorizar o ExpressRoute. O servidor que utiliza para monitorização pode ser um VM, ou no local, e deve ter acesso à Internet. Tem de instalar pelo menos um agente no local e um agente em cada segmento de rede que pretende monitorizar no Azure.
2. Na página **Bem-vindo**, clique em **Seguinte**.
3. Na página **Termos da Licença,** leia a licença e, em seguida, clique em **I Agree**.
4. Na página **'Pasta destino',** altere ou mantenha a pasta de instalação predefinido e, em seguida, clique em **Seguinte**.
5. Na página **Opções de Configuração** do Agente, pode optar por ligar o agente aos registos do Monitor Azure ou ao Gestor de Operações. Ou pode deixar as escolhas em branco se quiser configurar o agente mais tarde. Depois de fazer a sua(s) seleção, clique em **Seguinte**.

   * Se optar por ligar ao **Azure Log Analytics,** cole a chave **de ID** do Espaço de Trabalho e **espaço de trabalho** (Chave Primária) que copiou no Bloco de Notas na secção anterior. Em seguida, clique **em Seguinte**.

     ![ID e Chave](./media/how-to-npm/8.png)
   * Se optar por ligar ao **Gestor de Operações,** na página de Configuração do **Grupo de Gestão,** digite o **Nome do Grupo de Gestão,** **o Servidor de Gestão** e a Porta do **Servidor de Gestão**. Em seguida, clique **em Seguinte**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na página Conta de Ação do **Agente,** escolha a conta **do Sistema Local,** ou **o Domínio ou a Conta Informática Local.** Em seguida, clique **em Seguinte**.

     ![Conta](./media/how-to-npm/10.png)
6. Na página **Pronto a Instalar,** reveja as suas escolhas e, em seguida, clique em **Instalar**.
7. Na página **Configuração Concluída com Êxito**, clique em **Concluir**.
8. Quando estiver concluído, o Agente de Monitorização da Microsoft aparece no Painel de Controlo. Pode rever a sua configuração lá e verificar se o agente está ligado aos registos do Monitor Azure. Quando ligado, o agente apresenta uma mensagem indicando: **O Agente de Monitorização da Microsoft ligou-se com sucesso ao serviço Microsoft Operations Management Suite**.

9. Repita este procedimento para cada VNET que precisa de ser monitorizado.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Configurações de procuração de configuração (opcional)

Se estiver a utilizar um representante web para aceder à Internet, utilize os seguintes passos para configurar as definições de procuração para o Agente de Monitorização da Microsoft. Execute estes passos para cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Em caso afirmativo, consulte [Configurar as definições de procuração para o Agente de Monitorização da Microsoft utilizando um script](../azure-monitor/agents/agent-windows.md).

Para configurar as definições de procuração para o agente de monitorização da Microsoft utilizando o Painel de Controlo:

1. Abra o **Painel de Controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.
4. Selecione **Utilize um servidor proxy** e digite o URL e o número de porta, se for necessário. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Verificar a conectividade do agente

Pode verificar facilmente se os seus agentes estão a comunicar.

1. Num servidor com o agente de monitorização, abra o **Painel de Controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no **separador Azure Log Analytics.**
4. Na coluna **Status,** deve ver se o agente ligou-se com sucesso aos registos do Monitor Azure.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Abra as portas de firewall nos servidores do agente de monitorização

Para utilizar o protocolo TCP, tem de abrir portas de firewall para garantir que os agentes de monitorização podem comunicar.

Pode executar um script PowerShell para criar as teclas de registo que são exigidas pelo Monitor de Desempenho da Rede. Este script também cria as regras do Windows Firewall para permitir que os agentes de monitorização criem ligações TCP entre si. As teclas de registo criadas pelo script especificam se registam os registos de depurg e o caminho para o ficheiro de registos. Também define a porta TCP do agente utilizada para a comunicação. Os valores destas teclas são automaticamente definidos pelo script. Não deve mudar manualmente estas chaves.

A porta 8084 é aberta por defeito. Pode utilizar uma porta personalizada fornecendo o parâmetro 'portNumber' ao script. No entanto, se o fizer, deve especificar a mesma porta para todos os servidores em que executou o script.

>[!NOTE]
>O script 'EnableRules' PowerShell configura as regras do Windows Firewall apenas no servidor onde o script é executado. Se tiver uma firewall de rede, deve certificar-se de que permite que o tráfego destinado à porta TCP seja utilizado pelo Network Performance Monitor.
>
>

Nos servidores do agente, abra uma janela PowerShell com privilégios administrativos. Executar o script [EnableRules](https://aka.ms/npmpowershellscript) PowerShell (que descarregou anteriormente). Não use parâmetros.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Passo 3: Configurar regras do grupo de segurança da rede

Para monitorizar os servidores de agentes que estão em Azure, é necessário configurar as regras do grupo de segurança da rede (NSG) para permitir o tráfego de TCP numa porta utilizada pela NPM para transações sintéticas. A porta padrão é 8084. Isto permite que um agente de monitorização instalado num VM Azure comunique com um agente de monitorização no local.

Para obter mais informações sobre o NSG, consulte [grupos de segurança de rede.](../virtual-network/tutorial-filter-network-traffic.md)

>[!NOTE]
>Certifique-se de que instalou os agentes (tanto o agente do servidor no local como o agente do servidor Azure) e executou o script PowerShell antes de prosseguir com este passo.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Passo 4: Descubra ligações de espreitar

1. Navegue para o azulejo geral do Monitor de Desempenho da Rede indo para a página **De Todos os Recursos** e, em seguida, clique no espaço de trabalho NPM permitido.

   ![npm espaço de trabalho](./media/how-to-npm/npm.png)
2. Clique no azulejo geral do **Monitor de Desempenho** da Rede para levantar o painel de instrumentos. O painel contém uma página ExpressRoute, que mostra que o ExpressRoute está num "estado não configurado". Clique na **configuração do recurso** para abrir a página de configuração do Monitor de Desempenho da Rede.

   ![configuração de recursos](./media/how-to-npm/npm2.png)
3. Na página de configuração, navegue para o separador 'ExpressRoute Peerings', localizado no painel lateral esquerdo. Em seguida, clique em **Discover Now**.

   ![descobrir](./media/how-to-npm/13.png)
4. Quando a descoberta estiver concluída, verá uma lista contendo os seguintes itens:
   * Todas as ligações de observação da Microsoft no circuito ExpressRoute que estão associadas a esta subscrição.
   * Todas as ligações de observação privadas que se ligam aos VNets associados a esta subscrição.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Passo 5: Monitores de configuração

Nesta secção, configura os monitores. Siga os passos para o tipo de observação que pretende monitorizar: **espreitar privado,** ou **Microsoft a espreitar**.

### <a name="private-peering"></a>Peering privado

Para um espreoming privado, quando a descoberta terminar, você verá as regras para **o nome** de circuito único e **nome VNet**. Inicialmente, estas regras são desativadas.

![regras](./media/how-to-npm/14.png)

1. Verifique o Monitor nesta caixa **de verificação de espreitar.**
2. Selecione a caixa de verificação **Ativar a monitorização da saúde para este espreitamento**.
3. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de saúde digitando valores limiares. Sempre que o valor da condição for superior ao limiar selecionado para o par de rede/sub-rede selecionado, é gerado um evento de saúde.
4. Clique no botão ON-PREM AGENTS **Add Agents** para adicionar os servidores no local a partir dos quais pretende monitorizar a ligação de espreitamento privado. Certifique-se de que apenas escolhe agentes que tenham conectividade com o ponto final do serviço microsoft especificado na secção para o Passo 2. Os agentes no local devem poder chegar ao ponto final utilizando a ligação ExpressRoute.
5. Guarde as definições.
6. Depois de permitir as regras e selecionar os valores e agentes que pretende monitorizar, há uma espera de aproximadamente 30-60 minutos para que os valores comecem a povoar e os azulejos **de monitorização ExpressRoute** fiquem disponíveis.

### <a name="microsoft-peering"></a>Peering da Microsoft

Para o estoiramento da Microsoft, clique nas ligações de esprevação da Microsoft que pretende monitorizar e configure as definições.

1. Verifique o Monitor nesta caixa **de verificação de espreitar.** 
2. (Opcional) Pode alterar o ponto final do serviço da Microsoft. Por padrão, o NPM escolhe um ponto final de serviço da Microsoft como alvo. O NPM monitoriza a conectividade dos seus servidores no local até este ponto final alvo através do ExpressRoute. 
    * Para alterar este ponto final do alvo, clique no link **(editar)** em **Target:** e selecione outro ponto final de destino do serviço Microsoft a partir da lista de URLs.
      ![editar alvo](./media/how-to-npm/edit_target.png)<br>

    * Pode utilizar um URL ou endereço IP personalizado. Esta opção é particularmente relevante se estiver a utilizar o microsoft para estabelecer uma ligação aos serviços Azure PaaS, tais como Azure Storage, bases de dados SQL e Websites que são oferecidos em endereços IP públicos. Para isso, clique no link (Use o URL personalizado ou o **endereço IP em vez disso)** na parte inferior da lista de URL e, em seguida, introduza o ponto final público do seu serviço Azure PaaS que está ligado através do peering ExpressRoute Microsoft.
    ![URL personalizado](./media/how-to-npm/custom_url.png)<br>

    * Se estiver a utilizar estas definições opcionais, certifique-se de que apenas o ponto final do serviço microsoft é selecionado aqui. O ponto final deve ser ligado ao ExpressRoute e acessível pelos agentes no local.
3. Selecione a caixa de verificação **Ativar a monitorização da saúde para este espreitamento**.
4. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de saúde digitando valores limiares. Sempre que o valor da condição for superior ao limiar selecionado para o par de rede/sub-rede selecionado, é gerado um evento de saúde.
5. Clique no botão ON-PREM AGENTS **Add Agents** para adicionar os servidores no local a partir dos quais pretende monitorizar a ligação de observação da Microsoft. Certifique-se de que apenas escolhe agentes que tenham conectividade com os pontos finais do serviço da Microsoft especificados na secção para o Passo 2. Os agentes no local devem poder chegar ao ponto final utilizando a ligação ExpressRoute.
6. Guarde as definições.
7. Depois de permitir as regras e selecionar os valores e agentes que pretende monitorizar, há uma espera de aproximadamente 30-60 minutos para que os valores comecem a povoar e os azulejos **de monitorização ExpressRoute** fiquem disponíveis.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Passo 6: Ver azulejos de monitorização

Assim que vir os azulejos de monitorização, os circuitos ExpressRoute e os recursos de ligação estão a ser monitorizados pela NPM. Pode clicar no telha Microsoft Peering para aprofundar a saúde das ligações Microsoft Peering.

![telhas de monitorização](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Página do Monitor de Desempenho da Rede

A página NPM contém uma página para ExpressRoute que mostra uma visão geral da saúde dos circuitos e dos seus pares ExpressRoute.

![A screenshot mostra um dashboard com uma visão geral da saúde dos circuitos ExpressRoute e dos seus olhos.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista de circuitos

Para visualizar uma lista de todos os circuitos ExpressRoute monitorizados, clique no azulejo dos **circuitos ExpressRoute.** Pode selecionar um circuito e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização da largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizada para traçar as tabelas. Pode arrastar o rato sobre uma área da tabela para ampliar e ver pontos de dados de grãos finos.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Tendência de Perda, Latência e Produção

A largura de banda, a latência e os gráficos de perdas são interativos. Pode ampliar qualquer secção destes gráficos, utilizando controlos de ratos. Também pode ver a largura de banda, latência e dados de perda para outros intervalos clicando em **Data/Hora,** localizada abaixo do botão Ações no canto superior esquerdo.

![tendência](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Lista de pares

Para ver a lista de todas as ligações a redes virtuais através de um persco privado, clique no azulejo **Private Peerings** no painel de instrumentos. Aqui, pode selecionar uma ligação de rede virtual e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência.

![lista de circuitos](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Vista de nódes

Para visualizar a lista de todas as ligações entre os nós no local e os pontos finais do serviço Azure VMs/Microsoft para a ligação de pares ExpressRoute escolhida, clique em **Ver ver os links de nó**. Pode ver o estado de saúde de cada ligação, bem como a tendência de perda e latência associada a eles.

![vista de nosdes](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologia do circuito

Para ver a topologia do circuito, clique no azulejo **topologia.** Isto leva-o à vista topologia do circuito ou do persimento selecionado. O diagrama de topologia fornece a latência para cada segmento da rede. Cada bloco 3 lúpulo é representado por um nó do diagrama. Clicar num salto revela mais detalhes sobre o salto.

Pode aumentar o nível de visibilidade para incluir lúpulos no local movendo a barra de slider abaixo **dos Filtros**. Mover a barra de slider para a esquerda ou direita, aumenta/diminui o número de lúpulo no gráfico de topologia. A latência em cada segmento é visível, o que permite um isolamento mais rápido de segmentos de alta latência na sua rede.

![filtros](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vista detalhada da topologia de um circuito

Esta vista mostra ligações VNet.
![topologia detalhada](./media/how-to-npm/17.png)