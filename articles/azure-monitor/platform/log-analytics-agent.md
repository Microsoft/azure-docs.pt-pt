---
title: Recolher dados de registo com o agente do Log Analytics do Azure | Documentos da Microsoft
description: Este tópico ajuda-o a compreender como recolher dados e monitorizar computadores alojados no Azure, no local ou outro ambiente de cloud com o Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: 436685f3bba58ed7d06dfe834d808e7fe422176b
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751975"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Recolher dados de registo com o agente do Log Analytics do Azure

O agente do Azure Log Analytics, anteriormente conhecido como o agente Microsoft Monitoring Agent (MMA) ou do Linux do OMS, foi desenvolvido para gerenciamento abrangente em máquinas no local, computadores monitorizados por [System Center Operations Manager ](https://docs.microsoft.com/system-center/scom/)e as máquinas virtuais em qualquer cloud. Os agentes Windows e Linux anexar a um Azure Monitor e armazenam os dados recolhidos de registo de origens diferentes na sua área de trabalho do Log Analytics, bem como quaisquer registos exclusivos ou métricas, tal como definido numa solução de monitorização. 

Este artigo fornece uma visão geral detalhada do agente, sistema e os requisitos de rede e os métodos de implementação diferentes.   

## <a name="overview"></a>Descrição geral

![Diagrama de comunicação de agente do log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Antes de analisar e atuar sobre os dados recolhidos, tem primeiro de instalar e ligar os agentes para todas as máquinas que pretende enviar dados para o serviço do Azure Monitor. Pode instalar agentes nas suas VMs do Azure com a extensão de VM do Azure Log Analytics para Windows e Linux e para as máquinas num ambiente híbrido utilizando a configuração, a linha de comandos, ou com o Desired State Configuration (DSC) na automatização do Azure. 

O agente para Linux e Windows comunica com o serviço do Azure Monitor saído através da porta TCP 443 e, se o computador liga-se através de um servidor de firewall ou proxy para comunicar através da Internet, reveja os requisitos abaixo para compreender a configuração de rede é necessário. Se as políticas de segurança de TI não permitir que os computadores na rede para ligar à Internet, pode configurar uma [gateway do Log Analytics](gateway.md) e, em seguida, configurar o agente para ligar através do gateway para registos do Azure Monitor. O agente pode, em seguida, receber informações de configuração e enviar os dados recolhidos, consoante os dados, as regras de recolha e soluções de monitorização tiver ativado na sua área de trabalho. 

Se estiver a monitorizar um computador com o System Center Operations Manager 2012 R2 ou posterior, pode ser multihomed com o serviço do Azure Monitor para recolher dados e reencaminhar para o serviço e ainda ser monitorizadas pelo [Operations Manager](../../azure-monitor/platform/om-agents.md). Computadores Linux, o agente não inclui um componente de serviço de estado de funcionamento, como faz o agente do Windows e as informações são recolhidas e processa por um servidor de gestão em seu nome. Como computadores com Linux são monitorizadas de forma diferente com o Operations Manager, eles não recebem a configuração ou recolher dados diretamente e reencaminhar através do grupo de gestão, como um sistema gerido por agente do Windows. Como resultado, este cenário não é suportado com computadores com Linux relatórios para o Operations Manager.  

O agente do Windows pode relatar até quatro áreas de trabalho do Log Analytics, enquanto o agente Linux só suporta a geração de relatórios para um único espaço de trabalho.  

O agente para Linux e Windows não é apenas para ligar ao Azure Monitor, também suporta a automatização do Azure para alojar a função de trabalho de Runbook híbrida e outros serviços, como [controlo de alterações](../../automation/change-tracking.md) e [degestãodeatualizações](../../automation/automation-update-management.md). Para obter mais informações sobre a função de trabalho de Runbook híbrida, veja [Runbook Worker híbrido do Azure Automation](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemas de operativos do Windows
As seguintes versões do sistema operativo Windows são suportadas oficialmente para o agente do Windows:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, versão 1709 e versão 1803
* Windows 7 SP1 e posterior

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados
Esta secção fornece detalhes sobre as distribuições suportadas de Linux.    

A partir do versões lançadas em Agosto de 2018, estamos a efetuar as seguintes alterações ao nosso modelo de suporte:  

* Apenas o servidor são suportadas as versões, não cliente.  
* As novas versões do [distribuições aprovadas pelo Linux do Azure](../../virtual-machines/linux/endorsed-distros.md) sempre são suportados.  
* Todas as versões secundárias são suportadas para cada versão principal listado.
* Não são suportadas versões que passaram a data de fim do suporte do respetivo fabricante.  
* Novas versões de AMI não são suportadas.  
* Apenas as versões que executam o SSL 1.x por predefinição são suportados.

>[!NOTE]
>Se estiver a utilizar pela distro ou uma versão que não é atualmente suportada e não se alinham com o nosso modelo de suporte, recomendamos que bifurcar deste repositório, ter consciência de que o suporte da Microsoft não irá fornecer assistência com o agente bifurcado versões.

* Amazon Linux 2017.09 (x64)
* Linux centOS 6 (x86/x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64) e 18.04 LTS (x64) 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só é suportado em plataformas com x86_x64 (64-bit) e OpenSSL anteriormente 1.x não é suportada em qualquer plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela seguinte realça os pacotes necessários para as distribuições suportadas de Linux que o agente será instalado no.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca de C do GNU | 2.5-12 
|OpenSSL    | Bibliotecas de OpenSSL | 1.0.x ou 1.1.x |
|Curl | cliente de web de cURL | 7.15.5 |
|Ctypes de Python | | 
|PAM | Módulos de autenticação conectável | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessárias para recolher as mensagens syslog. O daemon de syslog padrão na versão 5 da versão do Oracle Linux, CentOS e Red Hat Enterprise Linux (sysklog) não é suportado para a recolha de eventos do syslog. Para recolher dados de syslog a partir desta versão nessas distribuições, o daemon de rsyslog deve ser instalado e configurado para substituir sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2
Para garantir a segurança dos dados em trânsito para registos do Azure Monitor, recomendamos que configure o agente para utilizar, pelo menos, Transport Layer Security (TLS) 1.2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**.  Para obter mais informações, consulte [enviar dados de forma segura através de TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede
As informações abaixo lista as informações de configuração de proxy e de firewall necessárias para o agente Linux e Windows para comunicar com os registos do Azure Monitor.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  
|*.azure-automation.net |Porta 443 |Saída|Sim |  

Para informações de firewall necessárias para o Azure Government, consulte [gestão do Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se planeja usar a função de trabalho de Runbook de híbrida de automatização do Azure para ligar e registar com o serviço de automatização para utilizar runbooks no seu ambiente, tem de ter acesso para o número de porta e os URLs descritos em [configurar sua rede para o Runbook Worker híbrido](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

O agente do Windows e Linux suporta a comunicação por meio de um servidor proxy ou gateway do Log Analytics para o Azure Monitor utilizando o protocolo HTTPS.  Autenticação anónima e básica (nome de utilizador/palavra-passe) são suportados.  Para o agente de Windows ligado diretamente ao serviço, a configuração de proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) no painel de controlo ou com o PowerShell.  

Para o agente do Linux, o servidor proxy é especificado durante a instalação ou [após a instalação](agent-manage.md#update-proxy-settings) ao modificar o ficheiro de configuração de proxy. Conf.  O valor de configuração de proxy de agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se o servidor proxy não requer que se autentique, o agente Linux ainda exige que fornecer um utilizador de pseudo-autenticação/palavra-passe. Isso pode ser qualquer nome de utilizador ou palavra-passe.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | https |
|Utilizador | Nome de utilizador opcional para a autenticação de proxy |
|palavra-passe | Palavra-passe opcional para a autenticação de proxy |
|proxyhost | Endereço ou FQDN do gateway de análise de registo do servidor proxy |
|porta | Número de porta opcional para o gateway de análise de registo do servidor de proxy |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se utilizar carateres especiais, tais como "\@" a palavra-passe, receberá um erro de ligação de proxy porque o valor é analisado incorretamente.  Para contornar este problema, codificar a palavra-passe no URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente 
Ligar máquinas na sua subscrição do Azure ou um ambiente híbrido diretamente com os registos do Azure Monitor pode ser feito através de métodos diferentes, dependendo das suas necessidades. A tabela seguinte realça a cada método para determinar o que funciona melhor na sua organização.

|Origem | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| -Log Analytics VM extensão para o [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) com a CLI do Azure ou com um modelo Azure Resource Manager<br>- [Manualmente a partir do portal do Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve-los para uma área de trabalho existente do Azure Monitor.|
| Computador híbrido com Windows|- [Instalação manual](agent-windows.md)<br>- [DSC de automatização do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instalar o Microsoft Monitoring agent a partir da linha de comando ou usando um método automatizado, como o DSC de automatização do Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou com um modelo Azure Resource Manager, se tiver implementado Microsoft O Azure Stack no seu datacenter.| 
| Computador híbrido com Linux| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamar um script de wrapper alojado no GitHub. | 
| System Center Operations Manager|[Integrar o Operations Manager com o Log Analytics](../../azure-monitor/platform/om-agents.md) | Configurar a integração entre o Operations Manager e o Azure Monitor registos para reencaminhar dados recolhidos de computadores Windows para um grupo de gestão de relatórios.|  

## <a name="next-steps"></a>Passos Seguintes

* Revisão [origens de dados](../../azure-monitor/platform/agent-data-sources.md) para compreender as origens de dados disponíveis para recolher dados do seu sistema Windows ou Linux. 

* Saiba mais sobre [registar as consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 

* Saiba mais sobre [soluções de monitorização](../../azure-monitor/insights/solutions.md) que adicionar a funcionalidade para o Azure Monitor e também, recolher dados para a área de trabalho do Log Analytics.
