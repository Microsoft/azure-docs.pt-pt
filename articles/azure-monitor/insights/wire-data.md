---
title: Solução de dados de fio em Azure Monitor | Microsoft Docs
description: Os dados de fio são dados consolidados de rede e desempenho de computadores com agentes do Log Analytics. Os dados de rede são combinados com os seus dados de registo para ajudá-lo a correlacionar os dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 5981a5f136d613ffcedda86797d807d2eecfab0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713631"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Solução de Dados de Fio 2.0 (Pré-visualização) no Monitor Azure

![Símbolo do Wire Data](media/wire-data/wire-data2-symbol.png)

Os dados de fio são dados consolidados de rede e desempenho recolhidos a partir de computadores ligados ao Windows e ligados ao Linux com o agente Log Analytics, incluindo os monitorizados pelo Gestor de Operações no seu ambiente. Os dados de rede são combinados com os seus outros dados de registo para ajudá-lo a correlacionar os dados.

Além do agente Log Analytics, a solução Wire Data utiliza agentes de dependência da Microsoft que instala em computadores na sua infraestrutura de TI. Os Agentes de Dependência monitorizam os dados de rede enviados para e a partir dos seus computadores para níveis de rede 2-3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os vários protocolos e portas utilizados. Os dados são então enviados para o Azure Monitor utilizando agentes.  

>[!NOTE]
>A solução Dados de Fio foi substituída pela [solução Mapa de Serviço.](../vm/service-map.md)  Ambos utilizam o agente Log Analytics e o agente De dependência para recolher dados de ligação à rede no Azure Monitor. 
> 
>Os clientes existentes que utilizam a solução Wire Data podem continuar a usá-la. Publicaremos orientações para uma linha temporal de migração para a mudança para o Mapa de Serviços.
>
>Os novos clientes devem instalar a [solução De Mapa](../vm/service-map.md) de Serviço ou [insights VM](../vm/vminsights-overview.md).  O conjunto de dados do Mapa de Serviço é comparável aos dados de fio.  Os insights VM incluem o conjunto de dados do Mapa de Serviço com dados de desempenho adicionais e funcionalidades para análise. 


Por predefinição, o Azure Monitor regista dados de CPU, memória, disco e dados de desempenho da rede a partir de contadores incorporados no Windows e Linux, bem como outros contadores de desempenho que pode especificar. A recolha de redes e outros dados é realizada em tempo real para cada agente, incluindo sub-redes e protocolos ao nível das aplicações que estão a ser utilizados pelo computador.  O Wire Data analisa os dados de rede ao nível das aplicações e não na camada de transporte de TCP.  A solução não procura ACKs nem SYNs individuais.  Após a conclusão do handshake, é considerada uma ligação em direto e marcada como Ligada. Esta ligação permanece ativa enquanto ambos os lados aceitarem que o socket esteja aberto e que os dados possam circular entre ambos.  Uma vez que ambos os lados fecham a ligação, está marcado como Desligado.  Desta forma, contabiliza apenas a largura de banda dos pacotes concluídos com êxito, não reporta os reenvios nem os pacotes com falhas.

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

No entanto, uma vez que está a ver metadados, não é necessariamente útil para a resolução de problemas aprofundada. Os dados de fio no Azure Monitor não são uma captura completa de dados de rede.  Não se destina a uma resolução de problemas avançada ao nível dos pacotes. A vantagem de utilizar o agente, em comparação com outros métodos de recolha, é que não é preciso instalar aparelhos, reconfigurar os interruptores de rede ou executar configurações complicadas. Os dados por fio são simplesmente baseados em agentes — instala o agente num computador e este irá monitorizar o seu próprio tráfego de rede. Outra vantagem é quando pretende monitorizar as cargas de trabalho em execução nos fornecedores de cloud, no fornecedor de serviços de alojamento ou no Microsoft Azure, onde o utilizador não possui a camada de recursos de infraestrutura.

## <a name="connected-sources"></a>Origens ligadas

O Wire Data obtém os dados através do Agente de Dependência da Microsoft. O Agente de Dependência depende do agente Log Analytics para as suas ligações ao Azure Monitor. Isto significa que um servidor deve ter o agente Log Analytics instalado e configurado com o agente Dependency. A tabela seguinte descreve as origens ligadas que a solução do Wire Data suporta.

