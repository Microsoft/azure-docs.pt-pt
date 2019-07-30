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
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dd3cd13b14f58e1ece0b91f924ebefb25682bbf7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638112"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Use versões para editar e testar sem afetar os aplicativos de preparo ou de produção

Versões permitem-lhe criar e publicar modelos diferentes. Uma boa prática é clonar o modelo de Active Directory atual para um diferente [versão](luis-concept-version.md) da aplicação antes de efetuar alterações ao modelo. 

Para trabalhar com as versões, abra a sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página e, em seguida, selecione **gerir** na barra superior, em seguida, selecione **versões** na navegação à esquerda. 

A lista de versões mostra quais versões são publicadas, onde elas são publicadas e qual versão está ativa no momento. 

[![Secção Gerir, página de versões](./media/luis-how-to-manage-versions/versions-import.png "secção Gerir, página de versões")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar uma versão

1. Selecione a versão que pretende clonar em seguida, selecione **Clone** da barra de ferramentas. 

2. Na **versão de Clone** caixa de diálogo, escreva um nome para a nova versão, como "0,2".

   ![Caixa de diálogo de versão do clone](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versão ID pode ser constituído apenas por carateres, dígitos ou "." e não pode ter mais de 10 caracteres.
 
   Uma nova versão com o nome especificado é criada e definida como a versão do Active Directory.

## <a name="set-active-version"></a>Definir versão Active Directory

Selecione uma versão da lista, em seguida, selecione **tornar Active** da barra de ferramentas. 

[![Seção Gerenciar, página versões, criar uma versão](./media/luis-how-to-manage-versions/versions-other.png "Seção Gerenciar, página versões, criar uma versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Versão de importação

1. Selecione **versão de importação** da barra de ferramentas. 

2. Na **nova versão de importação** janela pop-up, introduza o novo nome de versão de dez caractere. Apenas terá de definir um ID de versão, se a versão no ficheiro JSON já existe na aplicação.

    ![Seção Gerenciar, página versões, importando nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Depois de importar uma versão, a nova versão torna-se a versão do Active Directory.

### <a name="import-errors"></a>Erros de importação

* Erros de Criador: Se você receber um **erro criador** ao importar, você está tentando importar uma versão que usa um [criador](luis-language-support.md#custom-tokenizer-versions) diferente do que o aplicativo usa atualmente. Para corrigir isso, consulte [migrando entre versões do criador](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **elimine** uma versão, selecione uma versão da lista, em seguida, selecione **eliminar** da barra de ferramentas. Selecione **Ok**. 
* Para **mudar o nome** uma versão, selecione uma versão da lista, em seguida, selecione **mudar o nome** da barra de ferramentas. Introduza o novo nome e selecione **feito**. 
* Para **exportar** uma versão, selecione uma versão da lista, em seguida, selecione **aplicação de exportação** da barra de ferramentas. Escolha JSON para exportar para backup, escolha **exportar para contêiner** para [usar esse aplicativo em um contêiner Luis](luis-container-howto.md).  

