---
title: Configuração e problemas de gestão FAQ
titleSuffix: Azure Cloud Services
description: Este artigo lista as perguntas frequentes sobre configuração e gestão para os Serviços cloud do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 5821c72ae1be4759cf5aa76ff1f5af43337749c0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668588"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de configuração e gestão para serviços de nuvem azure: perguntas frequentes (PERGUNTAS)

Este artigo inclui perguntas frequentes sobre questões de configuração e gestão para os [Serviços cloud do Microsoft Azure.](https://azure.microsoft.com/services/cloud-services) Também pode consultar a [página tamanho VM](cloud-services-sizes-specs.md) dos Serviços cloud para obter informações sobre tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificados**

- [Porque é que a cadeia de certificados do meu certificado TLS/SSL de serviço de nuvem está incompleta?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Qual é o propósito do "Certificado de Encriptação de Ferramentas Windows Azure para Extensões"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Como posso gerar um Pedido de Assinatura de Certificado (RSE) sem "RDP-ing" na instância?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [O meu Certificado de Gestão de Serviços em Nuvem está a expirar. Como renová-lo?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Como automatizar a instalação do certificado TLS/SSL principal (.pfx) e do certificado intermédio (.p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Qual é o objetivo do certificado "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorização e registos**

- [Quais são as próximas capacidades do Cloud Service no portal Azure que podem ajudar a gerir e monitorizar aplicações?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Porque é que o IIS deixa de escrever para o diário de bordo?](#why-does-iis-stop-writing-to-the-log-directory)
- [Como posso permitir a exploração de WAD para serviços de nuvem?](#how-do-i-enable-wad-logging-for-cloud-services)

**Configuração da rede**

- [Como posso definir o tempo inativo para o equilíbrio de carga azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Como associo um endereço IP estático ao meu Serviço de Cloud?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quais são as funcionalidades e capacidades que o Azure basic IPS/IDS e DDOS fornece?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Como ativar http/2 em Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Permissões**

- [Os engenheiros internos da Microsoft podem remotamente trabalhar em casos de Serviço cloud sem permissão?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Não posso remotamente trabalhar para o Cloud Service VM utilizando o ficheiro RDP. I get following following error: Ocorreu um erro de autenticação (Código: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Dimensionamento**

- [Não posso escalar para além de X casos](#i-cannot-scale-beyond-x-instances)
- [Como posso configurar a Escala Automática com base nas métricas da memória?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Genérico**

- [Como adiciono "nosniff" ao meu site?](#how-do-i-add-nosniff-to-my-website)
- [Como personalizar o IIS para um papel web?](#how-do-i-customize-iis-for-a-web-role)
- [Qual é o limite de quota para o meu Serviço de Cloud?](#what-is-the-quota-limit-for-my-cloud-service)
- [Porque é que a unidade no meu VM de Serviço de Nuvem mostra muito pouco espaço em disco gratuito?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Como posso adicionar uma extensão Antimalware para os meus Serviços cloud de forma automatizada?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Como ativar a indicação de nome do servidor (SNI) para serviços de nuvem?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Como posso adicionar etiquetas ao meu Serviço Azure Cloud?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [O portal Azure não exibe a versão SDK do meu Serviço cloud. Como posso conseguir isto?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Quero encerrar o Serviço de Nuvem por vários meses. Como reduzir o custo de faturação do Cloud Service sem perder o endereço IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificados

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Porque é que a cadeia de certificados do meu certificado TLS/SSL de serviço de nuvem está incompleta?
    
Recomendamos que os clientes instalem a cadeia de certificados completo (folhacert, certs intermédios e cert de raiz) em vez de apenas o certificado de folha. Quando instala apenas o certificado de folha, confia no Windows para construir a cadeia de certificados andando no CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou No Windows Update quando o Windows está a tentar validar o certificado, o certificado pode ser considerado inválido. Ao instalar a cadeia completa de certificados, este problema pode ser evitado. O blog da [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazê-lo.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é o propósito do "Certificado de Encriptação de Ferramentas Windows Azure para Extensões"?

Estes certificados são automaticamente criados sempre que uma extensão é adicionada ao Serviço cloud. Mais comummente, esta é a extensão WAD ou a extensão RDP, mas pode ser outras, como a extensão Antimalware ou Log Collector. Estes certificados são utilizados apenas para encriptar e desencriptar a configuração privada para a extensão. A data de validade nunca é verificada, por isso não importa se o certificado está expirado. 

Pode ignorar estes certificados. Se quiser limpar os certificados, pode tentar apagar todos. O Azure lançará um erro se tentar apagar um certificado que está a ser utilizado.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar um Pedido de Assinatura de Certificado (RSE) sem "RDP-ing" na instância?

Consulte o seguinte documento de orientação:

[Obtenção de um certificado para utilização com web sites windows azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

A RSE é apenas um ficheiro de texto. Não tem de ser criado a partir da máquina onde o certificado será, em última análise, utilizado.Embora este documento esteja escrito para um Serviço de Aplicações, a criação de RSE é genérica e aplica-se também aos Serviços cloud.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>O meu Certificado de Gestão de Serviços em Nuvem está a expirar. Como renová-lo?

Pode utilizar os seguintes comandos PowerShell para renovar os seus Certificados de Gestão:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

O **Get-AzurePublishSettingsFile** criará um novo certificado de gestão em Certificados de**Gestão** de **Assinaturas** > no portal Azure. O nome do novo certificado parece "YourSubscriptionNam]-[CurrentDate]-credenciais".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Como automatizar a instalação do certificado TLS/SSL principal (.pfx) e do certificado intermédio (.p7b)?

Pode automatizar esta tarefa utilizando um script de arranque (batch/cmd/PowerShell) e registar esse script de arranque no ficheiro de definição de serviço. Adicione tanto o script de arranque como o certificado (ficheiro.p7b) na pasta do projeto do mesmo diretório do script de arranque.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Qual é o objetivo do certificado "Microsoft Azure Service Management for MachineKey"?

Este certificado é utilizado para encriptar chaves de máquinas em Funções Web Azure. Para saber mais, confira [este aviso.](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731)

Para obter mais informações, veja os artigos seguintes:
- [Como configurar e executar tarefas de arranque para um Serviço de Cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Tarefas de arranque do Serviço Comum cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorização e registos

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são as próximas capacidades do Cloud Service no portal Azure que podem ajudar a gerir e monitorizar aplicações?

A capacidade de gerar um novo certificado para o Protocolo de Ambiente de Trabalho Remoto (RDP) está para breve. Em alternativa, pode executar este guião:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
A possibilidade de escolher blob ou local para o seu local de carregamento csdef e cscfg está para breve. Utilizando o [New-AzureDeployment,](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)pode definir cada valor de localização.

Capacidade de monitorizar métricas ao nível da ocorrência. Estão disponíveis capacidades de monitorização adicionais em [Como Monitorizar os Serviços de Cloud](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Porque é que o IIS deixa de escrever para o diário de bordo?
Esgotou a quota de armazenamento local para escrever ao diário de bordo.Para corrigir isto, pode fazer uma de três coisas:
* Ative diagnósticos para IIS e os diagnósticos são periodicamente transferidos para o armazenamento de bolhas.
* Remova manualmente os ficheiros de registo do diretório de registo.
* Aumentar o limite de quota para os recursos locais.

Para obter mais informações, consulte os documentos seguintes:
* [Armazenar e ver dados de diagnóstico no Armazenamento Azure](/azure/storage/common/storage-introduction)
* [IIS Logs deixam de escrever no Serviço cloud](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Como posso permitir a exploração de WAD para serviços de nuvem?
Pode ativar o Registo de Diagnósticos Windows Azure (WAD) através das seguintes opções:
1. [Ativar a partir do Estúdio Visual](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Ativar através do código .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Ativar através da Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Para obter as definições atuais do SEU Serviço cloud, pode utilizar [As extensões get-AzureServiceDiagnostics](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd ou pode vê-lo através do portal a partir da lâmina "Cloud Services --> Extensões".


## <a name="network-configuration"></a>Configuração da rede

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como posso definir o tempo inativo para o equilíbrio de carga azure?
Pode especificar o prazo no seu ficheiro de definição de serviço (csdef) como este:

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
Ver [Novo: Tempo de marcha ção configurável para o Equilíbrio de Carga Azure](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para mais informações.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como associo um endereço IP estático ao meu Serviço de Cloud?
Para configurar um endereço IP estático, precisa de criar um IP reservado. Este IP reservado pode ser associado a um novo Serviço cloud ou a uma implementação existente. Consulte os seguintes documentos para obter mais detalhes:
* [Como criar um endereço IP reservado](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Reserve o endereço IP de um serviço de nuvem existente](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo Serviço cloud](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implementação em execução](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um Serviço cloud usando um ficheiro de configuração de serviço](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são as funcionalidades e capacidades que o Azure basic IPS/IDS e DDOS fornece?
O Azure tem IPS/IDS em servidores físicos do datacenter para se defender contra ameaças. Além disso, os clientes podem implementar soluções de segurança de terceiros, tais como firewalls de aplicações web, firewalls de rede, antimalware, deteção de intrusões, sistemas de prevenção (IDS/IPS) e muito mais. Para mais informações, consulte Proteja os seus dados e bens e cumpra as [normas globais](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)de segurança .

A Microsoft monitoriza continuamente servidores, redes e aplicações para detetar ameaças. A abordagem multifacetada de gestão de ameaças do Azure usa a deteção de intrusões, a prevenção de ataques distribuídos de negação de serviço (DDoS), testes de penetração, análise comportamental, deteção de anomalias e aprendizagem automática para fortalecer constantemente a sua defesa e reduzir riscos. O Microsoft Antimalware para o Azure protege os Serviços Azure Cloud e as máquinas virtuais. Tem a opção de implementar soluções de segurança de terceiros, tais como paredes de incêndio de aplicações web, firewalls de rede, antimalware, sistemas de deteção e prevenção de intrusões (IDS/IPS), e muito mais.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como ativar http/2 em Cloud Services VM?

O Windows 10 e o Windows Server 2016 vêm com suporte para HTTP/2 tanto do lado do cliente como do servidor. Se o seu cliente (browser) estiver a ligar-se ao servidor IIS através do TLS que negoceia http/2 através de extensões TLS, então não precisa de fazer qualquer alteração no lado do servidor. Isto porque, ao longo de TLS, o cabeçalho h2-14 especificando a utilização de HTTP/2 é enviado por padrão. Se, por outro lado, o seu cliente estiver a enviar um cabeçalho de upgrade para o HTTP/2, então terá de fazer a alteração abaixo no lado do servidor para garantir que a Atualização funciona e acaba com uma ligação HTTP/2. 

1. Executar regedit.exe.
2. Navegue na chave de registo: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Crie um novo valor DWORD chamado **DuoEnabled**.
4. Desloque o seu valor para 1.
5. Reinicie o seu servidor.
6. Vá ao seu **Web Site predefinido** e sob **Encadernações,** crie uma nova ligação TLS com o certificado auto-assinado acabado de criar. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: Browser, Apps e Web Server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Estes passos podem ser automatizados através de uma tarefa de arranque, para que sempre que uma nova instância paaS seja criada, possa fazer as alterações acima no registo do sistema. Para mais informações, consulte [como configurar e executar tarefas](cloud-services-startup-tasks.md)de arranque para um Serviço de Cloud .

 
Uma vez feito isto, pode verificar se o HTTP/2 foi ativado ou não utilizando um dos seguintes métodos:

- Ativar a versão Protocol nos registos IIS e ver os registos IIS. Mostrará HTTP/2 nos registos. 
- Ative a Ferramenta de Desenvolvimento F12 no Internet Explorer ou Microsoft Edge e mude para o separador Rede para verificar o protocolo. 

Para mais informações, consulte [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Permissões

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como posso implementar o acesso baseado em papéis para serviços de nuvem?
A Cloud Services não suporta o modelo de controlo de acesso baseado em papéis (RBAC), uma vez que não é um serviço baseado em Recursos Azure.

Ver [Compreender os diferentes papéis em Azure.](../role-based-access-control/rbac-and-directory-admin-roles.md)

## <a name="remote-desktop"></a>Ambiente de trabalho remoto

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Os engenheiros internos da Microsoft podem remotamente trabalhar em casos de Serviço cloud sem permissão?
A Microsoft segue um processo rigoroso que não permitirá que engenheiros internos se repertem no seu Serviço cloud sem permissão escrita (e-mail ou outra comunicação escrita) do proprietário ou do seu design.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Não posso remotamente trabalhar para o Cloud Service VM utilizando o ficheiro RDP. I get following following error: Ocorreu um erro de autenticação (Código: 0x80004005)

Este erro pode ocorrer se utilizar o ficheiro RDP a partir de uma máquina que esteja unida ao Diretório Ativo Azure. Para resolver este problema, siga estes passos:

1. Clique no ficheiro RDP que descarregou e, em seguida, **selecione Editar**.
2. Adicione "&#92;" como prefixo antes do nome de utilizador. Por exemplo, utilize **o nome de utilizador .\username** em vez do nome de **utilizador**.

## <a name="scaling"></a>Dimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Não posso escalar para além de X casos
A sua Subscrição Azure tem um limite no número de núcleos que pode utilizar. O escalonamento não funcionará se tiver usado todos os núcleos disponíveis. Por exemplo, se tiver um limite de 100 núcleos, isto significa que pode ter 100 instâncias de máquinavirtual de tamanho A1 para o seu Serviço cloud, ou 50 instâncias de máquinavirtual de tamanho A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Como posso configurar a Escala Automática com base nas métricas da memória?

A escala automática com base nas métricas de Memória para um Serviço de Cloud não é suportada atualmente. 

Para resolver este problema, pode utilizar insights de aplicação. A Escala Automática suporta os Insights de Aplicação como Fonte de Métricas e pode escalar a contagem de funções com base na métrica do hóspede como "Memória".  Tem de configurar os Insights de Aplicação no ficheiro de pacotes do cloud Service (*.cspkg) e ativar a extensão do Azure Diagnostics no serviço para implementar este feito.

Para mais detalhes sobre como utilizar uma métrica personalizada através de Application Insights para configurar auto-escala em serviços de nuvem, consulte [Iniciar com escala automática por métrica personalizada em Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Para obter mais informações sobre como integrar diagnósticos azure com insights de aplicação para serviços na nuvem, consulte [Enviar cloud service, máquina virtual ou dados de diagnóstico](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) de tecido de serviço para Insights de Aplicação

Para mais informações sobre o que permitirá insights de aplicação para serviços em nuvem, consulte [Insights de aplicação para serviços de nuvem azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Para obter mais informações sobre como ativar o registo de diagnósticos azure para serviços de nuvem, consulte [Configurar diagnósticos para serviços de nuvem azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Genérica

### <a name="how-do-i-add-nosniff-to-my-website"></a>Como adiciono "nosniff" ao meu site?
Para evitar que os clientes cheirem os tipos MIME, adicione uma definição no seu ficheiro *web.config.*

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

Também pode adicioná-lo como um cenário no IIS. Utilize o seguinte comando com o artigo [de tarefas de arranque comum.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Como personalizar o IIS para um papel web?
Utilize o script de arranque do IIS a partir do artigo [de tarefas de arranque comuns.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Qual é o limite de quota para o meu Serviço de Cloud?
Consulte [os limites específicos do serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Porque é que a unidade no meu VM de Serviço de Nuvem mostra muito pouco espaço em disco gratuito?
Este comportamento é esperado, e não deve causar qualquer problema à sua aplicação. O diário é ligado para a unidade %approot% nos VMs Azure PaaS, que essencialmente consome o dobro da quantidade de espaço que os ficheiros normalmente assumem. No entanto, há várias coisas a ter em conta que, essencialmente, transformam isto numa não-questão.

O tamanho da unidade %approot% é calculado como \<tamanho de .cspkg + tamanho máximo do diário + uma margem de espaço livre>, ou 1,5 GB, o que for maior. O tamanho do seu VM não tem qualquer influência neste cálculo. (O tamanho vm apenas afeta o tamanho do C temporário: unidade.) 

Não é suportado escrever para a unidade %approot%. Se estiver a escrever para o Azure VM, deve fazê-lo num recurso temporário de Armazenamento Local (ou outra opção, como armazenamento Blob, Ficheiros Azure, etc.). Portanto, a quantidade de espaço livre na pasta %approot% não é significativa. Se não tem a certeza se a sua aplicação está a escrever para a unidade %approot%, pode sempre deixar o seu serviço funcionar durante alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não vai escrever nada para a unidade %approot%. Uma vez que o VHD é criado a partir do seu .cspkg e montado no Azure VM, a única coisa que pode escrever para esta unidade é a sua aplicação. 

As definições do diário não são configuráveis, por isso não pode desligá-lo.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como posso adicionar uma extensão Antimalware para os meus Serviços cloud de forma automatizada?

Pode ativar a extensão Antimalware utilizando o script PowerShell na Tarefa de Arranque. Siga os passos nestes artigos para implementá-lo: 
 
- [Criar uma tarefa de arranque powerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implementação de Antimalware e como o permitir a partir do portal, consulte Cenários de [Implementação Antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como ativar a indicação de nome do servidor (SNI) para serviços de nuvem?

Pode ativar o SNI nos Serviços cloud utilizando um dos seguintes métodos:

**Método 1: Utilizar powershell**

A ligação SNI pode ser configurada utilizando o PowerShell cmdlet **New-WebBinding** numa tarefa de arranque para uma instância de função de Serviço cloud como abaixo:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Como [aqui](https://technet.microsoft.com/library/ee790567.aspx)descrito, o $sslFlags pode ser um dos valores como o seguinte:

|Valor|Significado|
------|------
|0|Sem SNI|
|1|SNI Habilitado|
|2|Ligação não SNI que utiliza Loja central de certificados|
|3|Ligação SNI que utiliza loja de certificados central|
 
**Método 2: Utilizar código**

A ligação SNI também poderia ser configurada por código na startup de funções, tal como descrito neste post de [blog:](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Utilizando qualquer uma das abordagens acima, os respetivos certificados (*.pfx) para os nomes de anfitriões específicos têm de ser instalados pela primeira vez nas instâncias de função utilizando uma tarefa de arranque ou através de código, de modo a que a ligação SNI seja eficaz.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como posso adicionar etiquetas ao meu Serviço Azure Cloud? 

Cloud Service é um recurso clássico. Apenas recursos criados através de tags de suporte do Gestor de Recursos Azure. Não é possível aplicar etiquetas a recursos clássicos como o Cloud Service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>O portal Azure não exibe a versão SDK do meu Serviço cloud. Como posso conseguir isto?

Estamos a trabalhar para trazer esta funcionalidade no portal Azure. Entretanto, pode utilizar os seguintes comandos PowerShell para obter a versão SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Quero encerrar o Serviço de Nuvem por vários meses. Como reduzir o custo de faturação do Cloud Service sem perder o endereço IP?

Um serviço de cloud já implantado é faturado para a Compute e Armazenamento que utiliza. Por isso, mesmo que desligues o VM Azure, ainda serás cobrado para o Armazenamento. 

Aqui está o que pode fazer para reduzir a sua faturação sem perder o endereço IP para o seu serviço:

1. [Reserve o endereço IP](../virtual-network/virtual-networks-reserved-public-ip.md) antes de eliminar as implementações.  Só será cobrado para este endereço IP. Para mais informações sobre a faturação do endereço IP, consulte os preços dos [endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Apague as implantações. Não apague o xxx.cloudapp.net, para que possa usá-lo para o futuro.
3. Se pretender reimplantar o Serviço cloud utilizando o mesmo IP de reserva que reservou na sua subscrição, consulte [endereços IP reservados para Serviços na Nuvem e Máquinas Virtuais](https://azure.microsoft.com/blog/reserved-ip-addresses/).

