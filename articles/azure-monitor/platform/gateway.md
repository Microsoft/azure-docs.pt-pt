---
title: Conecte os computadores utilizando o gateway Do Log Analytics ! Microsoft Docs
description: Ligue os computadores monitorizados pelo Gestor de Operações utilizando o gateway Do Log Analytics para enviar dados para o serviço Azure Automation and Log Analytics quando estes não têm acesso à Internet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: dc4d1b852b0a498de0834731b2b1cd1225b9748b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008859"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Conecte computadores sem acesso à Internet utilizando o gateway Log Analytics no Azure Monitor

>[!NOTE]
>À medida que o Microsoft Operations Management Suite (OMS) passa para o Microsoft Azure Monitor, a terminologia está a mudar. Este artigo refere-se ao OMS Gateway como o gateway Azure Log Analytics. 
>

Este artigo descreve como configurar a comunicação com a Azure Automation e o Azure Monitor utilizando o gateway Log Analytics quando os computadores que estão ligados diretamente ou que são monitorizados pelo Gestor de Operações não têm acesso à Internet. 

O gateway Log Analytics é um proxy para a frente HTTP que suporta o túnel HTTP utilizando o comando HTTP CONNECT. Este gateway envia dados para a Azure Automation e um espaço de trabalho Log Analytics em Azure Monitor em nome dos computadores que não conseguem ligar-se diretamente à internet. 

O gateway Log Analytics suporta:

* Reportando até os mesmos espaços de trabalho Log Analytics configurados em cada agente por trás dele e que são configurados com Azure Automation Hybrid Runbook Workers.  
* Computadores Windows nos quais o Agente de Monitorização da Microsoft está diretamente ligado a um espaço de trabalho log analytics no Azure Monitor.
* Computadores Linux nos quais um agente de Log Analytics para Linux está diretamente ligado a um espaço de trabalho log analytics no Azure Monitor.  
* System Center Operations Manager 2012 SP1 com UR7, Gestor de Operações 2012 R2 com UR3, ou um grupo de gestão em Gestor de Operações 2016 ou posteriormente que é integrado com Log Analytics.  

Algumas políticas de segurança de TI não permitem a ligação à Internet para computadores de rede. Estes computadores não conectados podem ser dispositivos de ponto de venda (POS) ou servidores que suportam serviços de TI, por exemplo. Para ligar estes dispositivos à Azure Automation ou a um espaço de trabalho Log Analytics para que possa geri-los e monitorizá-los, configurá-los para comunicar diretamente com o gateway Do Log Analytics. O gateway Do Log Analytics pode receber informações de configuração e transmitir dados em seu nome. Se os computadores estiverem configurados com o agente Log Analytics para ligar diretamente a um espaço de trabalho do Log Analytics, os computadores comunicam-se com o gateway Do Log Analytics.  

O gateway Log Analytics transfere dados dos agentes para o serviço diretamente. Não analisa nenhum dos dados em trânsito e o gateway não cache dados quando perde conectividade com o serviço. Quando o gateway não consegue comunicar com o serviço, o agente continua a executar e faz fila dos dados recolhidos no disco do computador monitorizado. Quando a ligação é restaurada, o agente envia os dados em cache recolhidos para o Azure Monitor.

Quando um grupo de gestão de Gestores de Operações é integrado com o Log Analytics, os servidores de gestão podem ser configurados para se conectarem ao gateway Log Analytics para receber informações de configuração e enviar dados recolhidos, dependendo da solução que ativou.  Os agentes do Gestor de Operações enviam alguns dados para o servidor de gestão. Por exemplo, os agentes podem enviar alertas do Gestor de Operações, dados de avaliação de configuração, dados espaciais e dados de capacidade. Outros dados de grande volume, tais como registos de Serviços de Informação da Internet (IIS), dados de desempenho e eventos de segurança, são enviados diretamente para o gateway Do Log Analytics. 

Se um ou mais servidores do Gestor de Operações Gateway forem implantados para monitorizar sistemas não fided os falsos numa rede de perímetro ou numa rede isolada, esses servidores não podem comunicar com uma porta de entrada do Log Analytics.  Os servidores do Gestor de Operações Gateway só podem reportar-se a um servidor de gestão.  Quando um grupo de gestão de Gestor de Operações está configurado para comunicar com o gateway Do Log Analytics, a informação de configuração de procuração é automaticamente distribuída em todos os computadores geridos por agente que está configurado para recolher dados de registo para o Azure Monitor, mesmo que a definição esteja vazia.

