---
title: O Azure Event Grid de funções depuração local
description: Saiba como depurar localmente as funções do Azure acionadas por um evento do Event Grid
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funções do Azure, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736503"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Função do Azure Event Grid acionador Local depuração

Este artigo demonstra como depurar uma função local que manipula um evento de Azure Event Grid gerado por uma conta de armazenamento. 

## <a name="prerequisites"></a>Pré-requisitos

- Criar ou utilizar uma aplicação de função existente
- Criar ou utilizar uma conta de armazenamento existente
- Baixe [ngrok](https://ngrok.com/) que permita ao Azure chamar sua função local

## <a name="create-a-new-function"></a>Criar uma nova função

Abra a aplicação de funções no Visual Studio e, com o botão direito no nome do projeto no Solution Explorer e clique em **adicionar > nova função do Azure**.

Na *nova função do Azure* janela, selecione **acionador do Event Grid** e clique em **OK**.

![Criar função nova](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Assim que a função é criada, abra o arquivo de código e o URL comentado na parte superior do ficheiro de cópia. Esta localização é utilizada quando configurar o acionador do Event Grid.

![Localização de cópia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Em seguida, defina um ponto de interrupção na linha que começa com `log.LogInformation`.

![Ponto de interrupção do conjunto](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Em seguida, **prima F5** para iniciar uma sessão de depuração.

## <a name="allow-azure-to-call-your-local-function"></a>Que permita ao Azure chama sua função e local

Para dividir numa função que está a ser depurada no seu computador, tem de ativar uma forma para o Azure comunicar com a sua função local a partir da cloud.

O [ngrok](https://ngrok.com/) utilitário oferece uma maneira para o Azure chamar a função em execução no seu computador. Inicie *ngrok* com o seguinte comando:

```bash
ngrok http -host-header=localhost 7071
```
Como o utilitário de configuração, a janela de comando deve ter um aspeto semelhante à captura de ecrã seguinte:

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Copiar o **HTTPS** URL gerada quando *ngrok* é executado. Este valor é utilizado quando configurar o ponto de extremidade de eventos do event grid.

## <a name="add-a-storage-event"></a>Adicionar um evento de armazenamento

Abra o portal do Azure e navegue para uma conta de armazenamento e clique nas **eventos** opção.

![Adicionar evento de conta de armazenamento](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Na *eventos* janela, clique na **subscrição de evento** botão. No *subscrição ainda* janela, clique na *tipo de ponto final* menu pendente e selecione **Hook de Web**.

![Selecione o tipo de subscrição](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Quando o tipo de ponto final estiver configurado, clique em **selecione um ponto de extremidade** para configurar o valor de ponto final.

![Selecione o tipo de ponto final](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

O *ponto final do subscritor* valor é composto de três valores diferentes. O prefixo é o URL HTTPS gerado pelo *ngrok*. O restante do URL vem da URL encontrado no arquivo de código de função, com o nome de função, adicionado ao final. Começando com o URL do arquivo de código de função, o *ngrok* substitui o URL `http://localhost:7071` e a função nome substitui `{functionname}`.

Captura de ecrã seguinte mostra como a URL final deve ser:

![Seleção de ponto final](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Depois de introduzir o valor apropriado, clique em **confirmar seleção**.

> [!IMPORTANT]
> Cada vez que iniciar *ngrok*, o URL HTTPS é novamente gerado e o valor é alterado. Por conseguinte, tem de criar uma nova subscrição de evento sempre que vai expor a sua função para o Azure através de *ngrok*.

## <a name="upload-a-file"></a>Carregar um ficheiro

Agora pode carregar um ficheiro à sua conta de armazenamento para acionar um evento do Event Grid para sua função local para processar. 

Open [Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) e ligue-se para a sua conta de armazenamento. 

- Expanda **contentores de BLOBs** 
- Com o botão direito e selecione **criar contentor de BLOBs**.
- Nome do contentor **testar**
- Selecione o *testar* contentor
- Clique nas **carregar** botão
- Clique em **carregar ficheiros**
- Selecione um ficheiro e carregá-lo para o contentor de BLOBs

## <a name="debug-the-function"></a>Depurar a função

Assim que o Event Grid reconhece que um novo ficheiro é carregado para o contentor de armazenamento, o ponto de interrupção for atingido na sua função local.

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste artigo, eliminar a **testar** contentor na sua conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

- [Utilizar o Event Grid para automatizar o redimensionamento de imagens carregadas](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Acionador do Event Grid para as funções do Azure](./functions-bindings-event-grid.md)
