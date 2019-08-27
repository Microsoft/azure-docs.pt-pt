---
title: Conectar-se ao armazenamento de BLOBs do Azure-aplicativo lógico do Azure
description: Criar e gerenciar BLOBs no armazenamento do Azure com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d57ea1a881980203b1c8f216239b27b64f0d71cd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051051"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Criar e gerenciar BLOBs no armazenamento de BLOBs do Azure com aplicativos lógicos do Azure

Este artigo mostra como você pode acessar e gerenciar arquivos armazenados como BLOBs em sua conta de armazenamento do Azure de dentro de um aplicativo lógico com o conector de armazenamento de BLOBs do Azure. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos. Por exemplo, você pode criar aplicativos lógicos que criam, obtêm, atualizam e excluem arquivos em sua conta de armazenamento.

Suponha que você tenha uma ferramenta que é atualizada em um site do Azure. que atua como o gatilho para seu aplicativo lógico. Quando esse evento acontece, você pode fazer com que seu aplicativo lógico atualize algum arquivo em seu contêiner de armazenamento de BLOBs, que é uma ação em seu aplicativo lógico.

> [!NOTE]
> Os aplicativos lógicos não dão suporte à conexão direta com contas de armazenamento do Azure por meio de firewalls. Para acessar essas contas de armazenamento, use qualquer opção aqui:
>
> * Crie um [ambiente de serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar a recursos em uma rede virtual do Azure.
>
> * Se você já usa o gerenciamento de API, pode usar esse serviço para este cenário. Para obter mais informações, consulte [arquitetura de integração corporativa simples](https://aka.ms/aisarch).

Se você for novo em aplicativos lógicos, examine [o que é o](../logic-apps/logic-apps-overview.md) início [rápido e aplicativos lógicos do Azure: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico. Para obter informações técnicas específicas do conector, consulte a [referência do conector de armazenamento de BLOBs do Azure](/connectors/azureblobconnector/).

## <a name="limits"></a>Limites

* Por padrão, as ações do armazenamento de BLOBs do Azure podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, mas até 1024 MB, as ações do armazenamento de BLOBs do Azure dão suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do blob** usa implicitamente o agrupamento.

* Os gatilhos do armazenamento de BLOBs do Azure não dão suporte ao agrupamento. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos com mais de 50 MB, siga este padrão:

  * Use um gatilho de armazenamento de BLOBs do Azure que retorne Propriedades de arquivo, como **quando um blob é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação **obter conteúdo de blob** do armazenamento de BLOBs do Azure, que lê o arquivo completo e usa implicitamente o agrupamento.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de armazenamento do Azure e um contêiner de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* O aplicativo lógico em que você precisa ter acesso à sua conta de armazenamento de BLOBs do Azure. Para iniciar seu aplicativo lógico com um gatilho do armazenamento de BLOBs do Azure, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar gatilho de armazenamento de BLOBs

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico com o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** quando as propriedades de um blob são adicionadas ou atualizadas no seu contêiner de armazenamento.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "blob do Azure" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa este gatilho: **Quando um blob é adicionado ou modificado (somente Propriedades)**

   ![Selecionar acionador](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de armazenamento de BLOBs agora](#create-connection). Ou, se sua conexão já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, selecione o contêiner e a pasta que você deseja monitorar.

   1. Na caixa **contêiner** , selecione o ícone de pasta.

   2. Na lista de pastas, escolha o colchete angular direito ( **>** ) e, em seguida, navegue até encontrar e selecione a pasta desejada.

      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência para a frequência com que você deseja que o gatilho Verifique a pasta em busca de alterações.

4. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

5. Agora, continue adicionando uma ou mais ações ao seu aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de BLOBs

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com o [gatilho](../connectors/connectors-native-recurrence.md)de recorrência.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. No designer do aplicativo lógico, no gatilho ou na ação, escolha **nova etapa**.

   ![Adicionar uma ação](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. Escolha o sinal de adição **+** () que aparece e selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "blob do Azure" como seu filtro. Na lista ações, selecione a ação desejada.

   Este exemplo usa esta ação: **Obter conteúdo do blob**

   ![Selecionar ação](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de armazenamento de BLOBs do Azure agora](#create-connection).
Ou, se sua conexão já existir, forneça as informações necessárias para a ação.

   Para este exemplo, selecione o arquivo desejado.

   1. Na caixa **blob** , selecione o ícone de pasta.
  
      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Localize e selecione o arquivo desejado com base no número de **ID** do blob. Você pode encontrar esse número de **ID** nos metadados do blob que são retornados pelo gatilho de armazenamento de blob descrito anteriormente.

5. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.
Para testar seu aplicativo lógico, verifique se a pasta selecionada contém um blob.

Este exemplo só Obtém o conteúdo de um blob. Para exibir o conteúdo, adicione outra ação que cria um arquivo com o blob usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um arquivo com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar-se à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de API aberta do conector (anteriormente, Swagger), consulte a [página de referência do conector](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)
