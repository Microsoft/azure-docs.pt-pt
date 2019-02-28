---
title: Configurar o Monitor de desempenho de rede dos circuitos do ExpressRoute - Azure | Documentos da Microsoft
description: Configure monitorização (NPM) para circuitos do ExpressRoute do Azure de rede com base na cloud. Isto inclui a monitorização ao longo do peering privado do ExpressRoute e peering da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: fb9ee97726632b7eeccc923596c1f5527a7c95bd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961621"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurar o Monitor de Desempenho de Rede para o ExpressRoute

Este artigo ajuda-o a configurar uma extensão de Monitor de desempenho de rede para monitorizar o ExpressRoute. Monitor de desempenho de rede (NPM) é uma solução que monitora a conectividade entre implementações de nuvem do Azure e localizações no local (das filiais, etc.) de monitorização de rede com base na cloud. NPM faz parte de registos do Azure Monitor. NPM oferece uma extensão para o ExpressRoute permite-lhe monitorizar o desempenho da rede sobre circuitos do ExpressRoute que estão configurados para utilizar o peering privado ou peering da Microsoft. Quando configurar NPM para ExpressRoute, pode detetar problemas de rede para identificar e eliminar. Este serviço também está disponível para a Cloud do Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode:

* Monitorize perdas e latência em várias VNets e definir alertas

* Monitorizar todos os caminhos (incluindo caminhos redundantes) na rede

* Resolver problemas de rede transitórios e de ponto no tempo difíceis de replicar

* Ajudar a determinar um segmento específico na rede que é responsável pela diminuição do desempenho

* Obtenção de débito por rede virtual (se tiver de agentes instalados em cada VNet)

* Ver o estado do sistema de ExpressRoute a partir de um ponto anterior no tempo

## <a name="workflow"></a>Workflow

Agentes de monitorização é instalado em vários servidores, no local e no Azure. Os agentes comunicarem entre si, mas não enviar dados, enviam pacotes de handshake TCP. A comunicação entre os agentes permite ao Azure mapear a topologia de rede e o caminho que pode levar o tráfego.

1. Crie uma área de trabalho do NPM. Este é o mesmo que uma área de trabalho do Log Analytics.
2. Instalar e configurar os agentes de software. (Se só quiser monitorizar através do Peering da Microsoft, não terá de instalar e configurar os agentes de software.): 
    * Instale agentes de monitorização nos servidores no local e as VMs do Azure (para peering privado).
    * Configure as definições nos servidores do agente de monitorização para permitir que os agentes de monitorização para se comunicar. (Abrir portas de firewall, etc.)
3. Configurar regras de grupo (NSG) de segurança de rede para permitir que o agente de monitorização instalado em VMs do Azure para comunicar com locais agentes de monitorização.
4. Configure a monitorização: Detetar automaticamente e gerir as redes que estão visíveis no NPM.

Se já estiver a utilizar o Monitor de desempenho de rede para monitorizar os outros serviços ou objetos e já tiver área de trabalho em uma das regiões suportadas, pode ignorar o passo 1 e 2 e iniciar a configuração com o passo 3.

## <a name="configure"></a>Passo 1: Criar uma área de trabalho

Crie uma área de trabalho na subscrição que tem a ligação de VNets a circuitos do ExpressRoute.

