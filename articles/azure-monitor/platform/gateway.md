---
title: Ligar computadores ao utilizar o gateway do Log Analytics | Documentos da Microsoft
description: Ligue os dispositivos e computadores monitorizados do Operations Manager com o gateway do Log Analytics para enviar dados para a automatização do Azure e o serviço do Log Analytics, quando não têm acesso à internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 41ffd7229383f1006bb846f975aeccf83256032a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807733"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway"></a>Ligar computadores sem acesso à internet com o gateway do Log Analytics

>[!NOTE]
>Como o Microsoft Operations Management Suite (OMS) faz a transição para o Microsoft Azure Monitor, a terminologia está mudando. Este artigo se refira ao Gateway de OMS que o gateway do Azure Log Analytics. 
>

Este artigo descreve como configurar a comunicação com a automatização do Azure e o Log Analytics ao utilizar o gateway do Log Analytics, quando os computadores que estejam diretamente ligados ou que são monitorizados pelo Operations Manager têm sem acesso à internet. 

O gateway do Log Analytics é um proxy de encaminhamento de HTTP que suporte a HTTP o túnel com o comando de ligação HTTP. Este gateway pode recolher dados e enviá-lo para a automatização do Azure e o Log Analytics em nome dos computadores que não estão ligados à internet.  

O gateway do Log Analytics suporta:

* Relatórios, até mesmo quatro do Log Analytics, agentes da área de trabalho que estão por trás dele e que estão configurados com o Azure Automation Hybrid Runbook Workers.  
* Computadores do Windows em que o Microsoft Monitoring Agent está diretamente ligado a uma área de trabalho do Log Analytics.
* Computadores com Linux no qual um agente do Log Analytics para Linux está diretamente ligado a uma área de trabalho do Log Analytics.  
* System Center Operations Manager 2012 SP1 UR7, Operations Manager 2012 R2 com o UR3 ou um grupo de gestão no Operations Manager 2016 ou posterior que está integrada com o Log Analytics.  

Algumas políticas de segurança de TI não permitem a ligação à internet para computadores de rede. Estes computadores solução desligadas poderiam ser o ponto de dispositivos de venda (POS) ou servidores que suportam serviços de TI, por exemplo. Para ligar estes dispositivos a automatização do Azure ou do Log Analytics para que possa gerir e monitorizar, configurá-las para se comunicar diretamente com o gateway do Log Analytics. O gateway do Log Analytics pode receber informações de configuração e reencaminhar dados em seu nome. Se os computadores são configurados com o agente do Log Analytics para ligar diretamente a uma área de trabalho do Log Analytics, os computadores em vez disso, se comunicar com o gateway do Log Analytics.  

O gateway do Log Analytics transfere dados dos agentes para o serviço diretamente. Ele não analisa a quaisquer dados em trânsito.

Quando um grupo de gestão do Operations Manager está integrado com o Log Analytics, os servidores de gestão podem ser configurados para ligar ao gateway do Log Analytics para receber informações de configuração e enviar os dados recolhidos, consoante a solução que tiver ativado .  Agentes do Operations Manager enviam alguns dados para o servidor de gestão. Por exemplo, agentes poderão enviar alertas do Operations Manager, os dados de avaliação de configuração, dados de espaço de instância e dados de capacidade. Outros dados de grande volume, como registos de serviços de informação Internet (IIS), dados de desempenho e eventos de segurança, são enviados diretamente para o gateway do Log Analytics. 

Se um ou mais servidores de Gateway do Operations Manager são implementados para monitorizar os sistemas não fidedignos na rede de perímetro ou uma rede isolada, esses servidores não consegue comunicar com um gateway do Log Analytics.  Servidores de Gateway do Gestor de operações podem comunicar-se apenas a um servidor de gestão.  Quando um grupo de gestão do Operations Manager estiver configurado para comunicar com o gateway do Log Analytics, as informações de configuração de proxy é automaticamente distribuídas a todos os computadores geridos por agente que está configurado para recolher dados para o Log Analytics, até mesmo Se a definição está vazia.    

Para fornecer elevada disponibilidade para diretamente ligado ou grupos de gestão de operações que comunicam com o Log Analytics através do gateway, utilize a rede balanceamento de carga (NLB) para redirecionar e distribuir o tráfego entre múltiplos servidores de gateway. Dessa forma, se um servidor de gateway ficar inativo, o tráfego será redirecionado para outro nó disponível.  

