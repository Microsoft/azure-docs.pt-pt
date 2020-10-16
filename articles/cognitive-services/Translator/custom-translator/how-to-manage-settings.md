---
title: Como gerir as configurações? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Como gerir configurações, criar espaço de trabalho, partilhar espaço de trabalho e gerir a chave de subscrição no Custom Tradutor.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 184073ef3ee1b6a67c844754f7da81f88bbbdf62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757032"
---
# <a name="how-to-manage-settings"></a>Como gerir as definições

Dentro da página de definições do Tradutor Personalizado, pode partilhar o seu espaço de trabalho, modificar a sua chave de subscrição do Tradutor e eliminar o espaço de trabalho.

Para aceder à página de definições:

1. Inscreva-se no portal [do Tradutor Personalizado.](https://portal.customtranslator.azure.ai/)
2. No portal Custom Tradutor, clique no ícone de engrenagem na barra lateral.

    ![Ligação de definição](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Assinatura de Tradutor Associado

Precisa de ter uma chave de subscrição do Tradutor associada ao seu espaço de trabalho para treinar ou implementar modelos.

Se não tiver uma subscrição, siga os passos abaixo:

1. Subscreva a criação de um recurso Tradutor. Acompanhe [como se inscrever para que](https://docs.microsoft.com/azure/cognitive-services/translator/translator-how-to-signup) o Tradutor subscreva e adquira uma chave Tradutor.
2. Note a chave para a sua assinatura Tradutor. Qualquer uma das Chaves1 ou Key2 são aceitáveis.
3. Navegue de volta para o portal de Tradutor Personalizado.

## <a name="create-a-new-workspace"></a>Criar uma nova área de trabalho

1. Clique no botão "+ Criar espaço de trabalho" na barra lateral "Custom Tradutor".

    ![Criar novo espaço de trabalho](media/how-to/create-new-workspace.png)

2. No diálogo, insira o nome do novo espaço de trabalho.
3. Clique em "Seguinte".
4. Escolha o tipo de assinatura.
5. Selecione região de subscrição. A região deve coincidir com a região selecionada quando a chave de recursos do Tradutor foi criada.
6. Introduza a chave para a subscrição do seu tradutor e, em seguida, clique no botão "Guardar".

    ![Criar novo diálogo de espaço de trabalho](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>O Tradutor Personalizado não suporta a criação de espaço de trabalho para o recurso API de texto tradutor (também conhecido como API. Chave de subscrição Azure) que foi criada dentro [do VNET Ativado](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet).

### <a name="modify-existing-key"></a>Modificar a chave existente

1. Navegue na página "Definições" para o seu espaço de trabalho.
2. Clique em Alterar Tecla

    ![Como adicionar chave de subscrição](media/how-to/how-to-add-subscription-key.png)

3. No diálogo, introduza a chave para a subscrição do Tradutor e, em seguida, clique no botão "Guardar".

    ![Como adicionar diálogo chave de subscrição](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Gerir a sua área de trabalho

Um espaço de trabalho é uma área de trabalho para compor e construir o seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos.

Se uma parte diferente do seu trabalho precisa de ser partilhada com pessoas diferentes, então criar múltiplos espaços de trabalho pode ser útil.

## <a name="share-your-workspace"></a>Partilhe o seu espaço de trabalho

No Custom Tradutor pode partilhar o seu espaço de trabalho com outros, se uma parte diferente do seu trabalho precisar de ser partilhada com pessoas diferentes.

1. Navegue para a página "Definições" do espaço de trabalho.
2. Clique no botão "Adicionar pessoas" na secção "Partilhar definições".

    ![Partilhar espaço de trabalho](media/how-to/share-workspace.png)

3. No diálogo, insira uma lista separada por vírgula de endereços de e-mail com os que pretende que este espaço de trabalho seja partilhado. Certifique-se de que partilha com o endereço de e-mail que a pessoa usa para iniciar sinsus no Custom Tradutor com. Em seguida, selecione o nível adequado de partilha de permissão e clique no botão "Guardar".

    ![Partilhar diálogo de espaço de trabalho](media/how-to/share-workspace-dialog.png)

4. Se o seu espaço de trabalho ainda tiver o nome padrão "O meu espaço de trabalho", terá de alterá-lo antes de partilhar o seu espaço de trabalho.
5. Clique em "Guardar".

## <a name="sharing-permissions"></a>Permissões de partilha

1. **Leitor:** Um leitor no espaço de trabalho será capaz de ver toda a informação no espaço de trabalho.

2. **Editor:** Um editor no espaço de trabalho será capaz de adicionar documentos, modelos de comboio e apagar documentos e projetos. Podem adicionar uma chave de subscrição, mas não podem modificar com quem o espaço de trabalho é partilhado, eliminar o espaço de trabalho ou alterar o nome do espaço de trabalho.

3. **Proprietário:** Um proprietário tem permissões completas para o espaço de trabalho.

## <a name="change-sharing-permission"></a>Alterar a permissão de partilha

Quando um espaço de trabalho é partilhado, a secção "Partilhar definições" mostra todos os endereços de e-mail com os quais este espaço de trabalho é partilhado. Pode alterar a permissão de partilha existente para cada endereço de e-mail se tiver acesso ao espaço de trabalho do proprietário.

1. Na secção "Partilha de definições" para cada e-mail, um menu suspenso mostra o nível de permissão atual.

2. Clique no menu dropdown e selecione o novo nível de permissão que pretende atribuir a esse endereço de e-mail.

    ![Partilhar definições de permissão](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Pin o seu espaço de trabalho

O seu primeiro espaço de trabalho criado é por defeito. Cada vez que iniciar sposição, o seu espaço de trabalho fixado é apresentado na carga do local. Se criou muitos espaços de trabalho e deseja fazer de um deles o seu padrão quando iniciar sôs o seu contrato, tem de o fixar.

1. Na barra lateral, clique no nome do espaço de trabalho que pretende fixar.
2. Navegue na página "Definições" para o seu espaço de trabalho.
3. Clique no ícone Pin.

    ![Espaço de trabalho pin](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como criar o seu espaço de trabalho e projetos](workspace-and-project.md)
