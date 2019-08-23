---
title: Habilitar a Azure Monitor para VMs (visualização) visão geral | Microsoft Docs
description: Saiba como implantar e configurar Azure Monitor para VMs. Descubra os requisitos do sistema.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: f395ba5d63463aa177b453d187d025a4461eff28
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905582"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Habilitar a Azure Monitor para VMs (visualização) visão geral

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para VMs. Use Azure Monitor para VMs para monitorar a integridade e o desempenho. Descubra dependências de aplicativos que são executadas em VMs (máquinas virtuais) do Azure e conjuntos de dimensionamento de máquinas virtuais, VMs locais ou VMs hospedadas em outro ambiente de nuvem.  

Para configurar Azure Monitor para VMs:

* Habilite uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais selecionando insights **(versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM.
* Habilite duas ou mais VMs do Azure e conjuntos de dimensionamento de máquinas virtuais usando Azure Policy. Esse método garante que, em VMs e conjuntos de dimensionamento novos e existentes, as dependências necessárias sejam instaladas e configuradas corretamente. VMs não compatíveis e conjuntos de dimensionamento são relatados, para que você possa decidir se deseja habilitá-los e corrigi-los.
* Ativar dois ou conjuntos de dimensionamento de VMs do Azure ou numa máquina virtual mais através de uma subscrição especificada ou o grupo de recursos com o PowerShell.
* Habilite Azure Monitor para VMs para monitorar VMs ou computadores físicos hospedados em sua rede corporativa ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende as informações nas secções seguintes. 

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor para VMs dá suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- EUA Centro-Oeste
- Oeste dos EUA 2<sup>1</sup>
- East US
- Canadá Central
- Reino Unido Sul
- Europa Ocidental
- Sudeste Asiático
- Leste da Austrália<sup>1</sup>
- Sudeste da Austrália<sup>1</sup>

<sup>1</sup> nesta região atualmente não suporta a funcionalidade de estado de funcionamento do Monitor do Azure para VMs.

>[!NOTE]
>Você pode implantar VMs do Azure de qualquer região. Essas VMs não são limitadas às regiões com suporte no espaço de trabalho Log Analytics.
>

Se você não tiver um espaço de trabalho, poderá criar um usando um destes recursos:
* [A CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Você também pode criar um espaço de trabalho enquanto estiver habilitando o monitoramento para uma única VM do Azure ou um conjunto de dimensionamento de máquinas virtuais no portal do Azure.

Para configurar um cenário em escala que usa Azure Policy, Azure PowerShell ou modelos de Azure Resource Manager, em seu espaço de trabalho do Log Analytics:

* Instale as soluções ServiceMap e InfrastructureInsights. Você pode concluir esta instalação usando um modelo de Azure Resource Manager fornecido. Ou, na guia **introdução** , selecione **Configurar espaço de trabalho**.
* Configure a área de trabalho do Log Analytics para recolher contadores de desempenho.

Para configurar seu espaço de trabalho para o cenário em escala, use um dos seguintes métodos:

* Use [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na página [**cobertura da política**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de Azure monitor para VMs, selecione **Configurar espaço de trabalho**. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela a seguir lista os sistemas operacionais Windows e Linux aos quais Azure Monitor para VMs dá suporte. Mais adiante nesta seção, você encontrará uma lista completa que detalha a versão principal e secundária do sistema operacional Linux e as versões de kernel com suporte.

|Versão do SO |Desempenho |Maps |Estado de Funcionamento |
|-----------|------------|-----|-------|
|Windows Server de 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10 1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14, 4, 16, 4, 18, 4 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9,4 | X<sup>1</sup> | | X |

<sup>1</sup> funcionalidade do desempenho do Azure Monitor para VMs está disponível apenas a partir do Azure Monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>O recurso de integridade do Azure Monitor para VMs não oferece [](../../virtual-machines/windows/nested-virtualization.md) suporte à virtualização aninhada em uma VM do Azure.
>

>[!NOTE]
>No sistema operacional Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - Versões kernel não padrão, como extensão de endereço físico (PAE) e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão do *2.6.16.21-0.8-xen* não é suportada.
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
> - Há suporte para o kernel CentOSPlus.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão de kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4,18 * |
| Ubuntu 16.04.3 | o kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |
|12 SP4 | 4.4. * |
|12 SP4 | Kernel ajustado pelo Azure |

### <a name="the-microsoft-dependency-agent"></a>O agente do Microsoft Dependency

O recurso de mapa no Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O agente de dependência depende do agente do Log Analytics para estabelecer ligação com o Log Analytics. Portanto, seu sistema deve ter o agente de Log Analytics instalado e configurado com o Dependency Agent.

Se você habilitar Azure Monitor para VMs para uma única VM do Azure ou usar o método de implantação em escala, use a extensão do agente de dependência de VM do Azure para instalar o agente como parte da experiência.

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

Em um ambiente híbrido, você pode baixar e instalar o agente de dependência manualmente. Se suas VMs estiverem hospedadas fora do Azure, use um método de implantação automatizado.

A tabela seguinte descreve as origens ligadas que a funcionalidade de mapa suporta num ambiente híbrido.

| Origem ligada | Suportadas | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Junto com o [agente do log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Windows precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Agentes do Linux | Sim | Junto com o [agente de log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Linux precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Grupo de gestão do System Center Operations Manager | Não | |

Você pode baixar o Dependency Agent nestes locais:

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para habilitar e acessar os recursos no Azure Monitor para VMs, você deve ter a função de *colaborador de log Analytics* . Para exibir o desempenho, a integridade e os dados do mapa, você deve ter a função *leitor de monitoramento* para a VM do Azure. A área de trabalho do Log Analytics tem de ser configurada para o Azure Monitor para as VMs.

Para obter mais informações sobre como controlar o acesso a uma área de trabalho do Log Analytics, consulte [gerir áreas de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como habilitar a Azure Monitor para VMs (visualização)

Habilite Azure Monitor para VMs usando um dos métodos descritos nesta tabela:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais | [Habilitar da VM](vminsights-enable-single-vm.md) | Você pode habilitar uma única VM do Azure selecionando insights **(versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de Azure Policy](vminsights-enable-at-scale-policy.md) | Você pode habilitar várias VMs do Azure usando Azure Policy e definições de política disponíveis. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Você pode habilitar várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou grupo de recursos especificado usando modelos Azure PowerShell ou Azure Resource Manager. |
| Nuvem híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Você pode implantar em VMs ou computadores físicos hospedados em seu datacenter ou em outros ambientes de nuvem. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho ativadas 

Azure Monitor para VMs configura um espaço de trabalho Log Analytics para coletar os contadores de desempenho que ele usa. As tabelas a seguir listam os objetos e contadores que são coletados a cada 60 segundos.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco lógico |% De espaço livre |
|Disco lógico |Média Disco seg/leitura |
|Disco lógico |Média Disco seg/transferência |
|Disco lógico |Média Disco seg/escritas |
|Disco lógico |Bytes de disco/seg |
|Disco lógico |Bytes Lidos de Disco/seg |
|Disco lógico |Leituras de disco/seg |
|Disco lógico |As transferências de disco/seg |
|Disco lógico |Bytes Escritos em Disco/seg |
|Disco lógico |Escritas de disco/seg |
|Disco lógico |Megabytes livres |
|Memória |MBytes disponíveis |
|Placa de rede |Bytes recebidos/seg |
|Placa de rede |Bytes enviados/seg |
|Processador |% Tempo do processador |

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco lógico |% De espaço utilizado |
|Disco lógico |Bytes Lidos de Disco/seg |
|Disco lógico |Leituras de disco/seg |
|Disco lógico |As transferências de disco/seg |
|Disco lógico |Bytes Escritos em Disco/seg |
|Disco lógico |Escritas de disco/seg |
|Disco lógico |Megabytes livres |
|Disco lógico |Bytes de disco lógico/seg |
|Memória |MBytes de memória disponíveis |
|Rede |Total de Bytes recebidos |
|Rede |Total de Bytes transmitidos |
|Processador |% Tempo do processador |

## <a name="management-packs"></a>Pacotes de gestão

Quando Azure Monitor para VMs é habilitado e configurado com um espaço de trabalho do Log Analytics, um pacote de gerenciamento é encaminhado para todos os computadores Windows que se reportam a esse espaço de trabalho. Se você [integrou seu grupo de gerenciamento de System Center Operations Manager](../../azure-monitor/platform/om-agents.md) com o espaço de trabalho log Analytics, o pacote de gerenciamento do mapa do serviço é implantado do grupo de gerenciamento para os computadores Windows que se reportam ao grupo de gerenciamento.  

O pacote de gerenciamento é denominado *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Ele é gravado em%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management packs \ pasta. A origem de dados que utiliza o pacote de gestão é % Program files%\Microsoft monitorização Agent\Agent\Health serviço State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico

A Microsoft recolhe automaticamente dados de utilização e desempenho através da sua utilização do serviço do Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso de mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, o endereço IP, o nome DNS e o nome da estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e a utilização, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento para sua VM, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs.

## <a name="next-steps"></a>Passos seguintes

Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
