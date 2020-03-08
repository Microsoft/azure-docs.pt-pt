---
title: Como gerir as definições? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como gerir as definições, criar espaço de trabalho, partilhar espaço de trabalho e gerir a chave de subscrição em Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381804"
---
# <a name="how-to-manage-settings"></a>Como gerir as definições

Dentro da página de definições de Tradutor Personalizado, pode criar um novo espaço de trabalho, partilhar o seu espaço de trabalho e adicionar ou modificar a sua chave de subscrição da Microsoft Translation.

Para aceder à página de definições:

1. Inscreva-se no portal [tradutor personalizado.](https://portal.customtranslator.azure.ai/)
2. No portal Custom Tradutor, clique no ícone da engrenagem na barra lateral.

    ![Definição de link](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associar a subscrição do Tradutor da Microsoft

É necessário ter uma chave de subscrição da Microsoft Tradutor Text API associada ao seu espaço de trabalho para treinar ou implementar modelos.

Se não tiver uma subscrição, siga os passos abaixo:

1. Subscreva a API de Texto do Tradutor da Microsoft. Este artigo mostra como subscrever a API de Texto do Tradutor da Microsoft.
2. Note a chave para a subscrição do seu tradutor. Qualquer uma das Key1 ou Key2 são aceitáveis.
3. Navegue de volta ao portal tradutor personalizado.

### <a name="add-existing-key"></a>Adicione a chave existente

1.  Navegue na página "Definições" para o seu espaço de trabalho.
2.  Clique em adicionar chave

    ![Como adicionar a chave de subscrição](media/how-to/how-to-add-subscription-key.png)

3. No diálogo, introduza a tecla para a subscrição do tradutor e, em seguida, clique no botão "Adicionar".

    ![Como adicionar a chave de subscrição](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Depois de adicionar uma chave, pode modificar ou eliminar a tecla a qualquer momento.

    ![Chave de subscrição após adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerencie o seu espaço de trabalho

Um espaço de trabalho é uma área de trabalho para compor e construir o seu sistema de tradução personalizada. Um espaço de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisa de ser partilhada com pessoas diferentes, então criar vários espaços de trabalho pode ser útil.

## <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

1.  Navegue para a página "Definições" do espaço de trabalho.
2.  Clique no botão "Novo espaço de trabalho" na secção "Criar novo espaço de trabalho".

    ![Criar novo espaço de trabalho](media/how-to/create-new-workspace.png)

4.  No diálogo, insira o nome do novo espaço de trabalho.
5.  Clique em "Criar".

    ![Criar novo diálogo no espaço de trabalho](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Partilhe o seu espaço de trabalho

Em Custom Tradutor pode partilhar o seu espaço de trabalho com outros, se uma parte diferente do seu trabalho precisa ser partilhada com pessoas diferentes.

1.  Navegue para a página "Definições" do espaço de trabalho.
2.  Clique no botão "Partilhar" na secção "Definições de Partilha".

    ![Partilhar espaço de trabalho](media/how-to/share-workspace.png)

3.  No diálogo, introduza uma lista separada de endereços de e-mail separados com a vírda que pretende partilhar este espaço de trabalho. Certifique-se de que partilha com o endereço de e-mail que a pessoa usa para iniciar sessão no Custom Tradutor. Em seguida, selecione o nível adequado de autorização de partilha.

4.  Se o seu espaço de trabalho ainda tiver o nome padrão "O meu espaço de trabalho", será obrigado a alterá-lo antes de partilhar o seu espaço de trabalho.
5.  Clique em "Guardar".

## <a name="sharing-permissions"></a>Partilha de permissões

1.  **Leitor:** O leitor no espaço de trabalho será capaz de ver toda a informação no espaço de trabalho.

2.  **Editor:** Um editor no espaço de trabalho poderá adicionar documentos, treinar modelos e apagar documentos e projetos. Podem adicionar uma chave de subscrição, mas não podem modificar com quem o espaço de trabalho é partilhado, eliminar o espaço de trabalho ou alterar o nome do espaço de trabalho.

3.  **Proprietário:** Um proprietário tem permissões completas para o espaço de trabalho.

## <a name="change-sharing-permission"></a>Alterar a permissão de partilha

Quando um espaço de trabalho é partilhado, a secção "Definições de Partilha" mostra todos os endereços de e-mail com os quais este espaço de trabalho é partilhado. Pode alterar a permissão de partilha existente para cada endereço de e-mail se tiver acesso ao espaço de trabalho do proprietário.

1.  Na secção "Definições de Partilha" para cada e-mail, um menu de dropdown mostra o nível de permissão atual.

2.  Clique no menu de dropdown e selecione o novo nível de permissão que pretende atribuir a esse endereço de e-mail.

    ![Definições de autorização de partilha](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como migrar o seu espaço](how-to-migrate.md) de trabalho e projeto a partir do Microsoft Tradutor [Hub](https://hub.microsofttranslator.com)
