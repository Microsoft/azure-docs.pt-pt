---
title: Ativar o Azure Monitor para um ambiente híbrido
description: Este artigo descreve como permite insights VM para um ambiente híbrido em nuvem que contém uma ou mais máquinas virtuais.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c3c8495b7355ee5d9ee8c28b4e0097a0080964d4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046644"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>Ativar insights em VM para uma máquina virtual híbrida
Este artigo descreve como permitir insights de VM para uma máquina virtual fora de Azure, incluindo no local e outros ambientes em nuvem.

> [!IMPORTANT]
> O método recomendado para permitir VMs híbridos é primeiro permitir [o Arco Azure para servidores para](../../azure-arc/servers/overview.md) que os VMs possam ser ativados para insights VM usando processos semelhantes aos VMs Azure. Este artigo descreve como embarcar VMs híbridos se optar por não utilizar o Arco Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho Log Analytics](./vminsights-configure-workspace.md).
- Consulte [sistemas operativos suportados](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 


## <a name="overview"></a>Descrição Geral
Máquinas virtuais fora de Azure requerem o mesmo agente log analytics e agente de dependência que são usados para VMs Azure. Uma vez que não é possível utilizar extensões VM para instalar os agentes, deve instalá-los manualmente no sistema operativo do hóspede ou instalá-los através de outro método. 

Consulte [os computadores Do Windows ao Azure Monitor](../agents/agent-windows.md) ou ligue os [computadores Linux ao Azure Monitor](../agents/agent-linux.md) para obter mais informações sobre a implementação do agente Log Analytics. Os detalhes para o agente de Dependência são fornecidos neste artigo. 

## <a name="firewall-requirements"></a>Requisitos de firewall
Os requisitos de firewall para o agente Log Analytics são fornecidos na [visão geral do agente Log Analytics](../agents/log-analytics-agent.md#network-requirements). O agente VM insights Map Dependency não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas. Os dados do Mapa são sempre transmitidos pelo agente Log Analytics ao serviço Azure Monitor, quer diretamente quer através do [gateway da Suite de Gestão de Operações,](../../azure-monitor/agents/gateway.md) se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à internet.


## <a name="dependency-agent"></a>Agente de Dependência

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução Mapa de Serviço.](./service-map.md)  

Você pode baixar o agente Desafiada a partir destes locais:

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F91D7301FD0360F4F56F78275545BB8856779899CA85E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC2422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

Pode instalar manualmente o agente Dependency nos computadores Windows, executando `InstallDependencyAgent-Windows.exe` . Se executar este ficheiro executável sem opções, inicia um assistente de configuração que pode seguir para instalar o agente interativamente. Exige que os privilégios *do Administrador* no so convidado instale ou desinstale o agente.

A tabela seguinte realça os parâmetros suportados pela configuração do agente a partir da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções de linha de comando. |
| /S | Executa uma instalação silenciosa sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com o `/?` parâmetro, **introduzaInstallDependencyAgent-Windows.exe /?**. .

Os ficheiros do agente de dependência do Windows são instalados em *C:\Program Files\Microsoft Dependency Agent* por padrão. Se o agente Desasusiting inificado após a configuração terminar, verifique se os registos são informativo sobre erros. O diretório de registos é *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="powershell-script"></a>Script do PowerShell
Utilize o seguinte script PowerShell para descarregar e instalar o agente:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência no Linux

O agente Dependency é instalado em servidores Linux da *InstallDependencyAgent-Linux64.bin*, um script de concha com um binário auto-extrator. Pode executar o ficheiro utilizando `sh` ou adicionar permissões de execução ao próprio ficheiro.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| - ajudar | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| --verificar | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Por exemplo, para executar o programa de instalação com o `-help` parâmetro, **insira instalar DependênciaAgent-Linux64.bin -help**. Instale o agente de dependência Linux como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin` .

Se o agente de dependência não arrancar, verifique se os registos são informativo de erro. Nos agentes Linux, o diretório de registos é */var/opt/microsoft/dependency-agent/log*.

Os ficheiros do agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Script de concha 
Utilize o seguinte script de amostra para descarregar e instalar o agente:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configuração do Estado Pretendido

Para implementar o agente de dependência utilizando a Configuração do Estado Desejado (DSC), pode utilizar o módulo xPSDesiredStateConfiguration com o seguinte código de exemplo:

```powershell
configuration VMInsights {

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



## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>VM não aparece no mapa

Se a instalação do seu agente de dependência tiver sido bem sucedida, mas não vir o computador no mapa, diagnostice o problema seguindo estes passos.

1. O Dependency Agent foi instalado com sucesso? Pode verificar esta situação ao confirmar que o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Microsoft Dependency agent".

    **Linux**: Procure o processo de execução "microsoft-dependency-agent".

2. Está no [nível de preços gratuitos do Log Analytics?](../insights/solutions.md) O plano Free permite até cinco computadores únicos. Quaisquer computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores já não estejam a enviar dados.

3. O computador está a enviar registos e dados perf para registos do Monitor Azure? Execute a seguinte consulta para o seu computador:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Devolveu um ou mais resultados? Os dados são recentes? Em caso afirmativo, o seu agente Log Analytics está a funcionar corretamente e a comunicar com o serviço. Caso contrário, verifique o agente no seu servidor: [Registar o agente Analytics para a resolução de problemas](../agents/agent-windows-troubleshoot.md) do Windows ou para o agente [Desapeito de Registo para a resolução de problemas do Linux](../agents/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Computador aparece no mapa mas não tem processos

Se vir o seu servidor no mapa, mas não tiver dados de processo ou ligação, isso indica que o agente Dependency está instalado e a funcionar, mas o controlador de kernel não carregou.

Verifique o ficheiro C:\Programas\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o ficheiro /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do ficheiro devem indicar o motivo pelo qual kernel não carregou. Por exemplo, o kernel poderá não ser suportado pelo Linux se tiver atualizado o kernel.


## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com insights VM.

- Para ver as dependências de aplicações descobertas, consulte [o Mapa de insights VM da Vista.](vminsights-maps.md)

- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte o desempenho do [See Azure VM](vminsights-performance.md).