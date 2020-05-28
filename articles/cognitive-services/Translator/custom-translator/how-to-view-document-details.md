---
title: Detalhes do documento - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: A página da lista de documentos mostra o primeiro documento de 10 no seu espaço de trabalho. Para cada um dos documentos, exibe o nome, emparelhamento, tipo, idioma, carimbo de tempo de upload e o endereço de e-mail do utilizador que fez o upload do documento.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 22c982babbed1322e97127379be75e7b070ed81f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994247"
---
# <a name="view-document-details"></a>Ver detalhes do documento

A página da lista de documentos mostra o primeiro documento de 10 no seu espaço de trabalho. Para cada um dos documentos, exibe o nome, emparelhamento, tipo, idioma, carimbo de tempo de upload e o endereço de e-mail do utilizador que fez o upload do documento.

Clique num documento individual para ver a página de detalhes do documento. A página de detalhes do documento mostra a lista de frases extraídas do documento.

- Por padrão, a linguagem "fonte" é selecionada no campo de dropdown, mas pode alternar para ver frases na linguagem-alvo.
- 20 frases são apresentadas por página por padrão. Pode utilizar o controlo de paginação para navegar entre páginas.

![detalhes do documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Eliminar um documento

O utilizador deve ser proprietário de um espaço de trabalho para apagar documento para apagar um documento. Além disso, se um documento estiver a ser utilizado por um modelo, que esteja em qualquer parte do processo de treino ou em qualquer parte do processo de implementação, o documento não pode ser eliminado.

1. Vá para a página de documentos
2.  Passe em qualquer registo de documentoe clique no ícone do lixo.

    ![Eliminar documento](media/how-to/how-to-delete-document-1.png)

3.  Confirmar Apagar.

    ![Eliminar confirmar](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Próximos passos

- Aprenda [a treinar um modelo.](how-to-train-model.md)
