---
title: Visão geral do agente do Log Analytics
description: Este tópico ajuda-o a compreender como recolher dados e monitorizar computadores hospedados no Azure, no local ou em outro ambiente em nuvem com o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2020
ms.openlocfilehash: e1fa2fe11873d08fae5add1ee3206f6f887975eb
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88960922"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do agente do Log Analytics
O agente Azure Log Analytics foi desenvolvido para uma gestão abrangente através de máquinas virtuais em qualquer nuvem, máquinas no local, e as monitorizadas pelo [System Center Operations Manager](/system-center/scom/). Os agentes windows e Linux enviam dados recolhidos de diferentes fontes para o seu espaço de trabalho Log Analytics no Azure Monitor, bem como quaisquer registos ou métricas únicos definidos numa solução de monitorização. O agente Log Analytics também suporta insights e outros serviços no Azure Monitor, como [o Azure Monitor para VMs,](../insights/vminsights-enable-overview.md) [Azure Security Center,](../../security-center/index.yml)e [Azure Automation.](../../automation/automation-intro.md)

Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede, bem como dos diferentes métodos de implantação.

> [!NOTE]
> Também pode ver o agente Log Analytics referido como o Agente de Monitorização da Microsoft (MMA) ou o agente OMS Linux.

> [!NOTE]
> A extensão Azure Diagnostics é um dos agentes disponíveis para recolher dados de monitorização do sistema operativo de recursos computatórios dos hóspedes. Consulte [a visão geral dos agentes do Monitor Azure ](agents-overview.md) para obter uma descrição dos diferentes agentes e orientações sobre a seleção dos agentes adequados para os seus requisitos.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de diagnósticos Azure
A [extensão de diagnóstico Azure](diagnostics-extension-overview.md) no Azure Monitor também pode ser usada para recolher dados de monitorização do sistema operativo convidado de máquinas virtuais Azure. Pode optar por utilizar qualquer um ou ambos dependendo dos seus requisitos. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para uma comparação detalhada dos agentes do Monitor Azure. 

As principais diferenças a ter em conta são:

