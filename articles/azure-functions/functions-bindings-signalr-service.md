---
title: Ligações do serviço de sinalização de funções Azure
description: Compreenda como utilizar as ligações do Serviço SignalR com funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523041"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este conjunto de artigos explica como autenticar e enviar mensagens em tempo real aos clientes ligados ao [Serviço De Sinalização Azure](https://azure.microsoft.com/services/signalr-service/) utilizando encadernações do Serviço SignalR em Funções Azure. As Funções do Azure suportam associações de entrada e saída para o SignalR Service.

| Ação | Tipo |
|---------|---------|
| Devolva o URL final do serviço e acesse de acesso | [Encadernação de entrada](./functions-bindings-signalr-service-input.md) |
| Enviar mensagens de serviço signalr |[Encadernação de saída](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [extensão Azure Tools] para utilizar com o Código do Estúdio Visual. |
| C# Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão de ferramentas azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Para mais informações sobre como configurar e utilizar o Serviço SignalR e as Funções Azure em conjunto, consulte o desenvolvimento e configuração das [Funções Azure com o Serviço De Sinalização Azure](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteca de Anotações (apenas Java)

Para utilizar as anotações do Serviço SignalR nas funções java, é necessário adicionar uma dependência ao artefacto de *sinalizador de funções azure-java-biblioteca* (versão 1.0 ou superior) ao seu ficheiro *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Passos seguintes

- [Devolva o URL final do serviço e acesse (encadernação de entrada)](./functions-bindings-signalr-service-input.md)
- [Enviar mensagens de serviço SignalR (encadernação de saída)](./functions-bindings-signalr-service-output.md) 
