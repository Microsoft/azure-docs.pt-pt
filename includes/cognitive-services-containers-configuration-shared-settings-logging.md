---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320534"
---
As `Logging` definições gerem ASP.NET suporte de registo core para o seu recipiente. Pode utilizar as mesmas definições e valores de configuração para o seu recipiente que utiliza para uma aplicação core ASP.NET. 

Os seguintes fornecedores de exploração madeireira são apoiados pelo contentor:

|Fornecedor|Objetivo|
|--|--|
|[Consola](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O fornecedor `Console` de exploração madeireira ASP.NET Core. Todas as configurações de configuração ASP.NET Core e valores predefinidos para este fornecedor de registo são suportados.|
|[Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O fornecedor `Debug` de exploração madeireira ASP.NET Core. Todas as configurações de configuração ASP.NET Core e valores predefinidos para este fornecedor de registo são suportados.|
|[Disk](#disk-logging)|O fornecedor de registo sondo JSON. Este fornecedor de registo escreve dados de registo para o suporte de saída.|

Este comando de contentorarma armazena informações de registo no formato JSON para o suporte de saída:

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

Este comando de contentores mostra informações de depuração, pré-fixadas com, `dbug`enquanto o recipiente está em funcionamento:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Exploração de discos

O `Disk` fornecedor de registo suporta as seguintes definições de configuração:

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Format` | String | O formato de saída para ficheiros de registo.<br/> **Nota:** Este valor deve `json` ser definido para permitir o prestador de registos. Se este valor for especificado sem especificar também um suporte de saída enquanto se instantaneamente um recipiente, ocorre um erro. |
| `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um ficheiro de registo. Quando o tamanho do ficheiro de registo atual se encontra ou excede este valor, um novo ficheiro de registo é iniciado pelo fornecedor de registo. Se -1 for especificado, o tamanho do ficheiro de registo é limitado apenas pelo tamanho máximo do ficheiro, se houver, para o suporte de saída. O valor predefinido é 1. |

Para obter mais informações sobre a configuração ASP.NET suporte de registo core, consulte a configuração do [ficheiro Definições](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

