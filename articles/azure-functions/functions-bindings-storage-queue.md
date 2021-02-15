---
title: Gatilho de armazenamento de fila Azure e encadernações para visão geral das funções Azure
description: Compreenda como utilizar o gatilho de armazenamento da fila Azure e a ligação de saída em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381482"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Gatilho de armazenamento de fila Azure e encadernações para visão geral das funções Azure

As funções Azure podem funcionar à medida que forem criadas novas mensagens de armazenamento da Fila Azure e podem escrever mensagens de fila dentro de uma função.

| Ação | Tipo |
|---------|---------|
| Executar uma função à medida que os dados de armazenamento de fila mudam | [Acionador](./functions-bindings-storage-queue-trigger.md) |
| Escreva mensagens de armazenamento de fila |[Ligação de saída](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

#### <a name="storage-extension-5x-and-higher"></a>Extensão de armazenamento 5.x e superior

Uma nova versão da extensão de encadernações de armazenamento está disponível como um [pacote NuGet de pré-visualização](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Esta pré-visualização introduz a capacidade de [se conectar usando uma identidade em vez de um segredo](./functions-reference.md#configure-an-identity-based-connection). Para aplicações .NET, também altera os tipos a que se pode ligar, substituindo os tipos de `WindowsAzure.Storage` e por tipos mais recentes de `Microsoft.Azure.Storage` [Azure.Storage.Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> O pacote de pré-visualização não está incluído num pacote de extensão e deve ser instalado manualmente. Para aplicações .NET, adicione uma referência ao pacote. Para todos os outros tipos de aplicações, consulte [Update your extensions].

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualize as suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo *host.jsno* ficheiro abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais nas versões 2.x e não só, consulte [host.jsem referência para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens. Mínimo é 00:00:00.100 (100 ms) e incrementos até 00:01:00 (1 min).  Nas Funções 2.x e superior o tipo de dados é um `TimeSpan` , enquanto na versão 1.x está em milissegundos.|
|visibilidadeTimeout|00:00:00|O intervalo de tempo entre as recauchutagens ao processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens de fila que o tempo de funcionamento das Funções recupera simultaneamente e processa em paralelo. Quando o número que está a ser processado chega ao `newBatchThreshold` , o tempo de funcionamento recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas a serem processadas por função é `batchSize` mais `newBatchThreshold` . Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela das mensagens recebidas numa fila, pode definir `batchSize` para 1. No entanto, esta definição elimina a conusncy desde que a sua aplicação de função seja apenas executado numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de veneno.|
|newBatchThreshold|lotEsize/2|Sempre que o número de mensagens a ser processadas se descientemente a este número, o tempo de funcionamento recupera outro lote.|
|mensagemEncoding|base64| Esta definição só está disponível na [versão de extensão 5.0.0 e superior](#storage-extension-5x-and-higher). Representa o formato de codificação de mensagens. Valores válidos são `base64` e `none` .|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)
- [Escreva mensagens de armazenamento de fila (ligação de saída)](./functions-bindings-storage-queue-output.md)
