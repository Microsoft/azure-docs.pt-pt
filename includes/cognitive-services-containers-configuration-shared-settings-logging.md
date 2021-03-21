---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001203"
---
As `Logging` definições gerem ASP.NET suporte de registo do Núcleo para o seu recipiente. Pode utilizar as mesmas definições e valores de configuração para o seu recipiente que utiliza para uma aplicação Core ASP.NET. 

Os seguintes prestadores de serviços de exploração madeireira são suportados pelo contentor:

|Fornecedor|Objetivo|
|--|--|
|[Consola](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O fornecedor de registo de `Console` ASP.NET Core. Todas as definições de configuração do núcleo ASP.NET e os valores predefinidos para este fornecedor de registos são suportados.|
|[Depurar](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O fornecedor de registo de `Debug` ASP.NET Core. Todas as definições de configuração do núcleo ASP.NET e os valores predefinidos para este fornecedor de registos são suportados.|
|[Disco](#disk-logging)|O provedor de registo JSON. Este fornecedor de registos escreve dados de registo para o suporte de saída.|

Este comando de contentor armazena informações no formato JSON para o suporte de saída:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Este comando do contentor mostra informações de depurar, prefixadas `dbug` com, enquanto o contentor está em funcionamento:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Registo de discos

O `Disk` fornecedor de registos suporta as seguintes definições de configuração:

| Name | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Format` | String | O formato de saída para ficheiros de registo.<br/> **Nota:** Este valor deve ser definido `json` para permitir o fornecedor de registos. Se este valor for especificado sem especificar também um suporte de saída durante a instantânea de um recipiente, ocorre um erro. |
| `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um ficheiro de log. Quando o tamanho do ficheiro de registo atual se encontra ou excede este valor, um novo ficheiro de registo é iniciado pelo fornecedor de registo. Se for especificado -1, o tamanho do ficheiro de registo é limitado apenas pelo tamanho máximo do ficheiro, se houver, para o suporte de saída. O valor predefinido é 1. |

Para obter mais informações sobre a configuração ASP.NET suporte de registo do núcleo, consulte [a configuração do ficheiro Definições](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).