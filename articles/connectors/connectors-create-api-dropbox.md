---
title: Ligue-se ao Dropbox
description: Automatizar tarefas e fluxos de trabalho que carregam e gerem ficheiros no Dropbox utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665756"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Faça upload e gerencie ficheiros no Dropbox utilizando apps Azure Logic

Com o conector Dropbox e as Aplicações Lógicas Azure, pode criar fluxos de trabalho automatizados que carregam e gerem ficheiros na sua conta Dropbox. 

Este artigo mostra como ligar ao Dropbox a partir da sua aplicação lógica e, em seguida, adicionar o Dropbox **Quando um ficheiro é criado** gatilho e o **dropbox obter conteúdo de ficheiro usando** a ação de caminho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta Dropbox,](https://www.dropbox.com/)que pode inscrever-se gratuitamente. As credenciais da sua conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta Dropbox.

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para este exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Debaixo da caixa de pesquisa, escolha **Todos.** Na caixa de pesquisa, introduza a "dropbox" como filtro.
A partir da lista de gatilhos, selecione este gatilho: **Quando um ficheiro é criado**

   ![Selecione o gatilho Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Inscreva-se com as suas credenciais de conta Dropbox e autorize o acesso aos seus dados dropbox para Apps Azure Logic.

1. Forneça as informações necessárias para o seu gatilho. 

   Neste exemplo, selecione a pasta onde pretende rastrear a criação de ficheiros. Para navegar nas pastas, escolha o ícone da pasta ao lado da caixa **pasta.**

## <a name="add-action"></a>Adicionar ação

Agora adicione uma ação que obtenha o conteúdo de qualquer novo ficheiro.

1. Sob o gatilho, escolha **o próximo passo**. 

1. Debaixo da caixa de pesquisa, escolha **Todos.** Na caixa de pesquisa, introduza a "dropbox" como filtro.
A partir da lista de ações, selecione esta ação: **Obtenha conteúdo de ficheiro usando o caminho**

1. Se ainda não autorizou a Azure Logic Apps a aceder ao Dropbox, autorize o acesso agora.

1. Para navegar pelo caminho do ficheiro que pretende utilizar, junto à caixa **'Caminho** de Ficheiros', escolha o botão elipses **(...**) 

   Também pode clicar dentro da caixa 'Caminho de **Ficheiro'** e, na lista de conteúdos dinâmicos, selecione **'',** cujo valor está disponível como saída a partir do gatilho adicionado na secção anterior.'

1. Quando terminar, guarde a sua aplicação lógica.

1. Para ativar a sua aplicação lógica, crie um novo ficheiro no Dropbox.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/dropbox/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