| **Origem ligada** | **Suportado** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | O Wire Data analisa e recolhe dados de computadores de agentes do Windows. <br><br> Além do [agente Log Analytics para windows,](../agents/agent-windows.md)os agentes do Windows requerem o agente Microsoft Dependency. Veja os [sistemas operativos suportados](../vm/vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Agentes do Linux | Sim | O Wire Data analisa e recolhe dados de computadores de agentes Linux.<br><br> Além do [agente Log Analytics para o Linux,](../vm/quick-collect-linux-computer.md)os agentes Linux requerem o agente da Microsoft Dependency. Veja os [sistemas operativos suportados](../vm/vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | O Wire Data analisa e recolhe dados dos agentes do Windows e Linux num [grupo de gestão do System Center Operations Manager](../agents/om-agents.md) ligado. <br><br> É necessária uma ligação direta do computador do Gestor de Operações do Centro de Sistema ao Monitor Azure. |
| Conta de armazenamento do Azure | Não | O Wire Data recolhe dados dos computadores de agentes, pelo que não existem dados para o mesmo recolher do Armazenamento do Microsoft Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é utilizado tanto pelo Gestor de Operações do System Center como pelo Azure Monitor para recolher e enviar dados. Dependendo do contexto, o agente é chamado de Agente de Operações do Centro de Sistema, Agente De Registo, MMA ou Agente Direto. System Center Operations Manager e Azure Monitor fornecem versões ligeiramente diferentes do MMA. Estas versões podem reportar-se ao Gestor de Operações do Centro de Sistema, ao Azure Monitor ou a ambas.

No Linux, o agente Log Analytics da Linux recolhe e envia dados para o Azure Monitor. Pode utilizar dados de fio em servidores com agentes diretamente ligados ao Azure Monitor ou em servidores que estão a ligar-se ao Azure Monitor através de grupos de gestão do Gestor de Operações do Centro de Sistema.

O agente de Dependência não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas. Os dados em Dados de Fio são sempre transmitidos pelo agente Log Analytics ao Azure Monitor, quer diretamente quer através do gateway Log Analytics.

![diagrama do agente](./media/wire-data/agents.png)

Se for um utilizador do Gestor de Operações do System Center com um grupo de gestão ligado ao Azure Monitor:

- Não é necessária nenhuma configuração adicional quando os agentes do Gestor de Operações do System Center podem aceder à internet para se ligarem ao Azure Monitor.
- É necessário configurar a porta de entrada do Log Analytics para trabalhar com o Gestor de Operações do Centro de Sistema quando os agentes do System Center Operations Manager não conseguem aceder ao Monitor do Azure através da internet.

Se os seus computadores Windows ou Linux não puderem ligar-se diretamente ao serviço, é necessário configurar o agente Log Analytics para se ligar ao Azure Monitor utilizando o gateway Do Registo Analytics. Pode descarregar o gateway Do Log Analytics a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Precisa da oferta da solução de [Informações e Análise de Dados](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Se estiver a utilizar a versão anterior da solução Wire Data, terá primeiro de a remover. No entanto, todos os dados capturados através da solução Wire Data original ainda estarão disponíveis no Wire Data 2.0 e na pesquisa de registos.
- Os privilégios do administrador são necessários para instalar ou desinstalar o agente Dependency.
- O agente Desadependido deve ser instalado num computador com um sistema operativo de 64 bits.

### <a name="operating-systems"></a>Sistemas operativos

As secções seguintes listam os sistemas operativos suportados para o agente Desacurado. O Wire Data não suporta arquiteturas de 32 bits de nenhum sistema operativo.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
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
As secções seguintes listam os sistemas operativos suportados para o agente de dependência do Linux.  

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões kernel não padrão, tais como PAE e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, não é suportado um sistema com a cadeia de desbloqueio "2.6.16.21-0.8-xen".
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

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
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | núcleo 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>Servidor empresarial SUSE Linux 11

| Versão do SO | Versão de kernel
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>Servidor empresarial SUSE Linux 12

| Versão do SO | Versão de kernel
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Downloads de agentes de dependência

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3B8B1C3DEE7512690E5A65F1DFC41B43831543B5C0FCCE8390F2282C |



## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução Wire Data para as áreas de trabalho.

1. Ativar a solução Activity Log Analytics a partir do [Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou utilizando o processo descrito em [soluções de monitorização adicionar a partir da Galeria de Soluções.](../insights/solutions.md)
2. Instale o agente Desadependição em cada computador onde pretende obter dados. O agente da Dependência pode monitorizar as ligações com vizinhos imediatos, por isso pode não precisar de um agente em todos os computadores.

> [!NOTE]
> Não pode adicionar a versão anterior da solução Wire Data às novas áreas de trabalho. Se tiver a solução Wire Data original ativada, poderá continuar a utilizá-la. No entanto, para utilizar o Wire Data 2.0, deve primeiro remover a versão original.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

São precisos privilégios de administrador para instalar ou desinstalar o agente.

O agente Dependency está instalado em computadores que executam o Windows através de InstallDependencyAgent-Windows.exe. Se executar este ficheiro executável sem opções, ele iniciará um assistente que pode seguir para instalar de forma interativa.

Utilize os seguintes passos para instalar o agente Dependency em cada computador que executa o Windows:

1. Instale o agente Log Analytics seguindo os passos na [Recolha de dados dos computadores Windows alojados no seu ambiente](../agents/agent-windows.md).
2. Descarregue o agente de dependência do Windows utilizando o link na secção anterior e, em seguida, execute-o utilizando o seguinte comando: `InstallDependencyAgent-Windows.exe`
3. Siga o assistente para instalar o agente.
4. Se o agente de dependência não arrancar, verifique se os registos são informativo de erro. Para agentes do Windows, o diretório do registo é %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Linha de comandos do Windows

Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista de sinalizadores de instalação, execute o instalador com o sinalizador /? da seguinte forma.

InstallDependencyAgent-Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obter uma lista das opções da linha de comandos. |
| <code>/S</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |

Os ficheiros do agente de dependência do Windows são colocados em C:\Program Files\Microsoft Dependency agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência no Linux

Para instalar ou configurar o agente é preciso acesso à raiz.

O agente De dependência é instalado em computadores Linux através do InstallDependencyAgent-Linux64.bin, um script de concha com um binário auto-extrator. Pode executar o ficheiro com _sh_ ou adicionar permissões de execução ao próprio ficheiro.

Utilize os seguintes passos para instalar o agente Desadependição em cada computador Linux:

1. Instale o agente Log Analytics seguindo os passos na [Recolha de dados dos computadores Linux alojados no seu ambiente](../vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Descarregue o agente de dependência Linux utilizando o link na secção anterior e instale-o como raiz utilizando o seguinte comando: sh InstallDependencyAgent-Linux64.bin
3. Se o agente de dependência não arrancar, verifique se os registos são informativo de erro. Para agentes do Linux, o diretório do registo é: /var/opt/microsoft/dependency-agent/log.

Para ver uma lista de sinalizadores de instalação, execute o programa de instalação com o sinalizador `-help` da seguinte forma.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>-help</code> | Obter uma lista das opções da linha de comandos. |
| <code>-s</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| <code>--check</code> | Verificar as permissões e o sistema operativo, mas não instalar o agente. |

Os ficheiros do agente de dependência são colocados nos seguintes diretórios:

| **Ficheiros** | **Localização** |
| --- | --- |
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente Dependency em muitos servidores ao mesmo tempo, ajuda a usar um script. Pode utilizar os seguintes exemplos de script para descarregar e instalar o agente Dependency no Windows ou no Linux.

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

Para implementar o agente de dependência através da configuração do estado desejado, pode utilizar o módulo xPSDesiredStateConfiguration e um pouco de código como o seguinte:

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

### <a name="uninstall-the-dependency-agent"></a>Desinstalar o agente de dependência

Utilize as seguintes secções para ajudá-lo a remover o agente Dependency.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o agente de dependência no Windows

Um administrador pode desinstalar o agente de dependência do Windows através do Painel de Controlo.

Um administrador também pode executar %Programfiles%\Microsoft Dependency Agent\Uninstall.exe de desinstalar o agente Dependency.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o agente de dependência no Linux

Para desinstalar completamente o agente de Dependência do Linux, deve remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos com o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gestão

Quando o Wire Data é ativado numa área de trabalho do Log Analytics, é enviado um pacote de gestão de 300 KB para todos os servidores Windows nessa área de trabalho. Se estiver a utilizar agentes do System Center Operations Manager num [grupo de gestão ligado](../agents/om-agents.md), o pacote de gestão do Dependency Monitor será implementado a partir do System Center Operations Manager. Se os agentes estiverem diretamente ligados, o Azure Monitor entrega o pacote de gestão.

O pacote de gestão é designado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. O mesmo é escrito em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A origem de dados que o pacote de gestão utiliza é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Utilizar a solução

Utilize as seguintes informações para instalar e configurar a solução.

- A solução Wire Data adquire dados de computadores com o Windows Server 2012 R2, Windows 8.1 e sistemas operativos posteriores.
- Os computadores dos quais pretende adquirir dados por fio precisam do Microsoft .NET Framework 4.0 ou posterior.
- Adicione a solução De Dados de Fio ao seu espaço de trabalho Log Analytics utilizando o processo descrito nas [soluções de monitorização Add da Galeria de Soluções](../insights/solutions.md). Não há nenhuma configuração adicional.
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

![Screenshot dos Agentes que captam a lâmina de tráfego de rede no painel De dados de arame 2.0 mostrando a largura de banda da rede consumida por cada computador.](./media/wire-data/log-search-example01.png)

Da mesma forma, pode utilizar o painel **Sub-redes Locais** para determinar a quantidade de tráfego de rede que é movida através das suas sub-redes. Frequentemente, os utilizadores definem as sub-redes em torno das áreas críticas das suas aplicações. Este painel oferece uma vista dessas áreas.

![Screenshot da lâmina de sub-redes local no painel De dados de fio 2.0 mostrando a largura de banda da rede consumida por cada LocalSubnet.](./media/wire-data/log-search-example02.png)

O painel **Protocolos ao Nível das Aplicações** é útil porque ajuda a saber quais os protocolos em utilização. Por exemplo, poderia esperar que o SSH não estivesse em utilização no seu ambiente de rede. A visualização das informações disponíveis no painel pode rapidamente confirmar ou refutar essa expectativa.

![Screenshot da lâmina de protocolos de nível de aplicação no painel de instrumentos De fio 2.0 mostrando a largura de banda da rede consumida por cada protocolo.](./media/wire-data/log-search-example03.png)

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
| Direção | Entrada ou saída |
| MaliciousIP | Endereço IP de uma origem maliciosa conhecida |
| Gravidade | Gravidade do potencial malware |
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

## <a name="next-steps"></a>Passos seguintes

- [Registos de pesquisa](../logs/log-query-overview.md) para ver registos detalhados da pesquisa dos dados por fio.

