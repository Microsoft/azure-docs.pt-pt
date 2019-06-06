---
title: Instruções detalhadas de v2 de Monitor de estado do Azure | Documentos da Microsoft
description: Obter instruções detalhadas para começar a trabalhar com o Monitor de estado v2. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734185"
---
# <a name="status-monitor-v2-detailed-instructions"></a>V2 do Monitor de estado: Instruções detalhadas

Este artigo descreve como integrar para a galeria do PowerShell e transfira o módulo de ApplicationMonitor.
Ele descreve os parâmetros mais comuns que precisará para começar a utilizar.
Ele também inclui instruções manuais no caso de não ter acesso à internet.

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Obter uma chave de instrumentação

Para começar, terá de uma chave de instrumentação. Para obter mais informações, consulte [criar um recurso do Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Executar o PowerShell como administrador com uma política de execução elevada

**Executar como administrador**

PowerShell necessita de permissões de nível de administrador para fazer alterações ao seu computador.

**Política de execução**
- Descrição: Por predefinição, a execução de scripts do PowerShell está desativada. Recomendamos que permite que scripts RemoteSigned para apenas o âmbito atual.
- Referência: [Sobre as políticas de execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parâmetro opcional:
    - `-Force`. Ignora o pedido de confirmação.

**Erros de exemplo**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para o PowerShell

Auditar a sua instância do PowerShell ao executar o `$PSVersionTable` comando.
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

Estas instruções eram escritas e testadas num computador com Windows 10 e as versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para a galeria do PowerShell

Estes passos irão preparar o seu servidor para transferir módulos da galeria do PowerShell.

> [!NOTE] 
> Galeria do PowerShell é suportada no Windows 10, Windows Server 2016 e o PowerShell 6.
> Para obter informações sobre as versões anteriores, consulte [instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Execute o PowerShell como administrador com uma política de execução elevada.
2. Instale o fornecedor do pacote de NuGet.
    - Descrição: Precisa esse provedor para interagir com repositórios baseados em NuGet, como a galeria do PowerShell.
    - Referência: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Ignora o pedido de confirmação.
    
    Receberá esta linha de comandos se NuGet não estiver configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configure a galeria do PowerShell como um repositório fidedigno.
    - Descrição: Por predefinição, a galeria do PowerShell é um repositório não fidedigno.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parâmetro opcional:
        - `-Proxy`. Especifica um servidor proxy para o pedido.

    Receberá esta linha de comandos se a galeria do PowerShell não fidedigna:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Pode confirmar esta alteração e todas as PSRepositories de auditoria executando o `Get-PSRepository` comando.

4. Instale a versão mais recente do PowerShellGet.
    - Descrição: Este módulo contém as ferramentas utilizadas para obter outros módulos da galeria do PowerShell. Versão 1.0.0.1 é fornecido com o Windows 10 e Windows Server. Versão 1.6.0 ou superior é obrigatório. Para determinar qual versão é instalada, execute o `Get-Command -Module PowerShellGet` comando.
    - Referência: [Instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Ignora o aviso "já está instalada" e instala a versão mais recente.

    Irá receber este erro se não estiver a utilizar a versão mais recente do PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie o PowerShell. Não é possível carregar a nova versão na sessão atual. Novas sessões do PowerShell irão carregar a versão mais recente do PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Transferir e instalar o módulo através da galeria do PowerShell

Estes passos irão transferir o módulo de Az.ApplicationMonitor da galeria do PowerShell.

1. Certifique-se de que todos os pré-requisitos da galeria do PowerShell são cumpridos.
2. Execute o PowerShell como administrador com uma política de execução elevada.
3. Instale o módulo de Az.ApplicationMonitor.
    - Referência: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-AllowPrerelease`. Permite a instalação das versões alfa e beta.
        - `-AcceptLicense`. Ignora a linha de comandos da "Licença aceite"
        - `-Force`. Ignora o aviso de "Repositório não confiáveis".

## <a name="download-and-install-the-module-manually-offline-option"></a>Transferir e instalar manualmente o módulo (opção offline)

Se por qualquer motivo, que não é possível ligar para o módulo do PowerShell, pode transferir e instalar o módulo de Az.ApplicationMonitor manualmente.

### <a name="manually-download-the-latest-nupkg-file"></a>Transferir manualmente o ficheiro de nupkg mais recente

1. Aceda a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecione a versão mais recente do arquivo na **histórico de versões** tabela.
3. Sob **opções de instalação**, selecione **Manual transferir**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opção 1: Instalar num diretório de módulos do PowerShell
Instale o módulo de PowerShell manualmente transferido para um diretório do PowerShell, pelo que irá ser detetável pelo sessões do PowerShell.
Para obter mais informações, consulte [instalar um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Deszipe nupkg como um ficheiro zip com o arquivo de expansão (v1.0.1.0)

- Descrição: A versão da base de Microsoft.PowerShell.Archive (v1.0.1.0) não é possível descomprimir ficheiros nupkg. Renomeie o arquivo com a extensão. zip.
- Referência: [Arquivo expanda](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Deszipe nupkg usando o arquivo de expansão (v1.1.0.0)

- Descrição: Utilize uma versão atual do arquivo de expandir para descompactar arquivos nupkg sem alterar a extensão.
- Referência: [Arquivo expanda](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opção 2: Deszipe e importar nupkg manualmente
Instale o módulo de PowerShell manualmente transferido para um diretório do PowerShell, pelo que irá ser detetável pelo sessões do PowerShell.
Para obter mais informações, consulte [instalar um módulo do PowerShell](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Se estiver a instalar o módulo em qualquer outro diretório, importar manualmente o módulo utilizando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLLs serão instalado através de caminhos relativos.
> Store o conteúdo do pacote no seu diretório de tempo de execução pretendido e confirme que as permissões de acesso permitem ler, mas não gravação.

1. Altere a extensão para ". zip" e extrair o conteúdo do pacote para o seu diretório de instalação pretendida.
2. Encontre o caminho de ficheiro do Az.ApplicationMonitor.psd1.
3. Execute o PowerShell como administrador com uma política de execução elevada.
4. Carregar o módulo com o `Import-Module Az.ApplicationMonitor.psd1` comando.
    

## <a name="route-traffic-through-a-proxy"></a>Encaminhar o tráfego através de um proxy

Ao monitorizar um computador na sua intranet privada, precisará encaminhar o tráfego através de um proxy HTTP.

Os comandos do PowerShell para transferir e instalar Az.ApplicationMonitor da galeria do PowerShell suportam um `-Proxy` parâmetro.
Reveja as instruções anteriores, quando escreve seus scripts de instalação.

Terá do Application Insights SDK enviar a telemetria da sua aplicação à Microsoft. Recomendamos que configure as definições de proxy para a sua aplicação no seu arquivo Web. config. Para obter mais informações, consulte [FAQ do Application Insights: Proxy passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Ativar monitorização

Utilize o `Enable-ApplicationInsightsMonitoring` comando para ativar a monitorização.

Consulte a [referência da API](status-monitor-v2-api-enable-monitoring.md) para uma descrição detalhada de como utilizar este cmdlet.



## <a name="next-steps"></a>Passos Seguintes

 Ver a telemetria:

- [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e utilização.
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Utilizar a análise de](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).

 Adicionar mais telemetria:

- [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para ativar chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo.

Faça mais com a v2 de Monitor de estado:

- Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
