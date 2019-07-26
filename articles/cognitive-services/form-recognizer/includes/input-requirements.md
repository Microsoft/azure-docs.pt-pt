---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 73a7795629a94395b43bfca191cec7b2c4773611
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481535"
---
O reconhecedor de formulário funciona em documentos de entrada que atendem a estes requisitos:

* O formato deve ser JPG, PNG ou PDF (texto ou verificado). Os PDFs inseridos em texto são melhores porque não há possibilidade de erro na extração e na localização de caracteres.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* O tamanho do arquivo deve ser inferior a 4 megabytes (MB).
* Para imagens, as dimensões devem estar entre 50 x 50 pixels e 4200 x 4200 pixels.
* Se examinados em documentos de papel, os formulários devem ser verificações de alta qualidade.
* O texto deve usar o alfabeto latino (caracteres em inglês).
* Os dados devem conter chaves e valores.
* As chaves podem aparecer acima ou à esquerda dos valores, mas não abaixo ou à direita.

No momento, o reconhecedor de formulário não oferece suporte a esses tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos ou células mescladas e assim por diante).
* Caixas de seleção ou botões de opção.
* Documentos PDF com mais de 50 páginas.