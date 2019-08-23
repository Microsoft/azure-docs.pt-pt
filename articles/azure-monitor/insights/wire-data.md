---
title: Solução de dados de transmissão em Azure Monitor | Microsoft Docs
description: Dados por fio são consolidados dados de rede e o desempenho dos computadores com agentes do Log Analytics. Os dados de rede são combinados com os seus dados de registo para ajudá-lo a correlacionar os dados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: 4e1324bb90c0b92daf709b695a0a8b3af9161c2e
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905431"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Solução Wire Data 2.0 (versão prévia) no Azure Monitor

![Símbolo do Wire Data](media/wire-data/wire-data2-symbol.png)

Dados por fio são consolidados dados de rede e de desempenho recolhidos a partir de computadores conectados ao Windows e Linux ligados com o agente do Log Analytics, incluindo aquelas monitorizados pelo Operations Manager no seu ambiente. Os dados de rede são combinados com os seus outros dados de registo para ajudá-lo a correlacionar os dados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para além do agente do Log Analytics, a solução de dados por fio usa agentes de dependência da Microsoft que é instalado em computadores na sua infraestrutura de TI. Os Agentes de Dependência monitorizam os dados de rede enviados para e a partir dos seus computadores para níveis de rede 2-3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os vários protocolos e portas utilizados. Em seguida, os dados são enviados para Azure Monitor usando agentes.  

>[!NOTE]
>Se você já implantou o Mapa do Serviço ou está considerando Mapa do Serviço ou [Azure monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), há um novo conjunto de dados de métricas de conexão coletado e armazenado no Azure monitor que fornece informações comparáveis para a transmissão de dados.

Por padrão, o Azure Monitor registra dados de CPU, memória, disco e dados de desempenho de rede de contadores criados no Windows e Linux, bem como outros contadores de desempenho que você pode especificar. A recolha de redes e outros dados é realizada em tempo real para cada agente, incluindo sub-redes e protocolos ao nível das aplicações que estão a ser utilizados pelo computador.  O Wire Data analisa os dados de rede ao nível das aplicações e não na camada de transporte de TCP.  A solução não procura ACKs nem SYNs individuais.  Após a conclusão do handshake, é considerada uma ligação em direto e marcada como Ligada. Esta ligação permanece ativa enquanto ambos os lados aceitarem que o socket esteja aberto e que os dados possam circular entre ambos.  Quando um dos lados fecha a conexão, ele é marcado como desconectado.  Desta forma, contabiliza apenas a largura de banda dos pacotes concluídos com êxito, não reporta os reenvios nem os pacotes com falhas.

