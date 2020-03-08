---
title: Gerir versões - LUIS
titleSuffix: Azure Cognitive Services
description: Versões permitem-lhe criar e publicar modelos diferentes. É uma boa prática clonar o modelo de Active Directory atual para uma versão diferente da aplicação antes de efetuar alterações ao modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393672"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Utilize versões para editar e testar sem impactar aplicações de encenação ou produção

Versões permitem-lhe criar e publicar modelos diferentes. Uma boa prática é clonar o atual modelo ativo para uma [versão](luis-concept-version.md) diferente da app antes de fazer alterações no modelo. 

Para trabalhar com versões, abra a sua aplicação selecionando o seu nome na página **my Apps** e, em seguida, selecione **Manage** na barra superior e, em seguida, selecione **Versões** na navegação esquerda. 

A lista de versões mostra quais as versões que são publicadas, onde são publicadas e qual a versão que está atualmente ativa. 

> [!div class="mx-imgBorder"]
> [![Gerir a página de secção, versões](./media/luis-how-to-manage-versions/versions-import.png "Gerir a seção, página de versões")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar uma versão

1. Selecione a versão que pretende clonar e, em seguida, selecione **Clone** a partir da barra de ferramentas. 

2. Na caixa de diálogo da **versão Clone,** digite um nome para a nova versão, como "0.2".

   ![Caixa de diálogo de versão do clone](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versão ID pode ser constituído apenas por carateres, dígitos ou "." e não pode ter mais de 10 caracteres.
 
   Uma nova versão com o nome especificado é criada e definida como a versão do Active Directory.

## <a name="set-active-version"></a>Definir versão Active Directory

Selecione uma versão da lista e, em seguida, **selecione Ativar** a partir da barra de ferramentas. 

> [!div class="mx-imgBorder"]
> [![Gerir a secção, versões página, fazer uma ação de versão](./media/luis-how-to-manage-versions/versions-other.png "Gerir a secção, versões página, fazer uma ação de versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Versão de importação

Pode importar uma versão `.json` ou `.lu` da sua aplicação.

1. Selecione **Importar** a partir da barra de ferramentas e, em seguida, selecione o formato. 

2. Na **nova versão Import,** introduza o novo nome de versão de dez caracteres. Só precisa de definir um ID da versão se a versão do ficheiro já existir na aplicação.

    ![Gerir a secção, página de versões, importar nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Depois de importar uma versão, a nova versão torna-se a versão do Active Directory.

### <a name="import-errors"></a>Erros de importação

* Erros tokenizer: Se tiver um erro de **tokenizer** ao importar, está a tentar importar uma versão que usa um [tokenizer](luis-language-support.md#custom-tokenizer-versions) diferente do que a aplicação utiliza atualmente. Para corrigir isto, consulte [Migrating entre versões de tokenizer](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **eliminar** uma versão, selecione uma versão da lista e, em seguida, selecione **Eliminar** a partir da barra de ferramentas. Selecione **Ok**. 
* Para mudar o **nome** de uma versão, selecione uma versão da lista e, em seguida, selecione **Rename** a partir da barra de ferramentas. Introduza novo nome e selecione **Done**. 
* Para **exportar** uma versão, selecione uma versão da lista e, em seguida, selecione a **aplicação Export** a partir da barra de ferramentas. Escolha a JSON para exportar para backup, escolha **Exportar para o recipiente** para utilizar esta [aplicação num recipiente LUIS](luis-container-howto.md).  

