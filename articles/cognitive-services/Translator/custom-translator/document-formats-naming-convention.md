---
title: Formatos de documento e convenções de nomenclatura - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia nos formatos de documento e a Convenção de nomenclatura no Translator personalizado. Esse conceito ajuda a gerir os nomes de documentos abd melhor evitar conflitos de nomenclatura.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: dec5ce37aa3b4e9d4d6fcab964c1e48b606a4cd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60512914"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formatos e orientações de convenção de nomenclatura de documentos

Qualquer ficheiro utilizado para a tradução personalizada deve ter pelo menos **quatro** carateres de comprimento.

Esta tabela inclui todos os formatos de ficheiro suportados que pode utilizar para criar o seu sistema de tradução:

| Formato            | Extensões   | Descrição                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | Um formato de documento paralela, a exportação de sistemas de memória de tradução. As linguagens utilizadas são definidas dentro do arquivo.                                                                                                                                                              |
| TMX               | .TMX         | Um formato de documento paralela, a exportação de sistemas de memória de tradução. As linguagens utilizadas são definidas dentro do arquivo.                                                                                                                                                              |
| ZIP               | . ZIP         | ZIP é um formato de arquivo.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Um formato para documentos paralelos da Microsoft                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Documento do Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Documento portátil do Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| Arquivo de texto         | .TXT         | UTF-16 ou UTF-8 codificado em arquivos de texto. O nome de ficheiro não pode conter os caracteres japoneses.                                                                                                                                                                                        |
| Arquivo de texto alinhado | . ALINHAR       | A extensão `.ALIGN` é uma extensão especial que pode utilizar se souber que as frases do par de documento são perfeitamente alinhadas. Se fornecer um `.ALIGN` arquivo, Translator personalizado não será alinhado as frases para. |
| Ficheiro do Excel        | .XLSX        | Ficheiro do Excel (2013 ou posterior). Em primeiro lugar da linha a linha da folha de cálculo deve ser o código de idioma.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formatos de dicionário

Para dicionários, personalizado Translator suporta todos os formatos de arquivo que aqueles são suportadas para o conjunto de preparação. Se estiver a utilizar o dicionário do Excel, certifique-se de que a primeira linha / linha da folha de cálculo deve ser códigos de idioma.

## <a name="zip-file-formats"></a>Formatos de arquivo zip

Documentos podem ser agrupados num arquivo zip único e carregados. O suporte de tradutor de personalizado zip formatos de arquivo (ZIP, GZ e TGZ).

Cada documento no ficheiro zip com a extensão TXT, HTML, HTM, PDF, DOCX, ALINHAR tem de seguir essa convenção de nomenclatura:

{nome do documento} \_{código de idioma} em que {nome do documento} é o nome do seu documento, {código de idioma} é o LanguageID ISO (dois caracteres), que indica que o documento contém as frases nesse idioma. Tem de existir um caráter de sublinhado (_) antes do código de idioma.

Por exemplo, para carregar os dois documentos paralelos dentro de um zip para um inglês para espanhol sistema, os ficheiros devem ter o nome "data_en" e "data_es".

Arquivos de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não são necessários para seguir a Convenção de nomenclatura de idioma específico.  

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criar e geri-los.
