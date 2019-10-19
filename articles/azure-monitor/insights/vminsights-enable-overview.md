---
title: Habilitar a Azure Monitor para VMs (visualização) visão geral | Microsoft Docs
description: Saiba como implantar e configurar Azure Monitor para VMs. Descubra os requisitos do sistema.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/24/2019
ms.openlocfilehash: 9d132faf0b4d1de232e2b7e6e5ab6730978e27a8
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555234"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Habilitar a Azure Monitor para VMs (visualização) visão geral

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para VMs. Use Azure Monitor para VMs para monitorar a integridade e o desempenho. Descubra dependências de aplicativos que são executadas em VMs (máquinas virtuais) do Azure e conjuntos de dimensionamento de máquinas virtuais, VMs locais ou VMs hospedadas em outro ambiente de nuvem.  

Para configurar Azure Monitor para VMs:

* Habilite uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais selecionando **insights (versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM.
* Habilite duas ou mais VMs do Azure e conjuntos de dimensionamento de máquinas virtuais usando Azure Policy. Esse método garante que, em VMs e conjuntos de dimensionamento novos e existentes, as dependências necessárias sejam instaladas e configuradas corretamente. VMs não compatíveis e conjuntos de dimensionamento são relatados, para que você possa decidir se deseja habilitá-los e corrigi-los.
* Habilite duas ou mais VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou grupo de recursos especificado usando o PowerShell.
* Habilite Azure Monitor para VMs para monitorar VMs ou computadores físicos hospedados em sua rede corporativa ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você entendeu as informações nas seções a seguir. 

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor para VMs dá suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- E.U.A. Centro-Oeste
- Oeste dos EUA 2<sup>1</sup>
- Este dos E.U.A.
- Leste dos EUA 2<sup>1</sup>
- Canadá Central
- Sul do Reino Unido
- Europa Setentrional<sup>1</sup>
- Europa Ocidental
- Sudeste Asiático
- Leste do Japão<sup>1</sup>
- Leste da Austrália<sup>1</sup>
- Sudeste da Austrália<sup>1</sup>

<sup>1</sup> essa região atualmente não dá suporte ao recurso de integridade do Azure monitor para VMs.

>[!NOTE]
>Você pode implantar VMs do Azure de qualquer região. Essas VMs não são limitadas às regiões com suporte no espaço de trabalho Log Analytics.
>

Se você não tiver um espaço de trabalho, poderá criar um usando um destes recursos:
* [O CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Você também pode criar um espaço de trabalho enquanto estiver habilitando o monitoramento para uma única VM do Azure ou um conjunto de dimensionamento de máquinas virtuais no portal do Azure.

Para configurar um cenário em escala que usa Azure Policy, Azure PowerShell ou modelos de Azure Resource Manager, em seu espaço de trabalho do Log Analytics:

* Instale as soluções ServiceMap e InfrastructureInsights. Você pode concluir esta instalação usando um modelo de Azure Resource Manager fornecido. Ou, na guia **introdução** , selecione **Configurar espaço de trabalho**.
* Configure o espaço de trabalho Log Analytics para coletar contadores de desempenho.

Para configurar seu espaço de trabalho para o cenário em escala, use um dos seguintes métodos:

* Use [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na página [**cobertura da política**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de Azure monitor para VMs, selecione **Configurar espaço de trabalho**. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela a seguir lista os sistemas operacionais Windows e Linux aos quais Azure Monitor para VMs dá suporte. Mais adiante nesta seção, você encontrará uma lista completa que detalha a versão principal e secundária do sistema operacional Linux e as versões de kernel com suporte.

|Versão do SO |Desempenho |Mapas |Saúde |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
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
|Ubuntu 18, 4, 16, 4 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 9,4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> o recurso de desempenho do Azure monitor para VMs está disponível apenas no Azure monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>O recurso de integridade do Azure Monitor para VMs não oferece suporte à [virtualização aninhada](../../virtual-machines/windows/nested-virtualization.md) em uma VM do Azure.
>

>[!NOTE]
>No sistema operacional Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - Versões de kernel não padrão, como a extensão de endereço físico (PAE) e Xen, não têm suporte para nenhuma distribuição do Linux. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão de *2.6.16.21-0,8-Xen* .
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
> - Há suporte para o kernel CentOSPlus.
> - O kernel do Linux deve ser corrigido para a vulnerabilidade Spectre. Consulte seu fornecedor de distribuição do Linux para obter mais detalhes.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7,4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6,10 | 2.6.32-754 |
| 6,9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versão do SO | Versão do kernel |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6,9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| 18, 4 | 5,0 (inclui kernel ajustado pelo Azure)<br>4,18 *<br>4.15* |
| 16.04.3 | 4,15. * |
| 16, 4 | 4,13. \*<br>4,11. \*<br>4,10. \*<br>4,8. \*<br>4,4. \* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel |
|:--|:--|
|12 SP4 | 4,12. * (inclui kernel ajustado para o Azure) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão do kernel |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>O Microsoft Dependency Agent

O recurso de mapa no Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O agente de dependência depende do agente de Log Analytics para sua conexão com Log Analytics. Portanto, seu sistema deve ter o agente de Log Analytics instalado e configurado com o Dependency Agent.

Se você habilitar Azure Monitor para VMs para uma única VM do Azure ou usar o método de implantação em escala, use a extensão do agente de dependência de VM do Azure para instalar o agente como parte da experiência.

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

Em um ambiente híbrido, você pode baixar e instalar o agente de dependência manualmente. Se suas VMs estiverem hospedadas fora do Azure, use um método de implantação automatizado.

A tabela a seguir descreve as fontes conectadas que o recurso de mapa dá suporte em um ambiente híbrido.

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

Para habilitar e acessar os recursos no Azure Monitor para VMs, você deve ter a função de *colaborador de log Analytics* . Para exibir o desempenho, a integridade e os dados do mapa, você deve ter a função *leitor de monitoramento* para a VM do Azure. O espaço de trabalho Log Analytics deve ser configurado para Azure Monitor para VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como habilitar a Azure Monitor para VMs (visualização)

Habilite Azure Monitor para VMs usando um dos métodos descritos nesta tabela:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Uma única VM do Azure ou conjunto de dimensionamento de máquinas virtuais | [Habilitar da VM](vminsights-enable-single-vm.md) | Você pode habilitar uma única VM do Azure selecionando **insights (versão prévia)** diretamente do conjunto de dimensionamento de máquinas virtuais ou VM. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de Azure Policy](vminsights-enable-at-scale-policy.md) | Você pode habilitar várias VMs do Azure usando Azure Policy e definições de política disponíveis. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais | [Habilitar por meio de modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Você pode habilitar várias VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou grupo de recursos especificado usando modelos Azure PowerShell ou Azure Resource Manager. |
| Cloud híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Você pode implantar em VMs ou computadores físicos hospedados em seu datacenter ou em outros ambientes de nuvem. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho habilitados 

Azure Monitor para VMs configura um espaço de trabalho Log Analytics para coletar os contadores de desempenho que ele usa. As tabelas a seguir listam os objetos e contadores que são coletados a cada 60 segundos.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |
|------------|-------------|
|LogicalDisk |% De espaço livre |
|LogicalDisk |Média de disco s/leitura |
|LogicalDisk |Média de disco s/transferência |
|LogicalDisk |Média de disco s/gravação |
|LogicalDisk |Bytes de disco/s |
|LogicalDisk |Bytes de leitura de disco/s |
|LogicalDisk |Leituras de disco/s |
|LogicalDisk |Transferências de disco/s |
|LogicalDisk |Bytes de gravação no disco/s |
|LogicalDisk |Gravações de disco/s |
|LogicalDisk |Megabytes livres |
|Memória |MBytes disponíveis |
|Adaptador de rede |Bytes recebidos/s |
|Adaptador de rede |Bytes enviados/s |
|Processador |% De tempo do processador |

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco lógico |% De espaço usado |
|Disco lógico |Bytes de leitura de disco/s |
|Disco lógico |Leituras de disco/s |
|Disco lógico |Transferências de disco/s |
|Disco lógico |Bytes de gravação no disco/s |
|Disco lógico |Gravações de disco/s |
|Disco lógico |Megabytes livres |
|Disco lógico |Bytes de disco lógico/s |
|Memória |Memória de MBytes disponíveis |
|Rede |Total de bytes recebidos |
|Rede |Total de bytes transmitidos |
|Processador |% De tempo do processador |

## <a name="management-packs"></a>Pacotes de gestão

Quando Azure Monitor para VMs é habilitado e configurado com um espaço de trabalho do Log Analytics, um pacote de gerenciamento é encaminhado para todos os computadores Windows que se reportam a esse espaço de trabalho. Se você [integrou seu grupo de gerenciamento de System Center Operations Manager](../../azure-monitor/platform/om-agents.md) com o espaço de trabalho log Analytics, o pacote de gerenciamento do mapa do serviço é implantado do grupo de gerenciamento para os computadores Windows que se reportam ao grupo de gerenciamento.  

O pacote de gerenciamento é denominado *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Ele foi gravado na pasta `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. A fonte de dados usada pelo pacote de gerenciamento é `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e de uso

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço de Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso de mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, o endereço IP, o nome DNS e o nome da estação de trabalho. A Microsoft não coleta nomes, endereços ou outras informações de contato.

Para obter mais informações sobre a coleta e o uso de dados, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento para sua VM, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs.

## <a name="next-steps"></a>Passos seguintes

Para saber como usar o recurso de integridade, consulte [exibir Azure monitor para VMs integridade](vminsights-health.md). Para exibir dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).
