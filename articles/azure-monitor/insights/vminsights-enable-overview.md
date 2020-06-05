---
title: Ativar o Monitor Azure para visão geral dos VMs
description: Aprenda a implementar e configurar o Monitor Azure para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 87d8b26110eba647975de577e9d7b5b0ed138266
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84423977"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Ativar o Monitor Azure para visão geral dos VMs

Este artigo fornece uma visão geral das opções disponíveis para permitir que o Azure Monitor para VMs nas suas máquinas virtuais monitorize a saúde e o desempenho. Descubra as dependências de aplicações que funcionam em máquinas virtuais Azure (VMs) e conjuntos de escala de máquinas virtuais, VMs no local ou VMs hospedados em outro ambiente de nuvem.  

Para configurar o Azure Monitor para VMs:

* Ativar um único Azure VM ou uma balança de máquina virtual definida selecionando **Insights** diretamente a partir do conjunto de escala de VM ou de máquina virtual.
* Ativar dois ou mais VMs Azure e conjuntos de balanças de máquinas virtuais utilizando a Política Azure. Este método garante que, nos conjuntos de VM e de escala existentes e novos, as dependências necessárias sejam instaladas e devidamente configuradas. VMs e conjuntos de escala não conformes são relatados, para que você possa decidir se ativar e remediar.
* Ativar dois ou mais VMs Azure ou conjuntos de balanças de máquinas virtuais através de uma subscrição ou grupo de recursos especificados utilizando o PowerShell.
* Ativar o Azure Monitor para VMs para monitorizar VMs ou computadores físicos alojados na sua rede corporativa ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende a informação nas seguintes secções. 

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução Mapa de Serviço.](service-map.md)  

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor for VMs suporta um espaço de trabalho Log Analytics nas seguintes regiões:

- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A.Oeste 2
- E.U.A. Centro-Sul
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Central
- E.U.A. Centro-Norte
- Gov Va dos EUA
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
>Pode monitorizar os VMs do Azure em qualquer região. Os VMs em si não se limitam às regiões apoiadas pelo espaço de trabalho Log Analytics.
>

