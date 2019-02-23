---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: dc527a4e1bdf9648ddfc9f582b0c146197214f26
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741154"
---
O `Logging` suporte de registo do ASP.NET Core para o contentor de gerir as definições. Pode usar as mesmas definições de configuração e os valores para o contentor que utiliza para uma aplicação ASP.NET Core. 

Suporta os seguintes fornecedores de registo do contentor:

|Fornecedor|Objetivo|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O ASP.NET Core `Console` provedor de log. Todas as definições de configuração do ASP.NET Core e valores predefinidos para este fornecedor de registo são suportadas.|
|[Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O ASP.NET Core `Debug` provedor de log. Todas as definições de configuração do ASP.NET Core e valores predefinidos para este fornecedor de registo são suportadas.|
|[Disco](#disk-logging)|O fornecedor de registo JSON. Este fornecedor de registo escreve dados de registo para a montagem de saída.|

### <a name="disk-logging"></a>Registo de disco

O `Disk` provedor de log suporta as seguintes definições de configuração:  

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Format` | Cadeia | O formato de saída para ficheiros de registo.<br/> **Nota:** Este valor tem de ser definido como `json` para ativar o fornecedor de registo. Se este valor for especificado sem também especificar uma montagem de saída ao instanciar um contentor, ocorrerá um erro. |
| `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um ficheiro de registo. Quando o tamanho do arquivo de log atual atende ou excede este valor, um novo ficheiro de registo é iniciado pelo fornecedor de registo. Se não for especificado -1, o tamanho do ficheiro de registo está limitado apenas pelo tamanho máximo do ficheiro, se existir, para a montagem de saída. O valor predefinido é 1. |

Para obter mais informações sobre como configurar o suporte de registo do ASP.NET Core, consulte [configuração das definições do ficheiro](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
