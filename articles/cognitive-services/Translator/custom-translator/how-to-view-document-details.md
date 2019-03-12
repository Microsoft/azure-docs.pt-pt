---
title: Detalhes do documento - Translator personalizado
titleSuffix: Azure Cognitive Services
description: A página de lista de documento mostra as primeiras 10 documento na sua área de trabalho. Para cada um dos documentos, ele exibe o nome, emparelhamento, tipo, idioma, carimbo de data / hora de carregamento e o endereço de e-mail do utilizador que carregou o documento.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: d380c8bcdcb4954901df0b460269493c5be0a6de
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777393"
---
# <a name="view-document-details"></a>Ver detalhes do documento

A página de lista de documento mostra as primeiras 10 documento na sua área de trabalho. Para cada um dos documentos, ele exibe o nome, emparelhamento, tipo, idioma, carimbo de data / hora de carregamento e o endereço de e-mail do utilizador que carregou o documento.

Clique num documento individual para ver a página de detalhes do documento. A página de detalhes do documento apresenta a lista de frases extraídas do documento.

- Por predefinição, a "origem" idioma é selecionado no campo de lista pendente, mas pode alternar para ver as frases no idioma de destino.
- 20 frases são apresentadas por página por predefinição. Pode usar o controle de paginação para navegar entre páginas.

![Detalhes do documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Eliminar um documento

Utilizador tem de ser um proprietário de área de trabalho para eliminar um documento para eliminar um documento. Além disso, se um documento está a ser utilizado por um modelo, o que está em qualquer parte do processo de treinamento ou qualquer parte do processo de implantação, não é possível eliminar o documento.

1. Aceda à página do documento
2.  Coloque o cursor em qualquer registro de documento e clique no ícone de caixote do lixo.

    ![Eliminar documento](media/how-to/how-to-delete-document-1.png)

3.  Confirme eliminação.

    ![Confirmação de eliminação](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais [como preparar um modelo](how-to-train-model.md).
