---
title: Ligar a solução de dados no Azure Monitor | Documentos da Microsoft
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
ms.openlocfilehash: fb75f1af2b644325ea614816637880a54eb09e80
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986129"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Ligar a solução de dados 2.0 (pré-visualização) no Azure Monitor

![Símbolo do Wire Data](media/wire-data/wire-data2-symbol.png)

Dados por fio são consolidados dados de rede e de desempenho recolhidos a partir de computadores conectados ao Windows e Linux ligados com o agente do Log Analytics, incluindo aquelas monitorizados pelo Operations Manager no seu ambiente. Os dados de rede são combinados com os seus outros dados de registo para ajudá-lo a correlacionar os dados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para além do agente do Log Analytics, a solução de dados por fio usa agentes de dependência da Microsoft que é instalado em computadores na sua infraestrutura de TI. Os Agentes de Dependência monitorizam os dados de rede enviados para e a partir dos seus computadores para níveis de rede 2-3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os vários protocolos e portas utilizados. Dados, em seguida, são enviados para o Azure Monitor através de agentes.  

>[!NOTE]
>Se já tiver implementado o mapa de serviço ou que estão Considerando o mapa de serviço ou [do Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), há um nova ligação métricas conjunto de dados que recolha e armazenam no Azure Monitor, que fornece informações comparáveis Wire data.

Por predefinição, o Azure Monitor regista os dados de CPU, memória, disco e dados de desempenho de rede dos contadores incorporadas no Windows e Linux, bem como outros contadores de desempenho que pode ser especificado. A recolha de redes e outros dados é realizada em tempo real para cada agente, incluindo sub-redes e protocolos ao nível das aplicações que estão a ser utilizados pelo computador.  O Wire Data analisa os dados de rede ao nível das aplicações e não na camada de transporte de TCP.  A solução não procura ACKs nem SYNs individuais.  Após a conclusão do handshake, é considerada uma ligação em direto e marcada como Ligada. Esta ligação permanece ativa enquanto ambos os lados aceitarem que o socket esteja aberto e que os dados possam circular entre ambos.  Assim que ambos os lados fecharem a ligação, será marcada como Desligada.  Desta forma, contabiliza apenas a largura de banda dos pacotes concluídos com êxito, não reporta os reenvios nem os pacotes com falhas.

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

No entanto, uma vez que está a ver metadados, não é necessariamente útil para a resolução de problemas aprofundada. Ligar a dados no Azure Monitor não são uma captura completa de dados de rede.  Não se destina a uma resolução de problemas avançada ao nível dos pacotes. A vantagem de utilizar o agente, em comparação com outros métodos de coleção, é que não tem de instalar aplicações, reconfigurar os comutadores de rede ou realizar configurações complicadas. Os dados por fio são simplesmente baseados em agentes — instala o agente num computador e este irá monitorizar o seu próprio tráfego de rede. Outra vantagem é quando pretende monitorizar as cargas de trabalho em execução nos fornecedores de cloud, no fornecedor de serviços de alojamento ou no Microsoft Azure, onde o utilizador não possui a camada de recursos de infraestrutura.

## <a name="connected-sources"></a>Origens ligadas

O Wire Data obtém os dados através do Agente de Dependência da Microsoft. O agente de dependência depende do agente do Log Analytics para as suas ligações para o Azure Monitor. Isso significa que um servidor têm de ter o agente do Log Analytics instalada e configurada com o agente de dependência. A tabela seguinte descreve as origens ligadas que a solução do Wire Data suporta.

