---
title: Ligar ao Dropbox
description: Automatizar tarefas e fluxos de trabalho que carregam e gerem ficheiros no Dropbox utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665756"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Faça upload e gerencie ficheiros no Dropbox utilizando aplicações da Azure Logic

Com o conector Dropbox e as Aplicações Lógicas Azure, pode criar fluxos de trabalho automatizados que carregam e gerem ficheiros na sua conta Dropbox. 

Este artigo mostra como ligar ao Dropbox a partir da sua aplicação lógica e, em seguida, adicionar o Dropbox **Quando um ficheiro é criado** gatilho e o conteúdo de ficheiro Dropbox Get usando a ação do **caminho.**

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta Dropbox](https://www.dropbox.com/), que pode inscrever-se gratuitamente. As credenciais da sua conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta Dropbox.

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para este exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Debaixo da caixa de pesquisa, escolha **All**. Na caixa de pesquisa, introduza "dropbox" como filtro.
A partir da lista de gatilhos, selecione este gatilho: **Quando um ficheiro é criado**

   ![Selecione gatilho Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Inscreva-se nas credenciais da sua conta Dropbox e autorize o acesso aos seus dados dropbox para Aplicações Lógicas Azure.

1. Forneça as informações necessárias para o seu gatilho. 

   Neste exemplo, selecione a pasta onde pretende rastrear a criação de ficheiros. Para navegar nas suas pastas, escolha o ícone da pasta ao lado da caixa de **pastas.**

## <a name="add-action"></a>Adicionar ação

Adicione agora uma ação que obtenha o conteúdo de qualquer novo ficheiro.

1. Sob o gatilho, escolha **o próximo passo**. 

1. Debaixo da caixa de pesquisa, escolha **All**. Na caixa de pesquisa, introduza "dropbox" como filtro.
A partir da lista de ações, selecione esta ação: Obtenha conteúdo de **ficheiro usando o caminho**

1. Se ainda não autorizou as Aplicações Lógicas Azure a aceder ao Dropbox, autorize já o acesso.

1. Para navegar no caminho de ficheiro que pretende utilizar, junto à caixa Do Caminho de **Arquivo,** escolha o botão elipses (**...**). 

   Também pode clicar dentro da caixa Do Caminho de **Ficheiros** e a partir da lista de conteúdos dinâmicos, selecione o caminho de **Ficheiro,** cujo valor está disponível como saída a partir do gatilho adicionado na secção anterior.

1. Quando terminar, guarde a sua aplicação lógica.

1. Para desencadear a sua aplicação lógica, crie um novo ficheiro no Dropbox.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/dropbox/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
