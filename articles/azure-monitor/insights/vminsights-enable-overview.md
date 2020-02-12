---
title: Habilitar a Azure Monitor para VMs (visualização) visão geral | Microsoft Docs
description: Saiba como implantar e configurar Azure Monitor para VMs. Descubra os requisitos do sistema.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: ce95223e9501ad7bec2bd260a8fe6f1537db5593
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400625"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Habilitar a Azure Monitor para VMs (visualização) visão geral

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para VMs. Use Azure Monitor para VMs para monitorar a integridade e o desempenho. Descubra dependências de aplicativos que são executadas em VMs (máquinas virtuais) do Azure e conjuntos de dimensionamento de máquinas virtuais, VMs locais ou VMs hospedadas em outro ambiente de nuvem.  

Para configurar Azure Monitor para VMs:

* Habilite uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais selecionando **insights (versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM.
* Habilite duas ou mais VMs do Azure e conjuntos de dimensionamento de máquinas virtuais usando Azure Policy. Esse método garante que, em VMs e conjuntos de dimensionamento novos e existentes, as dependências necessárias sejam instaladas e configuradas corretamente. VMs não compatíveis e conjuntos de dimensionamento são relatados, para que você possa decidir se deseja habilitá-los e corrigi-los.
* Ativar dois ou conjuntos de dimensionamento de VMs do Azure ou numa máquina virtual mais através de uma subscrição especificada ou o grupo de recursos com o PowerShell.
* Habilite Azure Monitor para VMs para monitorar VMs ou computadores físicos hospedados em sua rede corporativa ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende as informações nas secções seguintes. 

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor para VMs dá suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A. Oeste 2
- E.U.A. Centro-Sul
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Central
- E.U.A. Centro-Norte
- Canadá Central
- Sul do Reino Unido
- Europa do Norte
- Europa Ocidental
- Ásia Leste
- Ásia Sudeste
- Índia Central
- Leste do Japão
- Leste da Austrália
- Austrália Sudeste

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

|Versão do SO |Desempenho |Mapas |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18, 4, 16, 4 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> funcionalidade do desempenho do Azure Monitor para VMs está disponível apenas a partir do Azure Monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>No sistema operacional Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - Versões kernel não padrão, como extensão de endereço físico (PAE) e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão do *2.6.16.21-0.8-xen* não é suportada.
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
> - Há suporte para o kernel CentOSPlus.
> - O kernel do Linux deve ser corrigido para a vulnerabilidade Spectre. Consulte seu fornecedor de distribuição do Linux para obter mais detalhes.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão de kernel |
|:--|:--|
| 18, 4 | 5,0 (inclui kernel ajustado pelo Azure)<br>4,18 *<br>4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel |
|:--|:--|
|12 SP4 | 4,12. * (inclui kernel ajustado para o Azure) |
|12 SP3 | 4.4. * |
|12 SP2 | 4.4. * |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão de kernel |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>O agente do Microsoft Dependency

O recurso de mapa no Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O agente de dependência depende do agente do Log Analytics para estabelecer ligação com o Log Analytics. Portanto, seu sistema deve ter o agente de Log Analytics instalado e configurado com o Dependency Agent.

Se você habilitar Azure Monitor para VMs para uma única VM do Azure ou usar o método de implantação em escala, use a extensão do agente de dependência de VM do Azure para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) para instalar o agente como parte da experiência.

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

Em um ambiente híbrido, você pode baixar e instalar o agente de dependência manualmente ou usando um método automatizado.

A tabela seguinte descreve as origens ligadas que a funcionalidade de mapa suporta num ambiente híbrido.

| Origem ligada | Suportadas | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Junto com o [agente do log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Windows precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Agentes do Linux | Sim | Junto com o [agente de log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Linux precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Grupo de gestão do System Center Operations Manager | Não | |

Você pode baixar o Dependency Agent nestes locais:

| Ficheiros | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para habilitar e acessar os recursos no Azure Monitor para VMs, você deve ter a função de *colaborador de log Analytics* . Para exibir o desempenho, a integridade e os dados do mapa, você deve ter a função *leitor de monitoramento* para a VM do Azure. A área de trabalho do Log Analytics tem de ser configurada para o Azure Monitor para as VMs.

Para obter mais informações sobre como controlar o acesso a uma área de trabalho do Log Analytics, consulte [gerir áreas de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como habilitar a Azure Monitor para VMs (visualização)

Habilite Azure Monitor para VMs usando um dos métodos descritos nesta tabela:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais | [Habilitar da VM](vminsights-enable-single-vm.md) | Você pode habilitar uma única VM do Azure selecionando **insights (versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de Azure Policy](vminsights-enable-at-scale-policy.md) | Você pode habilitar várias VMs do Azure usando Azure Policy e definições de política disponíveis. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Você pode habilitar várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou grupo de recursos especificado usando modelos Azure PowerShell ou Azure Resource Manager. |
| Cloud híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Você pode implantar em VMs ou computadores físicos hospedados em seu datacenter ou em outros ambientes de nuvem. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho ativadas 

Azure Monitor para VMs configura um espaço de trabalho Log Analytics para coletar os contadores de desempenho que ele usa. As tabelas a seguir listam os objetos e contadores que são coletados a cada 60 segundos.

>[!NOTE]
>A lista a seguir de contadores de desempenho habilitados pelo Azure Monitor para VMs não o limita a habilitar contadores adicionais que você precisa coletar de VMs relatando para o espaço de trabalho. Além disso, se você desabilitar esses contadores, ele impedirá que o conjunto de gráficos de desempenho incluído no recurso de desempenho mostre a utilização de recursos de suas VMs.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco lógico |% De espaço livre |
|Disco lógico |Média de disco s/leitura |
|Disco lógico |Média de disco s/transferência |
|Disco lógico |Média de disco s/gravação |
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

O pacote de gerenciamento é denominado *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Ele foi gravado na pasta `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. A fonte de dados usada pelo pacote de gerenciamento é `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico

A Microsoft recolhe automaticamente dados de utilização e desempenho através da sua utilização do serviço do Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso de mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, o endereço IP, o nome DNS e o nome da estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e a utilização, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento para sua VM, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs.

## <a name="next-steps"></a>Passos seguintes

Para saber como usar o recurso de monitoramento de desempenho, consulte [exibir Azure monitor para VMs desempenho](vminsights-performance.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
