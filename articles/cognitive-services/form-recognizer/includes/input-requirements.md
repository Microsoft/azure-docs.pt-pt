---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459732"
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