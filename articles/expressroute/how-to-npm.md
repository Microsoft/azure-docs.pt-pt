---
title: 'Azure ExpressRoute: Configure NPM para circuitos'
description: Configure a monitorização da rede baseada na nuvem (NPM) para os circuitos Azure ExpressRoute. Isto cobre a monitorização sobre o peering privado ExpressRoute e o peering da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74076596"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurar o Monitor de Desempenho de Rede para o ExpressRoute

Este artigo ajuda-o a configurar uma extensão do Monitor de Desempenho da Rede para monitorizar o ExpressRoute. O Monitor de Desempenho de Rede (NPM) é uma solução de monitorização de rede com base na cloud que monitoriza a conectividade entre implementações na cloud do Azure e as localizações no local (sucursais, etc.). O NPM faz parte dos registos do Azure Monitor. O NPM oferece uma extensão para o ExpressRoute que permite monitorizar o desempenho da rede através dos circuitos do ExpressRoute que estão configurados para recorrer ao peering privado ou ao peering da Microsoft. Quando configurar o NPM para o ExpressRoute, poderá detetar problemas de rede relacionados com a identificação e eliminação. Este serviço também está disponível para a Cloud do Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode:

* Monitorizar perda e latência em vários VNets e definir alertas

* Monitorize todos os caminhos (incluindo caminhos redundantes) na rede

* Problemas transitórios e problemas de rede pontual que são difíceis de replicar

* Ajude a determinar um segmento específico na rede que é responsável pelo desempenho degradado

* Obtenha a entrada por rede virtual (se tiver agentes instalados em cada VNet)

* Consulte o estado do sistema ExpressRoute a partir de um ponto anterior no tempo

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Os agentes de monitorização estão instalados em vários servidores, tanto no local como no Azure. Os agentes comunicam uns com os outros, mas não enviam dados, enviam pacotes de aperto de mão da TCP. A comunicação entre os agentes permite ao Azure mapear a topologia da rede e o caminho que o tráfego poderia tomar.

1. Crie um espaço de trabalho NPM. Isto é o mesmo que um espaço de trabalho log Analytics.
2. Instale e configure agentes de software. (Se apenas quiser monitorizar o Microsoft Peering, não precisa de instalar e configurar agentes de software.): 
    * Instale agentes de monitorização nos servidores no local e nos VMs Azure (para espreitar privados).
    * Configure as definições nos servidores do agente de monitorização para permitir a comunicação dos agentes de monitorização. (Portas de firewall abertas, etc.)
3. Configure as regras do grupo de segurança da rede (NSG) para permitir que o agente de monitorização instalado em VMs Azure comunique com os agentes de monitorização no local.
4. Configurar a monitorização: Descubra automaticamente e gere quais as redes visíveis em NPM.

Se já estiver a utilizar o Monitor de Desempenho da Rede para monitorizar outros objetos ou serviços, e já tiver espaço de trabalho numa das regiões suportadas, pode saltar o Passo 1 e o Passo 2 e iniciar a sua configuração com o Passo 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Passo 1: Criar um espaço de trabalho

Crie um espaço de trabalho na subscrição que tenha a ligação VNets ao circuito ExpressRoute.

