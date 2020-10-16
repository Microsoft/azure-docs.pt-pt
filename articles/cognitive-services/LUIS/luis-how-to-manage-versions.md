---
title: Gerir versões - LUIS
titleSuffix: Azure Cognitive Services
description: As versões permitem-lhe construir e publicar diferentes modelos. Uma boa prática é clonar o atual modelo ativo para uma versão diferente da app antes de fazer alterações no modelo.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 3cf4ce5dc7061065ea501ae1c00cd5b9e5404770
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540868"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Use versões para editar e testar sem afetar aplicações de produção ou de produção

As versões permitem-lhe construir e publicar diferentes modelos. Uma boa prática é clonar o atual modelo ativo para uma [versão](luis-concept-version.md) diferente da app antes de fazer alterações no modelo.

A versão ativa é a versão que está a editar na secção **construção** do portal LUIS com intenções, entidades, funcionalidades e padrões. Ao utilizar as APIs de autoria, não precisa de definir a versão ativa porque as chamadas rest API específicas da versão incluem a versão na rota.

Para trabalhar com versões, abra a sua aplicação selecionando o seu nome na página **My Apps** e, em seguida, selecione **Gerir** na barra superior e, em seguida, selecione **Versões** na navegação à esquerda.

A lista de versões mostra quais são as versões publicadas, onde são publicadas e qual a versão que está atualmente ativa.

## <a name="clone-a-version"></a>Clone uma versão

1. Selecione a versão deseja clonar e, em seguida, selecione **Clone** da barra de ferramentas.

2. Na caixa de diálogo da **versão Clone,** escreva um nome para a nova versão como "0.2".

   ![Caixa de diálogo versão clone](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > O ID da versão pode consistir apenas em caracteres, dígitos ou '.' e não pode ser superior a 10 caracteres.

   Uma nova versão com o nome especificado é criada e definida como a versão ativa.

## <a name="set-active-version"></a>Definir versão ativa

Selecione uma versão da lista e, em seguida, **selecione Ative** a partir da barra de ferramentas.

## <a name="import-version"></a>Versão de importação

Pode importar uma `.json` ou uma versão da sua `.lu` aplicação.

1. Selecione **Importar** da barra de ferramentas e, em seguida, selecione o formato.

2. Na **nova janela** pop-up da nova versão importe, insira o novo nome da versão de dez caracteres. Basta definir um ID de versão se a versão do ficheiro já existir na aplicação.

    ![Gerir secção, página versões, importar nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Uma vez importa uma versão, a nova versão torna-se a versão ativa.

### <a name="import-errors"></a>Erros de importação

* Erros do tokenizer: Se tiver um **erro de tokenizer ao** importar, está a tentar importar uma versão que usa um [tokenizer](luis-language-support.md#custom-tokenizer-versions) diferente do que a aplicação atualmente utiliza. Para corrigir isto, consulte [migrar entre as versões tokenizer](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **eliminar** uma versão, selecione uma versão da lista e, em seguida, selecione **Eliminar** da barra de ferramentas. Selecione **OK**.
* Para **mudar o nome de** uma versão, selecione uma versão da lista e, em seguida, **selecione Rename da** barra de ferramentas. Introduza novo nome e selecione **Feito**.
* Para **exportar** uma versão, selecione uma versão da lista e, em seguida, selecione **a aplicação Export** a partir da barra de ferramentas. Escolha json ou LU para exportar para uma cópia de segurança ou para economizar no controlo de origem, escolha **Export for container** para usar esta [aplicação num recipiente LUIS](luis-container-howto.md).

