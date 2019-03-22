---
title: Ligue ao Dropbox - Azure Logic Apps
description: Carregar e gerir ficheiros com as APIs REST do Dropbox e o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314421"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Carregar e gerir ficheiros no Dropbox utilizando o Azure Logic Apps

Com o conector da Dropbox e o Azure Logic Apps, pode criar fluxos de trabalho automatizados que carregar e gerir os ficheiros na conta da Dropbox. 

Este artigo mostra como ligar a Dropbox da sua aplicação lógica e, em seguida, adicione a Dropbox **quando é criado um ficheiro** acionador e o Dropbox **obter conteúdo do ficheiro através do caminho** ação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* R [conta do Dropbox](https://www.dropbox.com/), que pode inscrever-se gratuitamente. As credenciais da conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta do Dropbox.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Na caixa de pesquisa, escolha **todos os**. Na caixa de pesquisa, introduza "dropbox" como o filtro.
Na lista de disparadores, selecione este acionador: **Quando é criado um ficheiro**

   ![Selecione o acionador do Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Inicie sessão com as credenciais da conta Dropbox e autorizar o acesso aos seus dados do Dropbox para o Azure Logic Apps.

1. Indique as informações necessárias para o acionador. 

   Neste exemplo, selecione a pasta onde pretende controlar a criação do ficheiro. Para procurar as pastas, escolha o ícone de pasta junto a **pasta** caixa.

## <a name="add-action"></a>Adicionar ação

Agora, adicione uma ação que obtém o conteúdo de qualquer novo ficheiro.

1. No acionador, escolha **passo seguinte**. 

1. Na caixa de pesquisa, escolha **todos os**. Na caixa de pesquisa, introduza "dropbox" como o filtro.
Na lista de ações, selecione a ação: **Obter conteúdo do ficheiro através do caminho**

1. Se já ainda não tem autorização do Azure Logic Apps para acessar o Dropbox, agora a autorizar o acesso.

1. Para navegar para o caminho do ficheiro que pretende utilizar, junto a **caminho do ficheiro** caixa, selecione as reticências (**...** ) botão. 

   Também pode clicar no interior da **caminho do ficheiro** caixa e, na lista de conteúdo dinâmico, selecione **caminho do ficheiro**, cujo valor está disponível como resultado do acionador adicionada na secção anterior.

1. Quando tiver terminado, guarde a aplicação lógica.

1. Para acionar a sua aplicação lógica, crie um novo ficheiro na Dropbox.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/dropbox/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
