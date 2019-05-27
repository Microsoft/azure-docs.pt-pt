---
title: Ativar o Azure Monitor para descrição geral de VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo descreve como implementar e configurar o Azure Monitor para VMs e os requisitos de sistema necessário.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 4eb07b29f68c5f495bdbe5e23f5b226480b66661
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524074"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Ativar o Azure Monitor para descrição geral de VMs (pré-visualização)

Este artigo fornece uma descrição geral das opções disponíveis para configurar o Azure Monitor para as VMs monitorizar o estado de funcionamento, desempenho e detetar dependências de aplicações em execução em máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais, as VMs no local, ou VMs alojadas no outro ambiente de cloud.  

Ativar o Azure Monitor para VMs com um dos seguintes métodos:

* Ativar uma única máquina virtual do Azure ou a seleção de conjunto de dimensionamento de máquina virtual **Insights (pré-visualização)** diretamente do VM ou de máquina virtual de conjunto de dimensionamento.
* Ativar dois ou conjuntos de dimensionamento de VMs do Azure e a máquina virtual mais utilizando a política do Azure. Através deste método, as dependências necessárias de VMs novas e existentes e conjuntos de dimensionamento são instaladas e configuradas corretamente. VMs não compatíveis e conjuntos de dimensionamento são relatados para que possa decidir se pretende ativá-los e como deseja resolvê-los.
* Ativar dois ou conjuntos de dimensionamento de VMs do Azure ou numa máquina virtual mais através de uma subscrição especificada ou o grupo de recursos com o PowerShell.
* Ative para monitorizar máquinas virtuais ou computadores físicos, hospedados em sua rede empresarial ou outro ambiente de cloud.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende as informações nas secções seguintes.

### <a name="log-analytics"></a>Log Analytics

Monitor do Azure para VMs oferece suporte a uma área de trabalho do Log Analytics nas seguintes regiões:

- EUA Centro-Oeste
- EUA Leste
- Canada Central<sup>1</sup>
- Sul do Reino Unido<sup>1</sup>
- Europa Ocidental
- Sudeste asiático<sup>1</sup>

<sup>1</sup> nesta região atualmente não suporta a funcionalidade de estado de funcionamento do Monitor do Azure para VMs.

>[!NOTE]
>Máquinas virtuais do Azure podem ser implementadas a partir de qualquer região e não fica limitadas para as regiões suportadas para a área de trabalho do Log Analytics.
>

