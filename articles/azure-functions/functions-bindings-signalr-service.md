---
title: Ligações do serviço signalR de funções Azure Functions
description: Compreenda como utilizar as ligações do Serviço SignalR com funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763494"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este conjunto de artigos explica como autenticar e enviar mensagens em tempo real a clientes ligados ao [Serviço Azure SignalR](https://azure.microsoft.com/services/signalr-service/) utilizando ligações do Serviço SignalR em Funções Azure. As Funções do Azure suportam associações de entrada e saída para o SignalR Service.

| Ação | Tipo |
|---------|---------|
| Lidar com mensagens do Serviço SignalR | [Ligação do gatilho](./functions-bindings-signalr-service-trigger.md) |
| Devolva o URL do ponto final de serviço e o token de acesso | [Encadernação de entrada](./functions-bindings-signalr-service-input.md) |
| Enviar mensagens de Serviço SignalR |[Ligação de saída](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools] com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./functions-bindings-register.md
[Extensão de Ferramentas Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Para obter mais informações sobre como configurar e utilizar o Serviço SignalR e as Funções Azure em conjunto, consulte o [desenvolvimento e configuração de Funções Azure com o Serviço Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteca de anotações (apenas Java)

Para utilizar as anotações do Serviço SignalR nas funções de Java, é necessário adicionar uma dependência ao artefacto *azure-funs-java-library signalr* (versão 1.0 ou superior) ao ficheiro *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Passos seguintes

- [Manuseie as mensagens do Serviço SignalR (ligação do gatilho)](./functions-bindings-signalr-service-trigger.md)
- [Devolva o URL do ponto final de serviço e o token de acesso (Ligação de entrada)](./functions-bindings-signalr-service-input.md)
- [Enviar mensagens de serviço SignalR (ligação de saída)](./functions-bindings-signalr-service-output.md)