---
title: Visão geral do agente de Log Analytics
description: Este tópico ajuda-o a compreender como recolher dados e monitorizar computadores hospedados em Azure, no local ou em outro ambiente na nuvem com log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 08c80b0a37e21d53fc974bd878501b9326c9449b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207229"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do agente de Log Analytics
O agente Azure Log Analytics foi desenvolvido para uma gestão abrangente através de máquinas virtuais em qualquer nuvem, máquinas no local, e as monitorizadas pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Os agentes Windows e Linux enviam dados recolhidos de diferentes fontes para o seu espaço de trabalho Log Analytics no Monitor Azure, bem como quaisquer registos ou métricas únicos, conforme definido numa solução de monitorização. O agente Log Analytics também suporta insights e outros serviços no Azure Monitor, tais como [O Monitor Azure para VMs,](../insights/vminsights-enable-overview.md) [Azure Security Center](/azure/security-center/)e [Azure Automation.](../../automation/automation-intro.md)

Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede, bem como dos diferentes métodos de implementação.

> [!NOTE]
> Pode também ver o agente Log Analytics referido como o Agente de Monitorização da Microsoft (MMA) ou o agente OMS Linux.

> [!NOTE]
> A extensão azure Diagnostics é um dos agentes disponíveis para recolher dados de monitorização do sistema operativo convidado de recursos computacionais. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para obter uma descrição dos diferentes agentes e orientação sobre a seleção dos agentes apropriados para os seus requisitos.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com extensão de diagnóstico de Azure
A extensão de [diagnóstico azure](diagnostics-extension-overview.md) no Monitor Azure também pode ser usada para recolher dados de monitorização do sistema operativo convidado de máquinas virtuais Azure. Pode optar por utilizar ambos ou ambos, dependendo dos seus requisitos. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para uma comparação detalhada dos agentes do Monitor Azure. 

As principais diferenças a ter em conta são:

- A extensão de diagnóstico azure só pode ser utilizada com máquinas virtuais Azure. O agente Log Analytics pode ser usado com máquinas virtuais em Azure, outras nuvens e no local.
- A extensão azure diagnostics envia dados para o Armazenamento Azure, [Métricas do Monitor Azure](data-platform-metrics.md) (apenas para windows) e Centros de Eventos. O agente Log Analytics recolhe dados para registos do [Monitor Azure](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Monitor Azure para VMs,](../insights/vminsights-overview.md)e outros serviços como [o Azure Security Center](/azure/security-center/).