1. Na [portal do Azure](https://portal.azure.com), selecione a subscrição que tem as VNETs em modo de peering para o seu circuito do ExpressRoute. Em seguida, procure a lista de serviços no **Marketplace** para Monitor de desempenho da rede. No retorno, clique para abrir o **Monitor de desempenho de rede** página.

   >[!NOTE]
   >Pode criar uma nova área de trabalho ou utilizar uma área de trabalho existente. Se pretender utilizar uma área de trabalho existente, tem de certificar-se de que a área de trabalho foi migrada para a nova linguagem de consulta. [Obter mais informações...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Na parte inferior da principal **Monitor de desempenho de rede** página, clique em **Create** para abrir **Monitor de desempenho de rede - criar nova solução** página. Clique em **registo de área de trabalho Analytics - Selecione uma área de trabalho** para abrir a página de áreas de trabalho. Clique em **+ criar nova área de trabalho** para abrir a página de área de trabalho.
3. Sobre o **área de trabalho do Log Analytics** página, selecione **criar nova**, em seguida, configure as seguintes definições:

  * Área de trabalho de análise de registo - escreva um nome para a área de trabalho.
  * Subscrição - se tiver várias subscrições, selecione aquele que pretende associar a área de trabalho.
  * Grupo de recursos - crie um grupo de recursos ou utilize um já existente.
  * -Esta localização é utilizada para especificar a localização da conta de armazenamento que é utilizada para os registos de ligação do agente.
  * Escalão de preço - selecione o escalão de preço.
  
    >[!NOTE]
    >O circuito do ExpressRoute pode estar em qualquer lugar do mundo. Não tem de estar na mesma região que a área de trabalho.
    >
  
    ![área de trabalho](./media/how-to-npm/4.png)<br><br>
4. Clique em **OK** para guardar e implementar o modelo de definições. Depois de valida o modelo, clique em **criar** para implementar a área de trabalho.
5. Depois da área de trabalho ter sido implementada, navegue para o **NetworkMonitoring(name)** recurso que criou. Validar as definições, em seguida, clique em **solução requer configuração adicional**.

   ![configuração adicional](./media/how-to-npm/5.png)

## <a name="agents"></a>Passo 2: Instalar e configurar agentes

### <a name="download"></a>2.1: Transfira o ficheiro de configuração do agente

1. Vá para o **definições comuns** separador da **configuração do Monitor de desempenho de rede** página para o seu recurso. Clique no agente que corresponde ao processador de seu servidor do **instalar agentes do Log Analytics** secção e transfira o ficheiro de configuração.
2. Em seguida, copie os **ID da área de trabalho** e **chave primária** ao bloco de notas.
3. Partir do **configurar agentes do Log Analytics para monitorizar através do protocolo TCP** secção, transfira o Script do Powershell. O script do PowerShell ajuda-o a abrir a porta de firewall relevantes para as transações de TCP.

  ![Script do PowerShell](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Instalar um agente de monitorização em cada servidor de monitorização (em cada VNET que pretende monitorizar)

Recomendamos que instale, pelo menos, dois agentes em cada lado da ligação do ExpressRoute para fornecer redundância (por exemplo, no local, VNETs do Azure). O agente tem de ser instalado num servidor do Windows (2008 SP1 ou posterior). A monitorização de circuitos do ExpressRoute com o sistema operacional de Desktop do Windows e o SO Linux não é suportada. Utilize os seguintes passos para instalar agentes:
   
  >[!NOTE]
  >Agentes promovida pelo SCOM (inclui [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) pode não ser capaz de detetar consistentemente a respetiva localização se estes estão alojados no Azure. Recomendamos que não use esses agentes em VNETs do Azure para monitorizar o ExpressRoute.
  >

1. Execute **configuração** para instalar o agente em cada servidor que pretende utilizar para monitorizar o ExpressRoute. O servidor que utiliza para a monitorização pode ser uma VM ou no local e tem de ter acesso à Internet. Tem de instalar pelo menos um agente no local e outra um agente em cada segmento de rede que pretende monitorizar no Azure.
2. Na página **Bem-vindo**, clique em **Seguinte**.
3. Sobre o **termos de licenciamento** página, leia a licença e, em seguida, clique em **concordo**.
4. Sobre o **pasta de destino** página, altere ou mantenha a pasta de instalação predefinida e, em seguida, clique em **próxima**.
5. Sobre o **opções de configuração do agente** página, pode optar por ligar o agente para registos do Azure Monitor ou o Operations Manager. Em alternativa, pode deixar as escolhas em branco se pretender configurar o agente mais tarde. Depois de fazer sua selection(s), clique em **seguinte**.

  * Se optar por ligar à **do Azure Log Analytics**, cole a **ID de área de trabalho** e **chave da área de trabalho** (chave primária) que copiou no bloco de notas na secção anterior. Clique depois em **Seguinte**.

    ![ID e a chave](./media/how-to-npm/8.png)
  * Se tiver escolhido estabelecer ligação ao **Operations Manager**, no **configuração do grupo de gestão** página, escreva o **nome do grupo de gestão**, **servidor de gestão** e o **porta do servidor de gestão**. Clique depois em **Seguinte**.

    ![Operations Manager](./media/how-to-npm/9.png)
  * Sobre o **conta de ação do agente** página, escolha o o **Sistema Local** conta, ou **domínio ou conta de computador Local**. Clique depois em **Seguinte**.

    ![Conta](./media/how-to-npm/10.png)
6. Sobre o **pronto para instalar** página, reveja as suas opções e, em seguida, clique em **instalar**.
7. Na página **Configuração Concluída com Êxito**, clique em **Concluir**.
8. Quando terminar, o Microsoft Monitoring Agent é apresentada no painel de controlo. Pode rever a configuração e certifique-se de que o agente está ligado aos registos do Azure Monitor. Quando estiver ligado, o agente apresenta uma mensagem que diz: **O Microsoft Monitoring Agent foi ligado com êxito para o serviço do Microsoft Operations Management Suite**.

9. Repita este procedimento para cada VNET que tem de ser monitorizados.

### <a name="proxy"></a>2.3: Configurar definições de proxy (opcionais)

Se estiver a utilizar um proxy web para aceder à Internet, utilize os seguintes passos para configurar definições de proxy para o Microsoft Monitoring Agent. Efetue estes passos para cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Se assim for, veja [para configurar definições de proxy para o Microsoft Monitoring Agent utilizando um script](../log-analytics/log-analytics-windows-agent.md).

Para configurar definições de proxy para o Microsoft Monitoring Agent com o painel de controlo:

1. Abra o **painel de controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.
4. Selecione **utilizar um servidor proxy** e escreva o URL e o número de porta, se for necessária. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.

  ![Proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Verificar a conectividade de agente

Pode verificar facilmente se os agentes estão a comunicar.

1. Num servidor com o agente de monitorização, abra a **painel de controlo**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique nas **do Azure Log Analytics** separador.
4. Na **estado** coluna, deverá ver que o agente ligado com êxito para os registos do Azure Monitor.

  ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Abrir as portas de firewall nos servidores de agente de monitorização

Para utilizar o protocolo TCP, é necessário abrir as portas de firewall para se certificar de que os agentes de monitorização podem comunicar.

Pode executar um script do PowerShell para criar as chaves de registo que são necessários para o Monitor de desempenho de rede. Este script cria também as regras de Firewall do Windows para permitir a monitorização de agentes para criar conexões TCP entre si. As chaves de registo criadas pelo script Especifique se pretende registar os registos de depuração e o caminho para o ficheiro de registos. Também define a porta TCP de agente utilizada para comunicação. Os valores para estas chaves são definidos automaticamente pelo script. Não deve alterar manualmente essas chaves.

Por predefinição a porta 8084 está aberta. Pode utilizar uma porta personalizada, fornecendo o parâmetro portNumber ao script. No entanto, se fizer isso, tem de especificar a mesma porta para todos os servidores em que executa o script.

>[!NOTE]
>O script do PowerShell 'EnableRules' configura regras de Firewall do Windows apenas no servidor onde o script é executado. Se tiver uma firewall de rede, certifique-se de que ela permite que o tráfego destinado a porta TCP utilizada pelo Monitor de desempenho de rede.
>
>

Nos servidores de agente, abra uma janela do PowerShell com privilégios administrativos. Executar o [EnableRules](https://aka.ms/npmpowershellscript) script do PowerShell (que transferiu anteriormente). Não utilize quaisquer parâmetros.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Passo 3: Configurar regras de grupo de segurança de rede

Para monitorizar os servidores de agente que estão no Azure, tem de configurar regras de grupo (NSG) de segurança de rede para permitir o tráfego TCP numa porta utilizada pelo NPM para transações sintéticas. A porta predefinida é 8084. Isso permite que um agente de monitorização instalado numa VM do Azure para comunicar com uma local do agente de monitorização.

Para obter mais informações sobre o NSG, consulte [grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Certifique-se de que instalou os agentes (o agente do server no local e o agente do servidor do Azure) e executou o script do PowerShell antes de continuar com este passo.
>

## <a name="setupmonitor"></a>Passo 4: Detetar ligações de peering

1. Navegue para o mosaico de descrição geral do Monitor de desempenho de rede ao aceder a **todos os recursos** página, em seguida, clique na lista de permissões NPM área de trabalho.

  ![área de trabalho do npm](./media/how-to-npm/npm.png)
2. Clique nas **Monitor de desempenho de rede** mosaico de descrição geral para apresentar o dashboard. O dashboard contém uma página do ExpressRoute, que mostra que o ExpressRoute é um "Estado não configurado". Clique em **configuração de funcionalidade** para abrir a página de configuração de Monitor de desempenho de rede.

  ![configuração de funcionalidade](./media/how-to-npm/npm2.png)
3. Na página de configuração, navegue até a guia "Peerings do ExpressRoute", localizada no painel do lado esquerdo. Em seguida, clique em **detetar agora**.

  ![detetar](./media/how-to-npm/13.png)
4. Quando tiver concluído a deteção, verá uma lista que contém os seguintes itens:
  * Todas as ligações de peering da Microsoft em circuitos de ExpressRoute que estão associados esta subscrição.
  * Todas as ligações de peering privadas que ligar às VNets associada a esta subscrição.
            
## <a name="configmonitor"></a>Passo 5: Configurar monitores

Nesta secção, vai configurar os monitores. Siga os passos para o tipo de peering que pretende monitorizar: **peering privado**, ou **peering da Microsoft**.

### <a name="private-peering"></a>Peering privado

Para peering privado, quando a deteção for concluído, verá serão regras para exclusivo **nome do circuito** e **nome da VNet**. Inicialmente, estas regras estão desativadas.

![regras](./media/how-to-npm/14.png)

1. Verifique os **monitorizar este peering** caixa de verificação.
2. Selecione a caixa de verificação **ativar a monitorização de estado de funcionamento para este peering**.
3. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de estado de funcionamento ao escrever os valores de limiar. Sempre que o valor da condição mais do seu limiar selecionado para o par de rede/sub-rede selecionada, é gerado um evento de estado de funcionamento.
4. Clique nos agentes do ON-PREM **adicionar agentes** botão para adicionar os servidores no local a partir do qual pretende monitorizar a ligação de peering privada. Certifique-se de que escolha apenas os agentes que tenham conectividade com o ponto final do serviço Microsoft que especificou na secção para o passo 2. Os agentes no local tem de ser capazes de alcançar o ponto de extremidade usando a ligação do ExpressRoute.
5. Guarde as definições.
6. Depois de ativar as regras e selecionar os valores e os agentes que pretende monitorizar, há uma espera de aproximadamente 30 a 60 minutos para os valores começar a preencher e o **a monitorização de ExpressRoute** mosaicos para se tornarem disponíveis.

### <a name="microsoft-peering"></a>Peering da Microsoft

Para peering da Microsoft, clique em do Microsoft peering ligação (ões) que pretende monitorizar e configurar as definições.

1. Verifique os **monitorizar este peering** caixa de verificação. 
2. (Opcional) Pode alterar o ponto de extremidade de serviço da Microsoft de destino. Por predefinição, o NPM escolhe um ponto de extremidade de serviço do Microsoft como o destino. NPM monitora a conectividade dos seus servidores no local para este ponto de extremidade de destino através do ExpressRoute. 
    * Para alterar este ponto de extremidade de destino, clique a **(editar)** ligação sob **destino:** e selecione outro ponto final do destino de serviço Microsoft na lista de URLs.
      ![Editar o destino](./media/how-to-npm/edit_target.png)<br>

    * Pode utilizar um URL ou endereço IP personalizado. Esta opção é especialmente relevante se estiver a utilizar o peering para estabelecer uma ligação a serviços de PaaS do Azure, como o armazenamento do Azure, bases de dados SQL e sites que são oferecidos em endereços IP públicos da Microsoft. Para tal, clique no link **(Utilize URL personalizado ou o endereço IP em vez disso)** na parte inferior da lista de URL, em seguida, introduza o ponto final público do seu serviço de PaaS do Azure que está ligado através do peering da Microsoft do ExpressRoute.
    ![URL personalizado](./media/how-to-npm/custom_url.png)<br>

    * Se estiver a utilizar estas definições opcionais, certifique-se de que apenas o Microsoft ponto final de serviço está selecionado aqui. O ponto final tem de ser ligada ao ExpressRoute e pode ser acedida pelos agentes no local.
3. Selecione a caixa de verificação **ativar a monitorização de estado de funcionamento para este peering**.
4. Escolha as condições de monitorização. Pode definir limiares personalizados para gerar eventos de estado de funcionamento ao escrever os valores de limiar. Sempre que o valor da condição mais do seu limiar selecionado para o par de rede/sub-rede selecionada, é gerado um evento de estado de funcionamento.
5. Clique nos agentes do ON-PREM **adicionar agentes** botão para adicionar os servidores no local a partir do qual pretende monitorizar a ligação de peering da Microsoft. Certifique-se de que escolha apenas os agentes que tenham conectividade com as Microsoft pontos finais de serviço que especificou na secção para o passo 2. Os agentes no local tem de ser capazes de alcançar o ponto de extremidade usando a ligação do ExpressRoute.
6. Guarde as definições.
7. Depois de ativar as regras e selecionar os valores e os agentes que pretende monitorizar, há uma espera de aproximadamente 30 a 60 minutos para os valores começar a preencher e o **a monitorização de ExpressRoute** mosaicos para se tornarem disponíveis.

## <a name="explore"></a>Passo 6: Vista de monitorização de mosaicos

Quando vir os mosaicos de monitorização, os circuitos do ExpressRoute e os recursos de ligação estão a ser monitorizados pelo NPM. Pode clicar num mosaico do Peering da Microsoft para fazer uma busca detalhada no estado de funcionamento de ligações de Peering da Microsoft.

![monitorização de mosaicos](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Página de Monitor de desempenho de rede

A página NPM contém uma página para o ExpressRoute, que mostra uma descrição geral do Estado de funcionamento dos circuitos do ExpressRoute e de peerings.

![Dashboard](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Lista de circuitos

Para ver uma lista de todos os monitorizados circuitos do ExpressRoute, clique nas **circuitos do ExpressRoute** mosaico. Pode selecionar um circuito e ver o estado de funcionamento, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizado para desenhar gráficos. É possível arrastar o mouse sobre uma área no gráfico para ampliar e ver os pontos de dados detalhados.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Tendência de perda, latência e débito

Os gráficos de largura de banda, latência e perda são interativos. Pode ampliar a qualquer seção de nestes gráficos, usando controles de mouse. Também pode ver as largura de banda, latência e perda de dados para outros intervalos clicando **data/hora**, localizado abaixo do botão de ações no canto superior esquerdo.

![tendência](./media/how-to-npm/16.png)

### <a name="peerings"></a>Lista de Peerings

Para ver a lista de todas as ligações a redes virtuais através do peering privado, clique nas **Peerings privados** mosaico no dashboard. Aqui, pode selecionar um virtual ligação de rede e para ver o estado de funcionamento, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência.

![lista de circuito](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Vista de nós

Para ver a lista de todas as ligações entre os nós no local e pontos finais de serviço de Azure VMs/Microsoft para a ligação de peering do ExpressRoute escolhido, clique em **ver ligações de nó**. Pode ver o estado de funcionamento de cada ligação, bem como a tendência de perda e latência associados aos mesmos.

![Vista de nós](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Topologia de circuito

Para ver a topologia de circuito, clique a **topologia** mosaico. Isto leva-o para a vista de topologia selecionado de circuito ou o peering. O diagrama de topologia fornece a latência para cada segmento na rede. Cada salto de camada 3 é representado por um nó do diagrama. Clicar num salto revela mais detalhes sobre o salto.

Pode aumentar o nível de visibilidade para incluir saltos no local ao mover a barra de controlo de deslize abaixo **filtros**. Mover a barra de controlo de deslize para a esquerda ou direita, aumentos/diminui o número de saltos no gráfico de topologia. A latência em cada segmento é visível, que permitem o isolamento mais rápido de segmentos de alta latência na sua rede.

![filtros](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vista de topologia detalhada de um circuito

Esta vista mostra as ligações de VNet.
![topologia detalhados](./media/how-to-npm/17.png)
