---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: 324fde5198fe3469e3979cf5cd102715118a49aa
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467326"
---
O Form Recogniser trabalha em documentos de entrada que satisfazem estes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou digitalizado) ou TIFF. Os PDFs incorporados por texto são os melhores porque não há possibilidade de erro na extração e localização do carácter.
* O tamanho do ficheiro deve ser inferior a 50 MB.
* As dimensões da imagem devem estar entre 50 x 50 pixels e 10000 x 10000 pixels.
* As dimensões PDF devem ser no máximo 17 x 17 polegadas, correspondentes aos tamanhos legais ou a3 e menores.
* No caso de PDF e TIFF, apenas as primeiras 200 páginas são processadas (com uma subscrição de nível livre, apenas as duas primeiras páginas são processadas).
* O tamanho total do conjunto de dados de formação deve ser de 500 páginas ou menos.
* Se os seus PDFs estiverem bloqueados por palavra-passe, deve remover o bloqueio antes de os submeter.
* Se digitalizados a partir de documentos em papel, os formulários devem ser digitalizados de alta qualidade.
* O texto deve utilizar o alfabeto latino (caracteres ingleses).
* Para uma aprendizagem não supervisionada (sem dados rotulados), os dados devem conter chaves e valores.
* Para uma aprendizagem não supervisionada (sem dados rotulados), as chaves devem aparecer acima ou à esquerda dos valores; não podem aparecer abaixo ou à direita.
