---
title: Configurar o mapa de serviço no Azure | Documentos da Microsoft
description: O Mapa de Serviços é uma solução no Azure que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes para implementar o mapa de serviço no seu ambiente e utilizá-lo numa variedade de cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: magoedte
ms.openlocfilehash: 26da504188a9060dbbb35330dbd8604bf5fe5e1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995134"
---
# <a name="configure-service-map-in-azure"></a>Configurar o mapa de serviço no Azure
O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Pode usá-lo para ver os servidores, como considerá-los – sistemas interconectados que fornecem serviços críticos. Mapa de serviço mostra ligações entre servidores, processos e as portas em qualquer arquitetura ligado a TCP sem qualquer configuração necessária, que não seja a instalação de um agente.

Este artigo descreve os detalhes de configuração de agentes e integração de mapa de serviço. Para obter informações sobre como utilizar o mapa de serviço, consulte [utilizar a solução mapa de serviço no Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Regiões do Azure suportadas
Mapa de serviço está atualmente disponível nas seguintes regiões do Azure:
- EUA Leste
- EUA Centro-Oeste
- Canadá Central
- Reino Unido Sul
- Europa Ocidental
- Sudeste Asiático

## <a name="supported-windows-operating-systems"></a>Sistemas de operativos do Windows
A seção a seguir lista os sistemas operativos suportados para o agente de dependência no Windows. 

>[!NOTE]
>Mapa de serviço oferece suporte a plataformas de 64 bits apenas.
>

### <a name="windows-server"></a>Windows Server
- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Ambiente de trabalho do Windows
- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados
A seção a seguir lista os sistemas operativos suportados para o agente de dependência no Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com o Kernel do RHEL).  

- Apenas as versões de kernel padrão e SMP Linux são suportadas.
- As versões kernel não padrão, tais como PAE e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão de "2.6.16.21-0.8-xen" não é suportado.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados.
- CentOSPlus kernel é suportada.
- O Oracle Unbreakable Enterprise Kernel (UEK) é abordado numa secção posterior deste artigo.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão de kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | o kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
| 11 SP4 | 3.0.* |

### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
| 12 SP2 | 4.4. * |
| 12 SP3 | 4.4. * |

## <a name="dependency-agent-downloads"></a>Downloads do agente de dependência

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>Origens ligadas
Mapa de serviço obtém seus dados do agente do Microsoft Dependency. O agente de dependência depende do agente do Log Analytics para as suas ligações para o Log Analytics. Isso significa que um servidor têm de ter o agente do Log Analytics instalada e configurada com o agente de dependência.  A tabela seguinte descreve as origens ligadas que suporte a solução mapa de serviço.

| Origem ligada | Suportadas | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Mapa de serviço analisa e recolhe dados a partir de computadores Windows. <br><br>Para além da [agente do Log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), agentes do Windows exigem o agente do Microsoft Dependency. Ver os sistemas operativos suportados para uma lista completa das versões de sistema operativo. |
| Agentes do Linux | Sim | Mapa de serviço analisa e recolhe dados de computadores Linux. <br><br>Para além da [agente do Log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), o agente do Microsoft Dependency necessitam de agentes do Linux. Ver os sistemas operativos suportados para uma lista completa das versões de sistema operativo. |
| Grupo de gestão do System Center Operations Manager | Sim | Mapa de serviço analisa e recolhe dados de agentes do Windows e Linux no conectado [grupo de gestão do System Center Operations Manager](../../azure-monitor/platform/om-agents.md). <br><br>É preciso uma ligação direta do computador do agente do System Center Operations Manager ao Log Analytics. |
| Conta de armazenamento do Azure | Não | Mapa de serviço recolhe dados a partir de computadores de agente, portanto, não há nenhum dado com ele para coletar do armazenamento do Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é utilizada pelo System Center Operations Manager e do Log Analytics para recolher e enviar dados de monitorização. (Este agente é chamado do agente do System Center Operations Manager, o agente do Log Analytics, o MMA ou o agente direto, dependendo do contexto.) System Center Operations Manager e do Log Analytics oferecem versões de outra fora da caixa do MMA. Estas versões podem reportar ao System Center Operations Manager, ao Log Analytics ou a ambos.  

