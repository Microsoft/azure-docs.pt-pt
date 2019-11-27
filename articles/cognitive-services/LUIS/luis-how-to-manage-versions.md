---
title: Gerenciar versões-LUIS
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221893"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Use versões para editar e testar sem afetar os aplicativos de preparo ou de produção

Versões permitem-lhe criar e publicar modelos diferentes. Uma boa prática é clonar o modelo ativo atual para uma [versão](luis-concept-version.md) diferente do aplicativo antes de fazer alterações no modelo. 

Para trabalhar com versões, abra seu aplicativo selecionando seu nome na página **meus aplicativos** e, em seguida, selecione **gerenciar** na barra superior e, em seguida, selecione **versões** no painel de navegação à esquerda. 

A lista de versões mostra quais versões são publicadas, onde elas são publicadas e qual versão está ativa no momento. 

> [!div class="mx-imgBorder"]
> [![Seção Gerenciar, página versões](./media/luis-how-to-manage-versions/versions-import.png "Gerir a seção, página de versões")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar uma versão

1. Selecione a versão que você deseja clonar e, em seguida, selecione **clonar** na barra de ferramentas. 

2. Na caixa de diálogo **clonar versão** , digite um nome para a nova versão, como "0,2".

   ![Caixa de diálogo de versão do clone](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versão ID pode ser constituído apenas por carateres, dígitos ou "." e não pode ter mais de 10 caracteres.
 
   Uma nova versão com o nome especificado é criada e definida como a versão do Active Directory.

## <a name="set-active-version"></a>Definir versão Active Directory

Selecione uma versão na lista e, em seguida, selecione **Ativar** na barra de ferramentas. 

> [!div class="mx-imgBorder"]
> [![Seção Gerenciar, página versões, criar uma versão](./media/luis-how-to-manage-versions/versions-other.png "Seção Gerenciar, página versões, criar uma versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Versão de importação

Você pode importar um `.json` ou uma versão `.lu` do seu aplicativo.

1. Selecione **importar** na barra de ferramentas e, em seguida, selecione o formato. 

2. Na janela pop-up **importar nova versão** , insira o novo nome de versão de dez caracteres. Você só precisa definir uma ID de versão se a versão no arquivo já existir no aplicativo.

    ![Seção Gerenciar, página versões, importando nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Depois de importar uma versão, a nova versão torna-se a versão do Active Directory.

### <a name="import-errors"></a>Erros de importação

* Erros de Criador: se você receber um **erro de criador** ao importar, você está tentando importar uma versão que usa um [criador](luis-language-support.md#custom-tokenizer-versions) diferente do que o aplicativo usa atualmente. Para corrigir isso, consulte [migrando entre versões do criador](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **excluir** uma versão, selecione uma versão na lista e, em seguida, selecione **excluir** na barra de ferramentas. Selecione **Ok**. 
* Para **renomear** uma versão, selecione uma versão na lista e, em seguida, selecione **renomear** na barra de ferramentas. Insira o novo nome e selecione **concluído**. 
* Para **Exportar** uma versão, selecione uma versão na lista e, em seguida, selecione **Exportar aplicativo** na barra de ferramentas. Escolha JSON para exportar para backup, escolha **exportar para contêiner** para [usar esse aplicativo em um contêiner Luis](luis-container-howto.md).  