O computador que executa o gateway do Log Analytics requer que o agente do Windows do Log Analytics identificar os pontos de extremidade de serviço que precisa se comunicar com o gateway. O agente também tem de direcionar o gateway para comunicar a áreas de trabalho mesmo que os agentes ou grupo de gestão do Operations Manager por trás do gateway estão configurados com. Esta configuração permite que o gateway e o agente para comunicar com a sua área de trabalho atribuída.

Um gateway pode ser multihomed para até quatro áreas de trabalho. Este é o número total de áreas de trabalho que suporta um agente do Windows.  

Cada agente tem de ter conectividade de rede para o gateway para que os agentes podem transferir automaticamente os dados de e para o gateway. Evite instalar o gateway num controlador de domínio.

O diagrama seguinte mostra os dados transmitidos de agentes diretos, através do gateway, para a automatização do Azure e o Log Analytics. A configuração de proxy do agente tem de corresponder à porta que o gateway do Log Analytics é configurado com.  

![Diagrama de comunicação do agente direto com os serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama seguinte mostra o fluxo de dados de um grupo de gestão do Operations Manager ao Log Analytics.   

![Diagrama de comunicação do Operations Manager com o Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurar o seu sistema

Computadores designados para executar o gateway do Log Analytics tem de ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos um processador de núcleo de 4 e 8 GB de memória 
* R [agente do Log Analytics para Windows](agent-windows.md) que está configurado para reportar a mesma área de trabalho que os agentes que comunicam através do gateway

### <a name="language-availability"></a>Disponibilidade de idioma

O gateway do Log Analytics está disponível nestes idiomas:

- Chinês (Simplificado)
- Chinês (Tradicional)
- Checo
- Neerlandês
- Português
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polaco
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de encriptação suportados
O gateway do Log Analytics suporta apenas Transport Layer Security (TLS) 1.0, 1.1 e 1.2.  Ele não suporta o Secure Sockets Layer (SSL).  Para garantir a segurança dos dados em trânsito para o Log Analytics, configurar o gateway a utilizar, pelo menos, TLS 1.2. Versões mais antigas do TLS ou SSL são vulneráveis. Embora eles permitem que atualmente compatibilidade com versões anteriores, evitarão usá-los.  

Para obter mais informações, consulte [enviar dados de forma segura através de TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número suportado de ligações de agente
A tabela seguinte mostra aproximadamente o número de agentes pode comunicar com um servidor de gateway. O suporte se baseia em agentes que carreguem cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é cerca de 2,7 GB por dia.

|Gateway |Agentes suportados (aproximado)|  
|--------|----------------------------------|  
|CPU: Intel Xeon processador E5 2660 v3 \@ núcleos de 2,6 GHz 2<br> Memória: 4GB<br> Largura de banda de rede: 1 Gbps| 600|  
|CPU: Intel Xeon processador E5 2660 v3 \@ 2,6 GHz 4 núcleos<br> Memória: 8 GB<br> Largura de banda de rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Transfira o gateway do Log Analytics

Obter a versão mais recente do ficheiro de configuração do gateway do Log Analytics a partir de qualquer uma [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) ou o portal do Azure.

Para obter o gateway do Log Analytics no portal do Azure, siga estes passos:

1. Navegue na lista de serviços e, em seguida, selecione **do Log Analytics**. 
1. Selecione uma área de trabalho.
1. No painel da área de trabalho, sob **gerais**, selecione **início rápido**. 
1. Sob **escolha uma origem de dados para ligar à área de trabalho**, selecione **computadores**.
1. Na **agente direto** painel, selecione **gateway de baixar o Log Analytics**.
 
   ![Captura de ecrã dos passos para transferir o gateway do Log Analytics](./media/gateway/download-gateway.png)

ou 

1. No painel da área de trabalho, sob **configurações**, selecione **definições avançadas**.
1. Aceda a **origens ligadas** > **servidores de Windows** e selecione **gateway de baixar o Log Analytics**.

## <a name="install-the-log-analytics-gateway"></a>Instalar o gateway do Log Analytics

Para instalar um gateway, siga estes passos.  (Se tiver instalado uma versão anterior, chamada reencaminhador do Log Analytics, ela será atualizada para esta versão.)

1. Na pasta de destino, faça duplo clique em **gateway.msi do Log Analytics**.
1. Na página **Bem-vindo**, selecione **Seguinte**.

   ![Captura de ecrã da página boas-vindas do Assistente de configuração do Gateway](./media/gateway/gateway-wizard01.png)

1. Sobre o **contrato de licença** página, selecione **aceito os termos no contrato de licença** para aceitar os termos de licença de Software da Microsoft e, em seguida, selecione **seguinte**.
1. Sobre o **endereço de proxy e porta** página:

   a. Introduza o número de porta TCP para ser utilizada para o gateway. A configuração utiliza este número de porta para configurar uma regra de entrada em Firewall do Windows.  O valor predefinido é 8080.
      O intervalo válido de número de porta é 1 e 65535. Se a entrada não se encaixa este intervalo, é apresentada uma mensagem de erro.

   b. Se o servidor onde está instalado o gateway tiver de comunicar através de um proxy, introduza o endereço de proxy em que o gateway tem de se ligar. Por exemplo, introduza `http://myorgname.corp.contoso.com:80`.  Se deixar este campo em branco, o gateway irá tentar ligar diretamente à internet.  Se o servidor proxy requer autenticação, introduza um nome de utilizador e palavra-passe.

   c. Selecione **Seguinte**.

   ![Captura de ecrã da configuração para o proxy de gateway](./media/gateway/gateway-wizard02.png)

1. Se não tiver o Microsoft Update ativado, será exibida a página do Microsoft Update e pode optar por ativá-la. Faça uma seleção e, em seguida, selecione **seguinte**. Caso contrário, avance para o passo seguinte.
1. Sobre o **pasta de destino** página, deixe a pasta predefinida C:\Program Files\OMS Gateway ou introduza a localização onde pretende instalar o gateway. Em seguida, selecione **Seguinte**.
1. Sobre o **pronto para instalar o** página, selecione **instalar**. Se o controle de conta de utilizador solicitar permissão para instalar, selecione **Sim**.
1. Após a conclusão da configuração, selecione **concluir**. Para verificar que o serviço está em execução, abra o snap-in de Services. msc e certifique-se de que **Gateway do OMS** é apresentado na lista de serviços e de que o respetivo estado é **em execução**.

   ![Captura de ecrã de serviços locais, que mostra que o Gateway do OMS está em execução](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede 
Configure o gateway de elevada disponibilidade utilizando a rede balanceamento de carga (NLB). Utilize o Microsoft Azure Load Balancer ou balanceadores de carga baseada em hardware.  O Balanceador de carga gere tráfego ao redirecionar as ligações pedidas dos agentes do Log Analytics ou servidores de gestão do Operations Manager em todos os seus nós. Se um servidor de gateway ficar inativo, o tráfego será redirecionado para outros nós.

>[!NOTE]
>Para saber como projetar e implementar um cluster de NLB do Windows Server 2016, veja [balanceamento de carga na rede](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing). 
>

Siga estes passos para configurar um cluster do Balanceador de carga da Microsoft:  

1. Utilize uma conta de administrador para iniciar sessão para o Windows Server que seja membro do cluster de Balanceador de carga.
1. No Gestor de servidores, abra **Gestor de balanceamento de carga na rede**, selecione **ferramentas**e, em seguida, selecione **Gestor de balanceamento de carga na rede**.
1. Para ligar um servidor de gateway do Log Analytics com o Microsoft Monitoring Agent instalado, endereço IP do cluster com o botão direito e, em seguida, clique em **Adicionar anfitrião ao Cluster**.

   ![Captura de ecrã da carga balanceamento Gestor de rede, com a adicionar ao Cluster de anfitriões selecionado](./media/gateway/nlb02.png)

1. Introduza o endereço IP do servidor de gateway que pretende ligar.

   ![Captura de ecrã da carga balanceamento Gestor de rede, que mostra a página Adicionar anfitrião ao Cluster: Ligar](./media/gateway/nlb03.png)
    
## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurar o agente Log Analytics e o grupo de gestão do Operations Manager
Nesta seção, verá como configurar os agentes, um grupo de gestão do Operations Manager ou do Azure Automation Hybrid Runbook Workers do Log Analytics ligadas diretamente com o gateway do Log Analytics para comunicar com a automatização do Azure ou o Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurar um agente de Log Analytics autónomo
Ao configurar o agente Log Analytics, substitua o valor de servidor proxy com o endereço IP do servidor de gateway do Log Analytics e o respetivo número de porta. Se implementar vários servidores de gateway por trás de um NLB, a configuração de proxy de agente do Log Analytics é o endereço IP virtual do NLB.  

>[!NOTE]
>Para instalar o agente Log Analytics no gateway e computadores do Windows que se ligam diretamente ao Log Analytics, consulte [computadores Windows ligar ao serviço Log Analytics no Azure](agent-windows.md). Para ligar computadores Linux, veja [configurar um agente do Log Analytics para computadores Linux num ambiente híbrido](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Depois de instalar o agente no servidor de gateway, tem de configurá-lo para reportar para a área de trabalho ou agentes da área de trabalho que se comunicam com o gateway. Se o agente do Windows do Log Analytics não está instalado no gateway, o evento 300 é escrito no registo de eventos do Gateway do OMS, que indica que o agente tem de ser instalado. Se o agente estiver instalado, mas não é configurado para reportar a mesma área de trabalho que os agentes que comunicam através do mesmo, o evento 105 é escrito no mesmo registo, que indica que o agente no gateway tem de ser configurados para reportar a mesma área de trabalho que os agentes que co mmunicate com o gateway.

Depois de concluir a configuração, reinicie o serviço de Gateway do OMS para aplicar as alterações. Caso contrário, o gateway irá rejeitar agentes que tentam comunicar com o Log Analytics e reportarão de 105 de evento no registo de eventos de Gateway do OMS. Isso também acontece quando adiciona ou remover uma área de trabalho da configuração do agente no servidor de gateway.   

Para informações relacionadas com o Runbook Worker do Automation híbrida, veja [automatizar recursos no seu datacenter ou na cloud utilizando o Runbook Worker híbrido](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurar o Operations Manager, onde todos os agentes de utilizam o mesmo servidor de proxy
A configuração de proxy do Operations Manager é automaticamente aplicada a todos os agentes que reportam ao Operations Manager, mesmo que a definição está vazia.  

Para utilizar o Gateway de OMS para suportar o Operations Manager, tem de ter:

* Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalado no servidor de Gateway de OMS e configurado com as áreas de trabalho do Log Analytics mesmo que o seu grupo de gestão está configurado para reportar a.
* Desde Conectividade Internet. Em alternativa, o Gateway de OMS devem estar conectado para um servidor proxy que está ligado à internet.

> [!NOTE]
> Não se especificar nenhum valor para o gateway, valores em branco são enviados por push para todos os agentes.
>

Se o grupo de gestão do Operations Manager está a registar com uma área de trabalho do Log Analytics pela primeira vez, não verá a opção de especificar a configuração de proxy para o grupo de gestão na consola de operações.  Esta opção só está disponível se o grupo de gestão foi registado com o serviço.  

Para configurar a integração, atualize a configuração de proxy do sistema, utilizando Netsh no sistema em que estiver a executar a consola de operações e em todos os servidores de gestão no grupo de gestão. Siga estes passos.

1. Abra uma linha de comandos elevada:

   a. Selecione **começar** e introduza **cmd**.  

   b. Com o botão direito **linha de comandos** e selecione **executar como administrador**.  

1. Introduza o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o Log Analytics, remova a alteração ao executar `netsh winhttp reset proxy`. Em seguida, na consola de operações, utilize o **configurar o servidor de proxy** opção para especificar o servidor de gateway do Log Analytics. 

1. Na consola do Operations Manager, sob **Operations Management Suite**, selecione **ligação**e, em seguida, selecione **configurar servidor Proxy**.

   ![Captura de ecrã do Operations Manager, que mostra a seleção de configurar o servidor de Proxy](./media/gateway/scom01.png)

1. Selecione **utilizar um servidor proxy para aceder ao Operations Management Suite** e, em seguida, introduza o endereço IP do servidor de gateway do Log Analytics ou o endereço IP virtual do NLB. Tenha cuidado para começar com o prefixo `http://`.

   ![Captura de ecrã do Operations Manager, que mostra o proxy de endereço do servidor](./media/gateway/scom02.png)

1. Selecione **Concluir**. Grupo de gestão do Operations Manager está agora configurado para comunicar através do servidor de gateway para o serviço Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurar o Operations Manager, onde os agentes específicos de utilizam um servidor proxy
Para ambientes de grandes ou complexos, pode desejar apenas servidores específicos (ou grupos) para utilizar o servidor de gateway do Log Analytics.  Para estes servidores, não é possível atualizar o agente do Operations Manager diretamente porque este valor é substituído pelo valor global para o grupo de gestão.  Em vez disso, substitua a regra utilizada para enviar estes valores.  

> [!NOTE] 
> Use essa técnica de configuração se pretende permitir para vários servidores de gateway do Log Analytics no seu ambiente.  Por exemplo, pode exigir servidores de gateway do Log Analytics específicos seja especificado de forma regional.
>

Para configurar servidores específicos ou grupos para utilizar o servidor de gateway do Log Analytics: 

1. Abra a consola do Operations Manager e selecione o **Authoring** área de trabalho.  
1. Na área de trabalho de criação, selecione **regras**. 
1. Na barra de ferramentas do Operations Manager, selecione o **âmbito** botão. Se este botão não estiver disponível, certifique-se de que tiver selecionado um objeto, não uma pasta, o **monitorização** painel. O **objetos de pacote de gestão do âmbito** caixa de diálogo apresenta uma lista de classes comuns de destinados, grupos ou objetos. 
1. Na **procure** , insira **serviço de estado de funcionamento** e selecione-o na lista. Selecione **OK**.  
1. Procure **regra de definição de Proxy do Advisor**. 
1. Na barra de ferramentas do Operations Manager, selecione **substitui** e, em seguida, aponte para **substituir o Rule\For um objeto específico da classe: Serviço de estado de funcionamento** e selecione um objeto da lista.  Ou crie um grupo personalizado que contém o objeto de serviço de estado de funcionamento dos servidores que pretende aplicar esta substituição para. Em seguida, aplica a substituição ao seu grupo personalizado.
1. Na **propriedades da substituição** caixa de diálogo caixa, adicione uma marca de verificação no **substituir** coluna seguinte para o **WebProxyAddress** parâmetro.  Na **valor de substituição** campo, introduza o URL do servidor de gateway do Log Analytics. Tenha cuidado para começar com o prefixo `http://`.  

    >[!NOTE]
    > Não precisa de ativar a regra. Ele já conseguiu automaticamente com uma substituição no pacote de gestão Microsoft System Center Advisor Secure referência substituir voltado o Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Selecione um pacote de gestão do **selecionar o pacote de gestão de destino** listar ou criar um novo pacote de gestão não selado, selecionando **New**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurar para trabalhadores de Runbook híbrida de automatização
Se tiver os Runbook Workers híbridos de automatização no seu ambiente, siga estes passos para obter soluções manuais, temporárias configurar o Gateway de OMS para dar suporte as funções de trabalho.

Para seguir os passos nesta secção, precisa saber onde reside a conta de automatização da região do Azure. Para localizar essa localização:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o serviço de automatização do Azure.
1. Selecione a conta de automatização do Azure adequada.
1. Ver a sua região sob **localização**.

   ![Captura de ecrã da localização de conta de automatização no portal do Azure](./media/gateway/location.png)

Utilize as tabelas seguintes para identificar o URL para cada localização.

**URLs do serviço de dados de tempo de execução de tarefa**

| **Localização** | **URL** |
| --- | --- |
| EUA Centro-Norte |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| EUA Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net |
| EUA Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canadá central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |

**URLs do serviço de agente**

| **Localização** | **URL** |
| --- | --- |
| EUA Centro-Norte |ncus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-agentservice-prod-1.azure-automation.net |
| EUA Centro-Sul |scus-agentservice-prod-1.azure-automation.net |
| EUA Leste 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canadá central |cc-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-agentservice-prod-1.azure-automation.net |
| Japão |jpe-agentservice-prod-1.azure-automation.net |
| Austrália |ase-agentservice-prod-1.azure-automation.net |

Se o computador é registrado automaticamente como uma função de trabalho de Runbook híbrida, utilize a solução de gestão de atualizações para gerir o patch. Siga estes passos.

1. Adicione os URLs do serviço de dados de tempo de execução de tarefa para a lista de anfitriões permitido no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço de gateway do Log Analytics, utilizando o cmdlet PowerShell seguinte: `Restart-Service OMSGatewayService`

Se o computador estiver associado a automatização do Azure utilizando o cmdlet de registo do Runbook Worker híbrido, siga estes passos:

1. Adicione o URL de registo do serviço de agente para a lista de anfitriões permitido no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione os URLs do serviço de dados de tempo de execução de tarefa para a lista de anfitriões permitido no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço de gateway do Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets do PowerShell útil
Pode utilizar os cmdlets para concluir as tarefas para atualizar as definições de configuração do gateway do Log Analytics. Antes de utilizar cmdlets, certifique-se de que para:

1. Instale o gateway do Log Analytics (Microsoft Windows Installer).
1. Abra uma janela de consola do PowerShell.
1. Importe o módulo ao escrever este comando: `Import-Module OMSGateway`
1. Se não ocorrer nenhum erro no passo anterior, o módulo foi importado com êxito e os cmdlets podem ser utilizados. Enter `Get-Module OMSGateway`
1. Depois de utilizar os cmdlets para fazer alterações, reinicie o serviço de Gateway do OMS.

Um erro no passo 3 significa que o módulo não foi importado. O erro poderá ocorrer quando PowerShell não é possível localizar o módulo. Pode encontrar o módulo no caminho de instalação de Gateway do OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatório) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de reencaminhamento (a montante) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de utilizador<br> Palavra-passe |Define o endereço (e a credencial) do proxy de reencaminhamento (a montante) |1. Defina um proxy de reencaminhamento e credenciais:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Defina um proxy de reencaminhamento que não necessita de autenticação: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desmarque a definição de proxy de reencaminhamento:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o anfitrião atualmente permitido (permitia apenas que o anfitrião permitido configurado localmente, transferido não automaticamente anfitriões) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Anfitrião (obrigatório) |Adiciona o anfitrião à lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Anfitrião (obrigatório) |Remove o anfitrião da lista de permitidos |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o certificado de cliente sujeitos a lista de permitidos |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o requerente do certificado de cliente da lista de permitidos |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém o cliente atualmente permitido assuntos de certificado (permitia apenas que os assuntos permitidos configurados localmente, transferidos não automaticamente assuntos) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Resolução de problemas
Para recolher eventos registados pelo gateway, deve ter instalado o agente de Log Analytics.

![Captura de ecrã da lista de Visualizador de eventos no registo de gateway do Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Evento de gateway do log Analytics IDs e descrições

A tabela seguinte mostra os IDs de evento e descrições para eventos de registo do gateway do Log Analytics.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicação com nenhum ID específico. |
| 401 |Configuração de errado. Por exemplo, listenPort = "text", em vez de um número inteiro. |
| 402 |Exceção na análise de mensagens de handshake TLS. |
| 403 |Erro de sistema de rede. Por exemplo, não é possível ligar ao servidor de destino. |
| 100 |Informações gerais. |
| 101 |Serviço foi iniciado. |
| 102 |Serviço foi parado. |
| 103 |Um comando de ligação HTTP foi recebido do cliente. |
| 104 |Não um ligação HTTP comando. |
| 105 |Servidor de destino não está na lista de permitidos ou porta de destino não é seguro (443). <br> <br> Certifique-se de que o agente MMA no seu servidor de Gateway do OMS e os agentes que comunicam com o Gateway do OMS estão ligados a mesma área de trabalho do Log Analytics. |
| 105 |Erro TcpConnection – certificado de cliente inválido: CN = Gateway. <br><br> Certifique-se de que está a utilizar o Gateway de OMS versão 1.0.395.0 ou superior. Certifique-se também de que o agente MMA no seu servidor de Gateway do OMS e os agentes a comunicar com o Gateway do OMS estão ligados a mesma área de trabalho do Log Analytics. |
| 106 |Versão do protocolo TLS/SSL não suportada.<br><br> O gateway do Log Analytics suporta apenas o TLS 1.0, TLS 1.1 e 1.2. Não suporta SSL.|
| 107 |A sessão TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho a recolher

A tabela seguinte mostra os contadores de desempenho disponíveis para o gateway do Log Analytics. Utilize o Monitor de desempenho para adicionar os contadores.

| **Nome** | **Descrição** |
| --- | --- |
| Ligação de cliente de Gateway/ativo do log Analytics |Número de ligações de rede (TCP) de cliente do Active Directory |
| Contagem de Gateway/erro de análise de registo |Número de erros |
| Cliente de Gateway/ligados do log Analytics |Número de clientes ligados |
| Contagem de Gateway/rejeição do log Analytics |Número de rejeições devido a qualquer erro de validação de TLS |

![Interface de gateway de captura de ecrã do Log Analytics, que mostra os contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência
Quando tem sessão iniciada portal do Azure, pode obter ajuda com o gateway do Log Analytics ou qualquer outro serviço do Azure ou recursos.
Para obter ajuda, selecione o ícone de ponto de interrogação no canto superior direito do portal e selecione **novo pedido de suporte**. Em seguida, conclua o novo formulário de pedido de suporte.

![Captura de ecrã de um novo pedido de suporte](./media/gateway/support.png)

## <a name="next-steps"></a>Passos Seguintes
[Adicionar origens de dados](../../azure-monitor/platform/agent-data-sources.md) para recolher dados a partir de origens ligadas e armazenar os dados em sua área de trabalho do Log Analytics.
