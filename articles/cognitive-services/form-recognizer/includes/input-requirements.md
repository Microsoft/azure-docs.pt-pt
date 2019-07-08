---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592624"
---
Formulário reconhecedor funciona em documentos de entrada que cumpram estes requisitos:

* Formato tem de ser JPG, PNG ou PDF (texto ou digitalizados). Incorporado no texto de PDFs são melhor porque não existe nenhuma possibilidade de erro na extração de carateres e localização.
* Tamanho do ficheiro tem de ser inferior a 4 megabytes (MB).
* Para imagens, dimensões tem de ser entre 50 x 50 pixéis e 4200 x 4200 pixels.
* Se analisados de documentos de documento, formulários devem ser análises de alta qualidade.
* Texto tem de utilizar o alfabeto latino (carateres em inglês).
* Dados devem ser impressa (não código).
* Dados tem de conter as chaves e valores.
* As chaves podem aparecer acima ou à esquerda dos valores, mas nunca abaixo ou à direita.

Formulário reconhecedor atualmente não suporta estes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos intercalados ou células e assim por diante).
* Botões de caixas de seleção ou opção.
* Mais de 50 páginas dos documentos em PDF.