---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116743"
---
O `Logging` suporte de registo do ASP.NET Core para o contentor de gerir as definições. Pode usar as mesmas definições de configuração e os valores para o contentor que utiliza para uma aplicação ASP.NET Core. 

Suporta os seguintes fornecedores de registo do contentor:

|Fornecedor|Objetivo|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O ASP.NET Core `Console` provedor de log. Todas as definições de configuração do ASP.NET Core e valores predefinidos para este fornecedor de registo são suportadas.|
|[Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O ASP.NET Core `Debug` provedor de log. Todas as definições de configuração do ASP.NET Core e valores predefinidos para este fornecedor de registo são suportadas.|
|[Disco](#disk-logging)|O fornecedor de registo JSON. Este fornecedor de registo escreve dados de registo para a montagem de saída.|

Este comando de contentor armazena informações de registo no formato JSON para a montagem de saída:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Este comando de contentor mostra informações de depuração, o prefixo `dbug`, enquanto o contentor está em execução:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Registo de disco

O `Disk` provedor de log suporta as seguintes definições de configuração:  

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Format` | Cadeia | O formato de saída para ficheiros de registo.<br/> **Nota:** Este valor tem de ser definido como `json` para ativar o fornecedor de registo. Se este valor for especificado sem também especificar uma montagem de saída ao instanciar um contentor, ocorrerá um erro. |
| `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um ficheiro de registo. Quando o tamanho do arquivo de log atual atende ou excede este valor, um novo ficheiro de registo é iniciado pelo fornecedor de registo. Se não for especificado -1, o tamanho do ficheiro de registo está limitado apenas pelo tamanho máximo do ficheiro, se existir, para a montagem de saída. O valor predefinido é 1. |

Para obter mais informações sobre como configurar o suporte de registo do ASP.NET Core, consulte [configuração das definições do ficheiro](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

