---
title: O tamanho da pasta TEMP padrão é muito pequeno para um papel Microsoft Docs
description: Uma função de serviço em nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 77c4f627668b91c4bc21ad73b3511c1775943229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460206"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>O tamanho da pasta TEMP padrão é muito pequeno numa função web/trabalhador de serviço de nuvem
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
Leia um blog que descreve [Como Aumentar o tamanho da Azure Web Role ASP.NET Pasta Temporária](https://docs.microsoft.com/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Veja mais [artigos de resolução de problemas](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) para serviços na nuvem.

Para aprender a resolver problemas de problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