No Linux, o agente do Log Analytics para Linux recolhe e envia dados para o Log Analytics de monitorização. Pode utilizar o mapa de serviço em servidores com agentes do Log Analytics, ligados diretamente ao serviço ou que estão a enviar relatórios a um grupo de gestão do Operations Manager integrado com o Log Analytics.  

Neste artigo, faremos referência a todos os agentes, se o Linux ou Windows ligado a um grupo de gestão do System Center Operations Manager ou diretamente ao Log Analytics, como o *agente do Log Analytics*. 

O agente do mapa de serviço não transmitir todos os dados em si, e não requer alterações às firewalls ou portas. Os dados no mapa de serviço sempre são transmitidos pelo agente do Log Analytics para o serviço do Log Analytics, diretamente ou através do gateway do Log Analytics.

![Agentes de mapa de serviço](media/service-map-configure/agents.png)

Se for um cliente do System Center Operations Manager com um grupo de gestão ligado ao Log Analytics:

- Se os agentes do System Center Operations Manager podem aceder à Internet para ligar ao Log Analytics, é necessária nenhuma configuração adicional.  
- Se os agentes do System Center Operations Manager não é possível aceder ao Log Analytics através da Internet, terá de configurar o gateway do Log Analytics para trabalhar com o System Center Operations Manager.
  
