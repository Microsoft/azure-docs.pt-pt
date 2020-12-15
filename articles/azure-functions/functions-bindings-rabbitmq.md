---
title: Ligações Azure RabbitMQ para funções azure
description: Aprenda a enviar gatilhos e encadernações Azure RabbitMQ em Funções Azure.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/11/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: cd6c8de0d9290aab273e25403bb1cb2d8fe07e27
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505765"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Ligações RabbitMQ para visão geral das funções azure

> [!NOTE]
> As ligações RabbitMQ só são totalmente suportadas nos planos **do Windows Premium.** O consumo e o Linux não são atualmente apoiados.

As funções Azure integram-se com [o RabbitMQ](https://www.rabbitmq.com/) através [de gatilhos e encadernações](./functions-triggers-bindings.md). As extensões de ligação Azure Functions RabbitMQ permitem-lhe enviar e receber mensagens usando a API RabbitMQ com Funções.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando uma mensagem RabbitMQ vem através da fila | [Acionador](./functions-bindings-rabbitmq-trigger.md) |
| Enviar mensagens RabbitMQ |[Ligação de saída](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

Para começar a desenvolver com esta extensão, certifique-se de que [primeiro configura um ponto final RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Para saber mais sobre o RabbitMQ, confira a página [de início.](https://www.rabbitmq.com/getstarted.html)

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools] com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./functions-bindings-register.md
[Extensão de Ferramentas Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As extensões de ligação RabbitMQ não são suportadas para as funções 1.x. Utilize as funções 2.x ou mais.

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando uma mensagem RabbitMQ é criada (Gatilho)](./functions-bindings-rabbitmq-trigger.md)
- [Enviar mensagens RabbitMQ de Funções Azure (ligação de saída)](./functions-bindings-rabbitmq-output.md)