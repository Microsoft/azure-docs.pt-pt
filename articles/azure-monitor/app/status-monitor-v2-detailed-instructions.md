---
title: Instruções detalhadas de v2 de Monitor de estado do Azure | Documentos da Microsoft
description: Obter instruções detalhadas para começar a trabalhar com o Monitor de estado v2. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 3aca64c7b0f1ad04967782cb3349da302db557a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145094"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Instruções detalhadas de v2 de Monitor de estado

Este documento detalhes como para carregar para a galeria do PowerShell e transfira o módulo de ApplicationMonitor. Documentámos os parâmetros de mais comuns necessários para começar a utilizar.
Também incluímos instruções manuais no caso de acesso à internet não está disponível.

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Chave de instrumentação

Para começar, tem de ter uma chave de instrumentação. Para obter mais informações, leia [criar um recurso do Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Executar o PowerShell como administrador com uma política de execução elevada

**Executar como administrador**: 
- Descrição: PowerShell terá permissões de nível de administrador para fazer alterações ao seu computador.

**A política de execução**:
- Descrição: Por predefinição, executar scripts do PowerShell será desativada. Recomendamos que permite que RemoteSigned scripts para apenas o âmbito atual.
- Referência: [Sobre as políticas de execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Parâmetros opcionais:
    - `-Force` Isto irá ignorar o pedido de confirmação.

**Erros de exemplo:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para o PowerShell

Auditar a sua versão atual do PowerShell ao executar o comando: `$PSVersionTable`.
O comando produz a seguinte saída:


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

Estas instruções eram escritas e testadas num computador Windows 10 com as versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para a galeria do PowerShell

Estes passos irão preparar o seu servidor para transferir módulos da galeria do PowerShell.

> [!NOTE] 
> Suporte para a galeria do PowerShell está incluído no Windows 10, Windows Server 2016 e o PowerShell 6. Para versões mais antigas, reveja este documento: [Instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Execute o PowerShell como administrador com uma política de execução elevada.
2. Fornecedor do pacote de NuGet 
    - Descrição: Esse provedor é necessário para interagir com repositórios baseados em NuGet como PowerShellGallery
    - Referência: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para o pedido.
        - `-Force` Isto irá ignorar o pedido de confirmação. 
    
    Receberá esta linha de comandos se NuGet não estiver configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Repositórios confiáveis
    - Descrição: Por predefinição, o PowerShellGallery é um repositório não fidedigno.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para o pedido.

    Receberá esta linha de comandos se a galeria do PowerShell não fidedigna:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Pode confirmar esta alteração e todas as PSRepositories de auditoria executando o cmd: `Get-PSRepository`

4. Versão do PowerShellGet 
    - Descrição: Este módulo contém as ferramentas utilizadas para obter outros módulos da galeria do PowerShell. V1.0.0.1 é fornecido com o Windows 10 e Windows Server. Versão mínima necessária é v1.6.0. Para fazer a auditoria de qual versão é instalada, execute o comando: `Get-Command -Module PowerShellGet`
    - Referência: [Instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para o pedido.
        - `-Force` Isto irá ignorar o aviso "já está instalada" e instale a versão mais recente.

    Irá receber este erro se não estiver a utilizar uma versão mais recente do PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie o PowerShell. Não é possível carregar a nova versão na sessão atual. Quaisquer novas sessões do Powershell têm o PowerShellGet mais recente carregado.

## <a name="download--install-via-powershell-gallery"></a>Transferir e instalar através da galeria do PowerShell

Estes passos irão transferir o módulo de Az.ApplicationMonitor da galeria do PowerShell.

1. Pré-requisitos para a galeria do PowerShell são necessários.
2. Execute o PowerShell como administrador com uma política de execução elevada.
3. Instalar o módulo de Az.ApplicationMonitor
    - Referência: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para o pedido.
        - `-AllowPrerelease` Isto irá permitir a instalação de versões alfa e beta.
        - `-AcceptLicense` Isto irá ignorar a linha de comandos da "Licença aceite"
        - `-Force` Isto irá ignorar o aviso de "Repositório não fidedigno"

## <a name="download--install-manually-offline-option"></a>Transferir e instalar manualmente (opção offline)

Manualmente, se por qualquer motivo não é possível ligar para o módulo do PowerShell, pode transferir e instalar o módulo de Az.ApplicationMonitor da mesma.

### <a name="manually-download-the-latest-nupkg"></a>Baixar manualmente a mais recentes nupkg

1. Navegue para: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Selecione a versão mais recente a partir do histórico de versão.
3. Localize "Opções de instalação" e selecione "Transferir Manual".

### <a name="option-1-install-into-powershell-modules-directory"></a>Opção 1: instalar no diretório de módulos do PowerShell
Instale o módulo de PowerShell manualmente transferido para um diretório do PowerShell, pelo que pode ser detetável pelo sessões do PowerShell.
Para obter mais informações, consulte: [Instalar um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Deszipe nupkg jako zip usando o arquivo de expansão (v1.0.1.0)

- Descrição: A versão da base de Microsoft.PowerShell.Archive (v1.0.1.0) não é possível descomprimir ficheiros nupkg. Renomeie o arquivo com a extensão ". zip".
- Referência: [Expanda-arquivo](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Deszipe nupkg usando o arquivo de expansão (v1.1.0.0).

- Descrição: Utilize uma versão atual do arquivo de expandir para descomprimir nupkgs sem renomear a extensão. 
- Referência: [Arquivo expanda](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Opção 2: deszipe e importar manualmente
Instale o módulo de PowerShell manualmente transferido para um diretório do PowerShell, pelo que pode ser detetável pelo sessões do PowerShell.
Para obter mais informações, consulte: [Instalar um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Se instalar em qualquer outro diretório, é necessário importar manualmente o módulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> A instalação irá instalar DLLs por meio de caminhos relativos. Store o conteúdo deste pacote para o seu diretório de tempo de execução pretendido e confirme que as permissões de acesso permitem ler, mas não gravação.

1. Altere a extensão para ". zip" e extrair conteúdo do pacote para o seu diretório de instalação pretendida.
2. Localize o caminho de ficheiro para "Az.ApplicationMonitor.psd1".
3. Execute o PowerShell como administrador com uma política de execução elevada. 
4. Carregar o módulo através do comando: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

Ao monitorizar uma máquina na sua intranet privada, será necessário encaminhar o tráfego através de um proxy http.

Os comandos do PowerShell para transferir e instalar o Az.ApplicationMonitor da galeria do PowerShell suportam um `-Proxy` parâmetro.
Reveja as instruções acima quando escrever seus scripts de instalação.

Terá do Application Insights SDK enviar a telemetria da sua aplicação à Microsoft. Recomendamos configurar definições de proxy para a sua aplicação na Web. config. Consulte [FAQ do Application Insights: Proxy Passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) para obter mais informações.


## <a name="enable-monitoring"></a>Ativar monitorização 

Cmd: `Enable-ApplicationInsightsMonitoring`

Reveja nossa [referência da API](status-monitor-v2-api-enable-monitoring.md) para uma descrição detalhada de como utilizar este cmdlet. 



## <a name="next-steps"></a>Passos Seguintes

 Ver a telemetria:

- [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
- [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas
- [Criar dashboards](../../azure-monitor/app/app-insights-dashboards.md)

 Adicionar mais telemetria:

- [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para permitir a inserção de chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo

Faça mais com a v2 de Monitor de estado:

- Utilize o nosso guia para [resolução de problemas](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
