---
title: Tamanho da pasta TEMP predefinida é demasiado pequeno para uma função | Documentos da Microsoft
description: Uma função de serviço na nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar a execução sem espaço.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7862e4d5c4dd603dacf5784df6c4194392ebc351
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918200"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Tamanho da pasta TEMP predefinida é demasiado pequeno numa função de web/de trabalho de serviço na nuvem
O diretório temporário do padrão de uma função de trabalho ou web de serviço do cloud tem um tamanho máximo de 100 MB, as quais podem se tornar total em algum momento. Este artigo descreve como evitar ficar sem espaço para o diretório temporário de.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que ficar sem espaço?
As padrão Windows as variáveis de ambiente TEMP e TMP estão disponíveis para código que está a ser executado em seu aplicativo. Do ponto de TEMP e TMP para um diretório único que tem um tamanho máximo de 100 MB. Todos os dados que são armazenados neste diretório não são mantidos durante o ciclo de vida do serviço em nuvem; Se as instâncias de função num serviço cloud sejam recicladas, o diretório é limpa.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implemente uma das seguintes alternativas:

* Configurar um recurso de armazenamento local e acessá-la diretamente em vez de utilizar TEMP ou TMP. Para aceder a um recurso de armazenamento local a partir do código que está a executar no seu aplicativo, chame o [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) método.
* Configurar um recurso de armazenamento local e aponte os diretórios TEMP e TMP para apontar para o caminho do recurso de armazenamento local. Essa modificação deve ser efetuada dentro de [Roleentrypoint](/previous-versions/azure/reference/ee772851(v=azure.100)) método.

O exemplo de código seguinte mostra como modificar os diretórios de destino para TEMP e TMP de dentro do método de OnStart:

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

## <a name="next-steps"></a>Passos Seguintes
Ler um blog que descreve [como aumentar o tamanho do Azure Web função pasta temporária do ASP.NET](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Ver mais [artigos de resolução de problemas](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) para serviços em nuvem.

Para saber como resolver problemas de função do serviço cloud, utilizando dados de diagnóstico do computador de PaaS do Azure, veja [série de blogue de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
