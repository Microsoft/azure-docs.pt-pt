---
title: Como gerir as configurações? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Como gerir configurações, criar espaço de trabalho, partilhar espaço de trabalho e gerir a chave de subscrição no Custom Tradutor.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: af46b48d47a53d5a8a19db7c50a9356824607a5d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962956"
---
# <a name="how-to-manage-settings"></a>Como gerir as definições

Dentro da página de definições do Tradutor Personalizado, pode criar um novo espaço de trabalho, partilhar o seu espaço de trabalho e adicionar ou modificar a sua chave de subscrição de Tradução microsoft.

Para aceder à página de definições:

1. Inscreva-se no portal [do Tradutor Personalizado.](https://portal.customtranslator.azure.ai/)
2. No portal Custom Tradutor, clique no ícone de engrenagem na barra lateral.

    ![Ligação de definição](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Assinatura de Tradutor Associado

Precisa de ter uma chave de subscrição do Tradutor associada ao seu espaço de trabalho para treinar ou implementar modelos.

Se não tiver uma subscrição, siga os passos abaixo:

1. Inscreva-se no Tradutor. Este artigo mostra como subscrever o Tradutor.
2. Note a chave para a sua assinatura de tradutor. Qualquer uma das Chaves1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal de Tradutor Personalizado.

### <a name="add-existing-key"></a>Adicionar a chave existente

1.    Navegue na página "Definições" para o seu espaço de trabalho.
2.    Clique em Adicionar Tecla

   ![Como adicionar chave de subscrição](media/how-to/how-to-add-subscription-key.png)

3. No diálogo, introduza a chave para a subscrição do seu tradutor e, em seguida, clique no botão "Adicionar".

    ![Como adicionar chave de subscrição](media/how-to/how-to-add-subscription-key-dialog.png)
4.    Depois de ter adicionado uma chave, pode modificar ou eliminar a chave a qualquer momento.

   ![Chave de subscrição após adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerir a sua área de trabalho

Um espaço de trabalho é uma área de trabalho para compor e construir o seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisa de ser partilhada com pessoas diferentes, então criar múltiplos espaços de trabalho pode ser útil.

## <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

1.    Navegue para a página "Definições" do espaço de trabalho.
2.    Clique no botão "Novo espaço de trabalho" na secção "Criar novo espaço de trabalho".

   ![Criar novo espaço de trabalho](media/how-to/create-new-workspace.png)

4.    No diálogo, insira o nome do novo espaço de trabalho.
5.    Clique em "Criar".

   ![Criar novo diálogo de espaço de trabalho](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Partilhe o seu espaço de trabalho

No Custom Tradutor pode partilhar o seu espaço de trabalho com outros, se uma parte diferente do seu trabalho precisar de ser partilhada com pessoas diferentes.

1.    Navegue para a página "Definições" do espaço de trabalho.
2.    Clique no botão "Partilhar" na secção "Partilhar Definições".

   ![Partilhar espaço de trabalho](media/how-to/share-workspace.png)

3.    No diálogo, insira uma lista separada por vírgula de endereços de e-mail com os que pretende que este espaço de trabalho seja partilhado. Certifique-se de que partilha com o endereço de e-mail que a pessoa usa para iniciar sinsus no Custom Tradutor com. Em seguida, selecione o nível adequado de permissão de partilha.

4.    Se o seu espaço de trabalho ainda tiver o nome padrão "O meu espaço de trabalho", terá de alterá-lo antes de partilhar o seu espaço de trabalho.
5.    Clique em "Guardar".

## <a name="sharing-permissions"></a>Permissões de partilha

1.    **Leitor:** Um leitor no espaço de trabalho será capaz de ver toda a informação no espaço de trabalho.

2.    **Editor:** Um editor no espaço de trabalho será capaz de adicionar documentos, modelos de comboio e apagar documentos e projetos. Podem adicionar uma chave de subscrição, mas não podem modificar com quem o espaço de trabalho é partilhado, eliminar o espaço de trabalho ou alterar o nome do espaço de trabalho.

3.    **Proprietário:** Um proprietário tem permissões completas para o espaço de trabalho.

## <a name="change-sharing-permission"></a>Alterar a permissão de partilha

Quando um espaço de trabalho é partilhado, a secção "Partilhar Definições" mostra todos os endereços de e-mail com os quais este espaço de trabalho é partilhado. Pode alterar a permissão de partilha existente para cada endereço de e-mail se tiver acesso ao espaço de trabalho do proprietário.

1.    Na secção "Configurações de partilha" para cada e-mail, um menu suspenso mostra o nível de permissão atual.

2.    Clique no menu dropdown e selecione o novo nível de permissão que pretende atribuir a esse endereço de e-mail.

   ![Partilhar definições de permissão](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Próximos passos

- Saiba [como migrar o seu espaço de trabalho e projeto](how-to-migrate.md) a partir do Microsoft Tradutor [Hub](https://hub.microsofttranslator.com)
