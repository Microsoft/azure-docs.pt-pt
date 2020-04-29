---
title: Ligue os computadores utilizando o portal Log Analytics [ Microsoft Docs
description: Conecte os computadores monitorizados pelos seus dispositivos e operações utilizando a porta de entrada Log Analytics para enviar dados para o serviço Azure Automation e Log Analytics quando estes não têm acesso à Internet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298340"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Ligar computadores sem acesso à Internet utilizando o gateway Log Analytics no Monitor Azure

>[!NOTE]
>À medida que a Microsoft Operations Management Suite (OMS) passa para o Microsoft Azure Monitor, a terminologia está a mudar. Este artigo refere-se a OMS Gateway como o portal Azure Log Analytics. 
>

Este artigo descreve como configurar a comunicação com o Azure Automation e o Azure Monitor utilizando o portal Log Analytics quando os computadores que estão diretamente ligados ou que são monitorizados pelo Gestor de Operações não têm acesso à Internet. 

O gateway Log Analytics é um proxy avançado HTTP que suporta o túnel HTTP utilizando o comando HTTP CONNECT. Esta porta de entrada envia dados para a Azure Automation e um espaço de trabalho de Log Analytics no Azure Monitor em nome dos computadores que não conseguem ligar-se diretamente à internet. 

O gateway Log Analytics suporta:

* Reportando até os mesmos espaços de trabalho log Analytics configurados em cada agente por trás e que estão configurados com Trabalhadores de Runbook Híbrido sacana da Azure Automation.  
* Os computadores Windows em que o Microsoft Monitoring Agent está diretamente ligado a um espaço de trabalho do Log Analytics no Monitor Azure.
* Computadores Linux em que um agente DeLog Analytics para o Linux está diretamente ligado a um espaço de trabalho de Log Analytics no Monitor Azure.  
* System Center Operations Manager 2012 SP1 com UR7, Operations Manager 2012 R2 com UR3, ou um grupo de gestão em Operations Manager 2016 ou mais tarde que está integrado com log Analytics.  

Algumas políticas de segurança de TI não permitem a ligação à Internet para computadores de rede. Estes computadores não ligados podem ser dispositivos ou servidores de ponto de venda (POS) que suportam serviços de TI, por exemplo. Para ligar estes dispositivos à Automatização Azure ou a um espaço de trabalho de Log Analytics para que possa geri-los e monitorizá-los, configure-os para comunicar diretamente com o gateway Log Analytics. O gateway Log Analytics pode receber informações de configuração e transmitir dados em seu nome. Se os computadores estiverem configurados com o agente Log Analytics para se ligarem diretamente a um espaço de trabalho do Log Analytics, os computadores comunicam com o portal Log Analytics.  

O gateway Log Analytics transfere os dados dos agentes para o serviço diretamente. Não analisa nenhum dos dados em trânsito e o portal não cache dados quando perde conectividade com o serviço. Quando o portal não consegue comunicar com o serviço, o agente continua a executar e faz fila dos dados recolhidos no disco do computador monitorizado. Quando a ligação é restaurada, o agente envia os dados em cache recolhidos para o Monitor Azure.

Quando um grupo de gestão do Gestor de Operações está integrado no Log Analytics, os servidores de gestão podem ser configurados para se conectarem ao portal Log Analytics para receber informações de configuração e enviar dados recolhidos, dependendo da solução que tiver ativado.  Os agentes do Gestor de Operações enviam alguns dados para o servidor de gestão. Por exemplo, os agentes podem enviar alertas do Gestor de Operações, dados de avaliação de configuração, dados do espaço e dados de capacidade. Outros dados de grande volume, tais como registos de Serviços de Informação de Internet (IIS), dados de desempenho e eventos de segurança, são enviados diretamente para o portal Log Analytics. 

Se um ou mais servidores do Gestor de Operações Gateway forem implantados para monitorizar sistemas não confiáveis numa rede de perímetro ou numa rede isolada, esses servidores não podem comunicar com um portal de Log Analytics.  Os servidores gateway do Gestor de Operações só podem reportar a um servidor de gestão.  Quando um grupo de gestão do Gestor de Operações é configurado para comunicar com o gateway Log Analytics, a informação de configuração proxy é distribuída automaticamente para todos os computadores geridos por agentes que estão configurados para recolher dados de registo para o Monitor Azure, mesmo que a definição esteja vazia.

Para fornecer uma elevada disponibilidade para grupos de gestão de operações diretamente conectados ou de operações que comunicam com um espaço de trabalho Log Analytics através do gateway, use o equilíbrio de carga de rede (NLB) para redirecionar e distribuir tráfego em vários servidores de gateway. Assim, se um servidor de gateway descer, o tráfego é redirecionado para outro nó disponível.  

O computador que executa o gateway Log Analytics requer que o agente do Log Analytics Windows identifique os pontos finais do serviço com os quais o gateway precisa de comunicar. O agente também precisa de direcionar a porta de entrada para reportar aos mesmos espaços de trabalho que os agentes ou o grupo de gestão do Gestor de Operações atrás do portal estão configurados. Esta configuração permite que o portal e o agente se comuniquem com o seu espaço de trabalho atribuído.

Um portal pode ser multialojado até quatro espaços de trabalho. Este é o número total de espaços de trabalho que um agente windows suporta.  

Cada agente deve ter conectividade de rede com o portal de entrada para que os agentes possam transferir automaticamente dados de e para o portal. Evite instalar o portal num controlador de domínio. Os computadores Linux que estão por trás de um servidor de gateway não podem utilizar o método de [instalação](agent-linux.md#install-the-agent-using-wrapper-script) do script de invólucro para instalar o agente Log Analytics para o Linux. O agente deve ser descarregado manualmente, copiado para o computador e instalado manualmente porque o portal apenas suporta a comunicação com os serviços Azure mencionados anteriormente.

O diagrama seguinte mostra dados que fluem de agentes diretos, através do portal, para A Automatização Azure e Log Analytics. A configuração proxy do agente deve corresponder à porta com a que o gateway Log Analytics está configurado.  

![Diagrama de comunicação de agente direto com serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama seguinte mostra o fluxo de dados de um grupo de gestão de Gestor de Operações para Log Analytics.   

![Diagrama de comunicação do Gestor de Operações com Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurar o seu sistema

Os computadores designados para executar o gateway Log Analytics devem ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos um processador de 4 núcleos e 8 GB de memória 
* Um [agente log Analytics para windows](agent-windows.md) que está configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam através do portal

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

O gateway Log Analytics suporta apenas a Segurança da Camada de Transporte (TLS) 1.0, 1.1 e 1.2.  Não suporta a camada de tomadas seguras (SSL).  Para garantir a segurança dos dados em trânsito para o Log Analytics, configure a porta de entrada para utilizar pelo menos TLS 1.2. Versões mais antigas de TLS ou SSL são vulneráveis. Embora atualmente permitam a retrocompatibilidade, evite usá-las.  

Para obter informações adicionais, reveja o [envio de dados de forma segura utilizando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número suportado de ligações de agentes

A tabela seguinte mostra aproximadamente quantos agentes podem comunicar com um servidor de gateway. O suporte baseia-se em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é de cerca de 2,7 GB por dia.

|Gateway |Agentes apoiados (aproximados)|  
|--------|----------------------------------|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 2 Cores<br> Memória: 4 GB<br> Largura de banda da rede: 1 Gbps| 600|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 4 Cores<br> Memória: 8 GB<br> Largura de banda da rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Descarregue o gateway Log Analytics

Obtenha a versão mais recente do ficheiro de configuração de gateway Log Analytics do Microsoft Download Center ([Download Link](https://go.microsoft.com/fwlink/?linkid=837444)) ou do portal Azure.

Para obter o portal Log Analytics a partir do portal Azure, siga estes passos:

1. Navegue na lista de serviços e, em seguida, selecione **Log Analytics**. 
1. Selecione uma área de trabalho.
1. Na sua lâmina de espaço de trabalho, em **Geral,** selecione **Quick Start**. 
1. Em 'Escolha uma fonte de **dados' para se ligar ao espaço**de trabalho, selecione **Computadores**.
1. Na lâmina **do Agente Direto,** selecione **descarregue**o gateway Analytics de registo .
 
   ![Screenshot dos passos para descarregar o gateway Log Analytics](./media/gateway/download-gateway.png)

ou 

1. Na lâmina do espaço de trabalho, em **Definições,** **selecione Definições Avançadas**.
1. Vá a Windows**Servers** de **Fontes** > Conectadas e selecione **descarregamento**de gateway Analytics de registo .

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instale o gateway Log Analytics utilizando o assistente de configuração

Para instalar um portal utilizando o assistente de configuração, siga estes passos. 

1. A partir da pasta de destino, clique duas vezes **log Analytics gateway.msi**.
1. Na página **Bem-vindo**, selecione **Seguinte**.

   ![Screenshot da página de boas-vindas no assistente de configuração gateway](./media/gateway/gateway-wizard01.png)

1. Na página do Contrato de **Licença,** selecione **I accept the terms in the License Agreement** to agree to the Microsoft Software License Terms, and then select **Next**.
1. Na página de **endereço seleção do Porto e do proxy:**

   a. Introduza o número da porta TCP a utilizar para o portal. A configuração utiliza este número de porta para configurar uma regra de entrada no Windows Firewall.  O valor padrão é 8080.
      O alcance válido do número da porta é de 1 a 65535. Se a entrada não cair neste intervalo, aparece uma mensagem de erro.

   b. Se o servidor onde o portal está instalado necessitar de comunicar através de um proxy, introduza o endereço proxy onde o gateway precisa de se ligar. Por exemplo, introduza `http://myorgname.corp.contoso.com:80`.  Se deixar este campo em branco, o portal tentará ligar-se diretamente à internet.  Se o seu servidor proxy necessitar de autenticação, introduza um nome de utilizador e uma senha.

   c. Selecione **Seguinte**.

   ![Screenshot de configuração para o proxy gateway](./media/gateway/gateway-wizard02.png)

1. Se não tiver o Microsoft Update ativado, aparece a página da Microsoft Update e pode optar por o ativar. Faça uma seleção e, em seguida, selecione **Next**. Caso contrário, continue para o próximo passo.
1. Na página **da Pasta destino,** deixe a pasta predefinida C:\Program Files\OMS Gateway ou introduza o local onde pretende instalar o gateway. Em seguida, selecione **Seguinte**.
1. Na página **Ready to install,** selecione **Instalar**. Se o Controlo de Conta do Utilizador solicitar permissão para instalar, selecione **Sim**.
1. Depois de terminar a configuração, selecione **Terminar**. Para verificar se o serviço está em funcionamento, abra o snap-in services.msc e verifique se o **OMS Gateway** aparece na lista de serviços e que o seu estado está **em execução**.

   ![Screenshot dos serviços locais, mostrando que o OMS Gateway está em execução](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instale o gateway Log Analytics utilizando a linha de comando

O ficheiro descarregado para o gateway é um pacote de instalador do Windows que suporta a instalação silenciosa a partir da linha de comando ou de outro método automatizado. Se não estiver familiarizado com as opções padrão de linha de comando para o Instalador do Windows, consulte [as opções da linha de comando](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
A tabela seguinte destaca os parâmetros suportados pela configuração.

|Parâmetros| Notas|
|----------|------| 
|PORTNUMBER | Número da porta tCP para gateway para ouvir |
|PROCURAÇÃO | Endereço IP do servidor proxy |
|INSTALADIR | Caminho totalmente qualificado para especificar a instalação de diretório sonéris de ficheiros de software gateway |
|USERNAME | ID do utilizador para autenticar com servidor proxy |
|SENHA | Palavra-passe do ID do utilizador para autenticar com procuração |
|LicenseAccepted | Especifique um valor de **1** para verificar se aceita o contrato de licença |
|HASAUTH | Especificar um valor de **1** quando os parâmetros USERNAME/PASSWORD são especificados |
|HASPROXY | Especificar um valor de **1** ao especificar o endereço IP para o parâmetro **PROXY** |

Para instalar silenciosamente o portal e configurá-lo com um endereço de procuração específico, número de porta, escrever o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Utilizando a opção de linha de comando /qn, a configuração, /qb mostra configuração durante a instalação silenciosa.  

Se precisar de fornecer credenciais para autenticar com o representante, escreva o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Após a instalação, pode confirmar que as definições são aceites (excluindo o nome de utilizador e a palavra-passe) utilizando os seguintes cmdlets PowerShell:

- **Get-OMSGatewayConfig** – Devolve a porta TCP o gateway está configurado para ouvir.
- **Get-OMSGatewayRelayProxy** – Devolve o endereço IP do servidor proxy com o qual configurapara comunicar.

## <a name="configure-network-load-balancing"></a>Configurar o equilíbrio da carga da rede

Pode configurar o portal para uma elevada disponibilidade utilizando o equilíbrio de carga de rede (NLB) utilizando o Equilíbrio de Carga da Rede Microsoft [(NLB),](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)o Equilíbrio de [Carga Azure](../../load-balancer/load-balancer-overview.md)ou os equilibradores de carga baseados em hardware. O equilibrador de carga gere o tráfego redirecionando as ligações solicitadas dos agentes de Log Analytics ou servidores de gestão do Gestor de Operações através dos seus nós. Se um servidor gateway cair, o tráfego é redirecionado para outros nós.

### <a name="microsoft-network-load-balancing"></a>Equilíbrio de carga da rede Microsoft

Para aprender a conceber e implementar um cluster de equilíbrio de carga de rede Windows Server 2016, consulte o [equilíbrio da carga da rede](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Os seguintes passos descrevem como configurar um cluster de equilíbrio de carga de rede da Microsoft.  

1. Inscreva-se no servidor do Windows que é membro do cluster NLB com uma conta administrativa.  
2. Abra o Gestor de Equilíbrio de Carga de Rede no Gestor do Servidor, clique em **Ferramentas,** e, em seguida, clique em Gestor de Equilíbrio de Carga de **Rede**.
3. Para ligar um servidor de gateway Log Analytics com o Agente de Monitorização da Microsoft instalado, clique no endereço IP do cluster e, em seguida, clique em **Adicionar Host ao Cluster**. 

    ![Gestor de equilíbrio de carga de rede - adicionar anfitrião ao cluster](./media/gateway/nlb02.png)
 
4. Introduza o endereço IP do servidor de gateway que pretende ligar. 

    ![Gestor de equilíbrio de carga de rede – Adicionar hospedeiro ao cluster: Connect](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Para aprender a desenhar e implementar um Equilíbrio de Carga Azure, veja [o que é o Azure Load Balancer?](../../load-balancer/load-balancer-overview.md) Para implementar um equilíbrio de carga básico, siga os passos descritos neste [arranque rápido,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) excluindo os passos descritos na secção **Criar servidores back-end**.   

> [!NOTE]
> Configurar o Equilíbrio de Carga Azure utilizando o **SKU Básico,** requer que as máquinas virtuais Azure pertençam a um Conjunto de Disponibilidade. Para saber mais sobre conjuntos de disponibilidade, consulte [Gerir a disponibilidade de máquinas virtuais windows em Azure](../../virtual-machines/windows/manage-availability.md). Para adicionar as máquinas virtuais existentes a um conjunto de disponibilidade, consulte o Conjunto de [Disponibilidade vm do Gestor](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)de Recursos set Azure .
> 

Após a criação do equilibrador de carga, é necessário criar um pool de backend, que distribui tráfego a um ou mais servidores de gateway. Siga os passos descritos na secção de artigos de arranque rápido [Crie recursos para o equilibrador](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)de carga .  

>[!NOTE]
>Ao configurar a sonda de saúde, deve ser configurada para utilizar a porta TCP do servidor gateway. A sonda de saúde adiciona ou remove os servidores de gateway da rotação do equilibrador de carga com base na sua resposta a verificações de saúde. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configure o grupo de gestão de gestores de operações e agente de análise de log analytics

Nesta secção, você verá como configurar agentes Log Analytics diretamente conectados, um grupo de gestão de Gestor de Operações, ou Trabalhadores de Runbook Híbrido sacana Azure Automation com a porta de entrada Log Analytics para comunicar com a Azure Automation ou Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configure um agente de Log Analytics autónomo

Ao configurar o agente Log Analytics, substitua o valor do servidor proxy pelo endereço IP do servidor de gateway Log Analytics e o seu número de porta. Se tiver implantado vários servidores de gateway atrás de um balanceador de carga, a configuração proxy do agente Log Analytics é o endereço IP virtual do equilibrista de carga.  

>[!NOTE]
>Para instalar o agente Log Analytics no gateway e computadores Windows que se ligam diretamente ao Log Analytics, consulte [o Connect Windows computers para o serviço Log Analytics no Azure](agent-windows.md). Para ligar os computadores Linux, consulte [os computadores Connect Linux para o Monitor Azure](agent-linux.md). 
>

Depois de instalar o agente no servidor gateway, configure-o para reportar ao espaço de trabalho ou agentes do espaço de trabalho que comunicam com o portal. Se o agente Do Windows de Log Analytics não estiver instalado no portal, o evento 300 está escrito no registo de eventos OMS Gateway, indicando que o agente precisa de ser instalado. Se o agente estiver instalado mas não configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam através dele, o evento 105 é escrito no mesmo registo, indicando que o agente no portal precisa de ser configurado para reportar ao mesmo espaço de trabalho que os agentes que comunicam com o portal.

Depois de concluir a configuração, reinicie o serviço **OMS Gateway** para aplicar as alterações. Caso contrário, o portal rejeitará agentes que tentem comunicar com o Log Analytics e reportará o evento 105 no registo de eventos OMS Gateway. Isto também acontecerá quando adicionar ou remover um espaço de trabalho da configuração do agente no servidor gateway.

Para obter informações relacionadas com o Automation Hybrid Runbook Worker, consulte [os recursos automate no seu datacenter ou nuvem utilizando o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configure Diretor de Operações, onde todos os agentes usam o mesmo servidor proxy

A configuração proxy do Gestor de Operações é automaticamente aplicada a todos os agentes que reportem ao Gestor de Operações, mesmo que a definição esteja vazia.  

Para utilizar o Gateway OMS para apoiar o Gestor de Operações, deve ter:

* O Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalado no servidor OMS Gateway e configurado com os mesmos espaços de trabalho do Log Analytics a que o seu grupo de gestão está configurado para reportar.
* Conectividade da Internet. Em alternativa, o OMS Gateway deve ser ligado a um servidor proxy que esteja ligado à internet.

> [!NOTE]
> Se especificar que não há valor para o portal, os valores em branco são empurrados para todos os agentes.
>

Se o seu grupo de gestão de Gestão de Operações estiver a registar-se pela primeira vez com um espaço de trabalho log Analytics, não verá a opção de especificar a configuração de proxy para o grupo de gestão na consola operações. Esta opção só está disponível se o grupo de gestão tiver sido registado no serviço.  

Para configurar a integração, atualize a configuração de proxy do sistema utilizando netsh no sistema onde está a executar a consola Operae e em todos os servidores de gestão do grupo de gestão. Siga estes passos.

1. Abra um pedido de comando elevado:

   a. Selecione **Iniciar** e introduzir **cmd**.  

   b. Clique no motivo de **comando** e selecione **Executar como administrador**.  

1. Introduza o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o `netsh winhttp reset proxy`Log Analytics, remova a alteração executando . Em seguida, na consola Operações, utilize a opção de **servidor proxy Configure** para especificar o servidor de gateway Log Analytics. 

1. Na consola do Gestor de Operações, em suite de gestão de **operações,** selecione **Connection**, e, em seguida, **selecione Configure Proxy Server**.

   ![Screenshot do Gestor de Operações, mostrando a seleção Configure Proxy Server](./media/gateway/scom01.png)

1. Selecione Utilize um servidor proxy para aceder ao Suite de Gestão de **Operações** e, em seguida, introduza o endereço IP do servidor de gateway Log Analytics ou endereço IP virtual do balanceor de carga. Tenha cuidado para começar `http://`com o prefixo .

   ![Screenshot do Gestor de Operações, mostrando o endereço do servidor proxy](./media/gateway/scom02.png)

1. Selecione **Concluir**. O seu grupo de gestão do Gestor de Operações está agora configurado para comunicar através do servidor de gateway para o serviço Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configure Diretor de Operações, onde agentes específicos usam um servidor proxy

Para ambientes grandes ou complexos, é de esperar que apenas servidores específicos (ou grupos) utilizem o servidor de gateway Log Analytics.  Para estes servidores, não é possível atualizar o agente do Gestor de Operações diretamente porque este valor é substituído pelo valor global para o grupo de gestão.  Em vez disso, anular a regra usada para empurrar estes valores.  

> [!NOTE] 
> Utilize esta técnica de configuração se pretender permitir vários servidores de gateway log Analytics no seu ambiente.  Por exemplo, pode exigir que servidores específicos de gateway log analytics sejam especificados numa base regional.
>

Para configurar servidores ou grupos específicos para utilizar o servidor de gateway Log Analytics: 

1. Abra a consola do Gestor de Operações e selecione o espaço de trabalho **da Autoria.**  
1. No espaço de trabalho de autoria, selecione **Regras**. 
1. Na barra de ferramentas do Gestor de Operações, selecione o botão **Scope.** Se este botão não estiver disponível, certifique-se de que selecionou um objeto, não uma pasta, no painel **de monitorização.** A caixa de diálogo **Scope Management Pack Objects** apresenta uma lista de classes, grupos ou objetos comuns direcionados. 
1. No campo **Procurar,** insira o Serviço de **Saúde** e selecione-o na lista. Selecione **OK**.  
1. Procure a Regra de **Definição de Procuração de Conselheiros**. 
1. Na barra de ferramentas do Gestor de Operações, selecione **Overrides** e, em seguida, aponte para **anular a Regra\Para um objeto específico de classe: Serviço de Saúde** e selecione um objeto da lista.  Ou criar um grupo personalizado que contenha o objeto de serviço de saúde dos servidores a que pretende aplicar esta sobreposição. Em seguida, aplique a sobreposição ao seu grupo personalizado.
1. Na caixa de diálogo **Override Properties,** adicione uma marca de verificação na coluna **Override** ao lado do parâmetro **WebProxyAddress.**  No campo **Sobreridegem Valor,** introduza o URL do servidor de gateway Log Analytics. Tenha cuidado para começar `http://`com o prefixo .  

    >[!NOTE]
    > Não precisas de ativar a regra. Já foi gerido automaticamente com uma sobreposição no pacote de gestão secure reference override do Microsoft System Center Advisor, que visa o Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Selecione um pacote de gestão da lista de pacotes de **gestão** de destino Select ou crie um novo pacote de gestão não selado selecionando **New**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurar para Automação Trabalhadores híbridos de runbook

Se tiver trabalhadores de resta híbrida automation no seu ambiente, siga estes passos para configurar a porta de entrada para apoiar os trabalhadores.

Consulte a secção de [configuração](../../automation/automation-hybrid-runbook-worker.md#network-planning) da sua secção de rede da documentação da Automação para encontrar o URL para cada região.

Se o seu computador estiver registado automaticamente como Um Trabalhador de Livro híbrido, por exemplo, se a solução de Gestão de Atualizações estiver ativada para um ou mais VMs, siga estes passos:

1. Adicione os URLs do serviço de dados de execução de trabalho à lista de anfitriões permitidos no gateway Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reiniciar o serviço de gateway Log Analytics utilizando o seguinte cmdlet PowerShell:`Restart-Service OMSGatewayService`

Se o seu computador estiver unido à Automação Azure utilizando o cmdlet de registo do Trabalhador do Livro De Execução Híbrido, siga estes passos:

1. Adicione o URL de registo do serviço do agente à lista de anfitriões permitido sintetizador na porta de entrada Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione os URLs do serviço de dados de execução de trabalho à lista de anfitriões permitidos no gateway Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reiniciar o serviço de gateway Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets powerShell úteis

Pode utilizar cmdlets para completar as tarefas para atualizar as definições de configuração do gateway Log Analytics. Antes de utilizar cmdlets, certifique-se de:

1. Instale o gateway Log Analytics (Instalador do Microsoft Windows).
1. Abra uma janela de consola PowerShell.
1. Importar o módulo digitando este comando:`Import-Module OMSGateway`
1. Se não ocorrer nenhum erro na etapa anterior, o módulo foi importado com sucesso e os cmdlets podem ser utilizados. Entrar`Get-Module OMSGateway`
1. Depois de utilizar os cmdlets para efazer alterações, reinicie o serviço OMS Gateway.

Um erro no passo 3 significa que o módulo não foi importado. O erro pode ocorrer quando a PowerShell não consegue encontrar o módulo. Pode encontrar o módulo na trajetória de instalação do OMS Gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (necessária) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço de procuração de retransmissão (a montante) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de utilizador<br> Palavra-passe (cadeia segura) |Define o endereço (e credencial) do proxy relé (a montante) |1. Definir um representante de retransmissão e credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Detete um proxy de retransmissão que não necessite de autenticação:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Limpe a definição de procuração de retransmissão:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o anfitrião atualmente permitido (apenas o anfitrião autorizado localmente, não automaticamente descarregado anfitriões permitidos) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hospedeiro (obrigatório) |Adiciona o anfitrião à lista permitida |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hospedeiro (obrigatório) |Remove o anfitrião da lista permitida |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o certificado de cliente sujeito à lista permitida |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o sujeito do certificado de cliente da lista permitida |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os sujeitos de certificado de cliente atualmente permitidos (apenas os sujeitos autorizados localmente configurados, não automaticamente descarregados sujeitos permitidos) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Resolução de problemas

Para recolher eventos registados pelo portal, deverá instalar o agente Log Analytics.

![Screenshot da lista de espectadores de eventos no log de gateway log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>IDs e descrições do evento de gateway log Analytics

The following table shows the event IDs and descriptions for Log Analytics gateway log events.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicação que não tenha identificação específica. |
| 401 |Configuração errada. Por exemplo, ouvirPorto = "texto" em vez de um inteiro. |
| 402 |Exceção na análise de mensagens de aperto de mão TLS. |
| 403 |Erro de rede. Por exemplo, não pode ligar-se ao servidor-alvo. |
| 100 |Informação geral. |
| 101 |O serviço começou. |
| 102 |O serviço parou. |
| 103 |Um comando HTTP CONNECT foi recebido do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |O servidor de destino não está na lista permitida, ou a porta de destino não é segura (443). <br> <br> Certifique-se de que o agente MMA no seu servidor OMS Gateway e os agentes que comunicam com o OMS Gateway estão ligados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERROR TcpConnection – Certificado de cliente inválido: CN=Gateway. <br><br> Certifique-se de que está a utilizar a versão OMS Gateway 1.0.395.0 ou superior. Certifique-se também de que o agente MMA no seu servidor OMS Gateway e os agentes que comunicam com o OMS Gateway estão ligados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Versão protocolo TLS/SSL não suportado.<br><br> O gateway Log Analytics suporta apenas TLS 1.0, TLS 1.1 e 1.2. Não suporta a SSL.|
| 107 |A sessão do TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho para recolher

A tabela seguinte mostra os contadores de desempenho disponíveis para o gateway Log Analytics. Utilize o Monitor de Desempenho para adicionar os contadores.

| **Nome** | **Descrição** |
| --- | --- |
| Log Analytics Gateway/Ative Client Connection |Número de ligações ativas à rede de clientes (TCP) |
| Log Analytics Gateway/Error Count |Número de erros |
| Gateway/Cliente Conectado de Log Analytics |Número de clientes conectados |
| Log Analytics Gateway/Contagem de rejeição |Número de rejeições devido a qualquer erro de validação do TLS |

![Screenshot da interface de gateway Log Analytics, mostrando contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência

Quando estiver inscrito no portal Azure, pode obter ajuda com o portal Log Analytics ou qualquer outro serviço ou funcionalidade Azure.
Para obter ajuda, selecione o ícone do ponto de interrogação no canto superior direito do portal e selecione **Novo pedido**de suporte . Em seguida, preencha o novo formulário de pedido de apoio.

![Screenshot de um novo pedido de apoio](./media/gateway/support.png)

## <a name="next-steps"></a>Passos seguintes

[Adicione fontes de dados](../../azure-monitor/platform/agent-data-sources.md) para recolher dados de fontes conectadas e armazenar os dados no seu espaço de trabalho Log Analytics.
