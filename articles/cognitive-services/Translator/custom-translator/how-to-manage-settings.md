---
title: Como gerenciar configurações? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como gerenciar configurações, criar espaço de trabalho, compartilhar espaço de trabalho e gerenciar a chave de assinatura no Tradutor personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595761"
---
# <a name="how-to-manage-settings"></a>Como gerenciar configurações

Na página Configurações do tradutor personalizado, você pode criar um novo espaço de trabalho, compartilhar seu espaço de trabalho e adicionar ou modificar sua chave de assinatura do Microsoft translation.

Para acessar a página de configurações:

1. Entre no portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai/) .
2. No portal do tradutor personalizado, clique no ícone de engrenagem na barra lateral.

    ![Link de configuração](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associando a assinatura do Microsoft Translator

Você precisa ter uma chave de assinatura do Microsoft API de Tradução de Texto associada ao seu espaço de trabalho para treinar ou implantar modelos.

Se você não tiver uma assinatura, siga as etapas abaixo:

1. Assine o Microsoft API de Tradução de Texto. Este artigo mostra como assinar o API de Tradução de Texto da Microsoft.
2. Anote a chave para sua assinatura do tradutor. Qualquer uma das key1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal do tradutor personalizado.

### <a name="add-existing-key"></a>Adicionar chave existente

1.  Navegue até a página "configurações" do seu espaço de trabalho.
2.  Clique em Adicionar chave

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key.png)

3. Na caixa de diálogo, insira a chave para sua assinatura do tradutor e clique no botão "Adicionar".

    ![Como adicionar chave de assinatura](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Depois de adicionar uma chave, você pode modificar ou excluir a chave a qualquer momento.

    ![Chave de assinatura após adicionar](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gerenciar seu espaço de trabalho

Um espaço de trabalho é uma área de trabalho para compor e criar seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisar ser compartilhada com pessoas diferentes, a criação de vários espaços de trabalho pode ser útil.

## <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

1.  Navegue até a página "configurações" do espaço de trabalho.
2.  Clique no botão "novo espaço de trabalho" na seção "criar novo espaço de trabalho".

    ![Criar nova área de trabalho](media/how-to/create-new-workspace.png)

4.  Na caixa de diálogo, digite o nome do novo espaço de trabalho.
5.  Clique em "criar".

    ![Caixa de diálogo Criar novo espaço de trabalho](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Compartilhar seu espaço de trabalho

No Tradutor personalizado, você pode compartilhar seu espaço de trabalho com outras pessoas, se diferentes partes do seu trabalho precisarem ser compartilhadas com pessoas diferentes.

1.  Navegue até a página "configurações" do espaço de trabalho.
2.  Clique no botão "compartilhar" na seção "configurações de compartilhamento".

    ![Espaço de trabalho de compartilhamento](media/how-to/share-workspace.png)

3.  Na caixa de diálogo, insira uma lista separada por vírgulas de endereços de email com os quais você deseja compartilhar esse espaço de trabalho. Certifique-se de compartilhar com o endereço de email que a pessoa usa para entrar no Tradutor personalizado com o. Em seguida, selecione o nível apropriado de permissão de compartilhamento.

4.  Se o seu espaço de trabalho ainda tiver o nome padrão "meu espaço de trabalho", será necessário alterá-lo antes de compartilhar seu espaço de trabalho.
5.  Clique em "salvar".

## <a name="sharing-permissions"></a>Permissões de partilha

1.  **Leitora** Um leitor no espaço de trabalho poderá exibir todas as informações no espaço de trabalho.

2.  **Editor** Um editor no espaço de trabalho poderá adicionar documentos, treinar modelos e excluir documentos e projetos. Eles podem adicionar uma chave de assinatura, mas não podem modificar com quem o espaço de trabalho é compartilhado, excluir o espaço de trabalho ou alterar o nome do espaço de trabalho.

3.  **Proprietário** Um proprietário tem permissões completas para o espaço de trabalho.

## <a name="change-sharing-permission"></a>Alterar permissão de compartilhamento

Quando um espaço de trabalho é compartilhado, a seção "configurações de compartilhamento" mostra todos os endereços de email com os quais esse espaço de trabalho é compartilhado. Você pode alterar a permissão de compartilhamento existente para cada endereço de email se tiver acesso de proprietário ao espaço de trabalho.

1.  Na seção "configurações de compartilhamento" para cada email, um menu suspenso mostra o nível de permissão atual.

2.  Clique no menu suspenso e selecione o novo nível de permissão que você deseja atribuir a esse endereço de email.

    ![Configurações de permissão de compartilhamento](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba [como migrar seu espaço de trabalho e projeto](how-to-migrate.md) do [Hub do Microsoft Translator](https://hub.microsofttranslator.com)
