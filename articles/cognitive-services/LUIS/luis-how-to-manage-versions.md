---
title: Gerir versões
titleSuffix: Language Understanding - Azure Cognitive Services
description: Versões permitem-lhe criar e publicar modelos diferentes. É uma boa prática clonar o modelo de Active Directory atual para uma versão diferente da aplicação antes de efetuar alterações ao modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: f919651cf39d1f2c48fca87da935e49e3affa79f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198908"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Utilizar uma versão para editar e teste sem afetar as aplicações de teste ou de produção

Versões permitem-lhe criar e publicar modelos diferentes. Uma boa prática é clonar o modelo de Active Directory atual para um diferente [versão](luis-concept-version.md) da aplicação antes de efetuar alterações ao modelo. 

Para trabalhar com as versões, abra a sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página e, em seguida, selecione **gerir** na barra superior, em seguida, selecione **versões** na navegação à esquerda. 

A lista de versões mostra as versões que são publicadas, onde eles são publicados e qual é a versão está atualmente ativa. 

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

[![Secção Gerir, página de versões, tomar uma ação de versão](./media/luis-how-to-manage-versions/versions-other.png "secção Gerir, página de versões, tomar uma ação de versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Versão de importação

1. Selecione **versão de importação** da barra de ferramentas. 

2. Na **nova versão de importação** janela pop-up, introduza o novo nome de versão de dez caractere. Apenas terá de definir um ID de versão, se a versão no ficheiro JSON já existe na aplicação.

    ![Gerir a seção, página de versões, importar a nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Depois de importar uma versão, a nova versão torna-se a versão do Active Directory.

### <a name="import-errors"></a>Importar erros

* Erros de atomizador: Se obtiver um **erro atomizador** ao importar, está a tentar importar uma versão que utiliza outra [atomizador](luis-language-support.md#custom-tokenizer-versions) que a aplicação utiliza atualmente. Para corrigir este problema, consulte [migrar entre versões atomizador](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **elimine** uma versão, selecione uma versão da lista, em seguida, selecione **eliminar** da barra de ferramentas. Selecione **Ok**. 
* Para **mudar o nome** uma versão, selecione uma versão da lista, em seguida, selecione **mudar o nome** da barra de ferramentas. Introduza o novo nome e selecione **feito**. 
* Para **exportar** uma versão, selecione uma versão da lista, em seguida, selecione **aplicação de exportação** da barra de ferramentas. Escolha o JSON para exportar para cópia de segurança, escolha **exportar para o contentor** ao [utilizar esta aplicação num contentor do LUIS](luis-container-howto.md).  

