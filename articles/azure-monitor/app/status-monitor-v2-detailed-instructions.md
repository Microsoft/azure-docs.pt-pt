---
title: Azure Application Insights Agente de instruções detalhadas / Microsoft Docs
description: Instruções detalhadas para começar com o Agente de Insights de Aplicação. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2a0eb83b4b056230e8e7ef103f220b216fceb9e9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537462"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agente de Insights de Aplicação (anteriormente chamado Monitor de Estado v2): Instruções detalhadas

Este artigo descreve como embarcar na PowerShell Gallery e descarregar o módulo ApplicationMonitor.
Incluídos são os parâmetros mais comuns que precisa para começar.
Também fornecemos instruções de descarregamento manual caso não tenha acesso à Internet.

## <a name="get-an-instrumentation-key"></a>Obtenha uma chave de instrumentação

Para começar, precisa de uma chave de instrumentação. Para mais informações, consulte [Criar um recurso Deinsights](create-new-resource.md#copy-the-instrumentation-key)de Aplicação .

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Executar PowerShell como Administrador com uma política de execução elevada

### <a name="run-as-admin"></a>Corra como Administrador

A PowerShell precisa de permissões de nível de administrador para fazer alterações no seu computador.
### <a name="execution-policy"></a>Política de execução
- Descrição: Por defeito, a execução dos scripts PowerShell é desativada. Recomendamos que os scripts RemoteSigned apenas se apresente minuem.
- Referência: Sobre políticas de [execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [política de execução de conjuntos](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parâmetro opcional:
    - `-Force`. Contorna o pedido de confirmação.

**Erros de exemplo**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para powerShell

Audite a sua instância `$PSVersionTable` de PowerShell executando o comando.
Este comando produz a seguinte saída:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Estas instruções foram escritas e testadas num computador que executa o Windows 10 e as versões acima listadas.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para a Galeria PowerShell

Estes passos prepararão o seu servidor para descarregar módulos da PowerShell Gallery.

> [!NOTE] 
> A PowerShell Gallery é suportada no Windows 10, Windows Server 2016 e PowerShell 6.
> Para obter informações sobre versões anteriores, consulte [instalar powerShellGet](/powershell/scripting/gallery/installing-psget).


1. Executar powerShell como Administrador com uma política de execução elevada.
2. Instale o fornecedor de pacotes NuGet.
    - Descrição: Você precisa deste fornecedor para interagir com repositórios baseados em NuGet como PowerShell Gallery.
    - Referência: [Instalar pacotesFornecedor](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Contorna o pedido de confirmação.
    
    Receberá este pedido se o NuGet não estiver configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configure a PowerShell Gallery como um repositório de confiança.
    - Descrição: Por defeito, a PowerShell Gallery é um repositório não confiável.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parâmetro opcional:
        - `-Proxy`. Especifica um servidor proxy para o pedido.

    Receberá este pedido se a PowerShell Gallery não for de confiança:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Pode confirmar esta alteração e auditar todos os `Get-PSRepository` PSRepositórios executando o comando.

4. Instale a versão mais recente do PowerShellGet.
    - Descrição: Este módulo contém a ferramenta utilizada para obter outros módulos da PowerShell Gallery. Versão 1.0.0.1 naves com Windows 10 e Windows Server. A versão 1.6.0 ou superior é necessária. Para determinar qual a versão `Get-Command -Module PowerShellGet` instalada, execute o comando.
    - Referência: [Instalação powerShellGet](/powershell/scripting/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Contorna o aviso "já instalado" e instala a versão mais recente.

    Receberá este erro se não estiver a utilizar a versão mais recente do PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reiniciar a PowerShell. Não pode carregar a nova versão na sessão atual. As novas sessões da PowerShell carregarão a versão mais recente do PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Descarregue e instale o módulo através da PowerShell Gallery

Estes passos descarregarão o módulo Az.ApplicationMonitor da PowerShell Gallery.

1. Certifique-se de que todos os pré-requisitos para a PowerShell Gallery são cumpridos.
2. Executar powerShell como Administrador com uma política de execução elevada.
3. Instale o módulo Az.ApplicationMonitor.
    - Referência: [Módulo de Instalação](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-AllowPrerelease`. Permite a instalação de lançamentos alfa e beta.
        - `-AcceptLicense`. Contorna o pedido de "Aceitar licença"
        - `-Force`. Contorna o aviso de "Repositório Não Confiável".

## <a name="download-and-install-the-module-manually-offline-option"></a>Descarregue e instale o módulo manualmente (opção offline)

Se por alguma razão não conseguir ligar-se ao módulo PowerShell, pode descarregar manualmente e instalar o módulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Descarregue manualmente o mais recente ficheiro nupkg

1. Aceda a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecione a versão mais recente do ficheiro na tabela **'História** da versão'.
3. Em condições de **instalação,** selecione **Manual Download**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opção 1: Instalar num diretório de módulos PowerShell
Instale o módulo PowerShell descarregado manualmente num diretório PowerShell para que seja detetável pelas sessões da PowerShell.
Para mais informações, consulte [a instalação de um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Unzip nupkg como um ficheiro zip utilizando o Expand-Archive (v1.0.1.0)

- Descrição: A versão base do Microsoft.PowerShell.Archive (v1.0.1.0) não consegue desapertar ficheiros nupkg. Mude o nome do ficheiro com a extensão .zip.
- Referência: [Expandir-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg unzip utilizando O Ampliação de Arquivo (v1.1.0.0)

- Descrição: Utilize uma versão atual do Expand-Archive para desapertar ficheiros nupkg sem alterar a extensão.
- Referência: [Expandir-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opção 2: Unzip e importe nupkg manualmente
Instale o módulo PowerShell descarregado manualmente num diretório PowerShell para que seja detetável pelas sessões da PowerShell.
Para mais informações, consulte [a instalação de um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Se estiver a instalar o módulo em qualquer outro diretório, importe manualmente o módulo utilizando o [Módulo de Importação](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Os DLLs serão instalados através de caminhos relativos.
> Guarde o conteúdo da embalagem no diretório de prazo de execução pretendido e confirme que as permissões de acesso permitem ler, mas não escrever.

1. Altere a extensão para ".zip" e extraia o conteúdo da embalagem no diretório de instalação pretendido.
2. Encontre o caminho de arquivo de Az.ApplicationMonitor.psd1.
3. Executar powerShell como Administrador com uma política de execução elevada.
4. Carregue o módulo `Import-Module Az.ApplicationMonitor.psd1` utilizando o comando.
    

## <a name="route-traffic-through-a-proxy"></a>Tráfego de rota através de um proxy

Quando monitorizar um computador na sua intranet privada, terá de encaminhar o tráfego HTTP através de um proxy.

O PowerShell ordena descarregar e instalar o Az.ApplicationMonitor `-Proxy` a partir da PowerShell Gallery suportaum parâmetro.
Reveja as instruções anteriores quando escrever os scripts de instalação.

O SDK de Insights de Aplicação terá de enviar a telemetria da sua aplicação para a Microsoft. Recomendamos que configure as definições de proxy para a sua aplicação no seu ficheiro web.config. Para mais informações, consulte [Application Insights FAQ: Proxy passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Ativar monitorização

Utilize `Enable-ApplicationInsightsMonitoring` o comando para ativar a monitorização.

Consulte a [referência da API](status-monitor-v2-api-enable-monitoring.md) para obter uma descrição detalhada de como utilizar este cmdlet.



## <a name="next-steps"></a>Passos seguintes

 Ver a telemetria:

- [Explore as métricas](../../azure-monitor/platform/metrics-charts.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use o Analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).

 Adicionar mais telemetria:

- [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.

Faça mais com o Agente de Insights de Aplicação:

- Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
