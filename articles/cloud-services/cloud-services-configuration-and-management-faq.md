---
title: Questões de configuração e gestão FAQ
description: Este artigo lista as perguntas frequentes sobre configuração e gestão para os Serviços de Cloud Microsoft Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 04a30a2446061df75d133bdbd088b7e71c59cade
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578215"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Questões de configuração e gestão para Azure Cloud Services (clássico): Perguntas frequentes (PERGUNTAS)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo inclui perguntas frequentes sobre problemas de configuração e gestão para [os Serviços de Cloud Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar a [página de tamanho VM dos Serviços cloud](cloud-services-sizes-specs.md) para obter informações sobre o tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificados**

- [Porque é que a cadeia de certificados do meu certificado Cloud Service TLS/SSL está incompleta?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Qual é o propósito do "Certificado de Encriptação de Ferramentas windows Azure para extensões"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Como posso gerar um Pedido de Assinatura de Certificado (CSR) sem "RDP-ing" no caso?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [O meu Certificado de Gestão de Serviços na Nuvem está a expirar. Como renová-lo?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Como automatizar a instalação do certificado TLS/SSL principal (.pfx) e certificado intermédio (.p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Qual é o objetivo do certificado "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorização e registos**

- [Quais são as próximas capacidades do Cloud Service no portal Azure que podem ajudar a gerir e monitorizar aplicações?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Porque é que o IIS para de escrever para o diretório de registos?](#why-does-iis-stop-writing-to-the-log-directory)
- [Como posso habilitar o registo do WAD para serviços na Nuvem?](#how-do-i-enable-wad-logging-for-cloud-services)

**Configuração de rede**

- [Como posso definir o tempo limite para o equilibrador de carga do Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Como posso associar um endereço IP estático ao meu Serviço cloud?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Quais são as funcionalidades e capacidades que o Azure basic IPS/IDS e DDOS fornece?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Como ativar HTTP/2 em Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Permissões**

- [Os engenheiros internos da Microsoft podem fazer desktop remoto para instâncias do Cloud Service sem permissão?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Não posso fazer um ambiente de trabalho remoto para Cloud Service VM utilizando o ficheiro RDP. Recebo o seguinte erro: ocorreu um erro de autenticação (Código: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Dimensionamento**

- [Não posso escalar para além de instâncias X](#i-cannot-scale-beyond-x-instances)
- [Como posso configurar a Auto-Escala com base em métricas de memória?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Genérico**

- [Como adiciono `nosniff` ao meu site?](#how-do-i-add-nosniff-to-my-website)
- [Como posso personalizar o IIS para um papel web?](#how-do-i-customize-iis-for-a-web-role)
- [Qual é o limite de quota para o meu Serviço Cloud?](#what-is-the-quota-limit-for-my-cloud-service)
- [Porque é que a unidade do meu VM cloud show muito pouco espaço de disco gratuito?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Como posso adicionar uma extensão antimalware para os meus Serviços Cloud de forma automatizada?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Como ativar a indicação do nome do servidor (SNI) para serviços na nuvem?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Como posso adicionar etiquetas ao meu Serviço Azure Cloud?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [O portal Azure não apresenta a versão SDK do meu Serviço cloud. Como posso conseguir isto?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Quero encerrar o Serviço Cloud por vários meses. Como reduzir o custo de faturação do Cloud Service sem perder o endereço IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificados

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Porque é que a cadeia de certificados do meu certificado Cloud Service TLS/SSL está incompleta?
    
Recomendamos que os clientes instalem a cadeia de certificados completa (cert de folha, certificados intermédios e cert de raiz) em vez de apenas o certificado de folha. Quando instala apenas o certificado de folha, confia no Windows para construir a cadeia de certificados andando no CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou no Windows Update quando o Windows estiver a tentar validar o certificado, o certificado pode ser considerado inválido. Ao instalar a cadeia de certificados completa, este problema pode ser evitado. O blog da [Como instalar um certificado SSL acorrentado](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) mostra como fazê-lo.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é o propósito do "Certificado de Encriptação de Ferramentas windows Azure para extensões"?

Estes certificados são automaticamente criados sempre que uma extensão é adicionada ao Serviço cloud. Mais comummente, esta é a extensão WAD ou a extensão RDP, mas pode ser outras, como a extensão Antimalware ou Log Collector. Estes certificados são utilizados apenas para encriptar e desencriptar a configuração privada para a extensão. A data de validade nunca é verificada, por isso não importa se o certificado está caducado. 

Pode ignorar estes certificados. Se quiser limpar os certificados, pode tentar apagá-los a todos. O Azure lançará um erro se tentar apagar um certificado que está a ser utilizado.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar um Pedido de Assinatura de Certificado (CSR) sem "RDP-ing" no caso?

Consulte o seguinte documento de orientação:

[Obtenção de um certificado para utilização com Web Sites Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

A RSE é apenas um ficheiro de texto. Não tem de ser criado a partir da máquina onde o certificado será utilizado em última instância.Embora este documento esteja escrito para um Serviço de Aplicações, a criação de RSE é genérica e aplica-se também aos Serviços cloud.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>O meu Certificado de Gestão de Serviços na Nuvem está a expirar. Como renová-lo?

Pode utilizar os comandos PowerShell para renovar os seus Certificados de Gestão:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

O **Get-AzurePublishSettingsFile** criará um novo certificado de gestão em  >  **Certificados de Gestão de Assinaturas** no portal Azure. O nome do novo certificado parece "YourSubscriptionNam]-[CurrentDate]-credenciais".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Como automatizar a instalação do certificado TLS/SSL principal (.pfx) e certificado intermédio (.p7b)?

Pode automatizar esta tarefa utilizando um script de arranque (batch/cmd/PowerShell) e registar o script de arranque no ficheiro de definição de serviço. Adicione o script de arranque e o certificado (.p7b file) na pasta do projeto do mesmo diretório do script de arranque.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Qual é o objetivo do certificado "Microsoft Azure Service Management for MachineKey"?

Este certificado é utilizado para encriptar as chaves da máquina em Azure Web Roles. Para saber mais, confira [este aviso.](/security-updates/securityadvisories/2018/4092731)

Para obter mais informações, veja os seguintes artigos:
- [Como configurar e executar tarefas de arranque para um Serviço cloud](./cloud-services-startup-tasks.md)
- [Tarefas de arranque do Serviço Comum de Nuvem](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Monitorização e registos

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são as próximas capacidades do Cloud Service no portal Azure que podem ajudar a gerir e monitorizar aplicações?

A capacidade de gerar um novo certificado para o Protocolo de Ambiente de Trabalho Remoto (RDP) está para breve. Em alternativa, pode executar este script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
A capacidade de escolher blob ou local para o seu local de upload csdef e cscfg está para breve. Utilizando [o New-AzureDeployment,](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-4.0.0&preserve-view=true)pode definir cada valor de localização.

Capacidade de monitorizar métricas ao nível da instância. Estão disponíveis capacidades de monitorização adicionais em [Como Monitorizar os Serviços de Cloud.](cloud-services-how-to-monitor.md)

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Porque é que o IIS para de escrever para o diretório de registos?
Esgotou a quota de armazenamento local para escrever para o diretório de registos.Para corrigir isto, pode fazer uma de três coisas:
* Habilitar os diagnósticos para o IIS e mover os diagnósticos periodicamente para o armazenamento de bolhas.
* Remova manualmente os ficheiros de registo do diretório de registo.
* Aumentar o limite de quota para os recursos locais.

Para obter mais informações, consulte os documentos seguintes:
* [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Os registos do IIS deixam de escrever no Cloud Service](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Como posso habilitar o registo do WAD para serviços na Nuvem?
Pode ativar o registo de diagnósticos do Windows Azure (WAD) através das seguintes opções:
1. [Ativar a partir do Estúdio Visual](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Ativar através do código .NET](./cloud-services-dotnet-diagnostics.md)
3. [Ativar através do PowerShell](./cloud-services-diagnostics-powershell.md)

Para obter as atuais definições de WAD do seu Serviço cloud, pode utilizar [o Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) PowerShell cmd ou pode vê-lo através do portal a partir da lâmina "Cloud Services --> Extensions".


## <a name="network-configuration"></a>Configuração de rede

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como posso definir o tempo limite para o equilibrador de carga do Azure?
Pode especificar o tempo limite no seu ficheiro de definição de serviço (csdef) desta forma:

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
Ver Novo: Tempo limite de marcha lenta para [Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como posso associar um endereço IP estático ao meu Serviço cloud?
Para configurar um endereço IP estático, precisa criar um IP reservado. Este IP reservado pode ser associado a um novo Serviço cloud ou a uma implementação existente. Consulte os seguintes documentos para mais detalhes:
* [Como criar um endereço IP reservado](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Reserve o endereço IP de um Serviço cloud existente](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo Serviço cloud](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implementação em execução](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um Serviço de Nuvem utilizando um ficheiro de configuração de serviço](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são as funcionalidades e capacidades que o Azure basic IPS/IDS e DDOS fornece?
O Azure tem IPS/IDS em servidores físicos do datacenter para se defender contra ameaças. Além disso, os clientes podem implementar soluções de segurança de terceiros, tais como firewalls de aplicações web, firewalls de rede, antimalware, deteção de intrusões, sistemas de prevenção (IDS/IPS) e muito mais. Para mais informações, consulte [Protect your data and assets e respeite as normas globais de segurança.](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)

A Microsoft monitoriza continuamente servidores, redes e aplicações para detetar ameaças. A abordagem multifacetada de gestão de ameaças da Azure utiliza a deteção de intrusões, a prevenção de ataques de negação de serviço distribuídos (DDoS), testes de penetração, análise comportamental, deteção de anomalias e aprendizagem automática para fortalecer constantemente a sua defesa e reduzir os riscos. O Microsoft Antimalware para Azure protege os Serviços Azure Cloud e máquinas virtuais. Além disso, tem a opção de implementar soluções de segurança de terceiros, tais como paredes de incêndio de aplicações web, firewalls de rede, antimalware, sistemas de deteção e prevenção de intrusões (IDS/IPS), e muito mais.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como ativar HTTP/2 em Cloud Services VM?

O Windows 10 e o Windows Server 2016 vêm com suporte para HTTP/2 no lado do cliente e do servidor. Se o seu cliente (browser) estiver a ligar-se ao servidor IIS através do TLS que negoceia HTTP/2 através de extensões TLS, então não precisa de fazer qualquer alteração no lado do servidor. Isto porque, sobre o TLS, o cabeçalho h2-14 especificando a utilização de HTTP/2 é enviado por predefinição. Se, por outro lado, o seu cliente estiver a enviar um cabeçalho de Atualização para atualizar para HTTP/2, então tem de fazer a alteração abaixo no lado do servidor para garantir que a Atualização funciona e que acaba por ter uma ligação HTTP/2. 

1. Corre regedit.exe.
2. Navegue pela chave de registo: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Crie um novo valor DWORD chamado **DuoEnabled**.
4. Desa parte para 1.
5. Reinicie o servidor.
6. Vá ao Seu **Web Site padrão** e em **Encadernações,** crie uma nova ligação TLS com o certificado auto-assinado acaba de ser criado. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: Browser, Apps e Web Server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Estes passos poderiam ser automatizados através de uma tarefa de arranque, de modo que sempre que um novo caso PaaS é criado, pode fazer as alterações acima no registo do sistema. Para obter mais informações, consulte [Como configurar e executar tarefas de arranque para um Cloud Service](cloud-services-startup-tasks.md).

 
Uma vez feito isto, pode verificar se o HTTP/2 foi ativado ou não utilizando um dos seguintes métodos:

- Ativar a versão Protocol nos registos do IIS e ver os registos do IIS. Mostrará HTTP/2 nos registos. 
- Ative a Ferramenta de Desenvolvimento F12 no Internet Explorer ou Microsoft Edge e mude para o separador 'Rede' para verificar o protocolo. 

Para mais informações, consulte [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Permissões

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como posso implementar o acesso baseado em funções para serviços cloud?
A Cloud Services não suporta o modelo de controlo de acesso baseado em funções (Azure RBAC), uma vez que não é um serviço baseado em Recursos Azure.

Ver [Compreender os diferentes papéis em Azure.](../role-based-access-control/rbac-and-directory-admin-roles.md)

## <a name="remote-desktop"></a>Ambiente de trabalho remoto

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Os engenheiros internos da Microsoft podem fazer desktop remoto para instâncias do Cloud Service sem permissão?
A Microsoft segue um processo rigoroso que não permitirá que engenheiros internos se ofirmem a um ambiente de trabalho remoto no seu Cloud Service sem permissão por escrito (e-mail ou outra comunicação escrita) do proprietário ou do seu design.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Não posso fazer um ambiente de trabalho remoto para Cloud Service VM utilizando o ficheiro RDP. Recebo o seguinte erro: ocorreu um erro de autenticação (Código: 0x80004005)

Este erro pode ocorrer se utilizar o ficheiro RDP a partir de uma máquina que se junta ao Diretório Ativo Azure. Para resolver este problema, siga estes passos:

1. Clique com o botão direito no ficheiro RDP que descarregou e, em seguida, **selecione Editar**.
2. Adicione "&#92;" como prefixo antes do nome de utilizador. Por exemplo, utilize **.\username** em vez de nome de  **utilizador**.

## <a name="scaling"></a>Dimensionamento

### <a name="i-cannot-scale-beyond-x-instances"></a>Não posso escalar para além de instâncias X
A sua Subscrição Azure tem um limite no número de núcleos que pode utilizar. A escala não funcionará se tiver usado todos os núcleos disponíveis. Por exemplo, se tiver um limite de 100 núcleos, isto significa que pode ter 100 caixas de máquinas virtuais de tamanho A1 para o seu Cloud Service ou 50 instâncias de máquina virtual de tamanho 50 A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Como posso configurar a Auto-Escala com base em métricas de memória?

A escala automática baseada em métricas de memória para um Serviço de Nuvem não é suportada atualmente. 

Para resolver este problema, pode utilizar o Application Insights. A Escala Automática suporta o Application Insights como uma Fonte de Métricas e pode escalar a contagem de exemplos de função com base na métrica do hóspede como "Memória".  Tem de configurar insights de aplicação no seu ficheiro de pacote de projetos cloud Service (*.cspkg) e permitir a extensão do Azure Diagnostics no serviço para implementar este feito.

Para obter mais detalhes sobre como utilizar uma métrica personalizada através de Application Insights para configurar a Auto-Escala nos Serviços de Cloud, consulte [Começar com escala automática por métrica personalizada em Azure](../azure-monitor/autoscale/autoscale-custom-metric.md)

Para obter mais informações sobre como integrar o Azure Diagnostics com Insights de Aplicações para Serviços em Nuvem, consulte enviar dados de [diagnóstico de Cloud Service, Virtual Machine ou Service Fabric para Application Insights](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)

Para obter mais informações sobre como ativar insights de aplicações para serviços em nuvem, consulte [Insights de Aplicações para Serviços em Nuvem Azure](../azure-monitor/app/cloudservices.md)

Para obter mais informações sobre como ativar o registo de diagnósticos Azure para serviços na nuvem, consulte [Configurar diagnósticos para serviços em nuvem Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Genérica

### <a name="how-do-i-add-nosniff-to-my-website"></a>Como adiciono `nosniff` ao meu site?
Para evitar que os clientes cheirem os tipos de MIME, adicione uma definição no seu *ficheiroweb.config.*

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

Também pode adicionar isto como uma definição no IIS. Utilize o seguinte comando com o artigo [de tarefas de arranque comum.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Como posso personalizar o IIS para um papel web?
Utilize o script de arranque IIS a partir do artigo [de tarefas de arranque comum.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Qual é o limite de quota para o meu Serviço Cloud?
Consulte [os limites específicos do serviço.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Porque é que a unidade do meu VM cloud show muito pouco espaço de disco gratuito?
Este comportamento é esperado, e não deve causar qualquer problema à sua aplicação. O diário é ligado para a unidade de %aproot% em Azure PaaS VMs, que essencialmente consome o dobro da quantidade de espaço que os ficheiros normalmente consomem. No entanto, há várias coisas a ter em conta que, essencialmente, transformam isto numa questão não relacionada.

O tamanho da unidade de %aproot% é calculado como <tamanho de .cspkg + tamanho máximo do diário + uma margem de espaço livre>, ou 1,5 GB, o que for maior. O tamanho do seu VM não tem qualquer influência neste cálculo. (O tamanho VM só afeta o tamanho do C temporário: unidade.) 

Não é suportado para escrever para a unidade de %aproot%. Se estiver a escrever para o Azure VM, deve fazê-lo num recurso local temporário (ou outra opção, como armazenamento Blob, Ficheiros Azure, etc.). Assim, a quantidade de espaço livre na pasta %aproot% não tem significado. Se não tiver a certeza se a sua aplicação está a escrever para a unidade de %aproot%, pode sempre deixar o seu serviço funcionar durante alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não vai escrever nada à unidade %aproot%. Uma vez que o VHD é criado a partir do seu `.cspkg` e montado no Azure VM, a única coisa que pode escrever a esta unidade é a sua aplicação. 

As definições do diário não são configuráveis, por isso não pode desligá-lo.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como posso adicionar uma extensão antimalware para os meus Serviços Cloud de forma automatizada?

Pode ativar a extensão antimalware utilizando o script PowerShell na Tarefa de Arranque. Siga os passos nestes artigos para implementá-lo: 
 
- [Criar uma tarefa de arranque PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0&preserve-view=true)

Para obter mais informações sobre cenários de implementação de Antimalware e como o permitir a partir do portal, consulte Os Cenários de [Implementação de Antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como ativar a indicação do nome do servidor (SNI) para serviços na nuvem?

Pode ativar o SNI nos Serviços cloud utilizando um dos seguintes métodos:

**Método 1: Utilizar powershell**

A ligação SNI pode ser configurada usando o PowerShell cmdlet **New-WebBinding** numa tarefa de arranque para uma instância de função de Cloud Service como abaixo:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Como [aqui](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))descrito, o $sslFlags pode ser um dos valores como os seguintes:

|Valor|Significado|
------|------
|0|Sem SNI|
|1|SNI Habilitado|
|2|Encadernação não SNI que utiliza Loja Central de Certificados|
|3|Encadernação SNI que utiliza loja de certificados centrais|
 
**Método 2: Utilizar código**

A ligação SNI também poderia ser configurada por código na startup de função, tal como descrito neste [post de blog:](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service)

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Utilizando qualquer uma das abordagens acima referidas, os respetivos certificados (*.pfx) para os nomes de anfitriões específicos devem ser instalados pela primeira vez nas instâncias de função utilizando uma tarefa de arranque ou através de código, a fim de que a ligação SNI seja eficaz.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como posso adicionar etiquetas ao meu Serviço Azure Cloud? 

O Cloud Service é um recurso clássico. Apenas recursos criados através de tags de suporte do Azure Resource Manager. Não é possível aplicar etiquetas a recursos clássicos, como o Cloud Service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>O portal Azure não apresenta a versão SDK do meu Serviço cloud. Como posso conseguir isto?

Estamos a trabalhar para trazer esta funcionalidade para o portal Azure. Entretanto, podes usar os comandos PowerShell para obter a versão SDK:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Quero encerrar o Serviço Cloud por vários meses. Como reduzir o custo de faturação do Cloud Service sem perder o endereço IP?

Um Serviço Cloud já implantado é cobrado para o Computação e Armazenamento que utiliza. Por isso, mesmo que desligue o Azure VM, ainda será cobrado pelo Armazenamento. 

Aqui está o que pode fazer para reduzir a sua faturação sem perder o endereço IP para o seu serviço:

1. [Reserve o endereço IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) antes de eliminar as implementações.  Só será cobrado para este endereço IP. Para obter mais informações sobre a faturação do endereço IP, consulte [os preços dos endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Elimine as implementações. Não apague a xxx.cloudapp.net, para que possa usá-la para o futuro.
3. Se pretender recolocar o Serviço cloud utilizando o mesmo IP de reserva que reservou na sua subscrição, consulte [endereços IP reservados para Serviços cloud e máquinas virtuais.](https://azure.microsoft.com/blog/reserved-ip-addresses/)