Se não tiver uma área de trabalho, pode criar uma com um dos seguintes métodos:
* [A CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Se estiver a ativar a monitorização para uma única VM do Azure ou o dimensionamento de máquinas virtuais definido no portal do Azure, pode criar uma área de trabalho durante este processo.

Para o cenário de à escala com o Azure Policy, Azure PowerShell ou modelos Azure Resource Manager, a sua área de trabalho do Log Analytics precisa primeiro as seguintes configurações:

* Instale as soluções ServiceMap e InfrastructureInsights. Pode concluir esta instalação com um modelo Azure Resource Manager que é fornecido, ou utilizando o **configurar a área de trabalho** opção encontrado no **começar** separador.
* Configure a área de trabalho do Log Analytics para recolher contadores de desempenho.

Para configurar a sua área de trabalho para o cenário de à escala, pode configurá-lo através de um dos seguintes métodos:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Do **configurar a área de trabalho** opção no Monitor do Azure para VMs [abrangência da apólice](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) página

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela seguinte lista os sistemas operativos Windows e Linux que são suportados com o Azure Monitor para as VMs. Uma lista completa, que fornece detalhes sobre a versão de SO Linux principal e secundária e versões de kernel suportadas será fornecida posteriormente nesta secção.

|Versão do SO |Desempenho |Maps |Saúde |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> funcionalidade do desempenho do Azure Monitor para VMs está disponível apenas a partir do Azure Monitor. Não está disponível quando acessá-lo diretamente a partir do painel do lado esquerdo da VM do Azure.

>[!NOTE]
>As seguintes informações aplicam-se para oferecer suporte a do sistema operativo Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - Versões kernel não padrão, como extensão de endereço físico (PAE) e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de versão do *2.6.16.21-0.8-xen* não é suportada.
> - Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados.
> - CentOSPlus kernel é suportada.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão de kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão de kernel |
|:--|:--|
| Ubuntu 18.04 | o kernel 4.15. * |
| Ubuntu 16.04.3 | o kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>O agente do Microsoft Dependency

O Azure Monitor para a funcionalidade de mapa de VMs obtém seus dados do agente do Microsoft Dependency. O agente de dependência depende do agente do Log Analytics para estabelecer ligação com o Log Analytics. Por conseguinte, o sistema tem de ter o agente do Log Analytics instalada e configurada com o agente de dependência.

Se ativar o Azure Monitor para as VMs para uma única VM do Azure ou utilizar o método de implementação à escala, terá de utilizar a extensão de agente de dependência de VM do Azure para instalar o agente como parte da experiência.

Num ambiente híbrido, pode transferir e instalar o agente de dependência de qualquer uma das duas formas: manualmente ou através de um método de implantação automatizado para máquinas virtuais que estão alojados fora do Azure.

A tabela seguinte descreve as origens ligadas que a funcionalidade de mapa suporta num ambiente híbrido.

| Origem ligada | Suportadas | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Para além da [agente do Log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), agentes do Windows exigem o agente do Microsoft Dependency. Para obter uma lista completa das versões de sistema operativo, consulte [sistemas operativos suportados](#supported-operating-systems). |
| Agentes do Linux | Sim | Para além da [agente do Log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), o agente do Microsoft Dependency necessitam de agentes do Linux. Para obter uma lista completa das versões de sistema operativo, consulte [sistemas operativos suportados](#supported-operating-systems). |
| Grupo de gestão do System Center Operations Manager | Não | |

O agente de dependência pode ser transferido a partir das seguintes localizações:

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ativar e acessar os recursos no Azure Monitor para as VMs, terá de ser atribuídas as seguintes funções de acesso:

- Para ativá-la, tem de ter o *Contribuidor do Log Analytics* função.

- Para ver o desempenho, o estado de funcionamento e mapear dados, tem de ter o *leitor monitorização* função para a VM do Azure. A área de trabalho do Log Analytics tem de ser configurada para o Azure Monitor para as VMs.

Para obter mais informações sobre como controlar o acesso a uma área de trabalho do Log Analytics, consulte [gerir áreas de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como ativar o Azure Monitor para VMs (pré-visualização)

Ativar o Azure Monitor para VMs utilizando um dos seguintes métodos descritos na tabela seguinte.

| Estado de implementação | Método | Descrição |
|------------------|--------|-------------|
| Único conjunto de dimensionamento de VM do Azure ou numa máquina virtual | [Diretamente a partir da VM](vminsights-enable-single-vm.md) | Pode ativar uma única máquina virtual do Azure, selecionando **Insights (pré-visualização)** diretamente do VM ou de máquina virtual de conjunto de dimensionamento. |
| Várias VMs do Azure ou os conjuntos de dimensionamento de máquinas virtuais | [Azure Policy](vminsights-enable-at-scale-policy.md) | Pode ativar várias VMs do Azure com o Azure Policy e definições de política disponíveis. |
| Várias VMs do Azure ou os conjuntos de dimensionamento de máquinas virtuais | [Modelos do Azure PowerShell ou do Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Pode ativar vários conjuntos de dimensionamento de VMs do Azure ou numa máquina virtual num especificado subscrição ou grupo de recursos com modelos do Azure PowerShell ou do Azure Resource Manager. |
| Nuvem híbrida | [Ativar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Pode implementar em máquinas virtuais ou computadores físicos que estão alojados no seu datacenter ou em outros ambientes de cloud. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho ativadas

Monitor do Azure para VMs configura uma área de trabalho do Log Analytics para recolher os contadores de desempenho que utiliza. A tabela seguinte lista os objetos e os contadores que são recolhidas a cada 60 segundos.

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

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico

A Microsoft recolhe automaticamente dados de utilização e desempenho através da sua utilização do serviço do Azure Monitor. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço. Para fornecer capacidades de resolução de problemas exatas e eficientes, os dados a partir da funcionalidade de mapa incluem informações sobre a configuração do seu software, como o sistema operativo e o versão, o endereço IP, o nome DNS e o nome de estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e a utilização, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para a máquina virtual, estas informações são disponíveis para análise com o Azure Monitor para as VMs. Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