Se não tiver um espaço de trabalho log analytics, pode criar um utilizando um dos recursos:
* [CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Também pode criar um espaço de trabalho enquanto está a permitir a monitorização de um único Azure VM ou escala de máquina virtual definida no portal Azure.

Para configurar um cenário em escala que utilize modelos de Azure Policy, Azure PowerShell ou Azure Resource Manager, no seu espaço de trabalho Log Analytics:

* Instale as soluções *ServiceMap* e *InfrastructureInsights.* Pode concluir esta instalação utilizando um modelo de Gestor de Recursos Azure fornecido. Ou no **separador Get Started** no portal Azure, selecione **Configure Workspace**.
* Configure o espaço de trabalho Log Analytics para recolher contadores de desempenho.

Para configurar o seu espaço de trabalho para o cenário em escala, utilize um dos seguintes métodos:

* Utilize [a Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na página Azure Monitor for VMs [**Policy Coverage,**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) selecione **Configure Workspace**. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela que se segue lista os sistemas operativos Windows e Linux que o Azure Monitor suporta para VMs. Mais tarde nesta secção, você encontrará uma lista completa que detalha o lançamento principal e menor do Linux OS e as versões de kernel suportadas.

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
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> A funcionalidade de desempenho do Azure Monitor para VMs só está disponível no Azure Monitor. Não está disponível diretamente a partir do painel esquerdo do Azure VM.

>[!NOTE]
>No sistema operativo Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - As versões não padrão do kernel, tais como a Extensão de Endereço Físico (PAE) e xen, não são suportadas para qualquer distribuição do Linux. Por exemplo, um sistema com a cadeia de desbloqueio de *2.6.16.21-0.8-xen* não é suportado.
> - Os núcleos personalizados, incluindo as reparações de núcleos padrão, não são suportados.
> - O núcleo CentOSPlus é suportado.
> - O núcleo Linux deve ser remendado para a vulnerabilidade do Espectro. Consulte o seu fornecedor de distribuição Linux para mais detalhes.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão de kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
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
| 18.04 | 5.3.0-1020<br>5.0 (inclui núcleo afinado aZure)<br>4.18* <br> 4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>Servidor empresarial SUSE Linux 12

| Versão do SO | Versão de kernel |
|:--|:--|
|12 SP4 | 4.12.* (inclui núcleo afinado aZure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão de kernel |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>O agente da Microsoft Dependency

A funcionalidade Mapa no Azure Monitor para VMs obtém os seus dados do agente da Microsoft Dependency. O agente de dependência conta com o agente Log Analytics para a sua ligação ao Log Analytics. Portanto, o seu sistema deve ter o agente Log Analytics instalado e configurado com o agente Dependency.

Quer ative o Azure Monitor para VMs para um único Azure VM ou utilize o método de implantação à escala, utilize a extensão do agente de dependência Azure VM para [windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instalar o agente como parte da experiência.

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução Mapa de Serviço.](service-map.md)  

Num ambiente híbrido, pode descarregar e instalar manualmente o agente Dependency ou utilizar um método automatizado.

A tabela seguinte descreve as fontes ligadas que a funcionalidade Map suporta num ambiente híbrido.

| Origem ligada | Suportado | Description |
|:--|:--|:--|
| Agentes do Windows | Yes | Juntamente com o [agente Log Analytics para windows,](../../azure-monitor/platform/log-analytics-agent.md)os agentes windows precisam do agente Dependency. Para obter mais informações, consulte [os sistemas operativos suportados.](#supported-operating-systems) |
| Agentes do Linux | Yes | Juntamente com o [agente Log Analytics para o Linux,](../../azure-monitor/platform/log-analytics-agent.md)os agentes Linux precisam do agente Desadependição. Para obter mais informações, consulte [os sistemas operativos suportados.](#supported-operating-systems) |
| Grupo de gestão do System Center Operations Manager | No | |

Você pode baixar o agente Desafiada a partir destes locais:

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF923A81F04EC0763C327567C582CE99C0C5A0BA0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA0960A9F |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ativar e aceder às funcionalidades em Azure Monitor para VMs, tem de ter a *função de colaborador do Log Analytics.* Para visualizar dados de desempenho, saúde e mapa, deve ter o papel *de leitor de monitorização* para o VM Azure. O espaço de trabalho Log Analytics deve ser configurado para monitor Azure para VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir espaços de trabalho.](../../azure-monitor/platform/manage-access.md)

## <a name="how-to-enable-azure-monitor-for-vms"></a>Como ativar o Monitor Azure para VMs

Ativar o Monitor Azure para VMs utilizando um dos métodos descritos neste quadro:

| Estado de implantação | Método | Description |
|------------------|--------|-------------|
| Único VM azul ou conjunto de balança de máquina virtual | [Ativar a partir do VM](vminsights-enable-single-vm.md) | Pode ativar um único Azure VM selecionando **Insights** diretamente a partir do conjunto de escala de VM ou de máquina virtual. |
| VMs múltiplos azure ou conjuntos de escala de máquina virtual | [Ativar através da Política Azure](vminsights-enable-at-scale-policy.md) | Pode ativar vários VMs Azure utilizando a Política Azure e definições de política disponíveis. |
| VMs múltiplos azure ou conjuntos de escala de máquina virtual | [Ativar através dos modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Pode ativar vários VMs Azure ou conjuntos de balanças de máquinas virtuais através de um grupo de subscrição ou recursos especificados utilizando modelos Azure PowerShell ou Azure Resource Manager. |
| Nuvem híbrida | [Permitir o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Pode implementar em VMs ou computadores físicos que estejam hospedados no seu datacenter ou em outros ambientes em nuvem. |

## <a name="management-packs"></a>Pacotes de gestão

Quando o Azure Monitor para VMs está ativado e configurado com um espaço de trabalho Log Analytics, um pacote de gestão é encaminhado para todos os computadores Windows que reportam a esse espaço de trabalho. Se [integrou o seu grupo de gestão de Operações do System Center](../../azure-monitor/platform/om-agents.md) com o espaço de trabalho Log Analytics, o pacote de gestão do Mapa de Serviços é implantado do grupo de gestão para os computadores Windows reportando ao grupo de gestão.  

O pacote de gestão chama-se *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Está escrito para `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` a pasta. A fonte de dados que o pacote de gestão utiliza é `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço. 

Para fornecer capacidades precisas e eficientes de resolução de problemas, a funcionalidade Mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operativo e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que ativou a monitorização para o seu VM, a informação de monitorização está disponível para análise no Azure Monitor para VMs.

## <a name="next-steps"></a>Passos seguintes

Para aprender a utilizar a função de monitorização de desempenho, consulte [o View Azure Monitor para o desempenho dos VMs](vminsights-performance.md). Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md).