Para fornecer alta disponibilidade para grupos de gestão de operações diretamente conectados ou que comunicam com um espaço de trabalho Log Analytics através do gateway, utilize o equilíbrio da carga de rede (NLB) para redirecionar e distribuir tráfego através de vários servidores de gateway. Assim, se um servidor de gateway se desligar, o tráfego é redirecionado para outro nó disponível.  

O computador que executa o gateway Log Analytics requer que o agente Do Windows Do Log Analytics identifique os pontos finais de serviço com os quais o gateway precisa de comunicar. O agente também precisa de direcionar a porta de entrada para reportar aos mesmos espaços de trabalho com os quais os agentes ou o grupo de gestão de gestores de operações por trás do portal estão configurados. Esta configuração permite que o gateway e o agente comuniquem com o seu espaço de trabalho atribuído.

Um portal pode ser multihomed até quatro espaços de trabalho. Este é o número total de espaços de trabalho que um agente Windows suporta.  

Cada agente deve ter conectividade de rede com o gateway para que os agentes possam transferir automaticamente dados de e para o portal. Evite instalar o gateway num controlador de domínio. Os computadores Linux que estão por trás de um servidor gateway não podem usar o método de [instalação](agent-linux.md#install-the-agent-using-wrapper-script) do script de invólucro para instalar o agente Log Analytics para o Linux. O agente deve ser descarregado manualmente, copiado para o computador e instalado manualmente porque o gateway só suporta comunicar com os serviços Azure mencionados anteriormente.

O diagrama seguinte mostra dados que fluem de agentes diretos, através do gateway, para a Azure Automation e Log Analytics. A configuração do agente proxy deve corresponder à porta com a qual o gateway Log Analytics está configurado.  

![Diagrama de comunicação de agente direto com serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama seguinte mostra o fluxo de dados de um grupo de gestão de Gestor de Operações para Log Analytics.   

![Diagrama de comunicação do Gestor de Operações com Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurar o seu sistema

Os computadores designados para executar o gateway Log Analytics devem ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos um processador de 4 núcleos e 8 GB de memória 
* Um [agente do Log Analytics para o Windows](agent-windows.md) que está configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam através do portal

### <a name="language-availability"></a>Disponibilidade linguística

O gateway Log Analytics está disponível nestes idiomas:

- Chinês (Simplificado)
- Chinês (Tradicional)
- Checo
- Neerlandês
- Inglês
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
- Espanhol (Internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de encriptação suportados

O gateway Log Analytics suporta apenas a Segurança da Camada de Transporte (TLS) 1.0, 1.1 e 1.2.  Não suporta a camada de tomadas seguras (SSL).  Para garantir a segurança dos dados em trânsito para o Log Analytics, configure o gateway para utilizar pelo menos TLS 1.2. As versões mais antigas de TLS ou SSL são vulneráveis. Embora atualmente permitam retrocompatibilidade, evite usá-las.  

Para obter informações adicionais, [reveja o envio de dados de forma segura utilizando o TLS 1.2](./data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número suportado de ligações de agentes

A tabela que se segue mostra aproximadamente quantos agentes podem comunicar com um servidor de gateway. O suporte baseia-se em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é de cerca de 2,7 GB por dia.

|Gateway |Agentes apoiados (aproximadamente)|  
|--------|----------------------------------|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 2 Cores<br> Memória: 4 GB<br> Largura de banda da rede: 1 Gbps| 600|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 4 Cores<br> Memória: 8 GB<br> Largura de banda da rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Descarregue o gateway Do Log Analytics

Obtenha a versão mais recente do ficheiro de configuração do gateway do Log Analytics a partir do Microsoft Download Center[(Download Link)](https://go.microsoft.com/fwlink/?linkid=837444)ou do portal Azure.

Para obter o gateway Log Analytics a partir do portal Azure, siga estes passos:

1. Navegue na lista de serviços e, em seguida, selecione **Log Analytics**. 
1. Selecione uma área de trabalho.
1. Na sua lâmina de espaço de trabalho, em **Geral,** selecione **Quick Start**. 
1. Em **Escolha uma fonte de dados para ligar ao espaço de trabalho,** selecione **Computadores**.
1. Na lâmina **Agente Direto,** selecione **Download Log Analytics gateway**.
 
   ![Screenshot dos passos para descarregar o gateway Log Analytics](./media/gateway/download-gateway.png)

ou 

1. Na sua lâmina de espaço de trabalho, em **Definições,** selecione **Definições Avançadas**.
1. Aceda a **Fontes Conectadas**  >  **Servidores Windows** e selecione **Download Log Analytics gateway**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instale gateway De Log Analytics usando o assistente de configuração

Para instalar um gateway utilizando o assistente de configuração, siga estes passos. 

1. A partir da pasta de destino, clique duas vezes **em gateway.mside Log Analytics**.
1. Na página **Bem-vindo**, selecione **Seguinte**.

   ![Screenshot da página de boas-vindas no assistente de configuração gateway](./media/gateway/gateway-wizard01.png)

1. Na página **Do Contrato de Licença,** selecione **aceito os termos do Contrato de Licença** para concordar com os Termos da Licença de Software da Microsoft e, em seguida, selecione **Seguinte**.
1. Na página de **endereços do Porto e proxy:**

   a. Introduza o número da porta TCP para ser utilizado para o gateway. A configuração utiliza este número de porta para configurar uma regra de entrada no Windows Firewall.  O valor predefinido é 8080.
      A gama válida do número da porta é de 1 a 65535. Se a entrada não cair neste intervalo, aparece uma mensagem de erro.

   b. Se o servidor onde o gateway está instalado necessitar de comunicar através de um representante, insira o endereço de procuração onde o gateway precisa de se ligar. Por exemplo, introduza `http://myorgname.corp.contoso.com:80`.  Se deixar este campo em branco, o portal tentará ligar-se diretamente à internet.  Se o seu servidor proxy necessitar de autenticação, insira um nome de utilizador e uma palavra-passe.

   c. Selecione **Seguinte**.

   ![Screenshot da configuração para o proxy gateway](./media/gateway/gateway-wizard02.png)

1. Se não tiver o Microsoft Update ativado, a página do Microsoft Update aparece e pode optar por ativer. Faça uma seleção e, em seguida, selecione **Next**. Caso contrário, avance para o passo seguinte.
1. Na página **'Pasta destino',** ou sai da pasta padrão C:\Program Files\OMS Gateway ou insira o local onde pretende instalar o gateway. Em seguida, selecione **Seguinte**.
1. Na página **Pronto para instalar,** selecione **Instalar**. Se o Controlo da Conta do Utilizador solicitar a sua instalação, selecione **Sim**.
1. Depois de terminar a configuração, **selecione Acabamento**. Para verificar se o serviço está em funcionamento, abra o snap-in do services.msc e verifique se o **OMS Gateway** aparece na lista de serviços e que o seu estado está **em execução**.

   ![Screenshot dos serviços locais, mostrando que o OMS Gateway está em execução](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instale o gateway Log Analytics utilizando a linha de comando

O ficheiro descarregado para o gateway é um pacote do Windows Installer que suporta a instalação silenciosa a partir da linha de comando ou de outro método automatizado. Se não estiver familiarizado com as opções padrão da linha de comando para o Instalador do Windows, consulte [as opções da linha de comando](/windows/desktop/msi/command-line-options).
 
A tabela a seguir realça os parâmetros suportados pela configuração.

|Parâmetros| Notas|
|----------|------| 
|PORTNUMBER | Número da porta TCP para porta de entrada para ouvir |
|PROXY | Endereço IP do servidor proxy |
|INSTALLDIR | Caminho totalmente qualificado para especificar o diretório de instalação de ficheiros de software gateway |
|USERNAME | ID do utilizador para autenticar com servidor proxy |
|SENHA | Palavra-passe do ID do utilizador para autenticar com procuração |
|LicenseAccepted | Especifique um valor de **1** para verificar se aceita contrato de licença |
|HASAUTH | Especifique um valor de **1** quando os parâmetros USERNAME/PASSWORD forem especificados |
|HASPROXY | Especificar um valor de **1** ao especificar o endereço IP para o parâmetro **PROXY** |

Para instalar silenciosamente o gateway e configurá-lo com um endereço de procuração específico, número de porta, digite o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

A utilização da opção de linha de comando /qn esconde a configuração, /qb mostra a configuração durante a instalação silenciosa.  

Se precisar de fornecer credenciais para autenticar com o representante, escreva o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Após a instalação, pode confirmar que as definições são aceites (excluindo o nome de utilizador e a palavra-passe) utilizando os seguintes cmdlets PowerShell:

- **Get-OMSGatewayConfig** – Devolve a porta TCP a porta de entrada está configurada para ouvir.
- **Get-OMSGatewayRelay –** Devolve o endereço IP do servidor proxy que o configura para comunicar.

## <a name="configure-network-load-balancing"></a>Configurar o equilíbrio da carga da rede

Pode configurar o gateway para uma elevada disponibilidade utilizando o equilíbrio de carga de rede (NLB) utilizando o [Equilíbrio de Carga da Rede Microsoft (NLB)](/windows-server/networking/technologies/network-load-balancing), o Balançador de Carga [Azure](../../load-balancer/load-balancer-overview.md)ou os equilibradores de carga baseados em hardware. O equilibrador de carga gere o tráfego redirecionando as ligações solicitadas dos agentes do Log Analytics ou dos servidores de gestão do Gestor de Operações através dos seus nós. Se um servidor Gateway se desligar, o tráfego é redirecionado para outros nós.

### <a name="microsoft-network-load-balancing"></a>Equilíbrio de carga de rede da Microsoft

Para aprender a desenhar e implementar um cluster de equilíbrio de carga de rede do Windows Server 2016, consulte [o equilíbrio da carga da Rede](/windows-server/networking/technologies/network-load-balancing). Os seguintes passos descrevem como configurar um cluster de equilíbrio de carga de rede da Microsoft.  

1. Inscreva-se no servidor windows que é um membro do cluster NLB com uma conta administrativa.  
2. Abra o Gestor de Equilíbrio de Carga de Rede no Gestor do Servidor, clique em **Ferramentas** e, em seguida, clique em **Network Load Balance Manager**.
3. Para ligar um servidor de gateway do Log Analytics com o Agente de Monitorização da Microsoft instalado, clique com o botão direito no endereço IP do cluster e, em seguida, clique em **Adicionar Anfitrião ao Cluster**. 

    ![Gestor de equilíbrio de carga de rede – adicione anfitrião ao cluster](./media/gateway/nlb02.png)
 
4. Introduza o endereço IP do servidor gateway que pretende ligar. 

    ![Gestor de equilíbrio de carga de rede – Adicionar anfitrião ao cluster: Connect](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Para aprender a desenhar e implementar um Equilibrador de Carga Azure, veja [o que é o Equilibr de Carga Azure?](../../load-balancer/load-balancer-overview.md) Para implantar um equilibrador de carga básico, siga os passos descritos neste [quickstart](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) excluindo os passos descritos na secção **Criar servidores de back-end**.   

> [!NOTE]
> Configurar o Balançador de Carga Azure utilizando o **SKU Básico,** requer que as máquinas virtuais Azure pertençam a um Conjunto de Disponibilidade. Para saber mais sobre os conjuntos de disponibilidade, consulte [Gerir a disponibilidade de máquinas virtuais do Windows em Azure.](../../virtual-machines/manage-availability.md) Para adicionar máquinas virtuais existentes a um conjunto de disponibilidade, consulte o Conjunto de [Disponibilidade VM do Gestor de Recursos do Azure.](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)
> 

Após a criação do equilibrador de carga, é necessário criar um pool de backend, que distribui o tráfego por um ou mais servidores de gateway. Siga os passos descritos na secção de artigos de arranque rápido [Crie recursos para o equilibrador de carga](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Ao configurar a sonda de saúde, deve ser configurada para utilizar a porta TCP do servidor gateway. A sonda de saúde adiciona ou remove dinamicamente os servidores de gateway da rotação do balanceador de carga com base na sua resposta aos controlos de saúde. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configure o grupo de gestão de agente e gestor de operações do Log Analytics

Nesta secção, você verá como configurar agentes de Log Analytics conectados diretamente, um grupo de gestão de Gestor de Operações, ou Azure Automation Hybrid Runbook Workers com a porta de entrada Log Analytics para comunicar com a Azure Automation ou Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configure um agente autónomo do Log Analytics

Ao configurar o agente Log Analytics, substitua o valor do servidor proxy pelo endereço IP do servidor gateway 'Log Analytics' e do seu número de porta. Se tiver implantado vários servidores de gateway atrás de um balanceador de carga, a configuração de procuração do agente Log Analytics é o endereço IP virtual do balançador de carga.  

>[!NOTE]
>Para instalar o agente Log Analytics nos computadores gateway e Windows que se ligam diretamente ao Log Analytics, consulte [os computadores Do Windows para o serviço Log Analytics em Azure](agent-windows.md). Para ligar computadores Linux, consulte [os computadores Connect Linux ao Azure Monitor](agent-linux.md). 
>

Depois de instalar o agente no servidor gateway, configuure-o para reportar ao espaço de trabalho ou aos agentes do espaço de trabalho que comunicam com o gateway. Se o agente Do Windows Do Log Analytics não estiver instalado no gateway, o evento 300 é escrito no registo de eventos OMS Gateway, indicando que o agente precisa de ser instalado. Se o agente estiver instalado mas não configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam através dele, o evento 105 é escrito no mesmo registo, indicando que o agente no gateway precisa de ser configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam com o gateway.

Depois de completar a configuração, reinicie o serviço **OMS Gateway** para aplicar as alterações. Caso contrário, o gateway rejeitará os agentes que tentem comunicar com o Log Analytics e reportará o evento 105 no registo de eventos OMS Gateway. Isto também acontecerá quando adicionar ou remover um espaço de trabalho da configuração do agente no servidor gateway.

Para obter informações relacionadas com o Trabalhador de Runbook Híbrido automation, consulte [os recursos de automatização no seu datacenter ou nuvem utilizando o Trabalhador de Runbook Híbrido.](../../automation/automation-hybrid-runbook-worker.md)

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configure Gestor de Operações, onde todos os agentes usam o mesmo servidor proxy

A configuração de procuração do Gestor de Operações é automaticamente aplicada a todos os agentes que reportam ao Gestor de Operações, mesmo que a definição esteja vazia.  

Para utilizar o OMS Gateway para apoiar o Gestor de Operações, tem de:

* Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalado no servidor OMS Gateway e configurado com os mesmos espaços de trabalho do Log Analytics a que o seu grupo de gestão está configurado para reportar.
* Conectividade na Internet. Em alternativa, o OMS Gateway deve ser ligado a um servidor proxy que esteja ligado à internet.

> [!NOTE]
> Se não especificar nenhum valor para o gateway, os valores em branco são empurrados para todos os agentes.
>

Se o seu grupo de gestão de Gestor de Operações estiver a registar-se pela primeira vez com um espaço de trabalho Log Analytics, não verá a opção de especificar a configuração de procuração para o grupo de gestão na consola Operações. Esta opção só está disponível se o grupo de gestão tiver sido registado no serviço.  

Para configurar a integração, atualize a configuração do sistema proxy utilizando o Netsh no sistema onde está a executar a consola Operações e em todos os servidores de gestão do grupo de gestão. Siga estes passos:

1. Abra uma solicitação de comando elevada:

   a. Selecione **Iniciar** e insira **cmd**.  

   b. Clique no **botão direito Pedido** de comando e selecione Executar como **administrador**.  

1. Introduza o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o Log Analytics, remova a alteração executando `netsh winhttp reset proxy` . Em seguida, na consola Operações, utilize a opção **de servidor de procuração Configure** para especificar o servidor de gateway 'Log Analytics'. 

1. Na consola Gerente de Operações, no **Conjunto de Gestão de Operações,** selecione **Connection** e, em seguida, selecione **Configure Proxy Server**.

   ![Screenshot do Gestor de Operações, mostrando a seleção Configure Proxy Server](./media/gateway/scom01.png)

1. Selecione **Utilize um servidor proxy para aceder ao Suite de Gestão** de Operações e, em seguida, insira o endereço IP do servidor gateway do Log Analytics ou do endereço IP virtual do esquilibrador de carga. Tenha cuidado para começar com o `http://` prefixo.

   ![Screenshot do Gestor de Operações, mostrando o endereço do servidor proxy](./media/gateway/scom02.png)

1. Selecione **Concluir**. O seu grupo de gestão de Gestor de Operações está agora configurado para comunicar através do servidor gateway para o serviço Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configure gestor de operações, onde agentes específicos usam um servidor proxy

Para ambientes grandes ou complexos, pode querer apenas servidores específicos (ou grupos) para utilizar o servidor de gateway Log Analytics.  Para estes servidores, não é possível atualizar diretamente o agente Gestor de Operações porque este valor é substituído pelo valor global para o grupo de gestão.  Em vez disso, anular a regra usada para empurrar estes valores.  

> [!NOTE] 
> Utilize esta técnica de configuração se pretender permitir vários servidores de gateway do Log Analytics no seu ambiente.  Por exemplo, pode exigir que servidores específicos do gateway do Log Analytics sejam especificados numa base regional.
>

Para configurar servidores ou grupos específicos para utilizar o servidor de gateway Do Log Analytics: 

1. Abra a consola Do Gestor de Operações e selecione o espaço de trabalho **de Autoring.**  
1. No espaço de trabalho de autoria, selecione **Regras.** 
1. Na barra de ferramentas Do Gestor de Operações, selecione o botão **'Âmbito'.** Se este botão não estiver disponível, certifique-se de que selecionou um objeto, não uma pasta, no painel **de monitorização.** A caixa de diálogo **de conjunto de conjuntos de gestão** de âmbito apresenta uma lista de classes, grupos ou objetos comuns. 
1. No campo **Procurar** por campo, insira o **Serviço de Saúde** e selecione-o da lista. Selecione **OK**.  
1. Procurar a **regra de definição de procuração do conselheiro**. 
1. Na barra de ferramentas do Gestor de Operações, selecione **Overrides** e, em seguida, aponte para **a Regra\Para um objeto específico de classe: Serviço de Saúde** e selecione um objeto da lista.  Ou criar um grupo personalizado que contenha o objeto de serviço de saúde dos servidores a que pretende aplicar esta sobreposição. Em seguida, aplique a substituição ao seu grupo personalizado.
1. Na caixa de diálogo **Override Properties,** adicione uma marca de verificação na coluna **Override** ao lado do parâmetro **WebProxyAddress.**  No campo **Valor de Substituição,** introduza o URL do servidor gateway Log Analytics. Tenha cuidado para começar com o `http://` prefixo.  

    >[!NOTE]
    > Não precisas de ativar a regra. Já é gerido automaticamente com uma sobreposição no pacote de gestão de override de referência seguro do Microsoft System Center Que visa o Microsoft System Center Monitoring Server Group.
    > 

1. Selecione um pacote de gestão da lista de pacotes de **gestão** de destino Select ou crie um novo pacote de gestão não selado selecionando **New**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configuração para Automação Hybrid Runbook Workers

Se tiver trabalhadores de runbook híbridos automation no seu ambiente, siga estes passos para configurar a porta de entrada para apoiar os trabalhadores.

Consulte a secção [de Configuração](../../automation/automation-hybrid-runbook-worker.md#network-planning) da sua rede da documentação da Automação para encontrar o URL para cada região.

Se o seu computador estiver registado automaticamente como Trabalhador de Runbook Híbrido, por exemplo, se a solução de Gestão de Atualização estiver ativada para um ou mais VMs, siga estes passos:

1. Adicione os URLs do serviço de dados de execução de trabalho à lista de anfitriões permitidos no gateway 'Log Analytics'. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço de gateway Log Analytics utilizando o seguinte cmdlet PowerShell: `Restart-Service OMSGatewayService`

Se o seu computador se juntar à Azure Automation utilizando o cmdlet de registo híbrido do Trabalhador runbook, siga estes passos:

1. Adicione o URL de registo de serviço do agente à lista de anfitriões permitidos no gateway Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione os URLs do serviço de dados de execução de trabalho à lista de anfitriões permitidos no gateway 'Log Analytics'. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço de gateway Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis PowerShell

Pode utilizar cmdlets para completar as tarefas para atualizar as definições de configuração do Gateway 'Log Analytics'. Antes de usar cmdlets, certifique-se de:

1. Instale o gateway 'Log Analytics' (Microsoft Windows Installer).
1. Abra uma janela da consola PowerShell.
1. Importe o módulo digitando este comando: `Import-Module OMSGateway`
1. Se não ocorrerem erros na etapa anterior, o módulo foi importado com sucesso e os cmdlets podem ser utilizados. Introduza `Get-Module OMSGateway`
1. Depois de utilizar os cmdlets para estois, reinicie o serviço OMS Gateway.

Um erro no passo 3 significa que o módulo não foi importado. O erro pode ocorrer quando o PowerShell não encontrar o módulo. Pode encontrar o módulo no caminho de instalação do OMS Gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (necessária) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy retransmissor (a montante) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de utilizador<br> Palavra-passe (cadeia segura) |Define o endereço (e credencial) do proxy relé (a montante) |1. Definir um representante e credencial de retransmissão:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Deite um representante de retransmissão que não necessite de autenticação: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Limpe a definição de procuração de retransmissão:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o anfitrião atualmente permitido (apenas o anfitrião localmente configurado, não automaticamente descarregado anfitriões permitidos) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hospedeiro (obrigatório) |Adiciona o anfitrião à lista permitida |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hospedeiro (obrigatório) |Remove o anfitrião da lista permitida |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Objeto (obrigatório) |Adiciona o certificado de cliente sujeito à lista permitida |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Objeto (obrigatório) |Remove o sujeito de certificado de cliente da lista permitida |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os sujeitos de certificado de cliente atualmente permitidos (apenas os sujeitos permitidos localmente configurados, não automaticamente descarregados sujeitos permitidos) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Resolução de problemas

Para recolher eventos registados pelo gateway, deverá ter o agente Log Analytics instalado.

![Screenshot da lista de espectadores de eventos no log de gateway do Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>IDs e descrições do evento Log Analytics gateway

A tabela seguinte mostra os IDs do evento e descrições para eventos de registo de gateway log Analytics.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicação que não tenha identificação específica. |
| 401 |Configuração errada. Por exemplo, ouçaPort = "text" em vez de um inteiro. |
| 402 |Exceção na análise de mensagens de aperto de mão TLS. |
| 403 |Erro de ligação em rede. Por exemplo, não é possível ligar-se ao servidor alvo. |
| 100 |Informação geral. |
| 101 |O serviço já começou. |
| 102 |O serviço parou. |
| 103 |Foi recebido um comando HTTP CONNECT do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |O servidor de destino não está na lista permitida, ou a porta de destino não é segura (443). <br> <br> Certifique-se de que o agente MMA no seu servidor OMS Gateway e os agentes que comunicam com o OMS Gateway estão ligados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERROR TcpConnection – Certificado de cliente inválido: CN=Gateway. <br><br> Certifique-se de que está a utilizar a versão 1.0.395.0 do OMS Gateway ou superior. Certifique-se também de que o agente MMA no seu servidor OMS Gateway e os agentes que comunicam com o OMS Gateway estão ligados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Versão do protocolo TLS/SSL não suportada.<br><br> O gateway Log Analytics suporta apenas TLS 1.0, TLS 1.1 e 1.2. Não suporta ssl.|
| 107 |A sessão TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho para recolher

A tabela seguinte mostra os contadores de desempenho disponíveis para o gateway Log Analytics. Utilize o Monitor de Desempenho para adicionar os contadores.

| **Name** | **Descrição** |
| --- | --- |
| Log Analytics Gateway/Ative Client Connection |Número de ligações ativas da rede de clientes (TCP) |
| Log Analytics Gateway/Contagem de erros |Número de erros |
| Log Analytics Gateway/Cliente conectado |Número de clientes conectados |
| Log Analytics Gateway/Contagem de rejeição |Número de rejeições devido a qualquer erro de validação TLS |

![Screenshot da interface de gateway do Log Analytics, mostrando contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência

Quando estiver inscrito no portal Azure, poderá obter ajuda com o gateway Log Analytics ou qualquer outro serviço ou funcionalidade Azure.
Para obter ajuda, selecione o ícone de ponto de interrogação no canto superior direito do portal e selecione **Novo pedido** de suporte . Em seguida, preencha o novo formulário de pedido de apoio.

![Screenshot de um novo pedido de suporte](./media/gateway/support.png)

## <a name="next-steps"></a>Passos seguintes

[Adicione fontes de dados](./agent-data-sources.md) para recolher dados de fontes conectadas e armazenar os dados no seu espaço de trabalho Log Analytics.