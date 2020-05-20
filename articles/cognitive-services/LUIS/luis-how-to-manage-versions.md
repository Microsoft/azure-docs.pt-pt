---
title: Gerir versões - LUIS
titleSuffix: Azure Cognitive Services
description: As versões permitem-lhe construir e publicar diferentes modelos. Uma boa prática é clonar o atual modelo ativo para uma versão diferente da app antes de fazer alterações no modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 2de39a2822056a4539c2dca915a483ab5bbc4965
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653973"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Utilize versões para editar e testar sem impactar aplicações de encenação ou produção

As versões permitem-lhe construir e publicar diferentes modelos. Uma boa prática é clonar o atual modelo ativo para uma [versão](luis-concept-version.md) diferente da app antes de fazer alterações no modelo.

A versão ativa é a versão que está a editar no portal LUIS **Construir** secção com intenções, entidades, funcionalidades e padrões. Ao utilizar as APIs de autoria, não precisa de definir a versão ativa porque as chamadas REST API específicas da versão incluem a versão na rota.

Para trabalhar com versões, abra a sua aplicação selecionando o seu nome na página **my Apps** e, em seguida, selecione **Manage** na barra superior e, em seguida, selecione **Versões** na navegação esquerda.

A lista de versões mostra quais as versões que são publicadas, onde são publicadas e qual a versão que está atualmente ativa.

## <a name="clone-a-version"></a>Clone uma versão

1. Selecione a versão que pretende clonar e, em seguida, selecione **Clone** a partir da barra de ferramentas.

2. Na caixa de diálogo da **versão Clone,** digite um nome para a nova versão, como "0.2".

   ![Caixa de diálogo da versão clone](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > O ID da versão só pode ser composto por caracteres, dígitos ou '.' e não pode ter mais de 10 caracteres.

   Uma nova versão com o nome especificado é criada e definida como a versão ativa.

## <a name="set-active-version"></a>Definir versão ativa

Selecione uma versão da lista e, em seguida, **selecione Ativar** a partir da barra de ferramentas.

## <a name="import-version"></a>Versão de importação

Pode importar uma `.json` ou uma versão da sua `.lu` aplicação.

1. Selecione **Importar** a partir da barra de ferramentas e, em seguida, selecione o formato.

2. Na **nova versão Import,** introduza o novo nome de versão de dez caracteres. Só precisa de definir um ID da versão se a versão do ficheiro já existir na aplicação.

    ![Gerir a secção, página de versões, importar nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Uma vez importado uma versão, a nova versão torna-se a versão ativa.

### <a name="import-errors"></a>Erros de importação

* Erros tokenizer: Se tiver um erro de **tokenizer** ao importar, está a tentar importar uma versão que usa um [tokenizer](luis-language-support.md#custom-tokenizer-versions) diferente do que a aplicação utiliza atualmente. Para corrigir isto, consulte [Migrating entre versões de tokenizer](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **eliminar** uma versão, selecione uma versão da lista e, em seguida, selecione **Eliminar** a partir da barra de ferramentas. Selecione **Ok**.
* Para mudar o **nome** de uma versão, selecione uma versão da lista e, em seguida, selecione **Rename** a partir da barra de ferramentas. Introduza novo nome e selecione **Done**.
* Para **exportar** uma versão, selecione uma versão da lista e, em seguida, selecione a **aplicação Export** a partir da barra de ferramentas. Escolha a JSON para exportar para backup, escolha **Exportar para o recipiente** para utilizar esta [aplicação num recipiente LUIS](luis-container-howto.md).