## <a name="costs"></a>Custos
Não há qualquer custo para o agente Log Analytics, mas pode incorrer em encargos para os dados ingeridos. Verifique [gerir a utilização e os custos com o Azure Monitor Logs](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados recolhidos num espaço de trabalho do Log Analytics.

## <a name="data-collected"></a>Dados recolhidos
A tabela que se segue lista os tipos de dados que pode configurar um espaço de trabalho do Log Analytics para recolher de todos os agentes conectados. Veja [o que é monitorizado pelo Azure Monitor para](../monitor-reference.md) uma lista de insights, soluções e outras soluções que utilizam o agente Log Analytics para recolher outros tipos de dados.

| Origem de Dados | Descrição |
| --- | --- |
| [Registos do Evento Windows](data-sources-windows-events.md) | Informações enviadas para o sistema de registo de eventos do Windows. |
| [Syslog](data-sources-syslog.md)                     | Informação enviada para o sistema de registo de eventos Linux. |
| [Desempenho](data-sources-performance-counters.md)  | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| [Registos do IIS](data-sources-iis-logs.md)                 | Informações de utilização para web sites IIS em execução no sistema operativo de hóspedes. |
| [Registos personalizados](data-sources-custom-logs.md)           | Eventos a partir de ficheiros de texto em computadores Windows e Linux. |

## <a name="data-destinations"></a>Destinos de dados
O agente Log Analytics envia dados para um espaço de trabalho de Log Analytics no Monitor Azure. O agente Windows pode ser multialojado para enviar dados para vários espaços de trabalho e grupos de gestão do System Center Operations Manager. O agente Linux pode enviar para um único destino.

## <a name="other-services"></a>Outros serviços
O agente do Linux e do Windows não é apenas para ligar ao Azure Monitor, também suporta a Azure Automation para acolher o papel de trabalhador do Livro Híbrido e outros serviços como [Change Tracking,](../../automation/change-tracking.md) [Update Management](../../automation/automation-update-management.md)e [Azure Security Center.](../../security-center/security-center-intro.md) Para obter mais informações sobre o papel de Trabalhador de Runbook Híbrido, consulte [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalação e configuração

Ao utilizar os agentes Log Analytics para recolher dados, é necessário compreender o seguinte para planear a implementação do seu agente:

* Para recolher dados de agentes do Windows, pode [configurar cada agente para reportar a um ou mais espaços](agent-windows.md)de trabalho , mesmo quando está a reportar a um grupo de gestão de Gestão de Operações do System Center. O agente do Windows pode reportar até quatro espaços de trabalho.
* O agente Linux não suporta multi-homing e só pode reportar a um único espaço de trabalho.
* O agente Windows suporta a [norma FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), enquanto o agente Linux não o suporta.  

Se estiver a utilizar o System Center Operations Manager 2012 R2 ou mais tarde:

* Cada grupo de gestão do Gestor de Operações pode ser [ligado a penas apenas um espaço de trabalho.](om-agents.md)
* Os computadores Linux que reportem a um grupo de gestão devem ser configurados para reportar diretamente a um espaço de trabalho de Log Analytics. Se os seus computadores Linux já estão a reportar diretamente a um espaço de trabalho e pretende monitorizá-los com o Gestor de Operações, siga estes passos para reportar a um grupo de gestão do Gestor de [Operações](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Pode instalar o agente Windows de Log Analytics no computador Windows e fazê-lo reportar tanto ao Gestor de Operações integrado com um espaço de trabalho e a um espaço de trabalho diferente.


Existem vários métodos para instalar o agente Log Analytics e ligar a sua máquina ao Monitor Azure, dependendo dos seus requisitos. A tabela seguinte destaca cada método para determinar qual o melhor funciona na sua organização.

|Origem | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| [Manualmente do portal Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Especifique VMs para implantar a partir do espaço de trabalho Log Analytics. |
| | Extensão VM de Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) utilizando o Azure CLI ou com um modelo de Gestor de Recursos Azure | A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve-as num espaço de trabalho existente no Azure Monitor. |
| | [Azure Monitor para VMs](../insights/vminsights-enable-overview.md) | Quando ativa a monitorização com o Monitor Azure para VMs, instala a extensão e o agente Log Analytics. |
| | [Fornecimento automático do Centro de Segurança Azure](../../security-center/security-center-enable-data-collection.md) | O Azure Security Center pode fornecer o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados se lhe permitir monitorizar vulnerabilidades e ameaças de segurança. Se ativado, qualquer VM novo ou existente sem um agente instalado será provisionado. |
| Computador híbrido com Windows| [Instalação manual](agent-windows.md) | Instale o agente de monitorização da Microsoft a partir da linha de comando. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizar a instalação com o Azure Automation DSC. |
| | [Modelo de gestor de recursos com pilha azure](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Utilize um modelo de Gestor de Recursos Azure se tiver implementado o Microsoft Azure Stack no seu datacenter.| 
| Computador híbrido com Linux| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para linux chamando um script de invólucro hospedado no GitHub. | 
| System Center Operations Manager|[Integrar o Gestor de Operações com Log Analytics](../../azure-monitor/platform/om-agents.md) | Configure a integração entre o Gestor de Operações e os registos do Monitor Azure para reencaminhar dados recolhidos de computadores Windows reportando a um grupo de gestão.|  


## <a name="supported-windows-operating-systems"></a>Sistemas operativos Windows suportados

As seguintes versões do sistema operativo Windows são oficialmente suportadas para o agente Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise e Pro
* Windows 8 Enterprise e Pro 
* Windows 7 SP1

>[!NOTE]
>Embora o agente Log Analytics para windows tenha sido projetado para suportar cenários de monitorização do servidor, percebemos que pode executar o cliente do Windows para suportar cargas de trabalho configuradas e otimizadas para o sistema operativo do servidor. O agente suporta o cliente Windows, no entanto as nossas soluções de monitorização não se focam em cenários de monitorização do cliente, a menos que explicitamente indicado.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

Esta secção fornece detalhes sobre as distribuições linux suportadas.

Começando com versões lançadas após agosto de 2018, estamos a fazer as seguintes alterações ao nosso modelo de suporte:  

* Apenas as versões do servidor são suportadas, não cliente.  
* Concentre-se em qualquer uma das [distros apoiadas pelo Azure Linux.](../../virtual-machines/linux/endorsed-distros.md) Note que pode haver algum atraso entre um novo distro/versão sendo O Endosso Do Azure Linux e sendo suportado para o agente Log Analytics Linux.
* Todas as versões menores são suportadas para cada versão principal listada.
* As versões que tenham ultrapassado a data de fim de suporte do seu fabricante não são suportadas.  
* Não são suportadas novas versões de AMI.  
* Apenas são suportadas versões que executam sSL 1.x por padrão.

>[!NOTE]
>Se estiver a utilizar um distro ou versão que não seja suportado atualmente e não se alinha com o nosso modelo de suporte, recomendamos que desista deste repo, reconhecendo que o suporte da Microsoft não irá fornecer assistência com versões de agente bifurcado.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) e 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>O OpenSSL 1.1.0 só é suportado em plataformas x86_x64 (64 bits) e openSSL mais cedo do que 1.x não é suportado em nenhuma plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela seguinte realça os pacotes necessários para distros linux suportados em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas openssl | 1.0.x ou 1.1.x |
|Curl | cURL web cliente | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de Autenticação Incorporável | | 

>[!NOTE]
>Ou o rsyslog ou o syslog-ng são obrigados a recolher mensagens syslog. O daemon padrão de slog na versão 5 da versão Red Hat Enterprise Linux, CentOS e Oracle Linux (sysklog) não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o daemon rsyslog deve ser instalado e configurado para substituir o sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os registos do Monitor Azure, encorajamo-lo vivamente a configurar o agente para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. As versões mais antigas da Camada de Tomadas TLS/Secure (SSL) revelaram-se vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**.  Para obter informações adicionais, reveja o [envio de dados de forma segura utilizando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito de suporte de assinatura de código SHA-2 para Windows
O agente Windows começará a utilizar exclusivamente a assinatura SHA-2 no dia 18 de maio de 2020. Esta alteração terá impacto nos clientes que utilizam o agente Log Analytics num sistema operativo legado como parte de qualquer serviço Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). A alteração não requer qualquer ação do cliente a menos que esteja a executar o agente numa versão antiga do SISTEMA (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes que executam uma versão osística legado são obrigados a tomar as seguintes ações nas suas máquinas antes de 18 de maio de 2020 ou os seus agentes deixarão de enviar dados para os seus espaços de trabalho Log Analytics:

1. Instale o mais recente Pacote de Serviços para o seu SISTEMA. As versões de pacote de serviço seleções necessárias são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações do Windows de assinatura SHA-2 para o seu SISTEMA, conforme descrito em 2019, o requisito de suporte de assinatura de [código SHA-2 para Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualização para a versão mais recente do agente Windows (versão 10.20.18029).
4. Recomendado configurar o agente para [utilizar TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisitos da rede
O agente do Linux e do Windows comunica a saída para o serviço Azure Monitor através da porta TCP 443, e se a máquina se ligar através de uma firewall ou servidor proxy para comunicar através da Internet, reveja os requisitos abaixo para entender a configuração de rede necessária. Se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à Internet, pode configurar um [gateway Log Analytics](gateway.md) e, em seguida, configurar o agente para ligar através da porta de entrada para os registos do Monitor Azure. O agente pode então receber informações de configuração e enviar dados recolhidos dependendo das regras de recolha de dados e soluções de monitorização que tenha ativado no seu espaço de trabalho.

![Diagrama de comunicação do agente de log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela que se segue lista as informações de configuração de procuração e firewall que são necessárias para que os agentes linux e Windows se comuniquem com os registos do Monitor Azure.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porto 443 |Entrada e saída|Sim |  
|*.oms.opinsights.azure.com |Porto 443 |Entrada e saída|Sim |  
|*.blob.core.windows.net |Porto 443 |Entrada e saída|Sim |
|*.azure-automation.net |Porto 443 |Entrada e saída|Sim |
|*.azure.com |Porto 443|Entrada e saída|Sim |

Para obter informações sobre firewall necessárias para o Governo de Azure, consulte a [gestão do Governo azure.](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs) 

Se planeia utilizar o Trabalhador do Livro Híbrido Azure Automation para se ligar e registar-se no serviço automationpara utilizar livros de execução ou soluções de gestão no seu ambiente, deve ter acesso ao número de porta e aos URLs descritos na Configuração da sua rede para o Trabalhador do [Livro de Execução Híbrido.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

### <a name="proxy-configuration"></a>Configuração do proxy

O agente Windows e Linux suporta a comunicação através de um servidor proxy ou de log analytics para o Monitor Azure utilizando o protocolo HTTPS.  A autenticação anónima e básica (nome de utilizador/palavra-passe) são suportadas.  Para o agente Windows ligado diretamente ao serviço, a configuração proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) do Painel de Controlo ou com powerShell.  

Para o agente Linux, o servidor proxy é especificado durante a instalação ou após a [instalação](agent-manage.md#update-proxy-settings) modificando o ficheiro de configuração proxy.conf.  O valor de configuração proxy do agente Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se o seu servidor proxy não necessitar que você aprove, o agente Linux ainda necessita de fornecer um pseudo utilizador/senha. Isto pode ser qualquer nome de utilizador ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | https |
|utilizador | Nome de utilizador opcional para autenticação por procuração |
|palavra-passe | Senha opcional para autenticação por procuração |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway Log Analytics |
|porta | Número de porta opcional para o servidor proxy/gateway Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se utilizar caracteres especiais\@como " " na sua palavra-passe, recebe um erro de ligação proxy porque o valor é analisado incorretamente.  Para resolver este problema, codifique a palavra-passe no URL utilizando uma ferramenta como [urlDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Passos seguintes

* Reveja [as fontes](agent-data-sources.md) de dados para compreender as fontes de dados disponíveis para recolher dados do seu sistema Windows ou Linux. 
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados. 
* Saiba mais sobre soluções de [monitorização](../insights/solutions.md) que acrescentem funcionalidade ao Monitor Azure e também recolha dados no espaço de trabalho do Log Analytics.

