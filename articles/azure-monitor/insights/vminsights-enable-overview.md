---
title: Ativar o Monitor Azure para VMs (pré-visualização) visão geral / Microsoft Docs
description: Aprenda a implementar e configurar o Monitor Azure para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 44422f66f6fc995dcaf96947ea05b183c7131ea3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362178"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Ativar o Monitor Azure para VMs (pré-visualização) visão geral

Este artigo fornece uma visão geral das opções disponíveis para a criação do Monitor Azure para VMs. Utilize o Monitor Azure para vMs para monitorizar a saúde e o desempenho. Descubra dependências de aplicações que funcionam em máquinas virtuais Azure (VMs) e conjuntos de escala de máquinas virtuais, VMs no local ou VMs hospedados em outro ambiente de nuvem.  

Para configurar o Monitor Azure para VMs:

* Ative uma única balança de máquinas Azure VM ou virtual selecionando **Insights (pré-visualização)** diretamente do conjunto de escala vm ou virtual.
* Ative dois ou mais VMs Azure e conjuntos de escala de máquinas virtuais utilizando a Política Azure. Este método garante que, nos vMs existentes e novos e conjuntos de escala, as dependências necessárias são instaladas e devidamente configuradas. São relatados VMs e conjuntos de escala não conformes, para que possa decidir se os permite e os remedia.
* Ativar dois ou conjuntos de dimensionamento de VMs do Azure ou numa máquina virtual mais através de uma subscrição especificada ou o grupo de recursos com o PowerShell.
* Ative o Monitor Azure para os VMs monitorizar em VMs ou computadores físicos hospedados na sua rede corporativa ou noutro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende as informações nas secções seguintes. 

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução do Mapa](service-map.md)de Serviços .  

### <a name="log-analytics"></a>Log Analytics

O Monitor Azure para VMs suporta um espaço de trabalho de Log Analytics nas seguintes regiões:

- EUA Centro-Oeste
- EUA Oeste
- EUA Oeste 2
- EUA Centro-Sul
- EUA Leste
- E.U.A. Leste 2
- EUA Central
- EUA Centro-Norte
- Canadá Central
- Reino Unido Sul
- Europa do Norte
- Europa Ocidental
- Ásia Oriental
- Sudeste Asiático
- Índia Central
- Leste do Japão
- Leste da Austrália
- Sudeste da Austrália

>[!NOTE]
>Pode implantar VMs Azure de qualquer região. Estes VMs não se limitam às regiões suportadas pelo espaço de trabalho Log Analytics.
>

