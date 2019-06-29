---
title: Formatos de documento e convenções de nomenclatura - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia nos formatos de documento e a Convenção de nomenclatura no Translator personalizado. Esse conceito ajuda a gerir os nomes de documentos abd melhor evitar conflitos de nomenclatura.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2f7a83be510e608bb3f630a2fb1860502d8e4475
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443414"
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

Para dicionários, personalizado Translator suporta todos os formatos de ficheiro que são suportados para conjuntos de formação. Se estiver a utilizar um dicionário de Excel, a primeira linha / linha da folha de cálculo deve ser códigos de idioma.

## <a name="zip-file-formats"></a>Formatos de arquivo zip

Documentos podem ser agrupados num arquivo zip único e carregados. O suporte de tradutor de personalizado zip formatos de arquivo (ZIP, GZ e TGZ).

Cada documento no ficheiro zip com a extensão TXT, HTML, HTM, PDF, DOCX, ALINHAR tem de seguir essa convenção de nomenclatura:

{nome do documento} \_{código de idioma} em que {nome do documento} é o nome do seu documento, {código de idioma} é o LanguageID ISO (dois caracteres), que indica que o documento contém as frases nesse idioma. Tem de existir um caráter de sublinhado (_) antes do código de idioma.

Por exemplo, para carregar os dois documentos paralelos dentro de um zip para um inglês para espanhol sistema, os ficheiros devem ter o nome "data_en" e "data_es".

Arquivos de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não são necessários para seguir a Convenção de nomenclatura de idioma específico.  

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criar e geri-los.
