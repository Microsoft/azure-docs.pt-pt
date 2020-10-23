---
title: Visão geral do agente do Log Analytics
description: Este tópico ajuda-o a compreender como recolher dados e monitorizar computadores hospedados no Azure, no local ou em outro ambiente em nuvem com o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: c09b50272ce73fe084e84d49be7d9d81cbe725aa
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207143"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do agente do Log Analytics
O agente Azure Log Analytics recolhe telemetria a partir de máquinas virtuais Windows e Linux em qualquer nuvem, máquinas no local, e as monitorizadas pelo [System Center Operations Manager](/system-center/scom/) e envia-os dados recolhidos para o seu espaço de trabalho Log Analytics no Azure Monitor. O agente Log Analytics também suporta insights e outros serviços no Azure Monitor, como [o Azure Monitor para VMs,](../insights/vminsights-enable-overview.md) [Azure Security Center,](../../security-center/index.yml)e [Azure Automation.](../../automation/automation-intro.md) Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede e dos métodos de implantação.

> [!NOTE]
> Também pode ver o agente Log Analytics referido como o Agente de Monitorização da Microsoft (MMA) ou o agente OMS Linux.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de diagnósticos Azure
A [extensão de diagnóstico Azure](diagnostics-extension-overview.md) no Azure Monitor também pode ser usada para recolher dados de monitorização do sistema operativo convidado de máquinas virtuais Azure. Pode optar por utilizar qualquer um ou ambos dependendo dos seus requisitos. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para uma comparação detalhada dos agentes do Monitor Azure. 

As principais diferenças a ter em conta são:

- A extensão de diagnóstico Azure só pode ser usada com máquinas virtuais Azure. O agente Log Analytics pode ser utilizado com máquinas virtuais em Azure, outras nuvens e no local.
- A extensão Azure Diagnostics envia dados para Azure Storage, [Azure Monitor Metrics (apenas](data-platform-metrics.md) Windows) e Centros de Eventos. O agente Log Analytics envia dados para [registos do Monitor Azure](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor para VMs, e outros serviços](../insights/vminsights-overview.md)como [o Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Custos
Não há qualquer custo para o agente Log Analytics, mas pode incorrer em encargos para os dados ingeridos. Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados recolhidos num espaço de trabalho do Log Analytics.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

 Consulte [os sistemas operativos suportados](agents-overview.md#supported-operating-systems) para obter uma lista das versões do sistema operativo Windows e Linux que são suportadas pelo agente Log Analytics. 


## <a name="data-collected"></a>Dados recolhidos
A tabela que se segue lista os tipos de dados que pode configurar um espaço de trabalho Log Analytics para recolher de todos os agentes conectados. Ver [O que é monitorizado pelo Azure Monitor?](../monitor-reference.md)

| Origem de dados | Descrição |
| --- | --- |
| [Registos do Evento Windows](data-sources-windows-events.md) | Informação enviada para o sistema de registo de eventos do Windows. |
| [Syslog](data-sources-syslog.md)                     | Informação enviada para o sistema de registo de eventos Linux. |
| [Desempenho](data-sources-performance-counters.md)  | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| [Registos do IIS](data-sources-iis-logs.md)                 | Informações de utilização para sites IIS em execução no sistema operativo do hóspede. |
| [Registos personalizados](data-sources-custom-logs.md)           | Eventos a partir de ficheiros de texto em computadores Windows e Linux. |

## <a name="data-destinations"></a>Destinos de dados
O agente Log Analytics envia dados para um espaço de trabalho log analytics no Azure Monitor. O agente Windows pode ser multihomed para enviar dados para vários espaços de trabalho e grupos de gestão de Gestão de Operações do System Center. O agente Linux pode enviar apenas para um único destino, seja um espaço de trabalho ou um grupo de gestão.

## <a name="other-services"></a>Outros serviços
O agente do Linux e do Windows não é apenas para ligar ao Azure Monitor. Outros serviços como o Azure Security Center e o Azure Sentinel dependem do agente e do seu espaço de trabalho conectado log analytics. O agente também apoia a Azure Automation para acolher o papel de trabalhador do Runbook Híbrido e outros serviços como [Change Tracking,](../../automation/change-tracking/overview.md) [Update Management](../../automation/update-management/update-mgmt-overview.md)e [Azure Security Center](../../security-center/security-center-intro.md). Para obter mais informações sobre o papel de Trabalhador de Runbook Híbrido, consulte [o Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Espaço de trabalho e limitações do grupo de gestão

Consulte [o agente configurar para apresentar-se a um grupo de gestão de Gestores de Operações](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) para obter detalhes sobre a ligação de um agente a um grupo de gestão de Gestores de Operações.

* Os agentes do Windows podem ligar-se a até quatro espaços de trabalho, mesmo que estejam ligados a um grupo de gestão de Gestores de Operações do System Center.
* O agente Linux não suporta multi-homing e só pode ligar-se a um único espaço de trabalho ou grupo de gestão.


## <a name="security-limitations"></a>Limitações de segurança

* Os agentes Windows e Linux suportam a [norma FIPS 140,](/windows/security/threat-protection/fips-140-validation)mas [outros tipos de endurecimento podem não ser suportados](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Opções de instalação

Existem vários métodos para instalar o agente Log Analytics e ligar a sua máquina ao Azure Monitor, dependendo dos seus requisitos. As secções seguintes listam os métodos possíveis para diferentes tipos de máquina virtual.
> [!NOTE]
> Não é suportado para clonar uma máquina com o Agente Desafiado de Log já configurado. Se o agente já foi associado a um espaço de trabalho, isto não funcionará para 'imagens douradas'.

### <a name="azure-virtual-machine"></a>Máquina virtual do Azure

- [O Azure Monitor para VMs](../insights/vminsights-enable-overview.md) fornece múltiplos métodos que permitem agentes à escala. Isto inclui a instalação do agente Log Analytics e do agente Desadependição. 
- [O Azure Security Center pode providenciar o agente Log Analytics](../../security-center/security-center-enable-data-collection.md) em todos os VMs Azure suportados e quaisquer novos que sejam criados se lhe permitir monitorizar vulnerabilidades e ameaças de segurança.
- Registar a extensão VM do [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) pode ser instalado com o portal Azure CLI, Azure PowerShell ou um modelo Azure Resource Manager.
- Instale manualmente máquinas virtuais Azure [a partir do portal Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Máquina virtual do Windows no local ou em outra nuvem 

- [Instale manualmente](agent-windows.md) o agente a partir da linha de comando.
- Automatizar a instalação com [a Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Use um [modelo de gestor de recursos com pilha de Azure](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Máquina virtual Linux no local ou em outra nuvem

- [Instale manualmente](../learn/quick-collect-linux-computer.md) o agente que chama um script de invólucro alojado no GitHub.
- Gestor de Operações do Centro de Sistemas [Integrar gestor de operações com o Azure Monitor](./om-agents.md) para encaminhar dados recolhidos de computadores Windows reportando a um grupo de gestão.

## <a name="workspace-id-and-key"></a>ID do espaço de trabalho e chave
Independentemente do método de instalação utilizado, irá requerer o ID do espaço de trabalho e a chave para o espaço de trabalho Log Analytics a que o agente irá ligar.. Selecione o espaço de trabalho do menu **de espaços de trabalho Log Analytics** no portal Azure. Em seguida, selecione **a gestão de agentes** na secção **Definições.** 

[![Detalhes do espaço de trabalho](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os registos do Azure Monitor, encorajamo-lo a configurar o agente para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**.  Para obter informações adicionais, [reveja o envio de dados de forma segura utilizando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Requisitos de rede
O agente do Linux e do Windows comunica a saída do serviço Azure Monitor sobre a porta TCP 443. Se a máquina ligar através de uma firewall ou servidor proxy para comunicar através da Internet, rever os requisitos abaixo para entender a configuração de rede necessária. Se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à Internet, pode configurar um [gateway Log Analytics](gateway.md) e, em seguida, configurar o agente para ligar através do gateway para o Azure Monitor. O agente pode então receber informações de configuração e enviar dados recolhidos.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela que se segue lista as informações de configuração de procuração e firewall necessárias para que os agentes Linux e Windows se comuniquem com os registos do Azure Monitor.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porto 443 |Saída|Yes |  
|*.oms.opinsights.azure.com |Porto 443 |Saída|Yes |  
|*.blob.core.windows.net |Porto 443 |Saída|Yes |
|*.azure-automation.net |Porto 443 |Saída|Yes |

Para obter informações sobre firewall necessárias para o Governo de Azure, consulte [a gestão do Governo Azure](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Se pretender utilizar o Azure Automation Hybrid Runbook Worker para ligar e registar-se com o serviço de Automação para utilizar runbooks ou soluções de gestão no seu ambiente, deve ter acesso ao número de porta e aos URLs descritos em [Configurar a sua rede para o Trabalhador de Runbook Híbrido.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

### <a name="proxy-configuration"></a>Configuração do proxy

O agente Windows e Linux suporta a comunicação através de um servidor proxy ou do gateway Log Analytics para o Azure Monitor utilizando o protocolo HTTPS.  A autenticação anónima e básica (nome de utilizador/palavra-passe) é suportada.  Para o agente Windows ligado diretamente ao serviço, a configuração proxy é especificada durante a instalação ou [após a colocação](agent-manage.md#update-proxy-settings) do Painel de Controlo ou com o PowerShell.  

Para o agente Linux, o servidor proxy é especificado durante a instalação ou após a [instalação](agent-manage.md#update-proxy-settings) modificando o ficheiro de configuração proxy.conf.  O valor de configuração de procuração do agente Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | https |
|utilizador | Nome de utilizador opcional para autenticação por procuração |
|palavra-passe | Senha opcional para autenticação por procuração |
|proxyhost | Endereço ou FQDN do servidor proxy/Log Analytics gateway |
|porta | Número de porta opcional para o servidor proxy/Log Analytics gateway |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se utilizar caracteres especiais como " \@ " na sua palavra-passe, recebe um erro de ligação proxy porque o valor é analisado incorretamente.  Para contornar este problema, codifique a palavra-passe no URL utilizando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Passos seguintes

* Reveja [as fontes de dados](agent-data-sources.md) para entender as fontes de dados disponíveis para recolher dados do seu sistema Windows ou Linux. 
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções. 
* Saiba mais [sobre soluções de monitorização](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também recolha dados no espaço de trabalho do Log Analytics.