Se não tiver um espaço de trabalho, pode criar um usando um destes recursos:
* [O Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Também pode criar um espaço de trabalho enquanto permite a monitorização de um único Azure VM ou escala de máquina virtual definida no portal Azure.

Para configurar um cenário em escala que utilize modelos de Política Azure, Azure PowerShell ou Azure Resource Manager, no seu espaço de trabalho Log Analytics:

* Instale as soluções ServiceMap e InfrastructureInsights. Pode concluir esta instalação utilizando um modelo de Gestor de Recursos Azure fornecido. Ou no separador **Get Started,** selecione **Configure Workspace**.
* Configure a área de trabalho do Log Analytics para recolher contadores de desempenho.

Para configurar o seu espaço de trabalho para o cenário em escala, utilize um dos seguintes métodos:

* Utilize [o Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* No Monitor Azure para a página de [**Cobertura política**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de VMs, selecione **Configure Workspace**. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela seguinte lista os sistemas operativos Windows e Linux que o Azure Monitor para VMs suporta. Mais tarde nesta secção, você encontrará uma lista completa que detalha a versão principal e menor do Sistema operativo Linux E as versões de kernel suportadas.

|Versão do SO |Desempenho |Maps |
|-----------|------------|-----|
|Windows Server de 2019 | X | X |
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

<sup>1</sup> A função performance do Monitor Azure para VMs está disponível apenas no Monitor Azure. Não está disponível diretamente do painel esquerdo do VM Azure.

>[!NOTE]
>No sistema operativo Linux:
> - Apenas as versões de kernel padrão e SMP Linux são suportadas.
> - Versões kernel não padrão, como extensão de endereço físico (PAE) e Xen, não são suportadas para qualquer distribuição de Linux. Por exemplo, um sistema com a cadeia de libertação de *2.6.16.21-0.8-xen* não é suportado.
> - Os núcleos personalizados, incluindo as recompilações de núcleos padrão, não são suportados.
> - O kernel CentOSPlus é suportado.
> - O kernel Linux deve ser remendado para a vulnerabilidade spectre. Consulte o seu fornecedor de distribuição Linux para obter mais detalhes.

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
| 18.04 | 5.0 (inclui kernel afinado em Azure)<br>4.18 *<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão de kernel |
|:--|:--|
|12 SP4 | 4.12.* (inclui kernel afinado em Azure) |
|12 SP3 | 4.4. * |
|12 SP2 | 4.4. * |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão de kernel |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>O agente do Microsoft Dependency

A funcionalidade Map no Monitor Azure para VMs obtém os seus dados do agente microsoft Dependency. O agente de dependência depende do agente do Log Analytics para estabelecer ligação com o Log Analytics. Por isso, o seu sistema deve ter o agente Log Analytics instalado e configurado com o agente Dependency.

Quer ative o Monitor Azure para VMs para um único VM Azure ou utilize o método de implementação em escala, utilize a extensão do agente de dependência Azure VM para [windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) para instalar o agente como parte da experiência.

>[!NOTE]
>As seguintes informações descritas nesta secção também são aplicáveis à [solução do Mapa](service-map.md)de Serviços .  

Num ambiente híbrido, pode descarregar e instalar manualmente o agente Dependency ou utilizando um método automatizado.

A tabela seguinte descreve as origens ligadas que a funcionalidade de mapa suporta num ambiente híbrido.

| Origem ligada | Suportadas | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Juntamente com o [agente Log Analytics para Windows,](../../azure-monitor/platform/log-analytics-agent.md)os agentes do Windows precisam do agente Dependency. Para mais informações, consulte [sistemas operativos suportados.](#supported-operating-systems) |
| Agentes do Linux | Sim | Juntamente com o [agente log analytics do Linux,](../../azure-monitor/platform/log-analytics-agent.md)os agentes linux precisam do agente dependency. Para mais informações, consulte [sistemas operativos suportados.](#supported-operating-systems) |
| Grupo de gestão do System Center Operations Manager | Não | |

Pode descarregar o agente dependency a partir destes locais:

| Ficheiro | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Portal do | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAa4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A66638F3F9B449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ativar e aceder às funcionalidades no Monitor Azure para VMs, deve ter a função de colaborador de *Log Analytics.* Para visualizar o desempenho, a saúde e os dados do mapa, deve ter o papel de *leitor de monitorização* para o VM Azure. A área de trabalho do Log Analytics tem de ser configurada para o Azure Monitor para as VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho de Log Analytics, consulte [Gerir espaços](../../azure-monitor/platform/manage-access.md)de trabalho .

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como ativar o Monitor Azure para VMs (pré-visualização)

Ativar o Monitor Azure para VMs utilizando um dos métodos descritos nesta tabela:

| Estado de implantação | Método | Descrição |
|------------------|--------|-------------|
| Conjunto de escala de vm azul único ou virtual | [Ativar a partir do VM](vminsights-enable-single-vm.md) | Pode ativar um único VM Azure selecionando **Insights (pré-visualização)** diretamente do conjunto de escala vm ou virtual. |
| Múltiplos VMs Azure ou conjuntos de escala de máquinas virtuais | [Ativar através da Política Azure](vminsights-enable-at-scale-policy.md) | Pode ativar vários VMs Azure utilizando a Política Azure e definições de política disponíveis. |
| Múltiplos VMs Azure ou conjuntos de escala de máquinas virtuais | [Ativar através de modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Pode ativar vários VMs Azure ou conjuntos de escala de máquinas virtuais através de um determinado grupo de subscrição ou recursos utilizando modelos Azure PowerShell ou Azure Resource Manager. |
| Nuvem híbrida | [Ativar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Pode ser implantado em VMs ou computadores físicos hospedados no seu datacenter ou em outros ambientes em nuvem. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho ativadas 

O Monitor Azure para VMs configura um espaço de trabalho log Analytics para recolher os contadores de desempenho que utiliza. As tabelas seguintes listam os objetos e balcões recolhidos a cada 60 segundos.

>[!NOTE]
>A seguinte lista de contadores de desempenho ativados pelo Monitor Azure para VMs não o limita a permitir contadores adicionais que precisa recolher de VMs reportando ao espaço de trabalho. Além disso, se desativar estes contadores, evitará que o conjunto de gráficos de desempenho incluídos com a função Performance mostre a utilização de recursos dos seus VMs.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco lógico |% Espaço Livre |
|Disco lógico |Avg. Disk sec/Read |
|Disco lógico |Avg. Disco sec/Transferência |
|Disco lógico |Avg. Disk sec/Write |
|Disco lógico |Bytes de Disco/seg |
|Disco lógico |Bytes Lidos de Disco/seg |
|Disco lógico |Leituras de Disco/seg |
|Disco lógico |As transferências de disco/seg |
|Disco lógico |Bytes Escritos em Disco/seg |
|Disco lógico |Escritas em disco/seg |
|Disco lógico |Megabytes livres |
|Memória |MBytes disponíveis |
|Placa de Rede |Bytes recebidos/seg |
|Placa de Rede |Bytes enviados/seg |
|Processador |% de tempo do processador |

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco Lógico |% De espaço utilizado |
|Disco Lógico |Bytes Lidos de Disco/seg |
|Disco Lógico |Leituras de Disco/seg |
|Disco Lógico |As transferências de disco/seg |
|Disco Lógico |Bytes Escritos em Disco/seg |
|Disco Lógico |Escritas em disco/seg |
|Disco Lógico |Megabytes livres |
|Disco Lógico |Bytes de disco lógico/seg |
|Memória |MBytes de memória disponíveis |
|Rede |Total de Bytes recebidos |
|Rede |Total de Bytes transmitidos |
|Processador |% de tempo do processador |

## <a name="management-packs"></a>Pacotes de gestão

Quando o Monitor Azure para VMs está ativado e configurado com um espaço de trabalho log Analytics, um pacote de gestão é encaminhado para todos os computadores Windows que reportam a esse espaço de trabalho. Se integrou o seu grupo de gestão de [Gestorde Operações](../../azure-monitor/platform/om-agents.md) do System Center com o espaço de trabalho log Analytics, o pacote de gestão do Mapa de Serviços é implementado do grupo de gestão para os computadores Windows reportando ao grupo de gestão.  

O pacote de gestão chama-se *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Está escrito para `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` pasta. A fonte de dados que o pacote de gestão utiliza é `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico

A Microsoft recolhe automaticamente dados de utilização e desempenho através da sua utilização do serviço do Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço. 

Para fornecer capacidades precisas e eficientes de resolução de problemas, a funcionalidade Map inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operativo e a versão, endereço IP, nome DNS e nome da estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a Declaração de [Privacidade dos Serviços Online](https://go.microsoft.com/fwlink/?LinkId=512132)da Microsoft .

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que permitiu a monitorização do seu VM, a informação de monitorização está disponível para análise no Monitor Azure para VMs.

## <a name="next-steps"></a>Passos Seguintes

Para aprender a utilizar a função de monitorização do Desempenho, consulte o Monitor View Azure para desempenho dos [VMs](vminsights-performance.md). Para visualizar as dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).
