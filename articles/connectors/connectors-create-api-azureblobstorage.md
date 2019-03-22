---
title: Ligar ao armazenamento de Blobs do Azure - Azure Logic Apps
description: Criar e gerir os blobs no armazenamento do Azure com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: ea3e97db9ec560306788943d92a7670025f38bdc
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310375"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Criar e gerir os blobs no armazenamento de Blobs do Azure com o Azure Logic Apps

Este artigo mostra como pode aceder e gerir ficheiros armazenados como blobs na sua conta de armazenamento do Azure de dentro de uma aplicação lógica com o conector do armazenamento de Blobs do Azure. Dessa forma, pode criar aplicações lógicas que automatizam as tarefas e fluxos de trabalho para gerir os seus ficheiros. Por exemplo, pode criar aplicações lógicas que criarem, obterem, atualizar e eliminar ficheiros na sua conta de armazenamento.

Suponha que tenha uma ferramenta que é atualizada a um site do Azure. que atua como o acionador da aplicação lógica. Quando ocorre este evento, pode ter a sua aplicação lógica atualizar alguns ficheiros no contentor de armazenamento de blob, que é uma ação na sua aplicação lógica.

> [!NOTE]
> O Logic Apps não suporta a ligação direta para contas de armazenamento do Azure através de firewalls. Para aceder a estas contas de armazenamento, utilize uma das opções aqui:
>
> * Criar uma [ambiente do serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode ligar aos recursos numa rede virtual do Azure.
>
> * Se já utilizar a gestão de API, pode utilizar este serviço para este cenário. Para mais informações, veja [arquitetura de integração empresarial simples](https://aka.ms/aisarch).

Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">referência do conector do armazenamento de Blobs do Azure</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Um [conta de armazenamento do Azure e um contentor de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* A aplicação lógica em que precisa de acesso à sua conta de armazenamento de Blobs do Azure. Para começar a sua aplicação lógica com um acionador do armazenamento de Blobs do Azure, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar o acionador do armazenamento de BLOBs

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode começar a um fluxo de trabalho de aplicação lógica com o **armazenamento de Blobs do Azure - quando um blob é adicionado ou modificado (propriedades apenas)** acionador quando propriedades de um blob é adicionado ou atualizado no seu contentor de armazenamento. 

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. Este exemplo utiliza o portal do Azure.

2. Na caixa de pesquisa, introduza "BLOBs do azure" como o filtro. Na lista de disparadores, selecione o acionador que pretende.

   Este exemplo utiliza este acionador: **Armazenamento de Blobs do Azure - quando um blob é adicionado ou modificado (propriedades apenas)**

   ![Selecionar acionador](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de armazenamento de BLOBs agora](#create-connection). Em alternativa, se a ligação já existir, fornecer as informações necessárias para o acionador.

   Neste exemplo, selecione o contentor e a pasta que pretende monitorizar.

   1. Na **contentor** caixa, selecione o ícone de pasta.

   2. Na lista de pastas, escolha o colchete de ângulo direito ( **>** ) e, em seguida, navegue até que encontre e selecione a pasta que pretende.

      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência para a frequência com que pretende que o acionador para verificar a pasta para que as alterações.

4. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

5. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados de Acionador.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de BLOBs

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Neste exemplo, a aplicação lógica começa com o [acionador de periodicidade](../connectors/connectors-native-recurrence.md).

1. No portal do Azure ou Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. Este exemplo utiliza o portal do Azure.

2. No Estruturador da aplicação lógica, sob o acionador ou ação, escolha **novo passo** > **adicionar uma ação**.

   ![Adicionar uma ação](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Para adicionar uma ação entre os passos existentes, mova o rato por cima da seta de ligação. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, escolha **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "BLOBs do azure" como o filtro. Na lista de ações, selecione a ação que pretende.

   Este exemplo utiliza esta ação: **Armazenamento de Blobs do Azure - obter conteúdo do blob**

   ![Ação de seleção](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de armazenamento de Blobs do Azure agora](#create-connection). Em alternativa, se a ligação já existir, fornecer as informações necessárias para a ação.

   Neste exemplo, selecione o ficheiro que pretende.

   1. Partir do **BLOBs** caixa, selecione o ícone de pasta.
  
      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Localize e selecione o ficheiro que pretende com base no blob **Id** número. Pode encontrar isto **Id** número nos metadados do blob que é devolvido pelo acionador do armazenamento de BLOBs descrito anteriormente.

5. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.
Para testar a aplicação lógica, certifique-se de que a pasta selecionada contém um blob.

Neste exemplo obtém apenas o conteúdo para um blob. Para ver os conteúdos, adicione outra ação que cria um ficheiro com o blob com outro conector. Por exemplo, adicione uma ação do OneDrive que cria um ficheiro com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ligar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito de API o conector aberta (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/azureblobconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