1. No [portal Azure,](https://portal.azure.com)selecione a Subscrição que tem os VNETs espreitadas para o seu circuito ExpressRoute. Em seguida, procure na lista de serviços no **Marketplace** o 'Network Performance Monitor'. Na devolução, clique para abrir a página do Monitor de Desempenho da **Rede.**

   >[!NOTE]
   >Pode criar um novo espaço de trabalho ou utilizar um espaço de trabalho existente. Se quiser utilizar um espaço de trabalho existente, deve certificar-se de que o espaço de trabalho foi migrado para a nova linguagem de consulta. [Mais informações...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Na parte inferior da página principal do Monitor de Desempenho da **Rede,** clique em **Criar** para abrir o Monitor de Desempenho da Rede - Criar uma nova página de **solução.** Clique em **Log Analytics Workspace - selecione um espaço de trabalho** para abrir a página Workspaces. Clique **+ Criar novo espaço** de trabalho para abrir a página Workspace.
3. Na página **do espaço de trabalho Log Analytics,** selecione Criar **Novo**e, em seguida, configurar as seguintes definições:

   * Log Analytics Workspace - Digite um nome para o seu Espaço de Trabalho.
   * Subscrição - Se tiver várias subscrições, escolha a que pretende associar ao novo Espaço de Trabalho.
   * Grupo de recursos - Criar um grupo de recursos, ou usar um existente.
   * Localização - Esta localização é utilizada para especificar a localização da conta de armazenamento que é utilizada para os registos de ligação do agente.
   * Nível de preços - Selecione o nível de preços.
  
     >[!NOTE]
     >O circuito ExpressRoute pode estar em qualquer lugar do mundo. Não tem de ser na mesma região que o Espaço de Trabalho.
     >
  
     ![área de trabalho](./media/how-to-npm/4.png)<br><br>
4. Clique em **OK** para guardar e implementar o modelo de definições. Uma vez que o modelo validar, clique **em Criar** para implementar o Espaço de Trabalho.
5. Depois de implementado o Workspace, navegue para o recurso **NetworkMonitoring (nome)** que criou. Validar as definições e, em seguida, clicar na **Solução requer configuração adicional**.

   ![configuração adicional](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Passo 2: Instalar e configurar agentes

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Descarregue o ficheiro de configuração do agente

1. Vá ao separador **Definições Comuns** da página de **configuração** do Monitor de Desempenho da Rede para o seu recurso. Clique no agente que corresponde ao processador do seu servidor a partir da secção **Deinstalar agentes de registo** e descarregue o ficheiro de configuração.
2. Em seguida, copie o ID do **espaço de trabalho** e a chave **primária** para o bloco de notas.
3. A partir dos agentes de análise de **registo configure para monitorização utilizando** a secção de protocolo TCP, descarregue o Script Powershell. O script PowerShell ajuda-o a abrir a porta de firewall relevante para as transações de TCP.

   ![Script do PowerShell](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Instale um agente de monitorização em cada servidor de monitorização (em cada VNET que pretende monitorizar)

Recomendamos que instale pelo menos dois agentes em cada lado da ligação ExpressRoute para despedimento (por exemplo, no local, VNETs Azure). O agente deve ser instalado num Servidor do Windows (2008 SP1 ou posterior). Não é suportado o monitoramento dos circuitos ExpressRoute utilizando o Windows Desktop OS e o Linux OS. Utilize os seguintes passos para instalar agentes:
   
  >[!NOTE]
  >Os agentes empurrados pela SCOM (inclui [MMA)](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)podem não ser capazes de detetar consistentemente a sua localização se estiverem hospedados em Azure. Recomendamos que não utilize estes agentes em VNETs Azure para monitorizar a ExpressRoute.
  >

1. Executar **Configuração** para instalar o agente em cada servidor que pretende utilizar para monitorizar o ExpressRoute. O servidor que utiliza para monitorização pode ser um VM, ou no local, e deve ter acesso à Internet. Precisa instalar pelo menos um agente no local e um agente em cada segmento de rede que pretende monitorizar no Azure.
2. Na página **Bem-vindo**, clique em **Seguinte**.
3. Na página Termos de **Licença,** leia a licença e clique em **I Agree**.
4. Na página **da Pasta destino,** altere ou mantenha a pasta de instalação predefinida e, em seguida, clique **em Seguinte**.
5. Na página Opções de Configuração do **Agente,** pode optar por ligar o agente aos registos do Monitor Azure ou ao Gestor de Operações. Ou pode deixar as escolhas em branco se quiser configurar o agente mais tarde. Depois de fazer a sua seleção, clique **em Seguinte**.

   * Se optou por ligar-se ao **Azure Log Analytics,** colá-lo o ID do **espaço de trabalho** e a chave do espaço de **trabalho** (Chave Primária) que copiou para o Bloco de Notas na secção anterior. Em seguida, clique **em Next**.

     ![ID e Chave](./media/how-to-npm/8.png)
   * Se optou por ligar-se ao Gestor de **Operações,** na página de Configuração do **Grupo de Gestão,** digite o Nome do **Grupo de Gestão,** Servidor de **Gestão**e a Porta do Servidor de **Gestão.** Em seguida, clique **em Next**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na página **da Conta de Ação do Agente,** escolha a conta do Sistema **Local,** ou **o Domínio ou a Conta De Computador Local**. Em seguida, clique **em Next**.

     ![Conta](./media/how-to-npm/10.png)
6. Na página **Ready to Install,** reveja as suas escolhas e, em seguida, clique em **Instalar**.
7. Na página **Configuração Concluída com Êxito**, clique em **Concluir**.
8. Quando estiver concluído, o Agente de Monitorização da Microsoft aparece no Painel de Controlo. Pode rever a sua configuração e verificar se o agente está ligado aos registos do Monitor Azure. Quando ligado, o agente apresenta uma mensagem indicando: O Agente de Monitorização da **Microsoft ligou-se com sucesso ao serviço Microsoft Operations Management Suite**.

9. Repita este procedimento para cada VNET que precisa de ser monitorizado.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Configurar as definições de procuração (opcional)

Se estiver a utilizar um representante web para aceder à Internet, utilize os seguintes passos para configurar as definições de procuração para o Microsoft Monitoring Agent. Execute estes passos para cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Em caso afirmativo, consulte [Para configurar as definições de procuração para o Microsoft Monitoring Agent utilizando um script](../log-analytics/log-analytics-windows-agent.md).

Para configurar as definições de procuração para o Agente de Monitorização da Microsoft utilizando o Painel de Controlo:

1. Abra o Painel de **Controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.
4. Selecione **Utilize um servidor proxy** e escreva o URL e o número de porta, se for necessário. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Verificar a conectividade do agente

Pode verificar facilmente se os seus agentes estão a comunicar.

1. Num servidor com o agente de monitorização, abra o Painel de **Controlo**.
2. Abra o **Agente de Monitorização**da Microsoft .
3. Clique no separador **Azure Log Analytics.**
4. Na coluna **'Estado',** deve ver se o agente ligou com sucesso aos registos do Monitor Azure.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Abra as portas de firewall nos servidores do agente de monitorização

Para utilizar o protocolo TCP, deve abrir portas de firewall para garantir que os agentes de monitorização possam comunicar.

Pode executar um script PowerShell para criar as teclas de registo que são exigidas pelo Monitor de Desempenho da Rede. Este script também cria as regras do Windows Firewall para permitir que os agentes de monitorização criem ligações TCP entre si. As teclas de registo criadas pelo script especificam se devem registar os registos de depuração e o caminho para o ficheiro de registos. Também define a porta tCP do agente utilizada para a comunicação. Os valores destas teclas são automaticamente definidos pelo script. Não deve mudar manualmente estas chaves.

A porta 8084 é aberta por defeito. Pode utilizar uma porta personalizada fornecendo o parâmetro 'portNumber' para o script. No entanto, se o fizer, deve especificar a mesma porta para todos os servidores em que executa o script.

>[!NOTE]
>O script PowerShell 'EnableRules' configura as regras do Windows Firewall apenas no servidor onde o script é executado. Se tiver uma firewall de rede, deve certificar-se de que permite o tráfego destinado à porta TCP ser utilizado pelo Monitor de Desempenho da Rede.
>
>

Nos servidores do agente, abra uma janela PowerShell com privilégios administrativos. Execute o script [EnableRules](https://aka.ms/npmpowershellscript) PowerShell (que descarregou anteriormente). Não use nenhum parâmetro.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Passo 3: Configurar as regras do grupo de segurança da rede

Para monitorizar os servidores de agentes que se encontram no Azure, é necessário configurar as regras do grupo de segurança de rede (NSG) para permitir o tráfego de TCP numa porta utilizada pela NPM para transações sintéticas. A porta padrão é 8084. Isto permite que um agente de monitorização instalado num VM Azure comunique com um agente de monitorização no local.

Para obter mais informações sobre o NSG, consulte [Network Security Groups](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Certifique-se de que instalou os agentes (tanto o agente de servidores no local como o agente de servidores Azure), e executou o script PowerShell antes de prosseguir com este passo.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Passo 4: Descubra as ligações de observação

1. Navegue para o azulejo de visão geral do Monitor de Desempenho da Rede indo para a página **All Resources** e, em seguida, clique no espaço de trabalho nPM listado em branco.

   ![npm espaço de trabalho](./media/how-to-npm/npm.png)
2. Clique no azulejo de visão geral do Monitor de Desempenho da **Rede** para elevar o painel de instrumentos. O painel contém uma página ExpressRoute, que mostra que o ExpressRoute está num "estado não configurado". Clique na **configuração** da funcionalidade para abrir a página de configuração do Monitor de Desempenho da Rede.

   ![configuração de recurso](./media/how-to-npm/npm2.png)
3. Na página de configuração, navegue para o separador 'ExpressRoute Peerings', localizado no painel lateral esquerdo. Em seguida, clique **em Descobrir Agora**.

   ![descobrir](./media/how-to-npm/13.png)
4. Quando a descoberta estiver concluída, verá uma lista contendo os seguintes itens:
   * Todas as ligações de observação da Microsoft nos circuitos ExpressRoute(s) estão associadas a esta subscrição.
   * Todas as ligações de observação privada que se ligam aos VNets associados a esta subscrição.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Passo 5: Monitores de configuração

Nesta secção, configura os monitores. Siga os passos para o tipo de observação que pretende monitorizar: **peering privado**, ou **Microsoft peering**.

### <a name="private-peering"></a>Peering privado

Para espreitar privados, quando a descoberta estiver concluída, você verá regras para nome de **circuito** único e **nome VNet**. Inicialmente, estas regras são desativadas.

![regras](./media/how-to-npm/14.png)

1. Verifique o Monitor esta caixa de **verificação de pares.**
2. Selecione a caixa de verificação **Activaa a monitorização da saúde para este epeering**.
3. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de saúde digitando valores limiares. Sempre que o valor da circunstância ultrapassa o limiar selecionado para o par de rede/subrede selecionado, gera-se um evento de saúde.
4. Clique no botão ON-PREM **Agentes adicionar agentes** para adicionar os servidores no local a partir dos quais pretende monitorizar a ligação de pares privados. Certifique-se de que escolhe apenas agentes que tenham conectividade com o ponto final do serviço da Microsoft que especificou na secção para o Passo 2. Os agentes no local devem poder chegar ao ponto final utilizando a ligação ExpressRoute.
5. Guarde as definições.
6. Depois de ativar as regras e selecionar os valores e agentes que pretende monitorizar, há uma espera de aproximadamente 30-60 minutos para que os valores comecem a povoar e os azulejos **de Monitorização ExpressRoute** fiquem disponíveis.

### <a name="microsoft-peering"></a>Peering da Microsoft

Para o peering da Microsoft, clique na ligação de peering da Microsoft que pretende monitorizar e configurar as definições.

1. Verifique o Monitor esta caixa de **verificação de pares.** 
2. (Opcional) Pode alterar o ponto final do serviço da Microsoft alvo. Por predefinição, a NPM escolhe um ponto final do serviço microsoft como alvo. O NPM monitoriza a conectividade dos seus servidores no local até este ponto final de destino através do ExpressRoute. 
    * Para alterar este ponto final do alvo, clique no link **(editar)** em **Target:**, e selecione outro ponto final do serviço microsoft da lista de URLs.
      ![editar alvo](./media/how-to-npm/edit_target.png)<br>

    * Pode utilizar um URL ou endereço IP personalizado. Esta opção é particularmente relevante se estiver a usar o peering da Microsoft para estabelecer uma ligação aos serviços Do Azure PaaS, tais como Armazenamento Azure, bases de dados SQL e Websites que são oferecidos em endereços IP públicos. Para isso, clique no link (Use o URL personalizado ou o **endereço IP)** na parte inferior da lista de URL e, em seguida, introduza o ponto final público do seu serviço Azure PaaS que está ligado através do peering expressRoute Microsoft.
    ![URL personalizado](./media/how-to-npm/custom_url.png)<br>

    * Se estiver a utilizar estas definições opcionais, certifique-se de que apenas o ponto final do serviço da Microsoft é selecionado aqui. O ponto final deve ser ligado ao ExpressRoute e acessível pelos agentes no local.
3. Selecione a caixa de verificação **Activaa a monitorização da saúde para este epeering**.
4. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de saúde digitando valores limiares. Sempre que o valor da circunstância ultrapassa o limiar selecionado para o par de rede/subrede selecionado, gera-se um evento de saúde.
5. Clique no botão ON-PREM **Agents Add Agents** para adicionar os servidores no local a partir dos quais pretende monitorizar a ligação de peering da Microsoft. Certifique-se de que escolhe apenas agentes que tenham conectividade com os pontos finais do serviço da Microsoft que especificou na secção para o Passo 2. Os agentes no local devem poder chegar ao ponto final utilizando a ligação ExpressRoute.
6. Guarde as definições.
7. Depois de ativar as regras e selecionar os valores e agentes que pretende monitorizar, há uma espera de aproximadamente 30-60 minutos para que os valores comecem a povoar e os azulejos **de Monitorização ExpressRoute** fiquem disponíveis.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Passo 6: Ver telhas de monitorização

Assim que vir os azulejos de monitorização, os circuitos ExpressRoute e os recursos de ligação estão a ser monitorizados pela NPM. Pode clicar em azulejo sinuoso da Microsoft para aprofundar a saúde das ligações Microsoft Peering.

![monitorização de azulejos](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Página do Monitor de Desempenho da Rede

A página NPM contém uma página para expressRoute que mostra uma visão geral da saúde dos circuitos e espreitamentos ExpressRoute.

![Dashboard](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista de circuitos

Para ver uma lista de todos os circuitos ExpressRoute monitorizados, clique nos **circuitos ExpressRoute.** Pode selecionar um circuito e visualizar o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização da largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizada para traçar as tabelas. Você pode arrastar o rato sobre uma área na tabela para ampliar e ver pontos de dados de grãos finos.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Tendência de Perda, Latência e Entrada

A largura de banda, a latência e as tabelas de perdas são interativas. Pode ampliar qualquer secção destes gráficos, utilizando controlos de rato. Também pode ver os dados de largura de banda, latência e perda para outros intervalos clicando na **Data/Hora**, localizado abaixo do botão Ações na parte superior esquerda.

![tendência](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Lista de pares

Para ver a lista de todas as ligações a redes virtuais através de um epeering privado, clique no azulejo **Private Peerings** no painel de instrumentos. Aqui, você pode selecionar uma conexão de rede virtual e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência.

![lista de circuitos](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Vista de nódosos

Para visualizar a lista de todas as ligações entre os nós no local e os pontos finais do serviço Azure VMs/Microsoft para a ligação de peering ExpressRoute escolhida, clique em **ver links**de nó . Pode ver o estado de saúde de cada ligação, bem como a tendência de perda e latência associada a eles.

![pontos de vista](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologia de circuito

Para ver topologia de circuito, clique no azulejo **topologia.** Isto leva-o à vista de topologia do circuito selecionado ou do olhar. O diagrama de topologia proporciona a latência para cada segmento da rede. Cada lúpulo de camada 3 é representado por um nó do diagrama. Clicar num salto revela mais detalhes sobre o lúpulo.

Pode aumentar o nível de visibilidade para incluir lúpulo no local, movendo a barra de slider abaixo dos **Filtros**. Mover a barra de slider para a esquerda ou direita, aumenta/diminui o número de lúpulo no gráfico de topologia. A latência em cada segmento é visível, o que permite um isolamento mais rápido de segmentos de latência na sua rede.

![filtros](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vista detalhada de Topologia de um circuito

Esta vista mostra ligações VNet.
![topologia detalhada](./media/how-to-npm/17.png)
