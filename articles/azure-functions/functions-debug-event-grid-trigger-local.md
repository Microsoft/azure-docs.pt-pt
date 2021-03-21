---
title: Azure Functions Event Grid depuração local
description: Aprenda a depurar localmente funções Azure desencadeadas por um evento de Grade de Eventos
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94833066"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Grelha de evento de função Azure desencadeia depuração local

Este artigo demonstra como depurar uma função local que lida com um evento Azure Event Grid criado por uma conta de armazenamento. 

## <a name="prerequisites"></a>Pré-requisitos

- Criar ou utilizar uma aplicação de função existente
- Criar ou utilizar uma conta de armazenamento existente
- Faça o download [do Ngrok](https://ngrok.com/) para permitir que o Azure ligue para a sua função local

## <a name="create-a-new-function"></a>Criar uma nova função

Abra a sua aplicação de função no Visual Studio e clique com o botão direito no nome do projeto no Solution Explorer e clique em **Adicionar > Nova Função de Azure**.

Na janela *New Azure Function,* selecione o **gatilho da Grelha de Eventos** e clique em **OK**.

![Criar função nova](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Uma vez criada a função, abra o ficheiro de código e copie o URL comentado na parte superior do ficheiro. Esta localização é utilizada ao configurar o gatilho da Grelha de Eventos.

![Localização da cópia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Em seguida, coloque um ponto de rutura na linha que começa com `log.LogInformation` .

![Definir ponto de rutura](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Em seguida, **pressione F5** para iniciar uma sessão de depuragem.

## <a name="allow-azure-to-call-your-local-function"></a>Deixe a Azure ligar para a sua função local

Para entrar numa função que está a ser depurada na sua máquina, tem de permitir uma forma de o Azure comunicar com a sua função local a partir da nuvem.

O utilitário [ngrok](https://ngrok.com/) fornece uma maneira de a Azure chamar a função em funcionamento na sua máquina. Iniciar *o ngrok* utilizando o seguinte comando:

```bash
ngrok http -host-header=localhost 7071
```
À medida que o utilitário é configurado, a janela de comando deve ser semelhante à seguinte imagem:

![Screenshot que mostra o Pedido de Comando depois de iniciar o utilitário "ngrok".](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Copie o **URL HTTPS** gerado quando *o ngrok* é executado. Este valor é utilizado ao configurar o ponto final do evento da grelha de evento.

## <a name="add-a-storage-event"></a>Adicione um evento de armazenamento

Abra o portal Azure e navegue para uma conta de armazenamento e clique na opção **Eventos.**

![Adicionar evento de conta de armazenamento](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Na janela *Eventos,* clique no botão De Assinatura de **Evento.** Na janela *de subscrição* de eventos, clique no *dropdown do tipo endpoint* e selecione **Web Hook**.

![Selecione o tipo de subscrição](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Uma vez configurado o tipo de ponto final, clique em **Selecionar um ponto final** para configurar o valor do ponto final.

![Selecione o tipo de ponto final](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

O valor *do Ponto Final* do Assinante é composto por três valores diferentes. O prefixo é o URL HTTPS gerado pelo *ngrok*. O restante do URL provém do URL encontrado no ficheiro de código de função, com o nome da função adicionado no final. Começando pelo URL a partir do ficheiro de código de função, o URL *ngrok* substitui `http://localhost:7071` e o nome da função substitui `{functionname}` .

A imagem que se segue mostra como deve ser o URL final:

![Seleção de pontos finais](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Uma vez introduzido o valor apropriado, clique em **Confirmar Seleção**.

> [!IMPORTANT]
> Sempre que inicia *o ngrok,* o URL HTTPS é regenerado e o valor muda. Por isso, tem de criar uma nova Subscrição de Eventos sempre que expor a sua função ao Azure via *ngrok*.

## <a name="upload-a-file"></a>Carregar um ficheiro

Agora pode enviar um ficheiro para a sua conta de armazenamento para desencadear um evento de Grade de Eventos para a sua função local. 

Abra [o Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e ligue-se à sua conta de armazenamento. 

- Expandir **recipientes blob** 
- Clique à direita e selecione **Criar o Recipiente Blob**.
- Nomear o **teste** do recipiente
- Selecione o recipiente *de teste*
- Clique no botão **Upload**
- Clique **em Carregar Ficheiros**
- Selecione um ficheiro e carreca-o para o recipiente blob

## <a name="debug-the-function"></a>Depurar a função

Uma vez que a Grelha de Eventos reconheça que um novo ficheiro é enviado para o recipiente de armazenamento, o ponto de rutura é atingido na sua função local.

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados neste artigo, elimine o recipiente **de teste** na sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o redimensionamento de imagens carregadas com o Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Gatilho da grelha de evento para funções Azure](./functions-bindings-event-grid.md)
