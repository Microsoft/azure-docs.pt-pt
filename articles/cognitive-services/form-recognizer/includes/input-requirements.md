---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379447"
---
O reconhecedor de formulário funciona em documentos de entrada que atendem a estes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou verificado) ou TIFF. Os PDFs inseridos em texto são melhores porque não há possibilidade de erro na extração e na localização de caracteres.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* Documentos PDF e TIFF devem ter 200 páginas ou menos, e o tamanho total do conjunto de dados de treinamento deve ser de 500 páginas ou menos.
* Para imagens, as dimensões devem estar entre 600 x 100 pixels e 4200 x 4200 pixels.
* Se examinados em documentos de papel, os formulários devem ser verificações de alta qualidade.
* O texto deve usar o alfabeto latino (caracteres em inglês).
* Para aprendizado não supervisionado (sem dados rotulados), os dados devem conter chaves e valores.
* Para aprendizado não supervisionado (sem dados rotulados), as chaves devem aparecer acima ou à esquerda dos valores; Eles não podem aparecer abaixo ou à direita.

No momento, o reconhecedor de formulário não oferece suporte a esses tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos ou células mescladas e assim por diante).
* Caixas de seleção ou botões de opção.
