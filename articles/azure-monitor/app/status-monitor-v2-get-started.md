---
title: Azure Application Insights Agent - a começar / Microsoft Docs
description: Um guia de arranque rápido para o Agente de Insights de Aplicações. Monitorize o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicações web hospedadas no local, em VMs ou em Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 690304ecef80d988f9a554cd10ce4689f5c72133
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070144"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Começar com o Azure Monitor Application Insights Agent para servidores no local

Este artigo contém os comandos de arranque rápido que se espera que funcionem para a maioria dos ambientes.
As instruções dependem da Galeria PowerShell para distribuir atualizações.
Estes comandos suportam o `-Proxy` parâmetro PowerShell.

Para obter uma explicação destes comandos, instruções de personalização e informações sobre a resolução de problemas, consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="download-and-install-via-powershell-gallery"></a>Faça o download e instale através da PowerShell Gallery

### <a name="install-prerequisites"></a>Pré-requisitos da instalação
Executar PowerShell como Administrador.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Close PowerShell.

### <a name="install-application-insights-agent"></a>Instalar agente de insights de aplicações
Executar PowerShell como Administrador.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Ativar monitorização
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Faça o download e instale manualmente (opção offline)
### <a name="download-the-module"></a>Descarregue o módulo
Descarregue manualmente a versão mais recente do módulo da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Desaperte e instale o Agente de Insights de Aplicação
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Ativar monitorização
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Passos seguintes

 Ver a telemetria:

- [Explore métricas](../platform/metrics-charts.md) para monitorizar o desempenho e a utilização.
- [Pesquisar eventos e registos](./diagnostic-search.md) para diagnosticar problemas.
- [Use Analytics](../log-query/log-query-overview.md) para consultas mais avançadas.
- [Criar painéis de instrumentos.](./overview-dashboard.md)

 Adicionar mais telemetria:

- [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria ao cliente web](./javascript.md) para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código para](./asp-net.md) que possa inserir rastreios e chamadas de registo.

Faça mais com o Application Insights Agent:

- Reveja as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para uma explicação dos comandos encontrados aqui.
- Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente de Insights de Aplicação.

