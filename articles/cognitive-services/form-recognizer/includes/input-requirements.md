---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657848"
---
O reconhecedor de formulário funciona em documentos de entrada que atendem a estes requisitos:

* O formato deve ser JPG, PNG ou PDF (texto ou verificado). Os PDFs inseridos em texto são melhores porque não há possibilidade de erro na extração e na localização de caracteres.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* O tamanho do arquivo deve ser inferior a 4 MB.
* Para imagens, as dimensões devem estar entre 600 x 100 pixels e 4200 x 4200 pixels.
* Se examinados em documentos de papel, os formulários devem ser verificações de alta qualidade.
* O texto deve usar o alfabeto latino (caracteres em inglês).
* Os dados devem conter chaves e valores.
* As chaves podem aparecer acima ou à esquerda dos valores, mas não abaixo ou à direita.

No momento, o reconhecedor de formulário não oferece suporte a esses tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos ou células mescladas e assim por diante).
* Caixas de seleção ou botões de opção.
* Documentos PDF com mais de 50 páginas.
