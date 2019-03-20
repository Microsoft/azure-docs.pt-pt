---
title: Problemas de configuração e gestão para perguntas frequentes do Microsoft Azure Cloud Services | Documentos da Microsoft
description: Este artigo apresenta uma lista de perguntas mais frequentes sobre configuração e gestão de serviços Cloud do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 90b6798f7b7ab3acc552135c73bb1e491e4a0111
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835525"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Configuração e problemas de gerenciamento de serviços Cloud do Azure: Perguntas mais frequentes (FAQ)

Este artigo contém perguntas freqüentes sobre problemas de configuração e gestão do [serviços Cloud do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços Cloud](cloud-services-sizes-specs.md) para informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificados**

- [Por que motivo está incompleta a cadeia de certificados do meu certificado SSL do serviço Cloud?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [O que é o objetivo do "Windows Azure ferramentas de encriptação para extensões de certificados"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Como gerar um certificado de assinatura do pedido (CSR) sem "RDP-ing" para a instância?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Meu certificado de gestão do serviço de nuvem está prestes a expirar. Como renová-la?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Como automatizar a instalação de certificate(.pfx) SSL principal e certificate(.p7b) intermédio?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [O que é o objetivo do certificado "Microsoft Azure Service Management para MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorização e registo**

- [Quais são os próximos recursos de serviço em nuvem no portal do Azure que pode ajudar a gerenciar e monitorar aplicativos?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Por que motivo é que o IIS parar gravação para o diretório de registo?](#why-does-iis-stop-writing-to-the-log-directory)
- [Como posso ativar o registo de WAD para serviços em nuvem?](#how-do-i-enable-wad-logging-for-cloud-services)

**Configuração da rede**

- [Como posso definir o tempo limite de inatividade para o Balanceador de carga do Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Como posso associar um endereço IP estático para o meu serviço em nuvem?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quais são as funcionalidades e capacidades básicas do Azure IPS IDS e DDOS fornece?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Como ativar o HTTP/2 na VM de serviços Cloud?](#how-to-enable-http2-on-cloud-services-vm)

**Permissões**

- [Pode aos engenheiros internos ambiente de trabalho remoto para instâncias de serviço em nuvem sem a permissão?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Não é o ambiente de trabalho remoto à VM do serviço de nuvem utilizando o ficheiro RDP. Obter a seguir o erro: Ocorreu um erro de autenticação (código: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Dimensionamento**

- [Eu não é possível dimensionar para além de X instâncias](#i-cannot-scale-beyond-x-instances)
- [Como posso configurar o dimensionamento automático com base em métricas de memória?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Genérico**

- [Como posso adicionar "nosniff" para o meu site?](#how-do-i-add-nosniff-to-my-website)
- [Como posso personalizar o IIS para uma função da web?](#how-do-i-customize-iis-for-a-web-role)
- [O que é o limite de quota para o meu serviço em nuvem?](#what-is-the-quota-limit-for-my-cloud-service)
- [Por que motivo a unidade na minha VM do serviço de nuvem mostra muito pouco espaço livre em disco?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Como posso adicionar uma extensão de Antimalware para meus serviços de nuvem de maneira automatizada?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Como ativar a indicação de nome de servidor (SNI) para serviços em nuvem?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Como posso adicionar etiquetas ao meu serviço de nuvem do Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [O portal do Azure não apresenta a versão do SDK do meu serviço em nuvem. Como posso obtê-lo?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Quer encerrar o serviço em nuvem por vários meses. Como reduzir o custo de faturação do serviço em nuvem sem perder o endereço IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificados

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que motivo está incompleta a cadeia de certificados do meu certificado SSL do serviço Cloud?
    
Recomendamos que os clientes instalar a cadeia de certificados completa (certificado de folha, certificados intermediários e certificado de raiz) em vez de apenas o certificado de folha. Quando instalar apenas o certificado de folha, contar com Windows para criar a cadeia de certificados, fazendo com que a CTL. Se intermitente da rede ou problemas DNS ocorrem no Azure ou o Windows Update quando o Windows está a tentar validar o certificado, o certificado pode ser considerado inválido. Ao instalar a cadeia de certificados completa, este problema pode ser evitado. Blog [como instalar um certificado SSL em cadeia](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazer isso.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>O que é o objetivo do "Windows Azure ferramentas de encriptação para extensões de certificados"?

Estes certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço Cloud. Normalmente, esta é a extensão WAD ou a extensão RDP, mas poderia ser outros, como a extensão de Antimalware ou Recoletor de registos. Estes certificados são utilizados apenas para encriptar e desencriptar a configuração privada da extensão. A data de expiração nunca é verificada, portanto, não importa se o certificado está expirado. 

Pode ignorar estes certificados. Se quiser limpar os certificados, pode tentar excluí-los todos. Azure irá gerar um erro se tentar eliminar um certificado que está a ser utilizado.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como gerar um certificado de assinatura do pedido (CSR) sem "RDP-ing" para a instância?

Consulte o documento principal das orientações seguintes:

[Obter um certificado para utilização com Web Sites do Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

O CSR é apenas um arquivo de texto. Ele não tem de ser criada a partir da máquina onde o certificado, por fim, irá ser utilizado. Embora este documento foi escrito para um serviço de aplicações, a criação de CSR é genérica e aplica-se também para serviços em nuvem.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Meu certificado de gestão do serviço de nuvem está prestes a expirar. Como renová-la?

Pode usar seguintes comandos do PowerShell para renovar os certificados de gestão:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

O **Get-AzurePublishSettingsFile** irá criar um novo certificado de gestão na **subscrição** > **certificados de gestão** no portal do Azure. O nome do novo certificado é semelhante a "YourSubscriptionNam]-[CurrentDate] - credenciais".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Como automatizar a instalação de certificate(.pfx) SSL principal e certificate(.p7b) intermédio?

Pode automatizar esta tarefa ao utilizar um script de inicialização (batch/cmd/PowerShell) e registe-se esse script de inicialização no arquivo de definição do serviço. Adicione o script de inicialização e o certificado (ficheiro. p7b) na pasta de projeto do mesmo diretório do script de inicialização.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>O que é o objetivo do certificado "Microsoft Azure Service Management para MachineKey"?

Este certificado é utilizado para encriptar chaves de máquinas em funções da Web do Azure. Para obter mais informações, confira esta consultoria [https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731].

Para obter mais informações, veja os artigos seguintes:
- [Como configurar e executar tarefas de arranque para um serviço Cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Tarefas de arranque do serviço Cloud comuns](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorização e registos

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são os próximos recursos de serviço em nuvem no portal do Azure que pode ajudar a gerenciar e monitorar aplicativos?

Capacidade de gerar um novo certificado para o protocolo RDP (Remote Desktop) estará disponível brevemente. Em alternativa, pode executar este script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Capacidade de escolher o blob ou local para sua csdef e cscfg carregar localização estará disponível brevemente. Usando [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), pode definir cada valor de localização.

Capacidade de monitorizar as métricas ao nível da instância. Capacidades de monitorização adicionais estão disponíveis no [como monitorizar serviços Cloud](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que motivo é que o IIS parar gravação para o diretório de registo?
Que tenha esgotado a quota de armazenamento local para escrever para o diretório de registo. Para corrigir este problema, pode fazer uma das três coisas:
* Ativar diagnósticos para o IIS e o diagnóstico periodicamente mudaram para armazenamento de Blobs.
* Remova manualmente os ficheiros de registo do diretório de registo.
* Aumente o limite de quota para recursos locais.

Para obter mais informações, consulte os seguintes documentos:
* [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Registos do IIS parar de escrever num serviço em nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Como posso ativar o registo de WAD para serviços em nuvem?
Pode ativar o registo de Windows Azure Diagnostics (WAD) por meio de opções seguintes:
1. [Ativar a partir do Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Ativar por meio de código do .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Ativar através do Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Para obter as definições de WAD atuais do seu serviço Cloud, pode utilizar [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd ou pode vê-lo através do portal a partir do painel de "Serviços Cloud--> extensões".


## <a name="network-configuration"></a>Configuração da rede

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como posso definir o tempo limite de inatividade para o Balanceador de carga do Azure?
Pode especificar o tempo limite no ficheiro de definição (. csdef) do serviço, como este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Consulte [novos: Tempo limite de inatividade configuráveis, para o Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como posso associar um endereço IP estático para o meu serviço em nuvem?
Para configurar um endereço IP estático, terá de criar um IP reservado. Este IP reservado pode ser associado a um novo serviço Cloud ou a uma implementação existente. Consulte os seguintes documentos para obter mais detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reservar o endereço IP de um serviço Cloud existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado para um novo serviço Cloud](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado para implementação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um serviço Cloud utilizando um ficheiro de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são as funcionalidades e capacidades básicas do Azure IPS IDS e DDOS fornece?
O Azure tem IPS/IDS em servidores físicos do Centro de dados para se Defender contra ameaças. Além disso, os clientes podem implementar soluções de segurança de terceiros, como firewalls de aplicações web, firewalls de rede, antimalware, deteção de intrusões, sistemas de prevenção (IDS/IPS) e muito mais. Para obter mais informações, consulte [proteger os seus dados e recursos e estar em conformidade com as normas de segurança global](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft monitoriza continuamente a servidores, redes e aplicações para detetar ameaças. Deteção de intrusões do Azure a gestão de ameaças multipronged abordagem utiliza, distributed denial of service (DDoS) prevenção de ataque, análise comportamental e teste de penetração, deteção de anomalias e aprendizagem automática para constantemente reforce a sua defesa e reduzir os riscos. O Microsoft Antimalware para o Azure protege máquinas virtuais e serviços Cloud do Azure. Tem a opção para implementar soluções de segurança de terceiros, além disso, como firewalls de aplicação web, firewalls de rede, antimalware, sistemas de deteção e prevenção de intrusões (IDS/IPS) e muito mais.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como ativar o HTTP/2 na VM de serviços Cloud?

Windows 10 e Windows Server 2016 vem com suporte para HTTP/2 no lado do cliente e servidor. Se o seu cliente (navegador) estiver a ligar ao servidor do IIS por TLS que negocia HTTP/2 através de extensões TLS, em seguida, não é necessário fazer qualquer alteração no lado do servidor. Isto acontece porque, ao longo do TLS, o cabeçalho de h2 14 especificando a utilização de HTTP/2 é enviado por predefinição. Se, por outro lado, o cliente está enviando um cabeçalho de atualização para atualizar para o HTTP/2, terá de efetuar a alteração abaixo no lado do servidor para garantir que a atualização funciona e ficar com uma ligação de HTTP/2. 

1. Execute regedit.exe.
2. Navegue para a chave de registo: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Criar um novo valor DWORD denominado **DuoEnabled**.
4. Defina seu valor como 1.
5. Reinicie o servidor.
6. Aceda ao seu **Web Site predefinido** e, em **enlaces**, crie um novo enlace de TLS com o certificado autoassinado que acabou de criar. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: Browser, aplicações e Web Server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Estes passos podem ser automatizados por meio de uma tarefa de arranque, para que sempre que é criada uma nova instância de PaaS, pode fazer as alterações acima no Registro do sistema. Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço Cloud](cloud-services-startup-tasks.md).

 
Assim que tiver sido feita, pode verificar se o HTTP/2 foi ativada ou não utilizando um dos seguintes métodos:

- Ativar a versão de protocolo nos registos do IIS e examinar os registos IIS. Ela mostrará HTTP/2 nos registos. 
- Ativar a ferramenta de desenvolvedor F12 no Internet Explorer/Microsoft Edge e mude para o separador de rede para verificar o protocolo. 

Para obter mais informações, consulte [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Permissões

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como posso implementar o acesso baseado em funções para os serviços Cloud?
Serviços cloud não suporta o modelo de controlo (RBAC) de acesso baseado em funções, dado que não é um serviço baseado no Azure Resource Manager.

Ver [compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Ambiente de trabalho remoto

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Pode aos engenheiros internos ambiente de trabalho remoto para instâncias de serviço em nuvem sem a permissão?
Microsoft segue um processo rigoroso que não permitirá aos engenheiros internos da área de trabalho remota no seu serviço Cloud sem permissão por escrito (e-mail ou outras comunicações escritas) de proprietário ou seus designee.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Não é o ambiente de trabalho remoto à VM do serviço de nuvem utilizando o ficheiro RDP. Obter a seguir o erro: Ocorreu um erro de autenticação (código: 0x80004005)

Este erro pode ocorrer se usar o ficheiro RDP a partir de uma máquina que está associado ao Azure Active Directory. Para resolver este problema, siga estes passos:

1. O ficheiro RDP que transferiu com o botão direito e, em seguida, selecione **editar**.
2. Adicionar "&#92;" como prefixo antes do nome de utilizador. Por exemplo, usar **. \Username.** em vez de **username**.

## <a name="scaling"></a>Dimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Eu não é possível dimensionar para além de X instâncias
A subscrição do Azure tem um limite no número de núcleos que pode utilizar. Dimensionamento não funcionará se tiver utilizado a todos os núcleos disponíveis. Por exemplo, se tiver um limite de 100 núcleos, isso significa que poderia ter 100 instâncias de máquinas virtuais A1 em tamanho normal do serviço em nuvem ou instâncias de máquina virtual de tamanho 50 A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Como posso configurar o dimensionamento automático com base em métricas de memória?

Dimensionamento automático com base em métricas de memória para serviços Cloud não é atualmente suportado. 

Para contornar este problema, pode utilizar o Application Insights. Dimensionamento automático suporta o Application Insights como uma origem de métricas e pode dimensionar a contagem de instâncias de função com base nas métricas de convidado, como "Memória".  Terá de configurar o Application Insights no seu ficheiro de pacote de projeto de serviço em nuvem (. cspkg) e ativar a extensão de diagnóstico do Azure no serviço para implementar esse feito.

Para obter mais detalhes sobre como usar uma métrica personalizada por meio do Application Insights para configurar o dimensionamento automático nos serviços Cloud, consulte [introdução ao dimensionamento automático por métrica personalizada no Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Para obter mais informações sobre como integrar o diagnóstico do Azure com o Application Insights para serviços em nuvem, consulte [enviar serviço de Cloud, máquinas virtuais ou recursos de infraestrutura do serviço de dados de diagnóstico para o Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Para obter mais informações sobre como ativar o Application Insights para serviços em nuvem, consulte [Application Insights para serviços Cloud do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Para obter mais informações sobre como ativar o registo de diagnósticos do Azure para serviços em nuvem, consulte [configurar diagnósticos para serviços Cloud do Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Genérico

### <a name="how-do-i-add-nosniff-to-my-website"></a>Como posso adicionar "nosniff" para o meu site?
Para impedir que os clientes a detecção dos tipos de MIME, adicione uma definição no seu *Web. config* ficheiro.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Também pode adicionar isso como uma definição no IIS. Utilize o seguinte comando com o [tarefas de arranque comuns](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artigo.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Como posso personalizar o IIS para uma função da web?
Utiliza o script de inicialização do IIS dos [tarefas de arranque comuns](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artigo.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>O que é o limite de quota para o meu serviço em nuvem?
Ver [específico do serviço limita](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que motivo a unidade na minha VM do serviço de nuvem mostra muito pouco espaço livre em disco?
Este comportamento está previsto, e não deve fazer com que qualquer problema à sua aplicação. Registro no diário está ativado para a unidade de % % approot em VMs de PaaS do Azure, que, essencialmente, consome double a quantidade de espaço que normalmente necessário ficheiros de cópia de segurança. No entanto, existem vários aspetos a ter em consideração que, essencialmente, transformar isso num problema não.

O tamanho da unidade % approot % é calculado como < tamanho de. cspkg + tamanho do diário máximo > + uma margem de espaço livre, ou seja, 1,5 GB que for maior. O tamanho da VM não tem efeito nesse cálculo. (O tamanho da VM só afeta o tamanho da unidade c: temporária.) 

Ele não é suportado para escrever para a unidade de % % approot. Se estiver escrevendo à VM do Azure, deve fazer isso num recurso de LocalStorage temporário (ou outra opção, como o armazenamento de BLOBs, ficheiros do Azure, etc.). Portanto, a quantidade de espaço livre na pasta % approot % não é significativa. Se não tiver a certeza se a sua aplicação está a escrever para a unidade de % % approot, pode sempre permitir que o serviço de execução por alguns dias e, em seguida, compare o "antes" e "depois" tamanhos. 

Azure não irá escrever nada para a unidade de % % approot. Depois do VHD é criado a partir de seu. cspkg e montado na VM do Azure, a única coisa que pode escrever a esta unidade é a sua aplicação. 

As definições de diário são não configurável, para que não pode desativá-la.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como posso adicionar uma extensão de Antimalware para meus serviços de nuvem de maneira automatizada?

Pode ativar a extensão de Antimalware, utilizando o script do PowerShell na tarefa de arranque. Siga os passos seguintes artigos para implementá-lo: 
 
- [Criar uma tarefa de arranque do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implementação de Antimalware e sobre como ativá-la a partir do portal, consulte [cenários de implementação de Antimalware](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como ativar a indicação de nome de servidor (SNI) para serviços em nuvem?

Pode ativar a SNI nos serviços Cloud utilizando um dos seguintes métodos:

**Método 1: Utilize o PowerShell**

O enlace de SNI pode ser configurado com o cmdlet do PowerShell **New-WebBinding** numa tarefa de arranque para uma instância de função de serviço em nuvem como abaixo:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Conforme descrito [aqui](https://technet.microsoft.com/library/ee790567.aspx), o $sslFlags poderia ser um dos valores como o seguinte:

|Value|Significado|
------|------
|0|Não existem SNI|
|1|SNI ativado |
|2 |Não SNI que utiliza a Central Store de certificado de ligação|
|3|Armazenar a vinculação de SNI que utiliza certificados Central |
 
**Método 2: Código de utilização**

O enlace de SNI também podia ser configurado por meio do código na inicialização de função, tal como descrito nesta [mensagem de blogue](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Utilizar qualquer uma das abordagens acima, os respectivos certificados (*. pfx) para os nomes de anfitrião específico tem de ser instalado pela primeira vez nas instâncias de função através de uma tarefa de arranque ou por meio do código para que o enlace de SNI ser eficaz.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como posso adicionar etiquetas ao meu serviço de nuvem do Azure? 

Serviço em nuvem é um recurso de clássico. Apenas os recursos criados por meio de marcas de suporte do Azure Resource Manager. Não pode aplicar etiquetas a recursos clássicos, como o serviço em nuvem. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>O portal do Azure não apresenta a versão do SDK do meu serviço em nuvem. Como posso obtê-lo?

Estamos a trabalhar no sentido de integrar esse recurso no portal do Azure. Enquanto isso, pode utilizar seguintes comandos do PowerShell para obter a versão do SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Quer encerrar o serviço em nuvem por vários meses. Como reduzir o custo de faturação do serviço em nuvem sem perder o endereço IP?

Um serviço em nuvem já implementado é faturado pela computação e armazenamento que utiliza. Então, mesmo se encerrar a VM do Azure, será ainda será cobrado o armazenamento. 

Eis o que pode fazer para reduzir a sua cobrança sem perder o endereço IP para o seu serviço:

1. [Reservar o endereço IP](../virtual-network/virtual-networks-reserved-public-ip.md) antes de eliminar as implementações.  Só serão cobradas para este endereço IP. Para obter mais informações sobre a faturação de endereço IP, consulte [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Elimine as implementações. Não elimine xxx.cloudapp.net, para que pode usá-lo no futuro.
3. Se pretender voltar a implementar o serviço em nuvem utilizando o mesmo IP de reserva que reservado na sua subscrição, veja [endereços IP reservados para serviços Cloud e máquinas virtuais](https://azure.microsoft.com/blog/reserved-ip-addresses/).

