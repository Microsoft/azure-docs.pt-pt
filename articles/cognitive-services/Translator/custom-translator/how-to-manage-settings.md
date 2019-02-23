---
title: Como gerir as definições? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como gerir as definições, criar área de trabalho, Compartilhe a área de trabalho e gere a chave de subscrição no Translator personalizado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: bbec2205fc6b29476cf531c071b5c2ac4c014bcb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732529"
---
# <a name="how-to-manage-settings"></a>Como gerir as definições

Na página de definições de tradutor de personalizado, pode criar uma nova área de trabalho, compartilhar sua área de trabalho e adiciona ou modifica a chave de subscrição do Microsoft Translation.

Para aceder à página de definições:

1. Inicie sessão para o [Translator personalizado](https://portal.customtranslator.azure.ai/) portal.
2. No portal personalizado Translator, clique no ícone de engrenagem na barra lateral.

    ![Ligação de definição](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associação de subscrição do Microsoft Translator

Tem de ter uma chave de assinatura de API de texto do Microsoft Translator associada à sua área de trabalho de preparar ou implementar modelos.

Se não tiver uma subscrição, siga os passos abaixo:

1. Subscreva a API de texto do Microsoft Translator. Este artigo mostra como subscrever-se para a API de texto do Microsoft Translator.
2. Tenha em atenção a chave para a sua subscrição do Microsoft translator. Qualquer um dos chave1 ou chave2 são aceitável.
3. Navegue de volta para o portal de tradutor de personalizado.

### <a name="add-existing-key"></a>Adicionar chave existente

1.  Navegue para a página de "Definições" da sua área de trabalho.
2.  Clique em Adicionar chave

    ![Como adicionar a chave de subscrição](media/how-to/how-to-add-subscription-key.png)

3. Na caixa de diálogo, introduza a chave para a sua subscrição do Microsoft translator, em seguida, clique no botão "Adicionar".

    ![Como adicionar a chave de subscrição](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Depois de adicionar uma chave, pode modificar ou eliminar a chave em qualquer altura.

    ![Chave de subscrição depois de adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerir a sua área de trabalho

Uma área de trabalho é uma área de trabalho para compor e criação de seu sistema de tradução personalizadas. Uma área de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho tem de ser partilhados com pessoas diferentes, em seguida, criar várias áreas de trabalho poderá ser útil.

## <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

1.  Navegue para a página de "Definições" da área de trabalho.
2.  Clique em "nova área de trabalho" botão na secção "Criar nova área de trabalho".

    ![Criar nova área de trabalho](media/how-to/create-new-workspace.png)

4.  Na caixa de diálogo, introduza o nome da área de trabalho nova.
5.  Clique em "Criar".

    ![Criar nova caixa de diálogo de área de trabalho](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Compartilhe sua área de trabalho

Tradutor personalizado pode partilhar sua área de trabalho com outras pessoas, se uma parte diferente do seu trabalho tem de ser partilhados com pessoas diferentes.

1.  Navegue para a página de "Definições" da área de trabalho.
2.  Clique no botão "Partilha" na secção "Definições de partilha".

    ![Compartilhe a área de trabalho](media/how-to/share-workspace.png)

3.  Na caixa de diálogo, introduza uma lista separada por vírgulas de endereços de e-mail que pretende que esta área de trabalho partilhada com. Certifique-se de partilha com o endereço de e-mail que a pessoa utiliza para iniciar sessão no Translator personalizado com. Em seguida, selecione o nível apropriado de permissões de partilha.

4.  Se a sua área de trabalho ainda tem o nome predefinido "Minha área de trabalho", ser-lhe-á pedido para alterá-lo antes de compartilhar sua área de trabalho.
5.  Clique em "Guardar".

## <a name="sharing-permissions"></a>Permissões de partilha

1.  **Leitor de:** Um leitor na área de trabalho será capaz de ver todas as informações na área de trabalho.

2.  **Editor:** Um editor na área de trabalho poderá adicionar documentos, formar modelos e eliminar documentos e projetos. Eles podem adicionar uma chave de subscrição, mas não é possível modificar que a área de trabalho é partilhada com, eliminar a área de trabalho ou altere o nome de área de trabalho.

3.  **Proprietário:** Um proprietário tem todas as permissões para a área de trabalho.

## <a name="change-sharing-permission"></a>Alterar permissões de partilha

Quando uma área de trabalho é partilhada, a seção de "Definições de partilha" mostra todos os endereços de e-mail que esta área de trabalho é partilhada com. Pode alterar existente a permissão para cada endereço de e-mail de partilha, se tiver acesso de proprietário à área de trabalho.

1.  Na secção "Definições de partilha" para cada e-mail um menu suspenso mostra o nível de permissão atual.

2.  Clique no menu de lista pendente e selecione o novo nível de permissão que pretende atribuir a esse endereço de e-mail.

    ![Definições de permissão de partilha](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais [como migrar a sua área de trabalho e o projeto](how-to-migrate.md) partir [Hub do Microsoft Translator](https://hub.microsofttranslator.com)
