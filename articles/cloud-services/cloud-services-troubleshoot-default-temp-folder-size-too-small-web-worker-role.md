---
title: O tamanho padrão da pasta TEMP é muito pequeno para uma função | Microsoft Docs
description: Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
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
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154986"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>O tamanho padrão da pasta TEMP é muito pequeno em uma função Web/de trabalho do serviço de nuvem
O diretório temporário padrão de um trabalho de serviço de nuvem ou função Web tem um tamanho máximo de 100 MB, que pode ficar cheio em algum momento. Este artigo descreve como evitar a execução sem espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que posso ficar sem espaço?
As variáveis de ambiente padrão do Windows TEMP e TMP estão disponíveis para o código em execução no seu aplicativo. TEMP e TMP apontam para um único diretório que tem um tamanho máximo de 100 MB. Todos os dados armazenados nesse diretório não são persistidos durante o ciclo de vida do serviço de nuvem; Se as instâncias de função em um serviço de nuvem forem recicladas, o diretório será limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implemente uma das seguintes alternativas:

* Configure um recurso de armazenamento local e acesse-o diretamente em vez de usar TEMP ou TMP. Para acessar um recurso de armazenamento local do código que está sendo executado em seu aplicativo, chame o método [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Configure um recurso de armazenamento local e aponte os diretórios TEMPORÁRIOs e TMP para apontarem para o caminho do recurso de armazenamento local. Essa modificação deve ser executada no método [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

O exemplo de código a seguir mostra como modificar os diretórios de destino para TEMP e TMP de dentro do método OnStart:

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
Leia um blog que descreve [como aumentar o tamanho da pasta ASP.NET temporária da função Web do Azure](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Veja mais [artigos de solução de problemas](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico de computador de PaaS do Azure, veja a [série de Blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
