---
title: Azure Funções Event Grid depuração local
description: Aprenda a depurar as funções do Azure localmente desencadeadapor um evento da Grelha de Eventos
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227073"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Rede de eventos de função azure gatilho depuração local

Este artigo demonstra como depurar uma função local que trata de um evento azure Event Grid criado por uma conta de armazenamento. 

## <a name="prerequisites"></a>Pré-requisitos

- Criar ou usar uma aplicação de função existente
- Criar ou utilizar uma conta de armazenamento existente
- Descarregue [ngrok](https://ngrok.com/) para permitir que Azure ligue para a sua função local

## <a name="create-a-new-function"></a>Criar uma nova função

Abra a sua aplicação de funções no Estúdio Visual e clique no nome do projeto no Solution Explorer e clique em **Adicionar > Função New Azure**.

Na janela *Função New Azure,* selecione o **gatilho da Grelha de Eventos** e clique em **OK**.

![Criar função nova](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Assim que a função for criada, abra o ficheiro de código e copie o URL comentado na parte superior do ficheiro. Esta localização é utilizada ao configurar o gatilho da Grelha de Eventos.

![Localização de cópia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Em seguida, coloque um ponto de `log.LogInformation`rutura na linha que começa com .

![Definir ponto de rutura](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Em seguida, **pressione F5** para iniciar uma sessão de depuração.

## <a name="allow-azure-to-call-your-local-function"></a>Permita que Azure ligue para a sua função local

Para quebrar uma função que está a ser depurada na sua máquina, deve permitir que o Azure se comunique com a sua função local a partir da nuvem.

O utilitário [ngrok](https://ngrok.com/) fornece uma maneira de azure ligar para a função em funcionamento na sua máquina. Comece *a ngrok* utilizando o seguinte comando:

```bash
ngrok http -host-header=localhost 7071
```
À medida que o utilitário é configurado, a janela de comando deve parecer semelhante à seguinte imagem:

![Comece ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Copie o URL **HTTPS** gerado quando o *ngrok* é executado. Este valor é utilizado ao configurar o ponto final do evento.

## <a name="add-a-storage-event"></a>Adicione um evento de armazenamento

Abra o portal Azure e navegue para uma conta de armazenamento e clique na opção **Eventos.**

![Adicionar evento de conta de armazenamento](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Na janela *Eventos,* clique no botão **de subscrição** do evento. Na janela *Even Subscription,* clique no dropdown do *Tipo Endpoint* e selecione **Web Hook**.

![Selecionar tipo de subscrição](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Uma vez configurado o tipo de ponto final, clique em **Selecionar um ponto final** para configurar o valor final.

![Selecione tipo de ponto final](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

O valor *do Ponto Final* do Assinante é composto por três valores diferentes. O prefixo é o URL HTTPS gerado pela *ngrok*. O restante url provém do URL encontrado no ficheiro código de função, com o nome da função adicionado no final. Começando com o URL do ficheiro de código `http://localhost:7071` de função, `{functionname}`o URL *da ngrok* substitui e o nome da função substitui .

A imagem que se segue mostra como deve ser o URL final:

![Seleção endpoint](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Depois de ter introduzido o valor apropriado, clique em **Confirmar Seleção**.

> [!IMPORTANT]
> Sempre que começa a *ngrok,* o URL HTTPS é regenerado e o valor muda. Por isso, tem de criar uma nova Subscrição de Eventos sempre que expor a sua função ao Azure via *ngrok*.

## <a name="upload-a-file"></a>Carregar um ficheiro

Agora pode enviar um ficheiro para a sua conta de armazenamento para desencadear um evento da Grelha de Eventos para a sua função local. 

Abra o Explorer de [Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e ligue-se à sua conta de armazenamento. 

- Expandir **contentores blob** 
- Clique à direita e selecione **Create Blob Container**.
- Nomeie o **teste** do recipiente
- Selecione o recipiente de *ensaio*
- Clique no botão **upload**
- Clique em **Ficheiros de Upload**
- Selecione um ficheiro e carregue-o para o recipiente blob

## <a name="debug-the-function"></a>Depurar a função

Uma vez que a Grelha de Eventos reconhece que um novo ficheiro é enviado para o recipiente de armazenamento, o ponto de rutura é atingido na sua função local.

![Comece ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste artigo, elimine o recipiente de **ensaio** na sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o redimensionamento de imagens carregadas com o Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Gatilho da Grelha de Eventos para Funções Azure](./functions-bindings-event-grid.md)