Se seus computadores Windows ou Linux não podem ligar-se diretamente ao serviço, tem de configurar o agente do Log Analytics para se ligar à área de trabalho do Log Analytics a utilizar o gateway. Para obter mais informações sobre como implementar e configurar o gateway do Log Analytics, consulte [ligar computadores sem acesso à Internet através do gateway do Log Analytics](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Pacotes de gestão
Quando o mapa de serviço é ativado numa área de trabalho do Log Analytics, um pacote de gestão de 300 KB é encaminhado para todos os servidores do Windows na área de trabalho. Se estiver a utilizar agentes do System Center Operations Manager num [grupo de gestão ligado](../../azure-monitor/platform/om-agents.md), o pacote de gestão do mapa de serviço é implementado a partir do System Center Operations Manager. 

O pacote de gestão é designado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Ele é escrito para %Programfiles%\Microsoft Monitoring Agent\Agent\Health serviço State\Management Packs\. A origem de dados que utiliza o pacote de gestão é % Program files%\Microsoft monitorização Agent\Agent\Health serviço State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Recolha de dados
Pode esperar que cada agente para transmitir aproximadamente 25 MB por dia, dependendo da complexidade de suas dependências de sistema são. Cada agente envia dados de dependência de mapa de serviço a cada 15 segundos.  

O agente de dependência geralmente consome 0,1% da memória do sistema e 0,1% do sistema da CPU.

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico
A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço do mapa de serviço. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço de mapa de serviço. Os dados incluem informações sobre a configuração de software, como o sistema operativo e versão. Ele também inclui endereço IP, o nome DNS e o nome da estação de trabalho a fim de fornecer capacidades de resolução de problemas exatas e eficientes. Não recolhemos nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e a utilização, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Instalação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="azure-vm-extension"></a>Extensão de VM do Azure
Existe uma extensão disponível para Windows (DependencyAgentWindows) e o Linux (DependencyAgentLinux) e pode facilmente implementar o agente de dependência para o seu VMs do Azure com um [extensão de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Com a extensão de VM do Azure, pode implementar o agente de dependência para o Windows e VMs do Linux utilizar um script de PowerShell ou diretamente na VM com um modelo do Azure Resource Manager.  Se implementar o agente com a extensão de VM do Azure, os agentes são automaticamente atualizados para a versão mais recente.

Para implementar a extensão de VM do Azure com o PowerShell, pode utilizar o exemplo seguinte:

```PowerShell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Uma forma mais fácil para garantir que o agente de dependência é instalado em suas VMs é incluir o agente no seu modelo do Azure Resource Manager.  O seguinte exemplo de código JSON pode ser adicionado para o *recursos* secção do seu modelo.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instale o agente de dependência no Microsoft Windows
O agente de dependência pode ser instalado manualmente em computadores Windows ao executar `InstallDependencyAgent-Windows.exe`. Se executar esse arquivo executável sem quaisquer opções, inicia um Assistente de configuração que pode seguir para instalar interativamente.  

>[!NOTE]
>São precisos privilégios de administrador para instalar ou desinstalar o agente.

Utilize os seguintes passos para instalar o agente de dependência em cada computador do Windows:

1.  Instalar o agente do Log Analytics para Windows, siga um dos métodos descritos [descrição geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Transferir o agente do Windows e executá-lo utilizando o seguinte comando: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Siga o Assistente de configuração para instalar o agente.
4.  Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Em agentes do Windows, o diretório de registo é %Programfiles%\Microsoft Agent\logs de dependência. 

#### <a name="windows-command-line"></a>Linha de comandos do Windows
Utilize as opções da tabela seguinte para instalar a partir de uma linha de comandos. Para ver uma lista de sinalizadores de instalação, execute o instalador com o sinalizador /? da seguinte forma.

    InstallDependencyAgent-Windows.exe /?

| Sinalizador | Descrição |
|:--|:--|
| /? | Obter uma lista das opções da linha de comandos. |
| /S | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |

Por predefinição, os arquivos para o agente de dependência do Windows são colocados no agente de dependência do C:\Program Files\Microsoft.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar o agente de dependência no Linux
O agente de dependência é instalado em computadores com Linux do `InstallDependencyAgent-Linux64.bin`, um script de shell com um binário auto-extraível. Pode executar o ficheiro utilizando `sh` ou adicionar permissões para o próprio ficheiro de execução.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.

Utilize os seguintes passos para instalar o agente de dependência em cada computador Linux:

1.  Instalar o agente de Log Analytics, um dos métodos descritos a seguir [descrição geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Instale o agente de dependência de Linux como raiz, executando o seguinte comando:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Em agentes do Linux, o diretório de registo é /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute a instalação do programa com a opção - ajuda sinalizador da seguinte forma.

    InstallDependencyAgent-Linux64.bin -help

| Sinalizador | Descrição |
|:--|:--|
| -Ajuda | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| -Verifique | Verificar as permissões e o sistema operativo, mas não instalar o agente. |

Ficheiros para o agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação
Para implementar facilmente o agente de dependência em vários servidores ao mesmo tempo, o exemplo de script a seguir é fornecido para transferir e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de shell para Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```
## <a name="desired-state-configuration"></a>Configuração do Estado Pretendido
Para implementar o agente de dependência com a Desired State Configuration (DSC), pode utilizar o módulo de xPSDesiredStateConfiguration com o código de exemplo seguinte:

```
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Remover o agente de dependência
### <a name="uninstall-agent-on-windows"></a>Desinstalar o agente no Windows
Um administrador pode desinstalar o agente de dependência para o Windows através do painel de controlo.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe dependência para desinstalar o agente de dependência.

### <a name="uninstall-agent-on-linux"></a>Desinstalar o agente no Linux
Pode desinstalar o agente de dependência do Linux com o seguinte comando.

RHEL, CentOs ou Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Resolução de problemas
Se tiver quaisquer problemas de instalação ou execução de mapa de serviço, nesta secção pode ajudar. Se ainda não é possível resolver o problema, entre em contato com Support da Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação do agente de dependência
#### <a name="installer-prompts-for-a-reboot"></a>Pedidos de instalador de um reinício
O agente de dependência *geralmente* não requer um reinício após a instalação ou desinstalação. No entanto, em certos casos raros, Windows Server exige uma reinicialização para continuar com uma instalação. Isto acontece quando uma dependência, normalmente, o Microsoft Visual C++ Redistributable, requer uma reinicialização devido a um arquivo bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Mensagem "não é possível instalar o agente de dependência: Não foi possível instalar as bibliotecas de tempo de execução do Studio Visual (código = [code_number]) "é apresentada

O agente do Microsoft Dependency baseia-se as bibliotecas de tempo de execução do Microsoft Visual Studio. Obterá uma mensagem se existe um problema durante a instalação das bibliotecas. 

Os programas de instalação de biblioteca de tempo de execução criam registos na pasta %LOCALAPPDATA%\temp. O ficheiro é dd_vcredist_arch_yyyymmddhhmmss.log, onde *arch* é de "x86" ou "amd64" e *aaaammddhhmmss* é a data e hora (relógio de 24 horas) quando o registo foi criado. O registo fornece detalhes sobre o problema que está a bloquear a instalação.

Poderá ser útil instalar o [bibliotecas de tempo de execução mais recente](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -se primeiro.

A tabela seguinte apresenta uma lista de números de código e resoluções sugeridas.

| Código | Descrição | Resolução |
|:--|:--|:--|
| 0x17 | O instalador de biblioteca exige uma atualização do Windows que não foi instalada. | Procure no registo do instalador mais recente da biblioteca.<br><br>Se uma referência a "Windows8.1-KB2999226-x64.msu" seguida de uma linha de "erro 0x80240017: Falha ao executar o pacote MSU,"não tem os pré-requisitos para instalar KB2999226. Siga as instruções na secção pré-requisitos [Universal o tempo de execução do C em Windows](https://support.microsoft.com/kb/2999226). Poderá ter de executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Execute o instalador do agente Microsoft Dependency novamente. |

### <a name="post-installation-issues"></a>Problemas de pós-instalação
#### <a name="server-doesnt-appear-in-service-map"></a>Servidor não aparece no mapa de serviço
Se a instalação do agente de dependência foi concluída com êxito, mas não a vir o seu servidor na solução mapa de serviço:
* O agente de dependência é instalado com êxito? Pode confirmar isto, verificando se o serviço está instalado e em execução.<br><br>
**Windows**: Procure o serviço com o nome "Agente Microsoft Dependency."<br>
**Linux**: Procure o processo em execução "microsoft--agente de dependência."

* Está o [escalão do Operations Management Suite/Log Analytics de preço gratuito](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Permite que o plano gratuito para até cinco servidores exclusivos do mapa de serviço. Todos os servidores subsequentes não aparecerão no mapa de serviço, mesmo que os cinco anterior já não estão a enviar dados.

* É o seu registo de envio do servidor e dados de desempenho para o Log Analytics? Vá para a pesquisa de registos e execute a seguinte consulta para o seu computador: 

    Utilização | onde computador = = "nome do computador" | resumir sum(Quantity), any(QuantityUnit) por tipo de dados

Obteve uma variedade de eventos nos resultados? Os dados são recentes? Se assim for, o agente do Log Analytics está operando corretamente e ao comunicar com o Log Analytics. Caso contrário, verifique o agente no seu servidor: [Agente de análise de registo para resolução de problemas do Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) ou [agente do Log Analytics para resolução de problemas do Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servidor é apresentado no mapa de serviço, mas não tem nenhum processo
Se vir o seu servidor no mapa de serviço, mas ele não tem processo ou ligação de dados, que indica que o agente de dependência é instalado e em execução, mas não carregou o driver do kernel. 

Verifique o ficheiro de C:\Program Files\Microsoft dependência Agent\logs\wrapper.log (Windows) ou o ficheiro de /var/opt/microsoft/dependency-agent/log/service.log (Linux). As última linhas do ficheiro devem indicar por que não carregou o kernel. Por exemplo, o kernel poderá não ser suportado no Linux, se atualizou o kernel.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [utilizar o mapa de serviço]( service-map.md) depois foi implementado e configurado.
