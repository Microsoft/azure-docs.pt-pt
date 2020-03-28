---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379447"
---
O Reconhecimento de Formulários trabalha em documentos de entrada que satisfaçam estes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou digitalizado) ou TIFF. Os PDFs incorporados em texto são os melhores porque não há possibilidade de erro na extração e localização de caracteres.
* Se os seus PDFs estiverem bloqueados por palavra-passe, tem de remover o bloqueio antes de os submeter.
* Os documentos PDF e TIFF devem ter 200 páginas ou menos, e o tamanho total do conjunto de dados de formação deve ser de 500 páginas ou menos.
* Para as imagens, as dimensões devem estar entre 600 x 100 pixels e 4200 x 4200 pixels.
* Se digitalizados a partir de documentos em papel, os formulários devem ser digitalizados de alta qualidade.
* O texto deve utilizar o alfabeto latino (caracteres em inglês).
* Para uma aprendizagem não supervisionada (sem dados rotulados), os dados devem conter chaves e valores.
* Para uma aprendizagem não supervisionada (sem dados rotulados), as teclas devem figurar acima ou à esquerda dos valores; não podem aparecer abaixo ou à direita.

O Reconhecimento de Formulários não suporta atualmente este tipo de dados de entrada:

* Mesas complexas (mesas aninhadas, cabeçalhos ou células fundidos, e assim por diante).
* Caixas de verificação ou botões de rádio.
