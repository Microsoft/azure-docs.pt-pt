---
title: O tamanho da pasta TEMP padrão é muito pequeno para uma função | Microsoft Docs
description: Uma função de serviço em nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743207"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>O tamanho da pasta TEMP padrão é muito pequeno em um papel web/trabalhador de serviço de nuvem (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

O diretório temporário padrão de um trabalhador de serviço de nuvem ou função web tem um tamanho máximo de 100 MB, que pode ficar cheio em algum momento. Este artigo descreve como evitar ficar sem espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que estou sem espaço?
As variáveis de ambiente Windows standard TEMP e TMP estão disponíveis para código que está em execução na sua aplicação. Tanto o TEMP como o TMP apontam para um único diretório que tem um tamanho máximo de 100 MB. Os dados armazenados neste diretório não são persistidos em todo o ciclo de vida do serviço em nuvem; se as instâncias de papel num serviço de nuvem forem recicladas, o diretório é limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implementar uma das seguintes alternativas:

* Configure um recurso de armazenamento local e aceda-o diretamente em vez de utilizar TEMP ou TMP. Para aceder a um recurso de armazenamento local a partir de código que está em execução dentro da sua aplicação, ligue para o método [RoleEnvironment.GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))
* Configure um recurso de armazenamento local e aponte os diretórios TEMP e TMP para apontar para o caminho do recurso de armazenamento local. Esta modificação deve ser realizada dentro do método [RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

O exemplo de código a seguir mostra como modificar os directórios-alvo para TEMP e TMP de dentro do método OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Leia um blog que descreve [Como Aumentar o tamanho da Azure Web Role ASP.NET Pasta Temporária](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Veja mais [artigos de resolução de problemas](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) para serviços na nuvem.

Para aprender a resolver problemas de problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).