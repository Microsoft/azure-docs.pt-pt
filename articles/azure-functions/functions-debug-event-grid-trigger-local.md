---
title: Azure Functions depuração local da grade de eventos
description: Saiba como depurar localmente o Azure Functions disparado por um evento de grade de eventos
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: e28abbe8d44094d8599545479f4611a84e9d9bd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085678"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Depuração local do gatilho de grade de eventos do Azure function

Este artigo demonstra como depurar uma função local que manipula um evento de grade de eventos do Azure gerado por uma conta de armazenamento. 

## <a name="prerequisites"></a>Pré-requisitos

- Criar ou usar um aplicativo de funções existente
- Criar ou usar uma conta de armazenamento existente
- Baixe o [ngrok](https://ngrok.com/) para permitir que o Azure chame sua função local

## <a name="create-a-new-function"></a>Criar uma nova função

Abra seu aplicativo de funções no Visual Studio e, em seguida, clique com o botão direito do mouse no nome do projeto na Gerenciador de Soluções e clique em **adicionar > nova função do Azure**.

Na janela *nova função do Azure* , selecione **gatilho de grade de eventos** e clique em **OK**.

![Criar função nova](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Depois que a função for criada, abra o arquivo de código e copie a URL comentada na parte superior do arquivo. Esse local é usado ao configurar o gatilho de grade de eventos.

![Local da cópia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Em seguida, defina um ponto de interrupção na linha que `log.LogInformation`começa com.

![Definir ponto de interrupção](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Em seguida, **pressione F5** para iniciar uma sessão de depuração.

## <a name="allow-azure-to-call-your-local-function"></a>Permitir que o Azure chame sua função local

Para dividir uma função que está sendo depurada em seu computador, você deve habilitar uma maneira para o Azure se comunicar com sua função local da nuvem.

O utilitário [ngrok](https://ngrok.com/) fornece uma maneira para o Azure chamar a função em execução no seu computador. Inicie o *ngrok* usando o seguinte comando:

```bash
ngrok http -host-header=localhost 7071
```
À medida que o utilitário é configurado, a janela de comando deve ser semelhante à captura de tela a seguir:

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Copie a URL **https** gerada quando *ngrok* for executado. Esse valor é usado ao configurar o ponto de extremidade do evento de grade de eventos.

## <a name="add-a-storage-event"></a>Adicionar um evento de armazenamento

Abra o portal do Azure e navegue até uma conta de armazenamento e clique na opção **eventos** .

![Adicionar evento de conta de armazenamento](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Na janela *eventos* , clique no botão **assinatura de evento** . Na janela de *assinatura uniforme* , clique na lista suspensa *tipo de ponto de extremidade* e selecione **gancho da Web**.

![Selecionar tipo de assinatura](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Depois que o tipo de ponto de extremidade for configurado, clique em **selecionar um ponto de extremidade** para configurar o valor do ponto de extremidade.

![Selecionar tipo de ponto de extremidade](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

O valor do *ponto de extremidade* do assinante é composto de três valores diferentes. O prefixo é a URL HTTPS gerada por *ngrok*. O restante da URL é proveniente da URL encontrada no arquivo de código da função, com o nome da função adicionado no final. Começando com a URL do arquivo de código de função, a URL ngrok `http://localhost:7071` substitui e o nome da `{functionname}`função é substituído.

A captura de tela a seguir mostra como a URL final deve ser a seguinte:

![Seleção de ponto de extremidade](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Depois de inserir o valor apropriado, clique em **confirmar seleção**.

> [!IMPORTANT]
> Toda vez que você inicia o *ngrok*, a URL https é regenerada e o valor é alterado. Portanto, você deve criar uma nova assinatura de evento sempre que você expor sua função para o Azure por meio do *ngrok*.

## <a name="upload-a-file"></a>Carregar um ficheiro

Agora você pode carregar um arquivo em sua conta de armazenamento para disparar um evento de grade de eventos para a função local manipular. 

Abra [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se à sua conta de armazenamento. 

- Expandir **contêineres de blob** 
- Clique com o botão direito do mouse e selecione **criar contêiner de blob**.
- Nomear o **teste** do contêiner
- Selecione o contêiner de *teste*
- Clique no botão **carregar**
- Clique em **carregar arquivos**
- Selecionar um arquivo e carregá-lo no contêiner de BLOBs

## <a name="debug-the-function"></a>Depurar a função

Quando a grade de eventos reconhece que um novo arquivo é carregado no contêiner de armazenamento, o ponto de interrupção é atingido na função local.

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste artigo, exclua o contêiner de **teste** em sua conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

- [Utilizar o Event Grid para automatizar o redimensionamento de imagens carregadas](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Gatilho de grade de eventos para Azure Functions](./functions-bindings-event-grid.md)
