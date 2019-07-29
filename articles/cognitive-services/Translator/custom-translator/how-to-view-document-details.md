---
title: Detalhes do documento-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: A página lista de documentos mostra o primeiro 10 documento em seu espaço de trabalho. Para cada um dos documentos, ele exibe o nome, o emparelhamento, o tipo, o idioma, o carimbo de data/hora de carregamento e o endereço de email do usuário que carregou o documento.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595589"
---
# <a name="view-document-details"></a>Ver detalhes do documento

A página lista de documentos mostra o primeiro 10 documento em seu espaço de trabalho. Para cada um dos documentos, ele exibe o nome, o emparelhamento, o tipo, o idioma, o carimbo de data/hora de carregamento e o endereço de email do usuário que carregou o documento.

Clique em um documento individual para exibir a página de detalhes do documento. A página detalhes do documento exibe a lista de sentenças extraídas do documento.

- Por padrão, o idioma de "origem" é selecionado no campo suspenso, mas você pode alternar para exibir frases no idioma de destino.
- 20 frases são exibidas por página por padrão. Você pode usar o controle de paginação para navegar entre as páginas.

![detalhes do documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Eliminar um documento

O usuário deve ser um proprietário do espaço de trabalho para excluir o documento para excluir um documento. Além disso, se um documento estiver em uso por um modelo, ou seja, em qualquer parte do processo de treinamento ou em qualquer parte do processo de implantação, o documento não poderá ser excluído.

1. Ir para a página do documento
2.  Focalize qualquer registro de documento e clique no ícone de lixeira.

    ![Excluir documento](media/how-to/how-to-delete-document-1.png)

3.  Confirmar exclusão.

    ![Excluir confirmação](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba [como treinar um modelo](how-to-train-model.md).
