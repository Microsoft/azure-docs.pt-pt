---
title: Problemas de configuração e gerenciamento para Serviços de Nuvem do Microsoft Azure perguntas frequentes | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre configuração e gerenciamento para Serviços de Nuvem do Microsoft Azure.
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
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 480ca33f02242499bdf9fff3fa35695e7d4841a0
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945460"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de configuração e gerenciamento para os serviços de nuvem do Azure: Perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre problemas de configuração e gerenciamento para [serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página tamanho da VM dos serviços de nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificados**

- [Por que a cadeia de certificados do meu certificado SSL do serviço de nuvem está incompleta?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Qual é a finalidade do "certificado de criptografia das ferramentas do Windows Azure para extensões"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Como posso gerar uma solicitação de assinatura de certificado (CSR) sem "RDP-ing" para a instância?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Meu certificado de gerenciamento de serviço de nuvem está expirando. Como renová-lo?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Como automatizar a instalação do certificado SSL principal (. pfx) e do certificado intermediário (. p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Qual é a finalidade do certificado "gerenciamento de serviços de Microsoft Azure para MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitoramento e registro em log**

- [Quais são os futuros recursos de serviço de nuvem no portal do Azure que podem ajudar a gerenciar e monitorar aplicativos?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Por que o IIS pára de gravar no diretório de log?](#why-does-iis-stop-writing-to-the-log-directory)
- [Como fazer habilitar o log de WAD para serviços de nuvem?](#how-do-i-enable-wad-logging-for-cloud-services)

**Configuração da rede**

- [Como fazer definir o tempo limite de ociosidade para o Azure Load Balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Como fazer associar um endereço IP estático ao meu serviço de nuvem?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quais são os recursos e as funcionalidades que os IPS/IDS e DDOS básico do Azure fornecem?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Como habilitar HTTP/2 na VM de serviços de nuvem?](#how-to-enable-http2-on-cloud-services-vm)

**Permissões**

- [A Microsoft interna pode engenheiros de área de trabalho remota para instâncias de serviço de nuvem sem permissão?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Não consigo a área de trabalho remota para a VM do serviço de nuvem usando o arquivo RDP. Obtenho o seguinte erro: Ocorreu um erro de autenticação (código: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Dimensionamento**

- [Não é possível dimensionar além de X instâncias](#i-cannot-scale-beyond-x-instances)
- [Como posso configurar o dimensionamento automático com base nas métricas de memória?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Genérica**

- [Como fazer adicionar "nosniff" ao meu site?](#how-do-i-add-nosniff-to-my-website)
- [Como fazer personalizar o IIS para uma função Web?](#how-do-i-customize-iis-for-a-web-role)
- [Qual é o limite de cota para meu serviço de nuvem?](#what-is-the-quota-limit-for-my-cloud-service)
- [Por que a unidade em minha VM de serviço de nuvem mostra muito pouco espaço livre em disco?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Como posso adicionar uma extensão antimalware para meus serviços de nuvem de forma automatizada?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Como habilitar o SNI (Indicação de Nome de Servidor) para serviços de nuvem?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Como posso adicionar marcas ao meu serviço de nuvem do Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [O portal do Azure não exibe a versão do SDK do meu serviço de nuvem. Como posso conseguir isso?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Quero desligar o serviço de nuvem por vários meses. Como reduzir o custo de cobrança do serviço de nuvem sem perder o endereço IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificados

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que a cadeia de certificados do meu certificado SSL do serviço de nuvem está incompleta?
    
Recomendamos que os clientes instalem a cadeia de certificados completa (certificado de folha, certificados intermediários e certificado raiz) em vez de apenas o certificado de folha. Ao instalar apenas o certificado de folha, você depende do Windows para criar a cadeia de certificados percorrendo a CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou Windows Update quando o Windows estiver tentando validar o certificado, o certificado poderá ser considerado inválido. Ao instalar a cadeia de certificados completo, esse problema pode ser evitado. O blog em [como instalar um certificado SSL encadeado](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazer isso.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é a finalidade do "certificado de criptografia das ferramentas do Windows Azure para extensões"?

Esses certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço de nuvem. Geralmente, essa é a extensão WAD ou a extensão RDP, mas pode ser outras, como a extensão de antimalware ou coletor de log. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada para a extensão. A data de expiração nunca é verificada, portanto, não importa se o certificado expirou. 

Você pode ignorar esses certificados. Se você quiser limpar os certificados, você pode tentar excluir todos eles. O Azure gerará um erro se você tentar excluir um certificado que está em uso.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar uma solicitação de assinatura de certificado (CSR) sem "RDP-ing" para a instância?

Consulte o seguinte documento de diretrizes:

[Obtendo um certificado para uso com sites do Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

O CSR é apenas um arquivo de texto. Ele não precisa ser criado no computador em que o certificado será usado por fim. Embora este documento seja escrito para um serviço de aplicativo, a criação do CSR é genérica e também se aplica aos serviços de nuvem.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Meu certificado de gerenciamento de serviço de nuvem está expirando. Como renová-lo?

Você pode usar os seguintes comandos do PowerShell para renovar seus certificados de gerenciamento:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

O **Get-AzurePublishSettingsFile** criará um novo certificado de gerenciamento nos**certificados de gerenciamento** de **assinatura** > no portal do Azure. O nome do novo certificado é semelhante a "YourSubscriptionNam]-[CurrentDate]-Credentials".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Como automatizar a instalação do certificado SSL principal (. pfx) e do certificado intermediário (. p7b)?

Você pode automatizar essa tarefa usando um script de inicialização (lote/cmd/PowerShell) e registrar esse script de inicialização no arquivo de definição de serviço. Adicione o script de inicialização e o certificado (arquivo. p7b) na pasta do projeto do mesmo diretório do script de inicialização.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Qual é a finalidade do certificado "gerenciamento de serviços de Microsoft Azure para MachineKey"?

Esse certificado é usado para criptografar chaves de computador em funções Web do Azure. Para saber mais, confira [este comunicado](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Para obter mais informações, veja os artigos seguintes:
- [Como configurar e executar tarefas de inicialização para um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Tarefas comuns de inicialização do serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorização e registos

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são os futuros recursos de serviço de nuvem no portal do Azure que podem ajudar a gerenciar e monitorar aplicativos?

A capacidade de gerar um novo certificado para protocolo RDP (RDP) estará disponível em breve. Como alternativa, você pode executar este script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
A capacidade de escolher o BLOB ou local para seu local de carregamento csdef e cscfg estará disponível em breve. Usando [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), você pode definir cada valor de local.

Capacidade de monitorar as métricas no nível da instância. Recursos de monitoramento adicionais estão disponíveis em [como monitorar serviços de nuvem](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que o IIS pára de gravar no diretório de log?
Você esgotou a cota de armazenamento local para gravar no diretório de log. Para corrigir isso, você pode executar uma das três ações a seguir:
* Habilite o diagnóstico para o IIS e faça com que o diagnóstico seja periodicamente movido para o armazenamento de BLOB.
* Remova manualmente os arquivos de log do diretório de log.
* Aumente o limite de cota para recursos locais.

Para obter mais informações, consulte os seguintes documentos:
* [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Logs do IIS param de gravar no serviço de nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Como fazer habilitar o log de WAD para serviços de nuvem?
Você pode habilitar o log do Windows Diagnóstico do Azure (WAD) por meio das seguintes opções:
1. [Habilitar do Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Habilitar por meio de código .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Habilitar por meio do PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Para obter as configurações de WAD atuais de seu serviço de nuvem, você pode usar o [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) PS cmd ou pode exibi-lo por meio do portal da folha "serviços de nuvem-> extensões".


## <a name="network-configuration"></a>Configuração de rede

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como fazer definir o tempo limite de ociosidade para o Azure Load Balancer?
Você pode especificar o tempo limite em seu arquivo de definição de serviço (csdef) como este:

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
Consulte [novo: Tempo limite de ociosidade configurável](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para Azure Load Balancer para obter mais informações.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como fazer associar um endereço IP estático ao meu serviço de nuvem?
Para configurar um endereço IP estático, você precisa criar um IP reservado. Esse IP reservado pode ser associado a um novo serviço de nuvem ou a uma implantação existente. Consulte os seguintes documentos para obter detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo serviço de nuvem](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implantação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são os recursos e as funcionalidades que os IPS/IDS e DDOS básico do Azure fornecem?
O Azure tem IPS/IDS em servidores físicos de datacenter para se defender contra ameaças. Além disso, os clientes podem implantar soluções de segurança de terceiros, como firewalls de aplicativos Web, firewalls de rede, Antimalware, detecção de intrusão, sistemas de prevenção (IDS/IPS) e muito mais. Para obter mais informações, consulte [proteger seus dados e ativos e obedecer aos padrões de segurança global](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

A Microsoft monitora continuamente servidores, redes e aplicativos para detectar ameaças. A abordagem de gerenciamento de ameaças com multidimensionamento do Azure usa detecção de intrusão, prevenção de ataque de DDoS (negação de serviço distribuída), teste de penetração, análise comportamental, detecção de anomalias e aprendizado de máquina para reforçar constantemente sua defesa e reduza os riscos. O Microsoft antimalware para Azure protege os serviços de nuvem do Azure e as máquinas virtuais. Você tem a opção de implantar soluções de segurança de terceiros, como paredes de fogo de aplicativo Web, firewalls de rede, Antimalware, sistemas de prevenção e detecção de intrusão (IDS/IPS) e muito mais.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como habilitar HTTP/2 na VM de serviços de nuvem?

O Windows 10 e o Windows Server 2016 vêm com suporte para HTTP/2 no lado do cliente e do servidor. Se o cliente (navegador) estiver se conectando ao servidor IIS por TLS que negocia o HTTP/2 via Extensões TLS, você não precisará fazer nenhuma alteração no lado do servidor. Isso ocorre porque, por TLS, o cabeçalho H2-14 que especifica o uso de HTTP/2 é enviado por padrão. Se, por outro lado, seu cliente estiver enviando um cabeçalho de atualização para atualizar para HTTP/2, você precisará fazer a alteração abaixo no lado do servidor para garantir que a atualização funcione e que você acabe com uma conexão HTTP/2. 

1. Execute regedit. exe.
2. Navegue até a chave do registro: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Crie um novo valor DWORD chamado **DuoEnabled**.
4. Defina seu valor como 1.
5. Reinicie o servidor.
6. Vá para o **site padrão** e, em **associações**, crie uma nova associação TLS com o certificado autoassinado recém-criado. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: Navegador, aplicativos e servidor Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Essas etapas podem ser automatizadas por meio de uma tarefa de inicialização, de modo que sempre que uma nova instância de PaaS for criada, ela poderá fazer as alterações acima no registro do sistema. Para obter mais informações, consulte [como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).

 
Quando isso tiver sido feito, você poderá verificar se o HTTP/2 foi habilitado ou não usando um dos seguintes métodos:

- Habilite a versão do protocolo nos logs do IIS e examine os logs do IIS. Ele mostrará HTTP/2 nos logs. 
- Habilite a ferramenta de desenvolvedor F12 no Internet Explorer/Microsoft Edge e alterne para a guia rede para verificar o protocolo. 

Para obter mais informações, consulte [http/2 no IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Permissões

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como posso implementar o acesso baseado em função para serviços de nuvem?
Os serviços de nuvem não dão suporte ao modelo RBAC (controle de acesso baseado em função), pois não é um serviço baseado em Azure Resource Manager.

Consulte [entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Ambiente de trabalho remoto

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>A Microsoft interna pode engenheiros de área de trabalho remota para instâncias de serviço de nuvem sem permissão?
A Microsoft segue um processo estrito que não permitirá que engenheiros internos para a área de trabalho remota em seu serviço de nuvem sem permissão por escrito (email ou outra comunicação por escrito) do proprietário ou de seus designs.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Não consigo a área de trabalho remota para a VM do serviço de nuvem usando o arquivo RDP. Obtenho o seguinte erro: Ocorreu um erro de autenticação (código: 0x80004005)

Esse erro pode ocorrer se você usar o arquivo RDP de um computador que tenha ingressado no Azure Active Directory. Para resolver este problema, siga estes passos:

1. Clique com o botão direito do mouse no arquivo RDP baixado e selecione **Editar**.
2. Adicione "&#92;" como prefixo antes do nome de usuário. Por exemplo, use **.\username** em vez de **username**.

## <a name="scaling"></a>Dimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Não é possível dimensionar além de X instâncias
Sua assinatura do Azure tem um limite para o número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significará que você poderia ter 100 instâncias de máquina virtual de tamanho a1 para seu serviço de nuvem ou instâncias de máquina virtual de tamanho de 50 a2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Como posso configurar o dimensionamento automático com base nas métricas de memória?

No momento, não há suporte para dimensionamento automático baseado em métricas de memória para serviços de nuvem. 

Para contornar esse problema, você pode usar Application Insights. O dimensionamento automático dá suporte a Application Insights como uma origem de métrica e pode dimensionar a contagem de instâncias de função com base na métrica de convidado como "memória".  Você precisa configurar Application Insights em seu arquivo de pacote de projeto de serviço de nuvem (*. cspkg) e habilitar Diagnóstico do Azure extensão no serviço para implementar esse feito.

Para obter mais detalhes sobre como utilizar uma métrica personalizada por meio de Application Insights para configurar o dimensionamento automático em serviços de nuvem, consulte Introdução [ao dimensionamento automático por métrica personalizada no Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Para obter mais informações sobre como integrar Diagnóstico do Azure com Application Insights para serviços de nuvem, consulte [Enviar serviço de nuvem, máquina virtual ou Service Fabric dados de diagnóstico para Application insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Para obter mais informações sobre como habilitar Application Insights para serviços de nuvem, consulte [Application insights para serviços de nuvem do Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Para obter mais informações sobre como habilitar o log de Diagnóstico do Azure para serviços de nuvem, consulte [Configurar o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Genérica

### <a name="how-do-i-add-nosniff-to-my-website"></a>Como fazer adicionar "nosniff" ao meu site?
Para impedir que os clientes sniffem os tipos MIME, adicione uma configuração em seu arquivo *Web. config* .

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

Você também pode adicionar isso como uma configuração no IIS. Use o seguinte comando com o artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Como fazer personalizar o IIS para uma função Web?
Use o script de inicialização do IIS do artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Qual é o limite de cota para meu serviço de nuvem?
Consulte [limites específicos do serviço](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que a unidade em minha VM de serviço de nuvem mostra muito pouco espaço livre em disco?
Esse é um comportamento esperado e não deve causar nenhum problema ao seu aplicativo. O registro em log está ativado para a unidade% approot% nas VMs de PaaS do Azure, que basicamente consome o dobro da quantidade de espaço que os arquivos normalmente ocupam. No entanto, há várias coisas a serem consideradas que, essencialmente, transforma isso em um não problema.

O tamanho da unidade% approot% é calculado \<como tamanho de. cspkg + tamanho máximo do diário + uma margem de espaço livre > ou 1,5 GB, o que for maior. O tamanho da VM não tem nenhuma influência sobre esse cálculo. (O tamanho da VM só afeta o tamanho da unidade C: temporária.) 

Não há suporte para gravação na unidade% approot%. Se você estiver gravando na VM do Azure, deverá fazer isso em um recurso temporário do LocalStorage (ou outra opção, como o armazenamento de BLOBs, arquivos do Azure, etc.). Portanto, a quantidade de espaço livre na pasta% approot% não é significativa. Se você não tiver certeza de que seu aplicativo está gravando na unidade% approot%, você sempre poderá deixar que seu serviço seja executado por alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não gravará nada na unidade% approot%. Depois que o VHD é criado a partir de seu. cspkg e montado na VM do Azure, a única coisa que pode gravar nessa unidade é seu aplicativo. 

As configurações de diário não são configuráveis, portanto, você não pode desativá-la.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como posso adicionar uma extensão antimalware para meus serviços de nuvem de forma automatizada?

Você pode habilitar a extensão Antimalware usando o script do PowerShell na tarefa de inicialização. Siga as etapas nestes artigos para implementá-lo: 
 
- [Criar uma tarefa de inicialização do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implantação antimalware e como habilitá-lo no portal, consulte [cenários de implantação Antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como habilitar o SNI (Indicação de Nome de Servidor) para serviços de nuvem?

Você pode habilitar o SNI em serviços de nuvem usando um dos seguintes métodos:

**Método 1: Usar o PowerShell**

A associação SNI pode ser configurada usando o cmdlet **New-webbind** do PowerShell em uma tarefa de inicialização para uma instância de função de serviço de nuvem, como a seguir:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Conforme descrito [aqui](https://technet.microsoft.com/library/ee790567.aspx), o $SSLFlags pode ser um dos valores como o seguinte:

|Value|Significado|
------|------
|0|Sem SNI|
|1|SNI habilitado |
|2 |Associação não SNI que usa o repositório de certificados central|
|3|Associação SNI que usa o repositório de certificados central |
 
**Método 2: Usar código**

A associação SNI também pode ser configurada por meio de código na inicialização da função, conforme descrito nesta postagem no [blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Usando qualquer uma das abordagens acima, os respectivos certificados (*. pfx) para os nomes de host específicos precisam ser instalados primeiro nas instâncias de função usando uma tarefa de inicialização ou por meio de código para que a associação de SNI seja efetiva.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como posso adicionar marcas ao meu serviço de nuvem do Azure? 

O serviço de nuvem é um recurso clássico. Somente os recursos criados por meio de Azure Resource Manager marcas de suporte. Você não pode aplicar marcas a recursos clássicos, como o serviço de nuvem. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>O portal do Azure não exibe a versão do SDK do meu serviço de nuvem. Como posso conseguir isso?

Estamos trabalhando para trazer esse recurso no portal do Azure. Enquanto isso, você pode usar os seguintes comandos do PowerShell para obter a versão do SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Quero desligar o serviço de nuvem por vários meses. Como reduzir o custo de cobrança do serviço de nuvem sem perder o endereço IP?

Um serviço de nuvem já implantado é cobrado pela computação e pelo armazenamento que ele usa. Portanto, mesmo que você desligue a VM do Azure, você ainda será cobrado pelo armazenamento. 

Veja o que você pode fazer para reduzir sua cobrança sem perder o endereço IP do seu serviço:

1. [Reserve o endereço IP](../virtual-network/virtual-networks-reserved-public-ip.md) antes de excluir as implantações.  Você será cobrado somente por esse endereço IP. Para obter mais informações sobre a cobrança de endereço IP, consulte [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Exclua as implantações. Não exclua o xxx.cloudapp.net, para que você possa usá-lo para o futuro.
3. Se você quiser reimplantar o serviço de nuvem usando o mesmo IP de reserva reservado em sua assinatura, consulte [IP reservado endereços para serviços de nuvem e máquinas virtuais](https://azure.microsoft.com/blog/reserved-ip-addresses/).

