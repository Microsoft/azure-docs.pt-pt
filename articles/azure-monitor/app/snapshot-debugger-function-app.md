---
title: Ative o Snapshot Debugger para aplicações .NET e .NET Core em Azure Functions | Microsoft Docs
description: Ativar o Snapshot Debugger para aplicações .NET e .NET Core em Funções Azure
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: ac25962cac36a149807b67a44b3b88a4f40c954a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211945"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Ativar o Snapshot Debugger para aplicações .NET e .NET Core em Funções Azure

A Snapshot Debugger trabalha atualmente para aplicações ASP.NET e ASP.NET Core que estão a ser executadas em Funções Azure em Planos de Serviço do Windows.

Recomendamos que execute a sua aplicação no nível de serviço Basic ou superior ao utilizar o Snapshot Debugger.

Para a maioria das aplicações, os níveis de serviço Gratuito e Partilhado não têm memória ou espaço de disco suficiente para guardar instantâneos.

## <a name="prerequisites"></a>Pré-requisitos

* [Ativar a monitorização de Insights de Aplicações na sua App de Função](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Ativar Snapshot Debugger

Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para permitir o Snapshot Debugger em outras plataformas suportadas:
* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Tecido de Serviço Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Para ativar o Snapshot Debugger na sua aplicação Função, tem de atualizar o seu `host.json` ficheiro adicionando a propriedade `snapshotConfiguration` conforme definido abaixo e redistribuir a sua função.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

O Snapshot Debugger está pré-instalado como parte do tempo de funcionamento das Funções Azure, que por padrão está desativado.

Uma vez que o Snapshot Debugger está incluído no tempo de funcionamento das Funções Azure, não é necessário adicionar pacotes NuGet extras ou configurações de aplicação.

Tal como referência, para uma simples aplicação Function (.NET Core), abaixo está como vai parecer o `.csproj` `{Your}Function.cs` , e depois `host.json` ativado Snapshot Debugger nele.

Projeto csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Classe de função

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Arquivo de anfitrião

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Ativar snapshot Debugger para outras nuvens

Atualmente, as únicas regiões que necessitam de modificações no ponto final são [o Governo de Azure](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) e [a Azure China.](https://docs.microsoft.com/azure/china/resources-developer-guide)

Abaixo está um exemplo da `host.json` atualização com o ponto final do agente da Cloud do governo dos EUA:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Abaixo estão as sobreposições suportadas do ponto final do agente Snapshot Debugger:

|Propriedade    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|AgenteEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Depurar Snapshot Debugger

Para desativar o Snapshot Debugger na sua aplicação Função, basta atualizar o seu `host.json` ficheiro definindo `false` a `snapshotConfiguration.isEnabled` propriedade.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Recomendamos que tenha snapshot Debugger habilitado em todas as suas aplicações para facilitar o diagnóstico de exceções à aplicação.

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as imagens sejam enviadas para a instância De Insights de Aplicação.
- [Veja as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Personalize a configuração Snapshot Debugger com base no seu caso de utilização na sua aplicação Função. Para obter mais informações, consulte [a configuração do instantâneo em host.jsem](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Para obter ajuda para resolver problemas com o Snapshot Debugger, consulte [snapshot Debugger resolução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).