| **Origem ligada** | **Suportado** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | O Wire Data analisa e recolhe dados de computadores de agentes do Windows. <br><br> Para além da [agente do Log Analytics para Windows](../../azure-monitor/platform/agent-windows.md), agentes do Windows exigem o agente de dependência da Microsoft. Veja os [sistemas operativos suportados](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Agentes do Linux | Sim | O Wire Data analisa e recolhe dados de computadores de agentes Linux.<br><br> Para além da [agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), agentes do Linux exigem o agente de dependência da Microsoft. Veja os [sistemas operativos suportados](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) para obter uma lista completa das versões do sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | O Wire Data analisa e recolhe dados dos agentes do Windows e Linux num [grupo de gestão do System Center Operations Manager](../../azure-monitor/platform/om-agents.md) ligado. <br><br> É necessária uma ligação direta do computador de agente do System Center Operations Manager para o Azure Monitor. |
| Conta de armazenamento do Azure | Não | O Wire Data recolhe dados dos computadores de agentes, pelo que não existem dados para o mesmo recolher do Armazenamento do Microsoft Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é utilizado pelo System Center Operations Manager e o Azure Monitor para recolher e enviar dados. Dependendo do contexto, o agente é chamado o agente do System Center Operations Manager, o agente do Log Analytics, o MMA ou o agente direto. O System Center Operations Manager e o Azure Monitor oferecem versões ligeiramente diferentes do MMA. Estas versões de cada relatório para o System Center Operations Manager, para o Azure Monitor ou a ambos.

No Linux, o agente do Log Analytics para Linux recolhe e envia dados para o Azure Monitor. Pode utilizar durante a transmissão de dados nos servidores com agentes ligados diretamente para o Azure Monitor, ou em servidores que estão a ligar para o Azure Monitor através de grupos de gestão do System Center Operations Manager.

O Agente de Dependência não transmite quaisquer dados propriamente ditos nem precisa de quaisquer alterações às firewalls ou portas. Os dados em dados por fio sempre são transmitidos pelo agente do Log Analytics para o Azure Monitor, diretamente ou através do gateway do Log Analytics.

![diagrama do agente](./media/wire-data/agents.png)

Se for um utilizador do System Center Operations Manager com um grupo de gestão ligado para o Azure Monitor:

- Nenhuma configuração adicional é necessária quando os agentes do System Center Operations Manager podem aceder à Internet para ligar ao Azure Monitor.
- Tem de configurar o gateway do Log Analytics para trabalhar com o System Center Operations Manager quando os agentes do System Center Operations Manager não é possível aceder ao Azure Monitor através da Internet.

Se seus computadores Windows ou Linux não podem ligar-se diretamente ao serviço, tem de configurar o agente do Log Analytics para se ligar ao Azure Monitor a utilizar o gateway do Log Analytics. Pode transferir o gateway do Log Analytics a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Precisa da oferta da solução de [Informações e Análise de Dados](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Se estiver a utilizar a versão anterior da solução Wire Data, terá primeiro de a remover. No entanto, todos os dados capturados através da solução Wire Data original ainda estarão disponíveis no Wire Data 2.0 e na pesquisa de registos.
- São precisos privilégios de administrador para instalar ou desinstalar o Agente de Dependência.
- O Agente de Dependência tem de estar instalado num computador com um sistema operativo de 64 bits.

### <a name="operating-systems"></a>Sistemas operativos

As secções seguintes listam os sistemas operativos suportados para o Agente de Dependência. O Wire Data não suporta arquiteturas de 32 bits de nenhum sistema operativo.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Ambiente de trabalho do Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com Kernel RHEL)

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões kernel não padrão, tais como PAE e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão _2.6.16.21-0.8-xen_ não é suportada.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados.
- O kernel do CentOSPlus kernel não é suportado.
- O Oracle Unbreakable Enterprise Kernel (UEK) é abordado numa secção posterior deste artigo.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux com Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Versão do SO** | **Versão de kernel** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Transferências do Agente de Dependência

| **Ficheiro** | **OS** | **Versão** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução Wire Data para as áreas de trabalho.

1. Ativar a solução Log Analytics da atividade do [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou utilizando o processo descrito [adicionar mnitoring soluções a partir da Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Instale o Agente de Dependência em todos os computadores a partir dos quais pretende obter dados. O Agente de Dependência pode monitorizar as ligações dos vizinhos imediatos, pelo que poderá não precisar de um agente em cada computador.

> [!NOTE]
> Não pode adicionar a versão anterior da solução Wire Data às novas áreas de trabalho. Se tiver a solução Wire Data original ativada, poderá continuar a utilizá-la. No entanto, para utilizar o Wire Data 2.0, deve primeiro remover a versão original.
> 
### <a name="install-the-dependency-agent-on-windows"></a>Instalar o Agente de Dependência no Windows

São precisos privilégios de administrador para instalar ou desinstalar o agente.

O Agente de Dependência é instalado em computadores com Windows através do ficheiro InstallDependencyAgent-Windows.exe. Se executar este ficheiro executável sem opções, ele iniciará um assistente que pode seguir para instalar de forma interativa.

Utilize os seguintes passos para instalar o Agente de Dependência em cada computador com o Windows:

1. Instalar o agente de Log Analytics, seguindo os passos na [recolher dados de computadores Windows alojados no seu ambiente](../../azure-monitor/platform/agent-windows.md).
2. Transfira o Agente de Dependência do Windows através da ligação da secção anterior e, em seguida, execute-o com o seguinte comando: `InstallDependencyAgent-Windows.exe`
3. Siga o assistente para instalar o agente.
4. Se o Agente de Dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Para agentes do Windows, o diretório do registo é %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Linha de comandos do Windows

Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista de sinalizadores de instalação, execute o instalador com o sinalizador /? da seguinte forma.

InstallDependencyAgent-Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obter uma lista das opções da linha de comandos. |
| <code>/S</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |

Por predefinição, os ficheiros do Agente de Dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar o Agente de Dependência no Linux

Para instalar ou configurar o agente é preciso acesso à raiz.

O Agente de Dependência é instalado em computadores Linux através do ficheiro InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração automática. Pode executar o ficheiro com _sh_ ou adicionar permissões de execução ao próprio ficheiro.

Utilize os seguintes passos para instalar o Agente de Dependência em cada computador Linux:

1. Instalar o agente de Log Analytics, seguindo os passos na [recolher dados de computadores Linux alojados no seu ambiente](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Transfira o Agente de Dependência do Linux através da ligação da secção anterior e, em seguida, instale-o como raiz com o seguinte comando: sh InstallDependencyAgent-Linux64.bin
3. Se o Agente de Dependência não conseguir iniciar, verifique os registos para obter informações de erro detalhadas. Para agentes do Linux, o diretório do registo é: /var/opt/microsoft/dependency-agent/log.

Para ver uma lista de sinalizadores de instalação, execute o programa de instalação com o sinalizador `-help` da seguinte forma.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>-help</code> | Obter uma lista das opções da linha de comandos. |
| <code>-s</code> | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| <code>--check</code> | Verificar as permissões e o sistema operativo, mas não instalar o agente. |

Os ficheiros do Agente de Dependência são colocados nos seguintes diretórios:

| **Ficheiros** | **Localização** |
| --- | --- |
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implementar facilmente o Agente de Dependência em vários servidores ao mesmo tempo, poderá ser útil utilizar um script. Pode utilizar os seguintes exemplos de script para transferir e instalar o Agente de Dependência no Windows ou Linux.

#### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

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

Para implementar o Agente de Dependência através da Configuração do Estado Pretendido, pode utilizar o módulo xPSDesiredStateConfiguration e um excerto de código, por exemplo:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Desinstalar o Agente de Dependência

Utilize as secções seguintes para ajudá-lo a remover o Agente de Dependência.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o Agente de Dependência no Windows

Um administrador pode desinstalar o Agente de Dependência do Windows através do Painel de Controlo.

Um administrador também pode executar o ficheiro %Programfiles%\Microsoft Dependency Agent\Uninstall.exe para desinstalar o Agente de Dependência.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o Agente de Dependência no Linux

Para desinstalar por completo o Agente de Dependência do Linux, tem de remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos com o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gestão

Quando o Wire Data é ativado numa área de trabalho do Log Analytics, é enviado um pacote de gestão de 300 KB para todos os servidores Windows nessa área de trabalho. Se estiver a utilizar agentes do System Center Operations Manager num [grupo de gestão ligado](../platform/om-agents.md), o pacote de gestão do Dependency Monitor será implementado a partir do System Center Operations Manager. Se os agentes estejam diretamente ligados, o Azure Monitor proporciona o pacote de gestão.

O pacote de gestão é designado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. O mesmo é escrito em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A origem de dados que o pacote de gestão utiliza é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Utilizar a solução

**Instalar e configurar a solução**

Utilize as seguintes informações para instalar e configurar a solução.

- A solução Wire Data adquire dados de computadores com o Windows Server 2012 R2, Windows 8.1 e sistemas operativos posteriores.
- Os computadores dos quais pretende adquirir dados por fio precisam do Microsoft .NET Framework 4.0 ou posterior.
- Adicionar a solução de ligar dados à sua área de trabalho do Log Analytics através do processo descrito em [adicionar soluções a partir da Galeria de soluções de monitorização](solutions.md). Não há nenhuma configuração adicional.
- Se pretender ver os dados por fio de uma solução específica, terá de ter a solução já adicionada à sua área de trabalho.

Depois de instalar os agentes e a solução, o mosaico Wire Data 2.0 é apresentado na área de trabalho.

![Mosaico Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Utilizar a solução Wire Data 2.0

Na página **Descrição Geral** da área de trabalho do Log Analytics no portal do Azure, clique no mosaico **Wire Data 2.0** para abrir o dashboard do Wire Data. O dashboard inclui os painéis na tabela seguinte. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo** na parte inferior do painel ou ao clicar no cabeçalho do painel.

| **Painel** | **Descrição** |
| --- | --- |
| Agentes que capturam tráfego de rede | Mostra o número de agentes que está a capturar o tráfego de rede e apresenta uma lista dos 10 principais computadores que estão a capturar o tráfego. Clique no número para executar uma pesquisa de registos para `WireData | summarize sum(TotalBytes) by Computer | take 500000`. Clique num computador na lista para executar uma pesquisa de registos para devolver o número total de bytes capturados. |
| Sub-redes Locais | Mostra o número de sub-redes locais que os agentes detetaram.  Clique no número para executar uma pesquisa de registos para `WireData | summarize sum(TotalBytes) by LocalSubnet`, que apresenta uma lista de todas as sub-redes com o número de bytes enviados através de cada um deles. Clique numa sub-rede na lista para executar uma pesquisa de registos para devolver o número total de bytes enviados pela sub-rede. |
| Protocolos de Nível das Aplicações | Mostra o número de protocolos ao nível das aplicações em utilização, tal como detetado pelos agentes. Clique no número para executar uma pesquisa de registos para `WireData | summarize sum(TotalBytes) by ApplicationProtocol`. Clique num protocolo para executar uma pesquisa de registos para devolver o número total de bytes com o protocolo. |

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
| Direção | Entrada ou saída |
| MaliciousIP | Endereço IP de uma origem maliciosa conhecida |
| Gravidade | Gravidade do potencial malware |
| RemoteIPCountry | País do endereço IP remoto |
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
