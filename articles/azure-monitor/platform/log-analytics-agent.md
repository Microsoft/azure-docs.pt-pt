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
ms.date: 08/12/2019
ms.author: magoedte
ms.openlocfilehash: 6c8f9c98d645f60ea9281d1ca2aa15731c9c1e80
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954994"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Coletar dados de log com o agente de Log Analytics

O agente de Log Analytics do Azure, mencionado anteriormente como o Microsoft Monitoring Agent (MMA) ou o agente do OMS Linux, foi desenvolvido para gerenciamento abrangente em máquinas locais, computadores monitorados pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)e máquinas virtuais em qualquer nuvem. Os agentes do Windows e Linux se conectam a um Azure Monitor e armazenam dados de log coletados de fontes diferentes em seu espaço de trabalho Log Analytics, bem como quaisquer logs ou métricas exclusivos, conforme definido em uma solução de monitoramento. 

Este artigo fornece uma visão geral detalhada do agente, sistema e os requisitos de rede e os métodos de implementação diferentes.

## <a name="overview"></a>Descrição geral

![Diagrama de comunicação de agente do log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Antes de analisar e agir sobre os dados coletados, primeiro você precisa instalar e conectar agentes para todos os computadores que deseja enviar dados para o serviço de Azure Monitor. Pode instalar agentes nas suas VMs do Azure com a extensão de VM do Azure Log Analytics para Windows e Linux e para as máquinas num ambiente híbrido utilizando a configuração, a linha de comandos, ou com o Desired State Configuration (DSC) na automatização do Azure. 

O agente para Linux e Windows comunica a saída para o serviço de Azure Monitor pela porta TCP 443 e, se o computador se conecta por meio de um firewall ou servidor proxy para se comunicar pela Internet, examine os requisitos abaixo para entender a configuração de rede Necessário. Se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet, você poderá configurar um [Gateway de log Analytics](gateway.md) e, em seguida, configurar o agente para se conectar por meio do gateway para Azure monitor logs. O agente pode receber informações de configuração e enviar dados coletados, dependendo de quais regras de coleta de dados e soluções de monitoramento você habilitou em seu espaço de trabalho. 

Ao usar os agentes de Log Analytics para coletar dados, você precisa entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você pode [configurar cada agente para reportar para um ou mais espaços de trabalho](agent-windows.md), mesmo enquanto estiver relatando a um grupo de gerenciamento de System Center Operations Manager. O agente do Windows pode relatar até quatro espaços de trabalho.
* O agente do Linux não dá suporte a hospedagem múltipla e só pode relatar para um único espaço de trabalho.

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* Cada grupo de gerenciamento de Operations Manager pode ser [conectado a apenas um espaço de trabalho](om-agents.md).
* Os computadores Linux que relatam para um grupo de gerenciamento devem ser configurados para relatar diretamente a um espaço de trabalho Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com Operations Manager, siga estas etapas para [relatar a um grupo de gerenciamento de Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Você pode instalar o Log Analytics agente do Windows no computador Windows e fazer com que ele se reporte a ambos Operations Manager integrados a um espaço de trabalho e a um espaço de trabalho diferente.

O agente para Linux e Windows não é apenas para se conectar ao Azure Monitor, ele também dá suporte à automação do Azure para hospedar a função de Hybrid runbook Worker e outros serviços, como [controle de alterações](../../automation/change-tracking.md), [Gerenciamento de atualizações](../../automation/automation-update-management.md)e [central de segurança do Azure ](../../security-center/security-center-intro.md). Para obter mais informações sobre a função de trabalho de Runbook híbrida, veja [Runbook Worker híbrido do Azure Automation](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemas de operativos do Windows

As seguintes versões do sistema operativo Windows são suportadas oficialmente para o agente do Windows:

* Windows Server de 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, versão 1709 e 1803
* Windows 7 SP1, Windows 8 Enterprise e pro e Windows 10 Enterprise e pro

>[!NOTE]
>Embora o agente Log Analytics para Windows tenha sido projetado para dar suporte a cenários de monitoramento de servidor, percebemos que você pode executar o cliente Windows para dar suporte a cargas de trabalho configuradas e otimizadas para o sistema operacional do servidor. O agente oferece suporte ao Windows Client, mas nossas soluções de monitoramento não se concentram em cenários de monitoramento de clientes, a menos que explicitamente declarado.

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
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só é suportado em plataformas com x86_x64 (64-bit) e OpenSSL anteriormente 1.x não é suportada em qualquer plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir realça os pacotes necessários para distribuições do Linux com suporte em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|OpenSSL    | Bibliotecas OpenSSL | 1,0. x ou 1.1. x |
|Curl | enrolando o cliente Web | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>O rsyslog ou o syslog-ng são necessários para coletar mensagens de syslog. O daemon de syslog padrão na versão 5 da versão do Oracle Linux, CentOS e Red Hat Enterprise Linux (sysklog) não é suportado para a recolha de eventos do syslog. Para coletar dados de syslog desta versão dessas distribuições, o daemon rsyslog deve ser instalado e configurado para substituir o sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para Azure Monitor logs, é altamente recomendável configurar o agente para usar pelo menos o protocolo TLS 1,2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**.  Para obter mais informações, consulte [enviar dados de forma segura através de TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente do Linux e do Windows se comunique com os logs de Azure Monitor.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  
|*.azure-automation.net |Porta 443 |Saída|Sim |  

Para obter informações de firewall necessárias para o Azure governamental, consulte [Gerenciamento do Azure governamental](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se planeja usar a função de trabalho de Runbook de híbrida de automatização do Azure para ligar e registar com o serviço de automatização para utilizar runbooks no seu ambiente, tem de ter acesso para o número de porta e os URLs descritos em [configurar sua rede para o Runbook Worker híbrido](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

O agente do Windows e Linux dá suporte à comunicação por meio de um servidor proxy ou Log Analytics gateway para Azure Monitor usando o protocolo HTTPS.  Autenticação anónima e básica (nome de utilizador/palavra-passe) são suportados.  Para o agente de Windows ligado diretamente ao serviço, a configuração de proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) no painel de controlo ou com o PowerShell.  

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

Conectar computadores em sua assinatura do Azure ou ambiente híbrido diretamente com Azure Monitor logs pode ser feito usando métodos diferentes, dependendo de seus requisitos. A tabela seguinte realça a cada método para determinar o que funciona melhor na sua organização.

|Origem | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| -Log Analytics VM extensão para o [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) com a CLI do Azure ou com um modelo Azure Resource Manager<br>- [Manualmente a partir do portal do Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve-los para uma área de trabalho existente do Azure Monitor.|
| Computador híbrido com Windows|- [Instalação manual](agent-windows.md)<br>- [DSC de automatização do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instalar o Microsoft Monitoring agent a partir da linha de comando ou usando um método automatizado, como o DSC de automatização do Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou com um modelo Azure Resource Manager, se tiver implementado Microsoft O Azure Stack no seu datacenter.| 
| Computador híbrido com Linux| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamar um script de wrapper alojado no GitHub. | 
| System Center Operations Manager|[Integrar o Operations Manager com o Log Analytics](../../azure-monitor/platform/om-agents.md) | Configure a integração entre Operations Manager e Azure Monitor logs para encaminhar os dados coletados de computadores com Windows relatando a um grupo de gerenciamento.|  

## <a name="next-steps"></a>Passos seguintes

* Revisão [origens de dados](../../azure-monitor/platform/agent-data-sources.md) para compreender as origens de dados disponíveis para recolher dados do seu sistema Windows ou Linux. 

* Saiba mais sobre [registar as consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 

* Saiba mais sobre [soluções de monitorização](../../azure-monitor/insights/solutions.md) que adicionar a funcionalidade para o Azure Monitor e também, recolher dados para a área de trabalho do Log Analytics.