Se tiver utilizado o [sFlow](http://www.sflow.org/) ou outro software com o [protocolo NetFlow da Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), as estatísticas e os dados apresentados dos dados por fio terão um aspeto familiar.

Alguns dos tipos de consultas da Pesquisa de registos incorporadas incluem:

- Agentes que fornecem dados por fio
- Endereço IP de agentes que fornecem os dados por fio
- Comunicações de saída por endereços IP
- Número de bytes enviados por protocolos de aplicações
- Número de bytes enviados por um serviço de aplicação
- Bytes recebidos por protocolos diferentes
- Total de bytes enviados e recebidos pela versão do IP
- Latência média das ligações que foram avaliadas como fiáveis
- Processos de computador que iniciaram ou receberam tráfego de rede
- Quantidade de tráfego de rede de um processo

Durante a pesquisa com dados por fio, pode filtrar e agrupar os dados para ver as informações sobre os agentes e os protocolos principais. Alternativamente, pode ver quando é que determinados computadores (endereços IP/endereços MAC) comunicaram uns com os outros, durante quanto tempo e a quantidade de dados que foi enviada — basicamente, pode ver os metadados sobre o tráfego de rede, os quais são baseados na procura.

No entanto, uma vez que está a ver metadados, não é necessariamente útil para a resolução de problemas aprofundada. Os dados de transmissão no Azure Monitor não são uma captura completa dos dados da rede.  Não se destina a uma resolução de problemas avançada ao nível dos pacotes. A vantagem de usar o agente, em comparação com outros métodos de coleção, é que você não precisa instalar dispositivos, reconfigurar os comutadores de rede ou executar configurações complicadas. Os dados por fio são simplesmente baseados em agentes — instala o agente num computador e este irá monitorizar o seu próprio tráfego de rede. Outra vantagem é quando pretende monitorizar as cargas de trabalho em execução nos fornecedores de cloud, no fornecedor de serviços de alojamento ou no Microsoft Azure, onde o utilizador não possui a camada de recursos de infraestrutura.

## <a name="connected-sources"></a>Origens ligadas

O Wire Data obtém os dados através do Agente de Dependência da Microsoft. A Dependency Agent depende do agente de Log Analytics para suas conexões a Azure Monitor. Isso significa que um servidor têm de ter o agente do Log Analytics instalada e configurada com o agente de dependência. A tabela seguinte descreve as origens ligadas que a solução do Wire Data suporta.

| **Origem ligada** | **Suportado** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | O Wire Data analisa e recolhe dados de computadores de agentes do Windows. <br><br> Para além da [agente do Log Analytics para Windows](../platform/agent-windows.md), agentes do Windows exigem o agente do Microsoft Dependency. Veja os [sistemas operativos suportados](vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Agentes do Linux | Sim | O Wire Data analisa e recolhe dados de computadores de agentes Linux.<br><br> Para além da [agente do Log Analytics para Linux](../learn/quick-collect-linux-computer.md), o agente do Microsoft Dependency necessitam de agentes do Linux. Veja os [sistemas operativos suportados](vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | O Wire Data analisa e recolhe dados dos agentes do Windows e Linux num [grupo de gestão do System Center Operations Manager](../platform/om-agents.md) ligado. <br><br> É necessária uma conexão direta do computador do agente de System Center Operations Manager para Azure Monitor. |
| Conta de armazenamento do Azure | Não | O Wire Data recolhe dados dos computadores de agentes, pelo que não existem dados para o mesmo recolher do Armazenamento do Microsoft Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é usado pelo System Center Operations Manager e Azure Monitor para coletar e enviar dados. Dependendo do contexto, o agente é chamado o agente do System Center Operations Manager, o agente do Log Analytics, o MMA ou o agente direto. System Center Operations Manager e Azure Monitor fornecem versões ligeiramente diferentes do MMA. Essas versões podem cada relatório para System Center Operations Manager, para Azure Monitor ou para ambos.

No Linux, o agente Log Analytics para Linux coleta e envia dados para Azure Monitor. Você pode usar dados de transmissão em servidores com agentes conectados diretamente a Azure Monitor ou em servidores que estão se conectando a Azure Monitor por meio de grupos de gerenciamento System Center Operations Manager.

O Dependency Agent não transmite nenhum dado e não requer nenhuma alteração em firewalls ou portas. Os dados em dados de transmissão sempre são transmitidos pelo agente de Log Analytics para Azure Monitor, diretamente ou por meio do gateway de Log Analytics.

![diagrama do agente](./media/wire-data/agents.png)

Se você for um usuário System Center Operations Manager com um grupo de gerenciamento conectado a Azure Monitor:

- Nenhuma configuração adicional é necessária quando seus agentes de System Center Operations Manager podem acessar a Internet para se conectarem ao Azure Monitor.
- Você precisa configurar o gateway de Log Analytics para trabalhar com System Center Operations Manager quando seus agentes de System Center Operations Manager não puderem acessar Azure Monitor pela Internet.

Se os computadores Windows ou Linux não puderem se conectar diretamente ao serviço, você precisará configurar o agente de Log Analytics para se conectar ao Azure Monitor usando o gateway de Log Analytics. Pode transferir o gateway do Log Analytics a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Precisa da oferta da solução de [Informações e Análise de Dados](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Se estiver a utilizar a versão anterior da solução Wire Data, terá primeiro de a remover. No entanto, todos os dados capturados através da solução Wire Data original ainda estarão disponíveis no Wire Data 2.0 e na pesquisa de registos.
- São necessários privilégios de administrador para instalar ou desinstalar o Dependency Agent.
- O agente de dependência deve ser instalado em um computador com um sistema operacional de 64 bits.

### <a name="operating-systems"></a>Sistemas operativos

As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent. O Wire Data não suporta arquiteturas de 32 bits de nenhum sistema operativo.

#### <a name="windows-server"></a>Windows Server

- Windows Server de 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Ambiente de trabalho do Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados
As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent no Linux.  

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões kernel não padrão, tais como PAE e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão de "2.6.16.21-0.8-xen" não é suportado.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão de kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4,18 * |
| Ubuntu 16.04.3 | o kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
| 12 SP2 | 4.4. * |
| 12 SP3 | 4.4. * |

### <a name="dependency-agent-downloads"></a>Downloads do agente de dependência

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução Wire Data para as áreas de trabalho.

1. Habilite a solução de Análise do Log de Atividades do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Instale o Dependency Agent em cada computador em que você deseja obter dados. O Dependency Agent pode monitorar conexões a vizinhos imediatos, portanto, talvez você não precise de um agente em todos os computadores.

> [!NOTE]
> Não pode adicionar a versão anterior da solução Wire Data às novas áreas de trabalho. Se tiver a solução Wire Data original ativada, poderá continuar a utilizá-la. No entanto, para utilizar o Wire Data 2.0, deve primeiro remover a versão original.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

São precisos privilégios de administrador para instalar ou desinstalar o agente.

O Dependency Agent é instalado em computadores que executam o Windows por meio de Installdependencyagent-Windows. exe. Se executar este ficheiro executável sem opções, ele iniciará um assistente que pode seguir para instalar de forma interativa.

Use as etapas a seguir para instalar o agente de dependência em cada computador que executa o Windows:

1. Instalar o agente de Log Analytics, seguindo os passos na [recolher dados de computadores Windows alojados no seu ambiente](../../azure-monitor/platform/agent-windows.md).
2. Baixe o agente de dependência do Windows usando o link na seção anterior e execute-o usando o seguinte comando:`InstallDependencyAgent-Windows.exe`
3. Siga o assistente para instalar o agente.
4. Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Para agentes do Windows, o diretório do registo é %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Linha de comandos do Windows

Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista de sinalizadores de instalação, execute o instalador com o sinalizador /? da seguinte forma.

InstallDependencyAgent-Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obter uma lista das opções da linha de comandos. |
| <code>/S</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |

Os arquivos para o agente de dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar o agente de dependência no Linux

Para instalar ou configurar o agente é preciso acesso à raiz.

O Dependency Agent é instalado em computadores Linux por meio de Installdependencyagent-Linux64. bin, um script de shell com um binário de extração automática. Pode executar o ficheiro com _sh_ ou adicionar permissões de execução ao próprio ficheiro.

Utilize os seguintes passos para instalar o agente de dependência em cada computador Linux:

1. Instalar o agente de Log Analytics, seguindo os passos na [recolher dados de computadores Linux alojados no seu ambiente](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Baixe o agente de dependência do Linux usando o link na seção anterior e, em seguida, instale-o como raiz usando o seguinte comando: SH Installdependencyagent-Linux64. bin
3. Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Para agentes do Linux, o diretório do registo é: /var/opt/microsoft/dependency-agent/log.

Para ver uma lista de sinalizadores de instalação, execute o programa de instalação com o sinalizador `-help` da seguinte forma.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>-help</code> | Obter uma lista das opções da linha de comandos. |
| <code>-s</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| <code>--check</code> | Verificar as permissões e o sistema operativo, mas não instalar o agente. |

Ficheiros para o agente de dependência são colocados nos seguintes diretórios:

| **Ficheiros** | **Localização** |
| --- | --- |
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o Dependency Agent em vários servidores de uma vez, ele ajuda a usar um script. Você pode usar os exemplos de script a seguir para baixar e instalar o Dependency Agent no Windows ou no Linux.

#### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script de shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configuração do Estado Pretendido

Para implantar o Dependency Agent por meio da configuração de estado desejado, você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código como o seguinte:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Desinstalar o Dependency Agent

Use as seções a seguir para ajudá-lo a remover o Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o Dependency Agent no Windows

Um administrador pode desinstalar o agente de dependência para o Windows através do painel de controlo.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe dependência para desinstalar o agente de dependência.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o Dependency Agent no Linux

Para desinstalar completamente o Dependency Agent do Linux, você deve remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos com o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gestão

Quando o Wire Data é ativado numa área de trabalho do Log Analytics, é enviado um pacote de gestão de 300 KB para todos os servidores Windows nessa área de trabalho. Se estiver a utilizar agentes do System Center Operations Manager num [grupo de gestão ligado](../platform/om-agents.md), o pacote de gestão do Dependency Monitor será implementado a partir do System Center Operations Manager. Se os agentes estiverem conectados diretamente, Azure Monitor entregará o pacote de gerenciamento.

O pacote de gestão é designado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. O mesmo é escrito em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A origem de dados que o pacote de gestão utiliza é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Utilizar a solução

Utilize as seguintes informações para instalar e configurar a solução.

- A solução Wire Data adquire dados de computadores com o Windows Server 2012 R2, Windows 8.1 e sistemas operativos posteriores.
- Os computadores dos quais pretende adquirir dados por fio precisam do Microsoft .NET Framework 4.0 ou posterior.
- Adicione a solução de dados de transmissão ao seu espaço de trabalho Log Analytics usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](solutions.md). Não há nenhuma configuração adicional.
- Se pretender ver os dados por fio de uma solução específica, terá de ter a solução já adicionada à sua área de trabalho.

Depois de instalar os agentes e a solução, o mosaico Wire Data 2.0 é apresentado na área de trabalho.

![Mosaico Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Utilizar a solução Wire Data 2.0

Na página **Descrição Geral** da área de trabalho do Log Analytics no portal do Azure, clique no mosaico **Wire Data 2.0** para abrir o dashboard do Wire Data. O dashboard inclui os painéis na tabela seguinte. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo** na parte inferior do painel ou ao clicar no cabeçalho do painel.

| **Painel** | **Descrição** |
| --- | --- |
| Agentes que capturam tráfego de rede | Mostra o número de agentes que está a capturar o tráfego de rede e apresenta uma lista dos 10 principais computadores que estão a capturar o tráfego. Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Clique num computador na lista para executar uma pesquisa de registos para devolver o número total de bytes capturados. |
| Sub-redes Locais | Mostra o número de sub-redes locais que os agentes detetaram.  Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, que apresenta uma lista de todas as sub-redes com o número de bytes enviados através de cada um deles. Clique numa sub-rede na lista para executar uma pesquisa de registos para devolver o número total de bytes enviados pela sub-rede. |
| Protocolos de Nível das Aplicações | Mostra o número de protocolos ao nível das aplicações em utilização, tal como detetado pelos agentes. Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Clique num protocolo para executar uma pesquisa de registos para devolver o número total de bytes com o protocolo. |

![Dashboard do Wire Data](./media/wire-data/wire-data-dash.png)

Pode utilizar o painel **Agentes a capturar tráfego de rede** para determinar a quantidade de largura de banda de rede que está a ser consumida pelos computadores. Este painel pode ajudá-lo a encontrar facilmente o computador _mais ativo_ no seu ambiente. Esses computadores podem estar sobrecarregados, a agir de forma anormal ou a utilizar mais recursos de rede do que o habitual.

![exemplo de pesquisa de registos](./media/wire-data/log-search-example01.png)

Da mesma forma, pode utilizar o painel **Sub-redes Locais** para determinar a quantidade de tráfego de rede que é movida através das suas sub-redes. Frequentemente, os utilizadores definem as sub-redes em torno das áreas críticas das suas aplicações. Este painel oferece uma vista dessas áreas.

![exemplo de pesquisa de registos](./media/wire-data/log-search-example02.png)

O painel **Protocolos ao Nível das Aplicações** é útil porque ajuda a saber quais os protocolos em utilização. Por exemplo, poderia esperar que o SSH não estivesse em utilização no seu ambiente de rede. A visualização das informações disponíveis no painel pode rapidamente confirmar ou refutar essa expectativa.

![exemplo de pesquisa de registos](./media/wire-data/log-search-example03.png)

Também é útil para saber se o tráfego do protocolo aumenta ou diminui ao longo do tempo. Por exemplo, se a quantidade de dados a ser transmitida por uma aplicação estiver a aumentar, pode ser algo a que deve estar atento ou que possa considerar significativo.

## <a name="input-data"></a>Dados de entrada

Os dados por fio recolhem metadados sobre o tráfego de rede com os agentes que tiver ativado. Cada agente envia dados em intervalos de cerca de 15 segundos.

## <a name="output-data"></a>Dados de saída

É criado um registo com um tipo de _WireData_ para cada tipo de dados de entrada. Os registos do WireData têm as propriedades mostradas na tabela seguinte:

| Propriedade | Descrição |
|---|---|
| Computador | Nome do computador onde os dados foram recolhidos |
| TimeGenerated | Hora do registo |
| LocalIP | Endereço IP do computador local |
| SessionState | Ligado ou desligado |
| ReceivedBytes | Quantidade de bytes recebidos |
| ProtocolName | Nome do protocolo de rede utilizado |
| IPVersion | Versão do IP |
| Direction | Entrada ou saída |
| MaliciousIP | Endereço IP de uma origem maliciosa conhecida |
| Severity | Gravidade do potencial malware |
| RemoteIPCountry | País/região do endereço IP remoto |
| ManagementGroupName | Nome do grupo de gestão do Operations Manager |
| SourceSystem | Origem onde os dados foram recolhidos |
| SessionStartTime | Hora de início da sessão |
| SessionEndTime | Hora de fim da sessão |
| LocalSubnet | Sub-rede onde os dados foram recolhidos |
| LocalPortNumber | Número de porta local |
| RemoteIP | Endereço IP remoto utilizado pelo computador remoto |
| RemotePortNumber | Número de porta utilizado pelo endereço IP remoto |
| SessionID | Um valor exclusivo que identifica a sessão de comunicação entre dois endereços IP |
| SentBytes | Número de bytes enviados |
| TotalBytes | Número total de bytes enviados durante a sessão |
| ApplicationProtocol | Tipo de protocolo de rede utilizado   |
| ProcessID | ID do processo do Windows |
| ProcessName | Caminho e nome do processo |
| RemoteIPLongitude | Valor da longitude do IP |
| RemoteIPLatitude | Valor da latitude do IP |

## <a name="next-steps"></a>Passos Seguintes

- [Registos de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para ver registos detalhados da pesquisa dos dados por fio.