---
title: Conectar-se ao dropbox
description: Automatizar tarefas e fluxos de trabalho que carregam e gerenciam arquivos no Dropbox usando o aplicativo lógico do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665756"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Carregar e gerenciar arquivos no Dropbox usando o aplicativo lógico do Azure

Com o conector do Dropbox e os aplicativos lógicos do Azure, você pode criar fluxos de trabalho automatizados que carregam e gerenciam arquivos em sua conta do dropbox. 

Este artigo mostra como se conectar ao dropbox de seu aplicativo lógico e, em seguida, adicionar o gatilho **quando um arquivo é criado** e o **conteúdo do Dropbox Get file usando** a ação Path.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta do Dropbox](https://www.dropbox.com/), que você pode assinar gratuitamente. Suas credenciais de conta são necessárias para criar uma conexão entre seu aplicativo lógico e sua conta do dropbox.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para este exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Na caixa de pesquisa, escolha **tudo**. Na caixa de pesquisa, digite "Dropbox" como filtro.
Na lista de gatilhos, selecione este gatilho: **quando um arquivo é criado**

   ![Selecionar gatilho do Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para aplicativos lógicos do Azure.

1. Forneça as informações necessárias para seu gatilho. 

   Neste exemplo, selecione a pasta onde você deseja acompanhar a criação do arquivo. Para procurar suas pastas, escolha o ícone de pasta ao lado da caixa **pasta** .

## <a name="add-action"></a>Adicionar ação

Agora, adicione uma ação que obtém o conteúdo de qualquer arquivo novo.

1. No gatilho, escolha a **próxima etapa**. 

1. Na caixa de pesquisa, escolha **tudo**. Na caixa de pesquisa, digite "Dropbox" como filtro.
Na lista ações, selecione esta ação: **obter o conteúdo do arquivo usando o caminho**

1. Se você ainda não tiver autorizado o aplicativo lógico do Azure para acessar o Dropbox, autorize o acesso agora.

1. Para procurar o caminho do arquivo que você deseja usar, ao lado da caixa **caminho do arquivo** , escolha o botão de reticências ( **...** ). 

   Você também pode clicar dentro da caixa **caminho do arquivo** e, na lista conteúdo dinâmico, selecionar **caminho do arquivo**, cujo valor está disponível como saída do gatilho adicionado na seção anterior.

1. Quando tiver terminado, salve seu aplicativo lógico.

1. Para disparar seu aplicativo lógico, crie um novo arquivo no dropbox.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/dropbox/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