- A extensão de diagnóstico Azure só pode ser usada com máquinas virtuais Azure. O agente Log Analytics pode ser utilizado com máquinas virtuais em Azure, outras nuvens e no local.
- A extensão Azure Diagnostics envia dados para Azure Storage, [Azure Monitor Metrics (apenas](data-platform-metrics.md) Windows) e Centros de Eventos. O agente Log Analytics recolhe dados para os [registos do Monitor Azure](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor para VMs, e outros serviços](../insights/vminsights-overview.md)como [o Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Custos
Não há qualquer custo para o agente Log Analytics, mas pode incorrer em encargos para os dados ingeridos. Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados recolhidos num espaço de trabalho do Log Analytics.

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
O agente Log Analytics envia dados para um espaço de trabalho log analytics no Azure Monitor. O agente Windows pode ser multihomed para enviar dados para vários espaços de trabalho e grupos de gestão de Gestão de Operações do System Center. O agente Linux pode enviar para apenas um destino.

## <a name="other-services"></a>Outros serviços
O agente do Linux e windows não é apenas para ligar ao Azure Monitor, também suporta a Azure Automation para acolher o papel de trabalhador do Runbook Híbrido e outros serviços como [Change Tracking,](../../automation/change-tracking.md) [Update Management](../../automation/update-management/update-mgmt-overview.md)e [Azure Security Center.](../../security-center/security-center-intro.md) Para obter mais informações sobre o papel de Trabalhador de Runbook Híbrido, consulte [o Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalação e configuração

Ao utilizar os agentes do Log Analytics para recolher dados, tem de compreender o seguinte para planear a implementação do seu agente:

* Para recolher dados de agentes do Windows, pode [configurar cada agente para reportar a um ou mais espaços de trabalho](agent-windows.md), mesmo quando está a reportar a um grupo de gestão de Gestores de Operações do System Center. O agente windows pode reportar até quatro espaços de trabalho.
* O agente Linux não suporta multi-homing e só pode reportar-se a um único espaço de trabalho.
* O agente Windows suporta a [norma FIPS 140,](/windows/security/threat-protection/fips-140-validation)enquanto o agente Linux não o suporta.  

Se estiver a utilizar o Gestor de Operações do Centro de Sistema 2012 R2 ou mais tarde:

* Cada grupo de gestão de Gestores de Operações pode ser [ligado a apenas um espaço de trabalho.](om-agents.md)
* Os computadores Linux que reportam a um grupo de gestão devem ser configurados para reportar diretamente a um espaço de trabalho log analytics. Se os seus computadores Linux já estão a reportar diretamente a um espaço de trabalho e pretende monitorá-los com o Gestor de Operações, siga estes passos para [reportar a um grupo de gestão de Gestores de Operações.](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)
* Pode instalar o agente Do Windows do Log Analytics no computador Windows e informá-lo para ambos os Gestores de Operações integrados com um espaço de trabalho e um espaço de trabalho diferente.


Existem vários métodos para instalar o agente Log Analytics e ligar a sua máquina ao Azure Monitor, dependendo dos seus requisitos. A tabela a seguir destaca cada método para determinar qual o melhor funciona na sua organização.

|Origem | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| [Manualmente a partir do portal Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Especifique os VMs para implantar a partir do espaço de trabalho Do Log Analytics. |
| | Log Analytics VM extensão para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando Azure CLI ou com um modelo de Gestor de Recursos Azure | A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve-as num espaço de trabalho existente do Azure Monitor. |
| | [Azure Monitor para VMs](../insights/vminsights-enable-overview.md) | Quando ativa a monitorização com o Azure Monitor para VMs, instala a extensão e o agente Log Analytics. |
| | [Azure Security Center Provisioning automático](../../security-center/security-center-enable-data-collection.md) | O Azure Security Center pode providenciar o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados se lhe permitir monitorizar vulnerabilidades e ameaças de segurança. Se estiver ativado, qualquer VM novo ou existente sem um agente instalado será a provisionado. |
| Computador híbrido com Windows| [Instalação manual](agent-windows.md) | Instale o agente de monitorização da Microsoft a partir da linha de comando. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizar a instalação com a Azure Automation DSC. |
| | [Modelo de gestor de recursos com pilha de Azure](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Utilize um modelo de Gestor de Recursos Azure se tiver implementado o Microsoft Azure Stack no seu datacenter.| 
| Computador híbrido com Linux| [Instalação manual](../learn/quick-collect-linux-computer.md)|Instale o agente para linux chamando um script de invólucro hospedado no GitHub. | 
| System Center Operations Manager|[Integrar Gestor de Operações com Log Analytics](./om-agents.md) | Configure a integração entre o Gestor de Operações e os registos do Azure Monitor para encaminhar dados recolhidos de computadores Windows reportando a um grupo de gestão.|  


## <a name="supported-windows-operating-systems"></a>Sistemas operativos Windows suportados

As seguintes versões do sistema operativo Windows são oficialmente suportadas para o agente Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multi-sessão) e Pro
* Windows 8 Enterprise e Pro 
* Windows 7 SP1

>[!NOTE]
>Embora o agente Log Analytics para o Windows tenha sido concebido para suportar cenários de monitorização do servidor, percebemos que pode executar o cliente windows para suportar cargas de trabalho configuradas e otimizadas para o sistema operativo do servidor. O agente suporta o cliente Windows, no entanto as nossas soluções de monitorização não se focam em cenários de monitorização do cliente, a menos que explicitamente indicado.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

Esta secção fornece detalhes sobre as distribuições de Linux suportadas.

Começando com versões lançadas após agosto de 2018, estamos a fazer as seguintes alterações ao nosso modelo de suporte:  

* Apenas as versões do servidor são suportadas, não clientes.  
* Concentre o suporte em qualquer um dos [distros Azure Linux Endossados.](../../virtual-machines/linux/endorsed-distros.md) Note que pode haver algum atraso entre um novo distro/versão sendo Azure Linux Endossado e suportado para o agente Log Analytics Linux.
* Todos os lançamentos menores são suportados para cada versão principal listada.
* As versões que tenham passado a data de fim de suporte do fabricante não são suportadas.  
* As novas versões da AMI não são suportadas.  
* Apenas as versões que executam o SSL 1.x por padrão são suportadas.

>[!NOTE]
>Se estiver a utilizar um distro ou versão que não esteja atualmente suportado e não esteja alinhado com o nosso modelo de suporte, recomendamos que você forque este repo, reconhecendo que o suporte da Microsoft não fornecerá assistência com versões de agentes forcados.


### <a name="python-2-requirement"></a>Requisito python 2
 O agente Log Analytics requer Python 2. Se a sua máquina virtual estiver a utilizar um distro que não inclua python 2 por defeito, então deve instalá-lo. Os seguintes comandos de amostra instalarão Python 2 em diferentes distros.

 - Chapéu Vermelho, CentOS, Oráculo: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

A piton2 executável deve ser aliasada à *pitão* utilizando o seguinte procedimento:

1. Executar o seguinte comando para ver qualquer pseudónimo de python atual, se um existir. Se o fizer, tome nota da prioridade para o próximo passo.
 
    ```
    sudo update-alternatives ––display python
    ```

2. Execute o seguinte comando. Substitua *\<priority\>* por um número maior do que a prioridade de qualquer ligação existente, ou 1 se não existirem atualmente ligações.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 <priority>
    ```

### <a name="supported-distros"></a>Distros apoiados

As seguintes versões do sistema operativo Linux são oficialmente suportadas para o agente Linux:

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) e 8 (x64)
* Debian GNU/Linux 8 e 9 (x64)
* Ubuntu 14.04 LTS (x86/x64), 16,04 LTS (x64) e 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>O OpenSSL 1.1.0 só é suportado em plataformas x86_x64 (64 bits) e o OpenSSL antes de 1.x não é suportado em nenhuma plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela seguinte realça as embalagens necessárias para os distros Linux suportados em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0.x ou 1.1.x |
|Curl | cURL cliente web | 7.15.5 |
|Python | | 2.6+ ou 3.3+
|Pitão-ctypes | | 
|PAM | Módulos de Autenticação Incorporável | | 

>[!NOTE]
>Ou rssyslog ou syslog-ng são necessários para recolher mensagens syslog. O syslog daemon padrão na versão 5 da red hat Enterprise Linux, CentOS e oracle Linux versão (sysklog) não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o anão de rsyslog deve ser instalado e configurado para substituir o sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os registos do Azure Monitor, encorajamo-lo a configurar o agente para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**.  Para obter informações adicionais, [reveja o envio de dados de forma segura utilizando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito de suporte de assinatura de código SHA-2 para windows
O agente do Windows começará a utilizar exclusivamente a assinatura SHA-2 no dia 2 de novembro de 2020. Esta alteração irá afetar os clientes que utilizam o agente Log Analytics num sistema operativo legado como parte de qualquer serviço Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). A alteração não requer qualquer ação do cliente a menos que esteja a executar o agente numa versão antiga do SISTEMA (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes que executam uma versão de SISTEMA legado são obrigados a tomar as seguintes ações nas suas máquinas antes de 2 de novembro de 2020 ou os seus agentes deixarão de enviar dados para os seus espaços de trabalho Log Analytics:

1. Instale o mais recente Pacote de Serviços para o seu SISTEMA. As versões de pacote de serviços necessárias são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações de assinatura SHA-2 do Windows para o seu SISTEMA como descrito em [2019 Requisito de Suporte de Assinatura de Código SHA-2 para Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualização para a versão mais recente do agente do Windows (versão 10.20.18029).
4. Recomendado para configurar o agente para [utilizar o TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisitos da rede
O agente do Linux e do Windows comunica a saída do serviço Azure Monitor através da porta TCP 443, e se a máquina ligar através de uma firewall ou servidor de procuração para comunicar através da Internet, rever os requisitos abaixo para entender a configuração de rede necessária. Se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à Internet, pode configurar um [gateway Log Analytics](gateway.md) e, em seguida, configurar o agente para ligar através do gateway para os registos do Azure Monitor. O agente pode então receber informações de configuração e enviar dados recolhidos dependendo das regras de recolha de dados e das soluções de monitorização que ativou no seu espaço de trabalho.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela que se segue lista as informações de configuração de procuração e firewall necessárias para que os agentes Linux e Windows comuniquem com os registos do Azure Monitor.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porto 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porto 443 |Saída|Sim |  
|*.blob.core.windows.net |Porto 443 |Saída|Sim |
|*.azure-automation.net |Porto 443 |Saída|Sim |

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

