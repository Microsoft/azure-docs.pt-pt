---
title: Azure Application Insights Agent instruções detalhadas | Microsoft Docs
description: Instruções detalhadas para começar com o Application Insights Agent. Monitorize o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicações web hospedadas no local, em VMs ou em Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 29922f088a51e4876e5e2ec8fe87c3bbce4482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521684"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Agent (anteriormente denominado Status Monitor v2): Instruções detalhadas

Este artigo descreve como embarcar na PowerShell Gallery e descarregar o módulo ApplicationMonitor.
Incluem-se os parâmetros mais comuns que terá de começar.
Também fornecemos instruções de descarregamento manual no caso de não ter acesso à Internet.

## <a name="get-an-instrumentation-key"></a>Obtenha uma chave de instrumentação

Para começar, precisa de uma chave de instrumentação. Para obter mais informações, consulte [Criar um recurso 'Insights' de Aplicações.](create-new-resource.md#copy-the-instrumentation-key)

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Executar PowerShell como Administrador com uma política de execução elevada

### <a name="run-as-admin"></a>Corra como Administrador

O PowerShell precisa de permissões ao nível do administrador para escoar alterações no seu computador.
### <a name="execution-policy"></a>Política de execução
- Descrição: Por predefinição, os scripts PowerShell em execução são desativadas. Recomendamos permitir scripts remoteSigned apenas para o âmbito Atual.
- Referência: Sobre políticas de [execução](/powershell/module/microsoft.powershell.core/about/about_execution_policies) e [política de execução de conjuntos](/powershell/module/microsoft.powershell.security/set-executionpolicy).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- Parâmetro opcional:
    - `-Force`. Ignora o pedido de confirmação.

**Erros de exemplo**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

Audite a sua instância de PowerShell executando o `$PSVersionTable` comando.
Este comando produz a seguinte saída:

```output
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

Estas instruções foram escritas e testadas num computador que executa o Windows 10 e nas versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para a PowerShell Gallery

Estes passos irão preparar o seu servidor para descarregar módulos da PowerShell Gallery.

> [!NOTE] 
> A PowerShell Gallery é suportada no Windows 10, Windows Server 2016 e PowerShell 6+.
> Para obter informações sobre versões anteriores, consulte [instalar o PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Executar PowerShell como Administrador com uma política de execução elevada.
2. Instale o fornecedor de pacotes NuGet.
    - Descrição: Você precisa deste fornecedor para interagir com repositórios baseados em NuGet como a PowerShell Gallery.
    - Referência: [Instalar-PacoteProvider](/powershell/module/packagemanagement/install-packageprovider).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Ignora o pedido de confirmação.
    
    Receberá este pedido se o NuGet não estiver configurado:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. Configure a PowerShell Gallery como um repositório de confiança.
    - Descrição: Por defeito, a PowerShell Gallery é um repositório não fidedvo.
    - Referência: [Set-PSRepository](/powershell/module/powershellget/set-psrepository).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - Parâmetro opcional:
        - `-Proxy`. Especifica um servidor proxy para o pedido.

    Receberá este pedido se a PowerShell Gallery não for de confiança:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    Pode confirmar esta alteração e auditar todas as PSRepositórios executando o `Get-PSRepository` comando.

4. Instale a versão mais recente do PowerShellGet.
    - Descrição: Este módulo contém a ferramenta utilizada para obter outros módulos da PowerShell Gallery. Versão 1.0.0.1 navios com Windows 10 e Windows Server. É necessária a versão 1.6.0 ou superior. Para determinar qual a versão instalada, executar o `Get-Command -Module PowerShellGet` comando.
    - Referência: [Instalação do PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet` .
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-Force`. Contorna o aviso "já instalado" e instala a versão mais recente.

    Receberá este erro se não estiver a utilizar a versão mais recente do PowerShellGet:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. Reinicie a PowerShell. Não pode carregar a nova versão na sessão atual. As novas sessões PowerShell irão carregar a versão mais recente do PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Faça o download e instale o módulo através da PowerShell Gallery

Estes passos irão descarregar o módulo Az.ApplicationMonitor da PowerShell Gallery.

1. Certifique-se de que todos os pré-requisitos para a PowerShell Gallery são cumpridos.
2. Executar PowerShell como Administrador com uma política de execução elevada.
3. Instale o módulo Az.ApplicationMonitor.
    - Referência: [Módulo de instalação](/powershell/module/powershellget/install-module).
    - Comando: `Install-Module -Name Az.ApplicationMonitor` .
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para o pedido.
        - `-AllowPrerelease`. Permite a instalação de lançamentos alfa e beta.
        - `-AcceptLicense`. Ignora o pedido de "Aceitar Licença"
        - `-Force`. Ignora o aviso de "Repositório Não Fidedquirício".

## <a name="download-and-install-the-module-manually-offline-option"></a>Faça o download e instale o módulo manualmente (opção offline)

Se, por qualquer motivo, não conseguir ligar-se ao módulo PowerShell, pode descarregar e instalar manualmente o módulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Descarregue manualmente o mais recente ficheiro nupkg

1. Aceda a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecione a versão mais recente do ficheiro na tabela **Histórico da versão.**
3. Nas **opções de instalação**, selecione **Download Manual**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opção 1: Instalar num diretório de módulos PowerShell
Instale o módulo PowerShell descarregado manualmente num diretório PowerShell para que seja detetado pelas sessões PowerShell.
Para obter mais informações, consulte [a instalação de um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Desaperte o nupkg como ficheiro zip utilizando Expand-Archive (v1.0.1.0)

- Descrição: A versão base do Microsoft.PowerShell.Archive (v1.0.1.0) não consegue desapertar ficheiros nupkg. Mude o nome do ficheiro com a extensão .zip.
- Referência: [Expandir-Arquivo](/powershell/module/microsoft.powershell.archive/expand-archive).
- Comando:

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Unzip nupkg utilizando Expand-Archive (v1.1.0.0)

- Descrição: Utilize uma versão atual de Expand-Archive para desapertar ficheiros nupkg sem alterar a extensão.
- Referência: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opção 2: Desapertar e importar nupkg manualmente
Instale o módulo PowerShell descarregado manualmente num diretório PowerShell para que seja detetado pelas sessões PowerShell.
Para obter mais informações, consulte [a instalação de um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Se estiver a instalar o módulo em qualquer outro diretório, importe manualmente o módulo utilizando [o Módulo de Importação](/powershell/module/microsoft.powershell.core/import-module).

> [!IMPORTANT] 
> Os DLLs serão instalados através de caminhos relativos.
> Guarde o conteúdo da embalagem no seu diretório de tempo de execução pretendido e confirme que as permissões de acesso permitem ler, mas não escrever.

1. Altere a extensão para ".zip" e extraa o conteúdo da embalagem para o seu diretório de instalação pretendido.
2. Encontre o caminho do ficheiro Az.ApplicationMonitor.psd1.
3. Executar PowerShell como Administrador com uma política de execução elevada.
4. Carregue o módulo utilizando o `Import-Module Az.ApplicationMonitor.psd1` comando.
    

## <a name="route-traffic-through-a-proxy"></a>Tráfego de rota através de um proxy

Quando monitorizar um computador na sua intranet privada, terá de encaminhar o tráfego HTTP através de um representante.

Os comandos PowerShell para descarregar e instalar Az.ApplicationMonitor da PowerShell Gallery suportam um `-Proxy` parâmetro.
Reveja as instruções anteriores quando escrever os scripts de instalação.

O Application Insights SDK terá de enviar a telemetria da sua aplicação para a Microsoft. Recomendamos que configufique as definições de procuração para a sua aplicação no seu ficheiro web.config. Para obter mais informações, consulte [a Application Insights FAQ: Proxy passthrough](../faq.md#proxy-passthrough).


## <a name="enable-monitoring"></a>Ativar monitorização

Utilize o `Enable-ApplicationInsightsMonitoring` comando para ativar a monitorização.

Consulte a [referência API](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) para obter uma descrição detalhada de como utilizar este cmdlet.



## <a name="next-steps"></a>Passos seguintes

 Ver a telemetria:

- [Explore métricas](../essentials/metrics-charts.md) para monitorizar o desempenho e a utilização.
- [Pesquisar eventos e registos](./diagnostic-search.md) para diagnosticar problemas.
- [Use Analytics](../logs/log-query-overview.md) para consultas mais avançadas.
- [Criar painéis de instrumentos.](./overview-dashboard.md)

 Adicionar mais telemetria:

- [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria ao cliente web](./javascript.md) para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código para](./asp-net.md) que possa inserir rastreios e chamadas de registo.

Faça mais com o Application Insights Agent:

- Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente de Insights de Aplicação.

