---
title: Formatos de documento e convenções de nomenclatura – Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia sobre formatos de documento e Convenção de nomenclatura no Tradutor personalizado. Esse conceito ajuda a gerenciar nomes de documentos melhor Abd evitar conflitos de nomenclatura.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595849"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Orientação sobre formatos de documento e Convenção de nomenclatura

Qualquer arquivo usado para tradução personalizada deve ter pelo menos **quatro** caracteres de comprimento.

Esta tabela inclui todos os formatos de arquivo com suporte que você pode usar para criar seu sistema de Tradução:

| Formato            | Extensões   | Descrição                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | Um formato de documento paralelo, exportação de sistemas de memória de tradução. Os idiomas usados são definidos dentro do arquivo.                                                                                                                                                              |
| TMX               | .TMX         | Um formato de documento paralelo, exportação de sistemas de memória de tradução. Os idiomas usados são definidos dentro do arquivo.                                                                                                                                                              |
| RÁPIDA               | . RÁPIDA         | ZIP é um formato de arquivo morto.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Um formato Microsoft para documentos paralelos                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Documento do Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Documento portátil do Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| Arquivo de texto         | . LOCALIZADO         | Arquivos de texto codificados em UTF-16 ou UTF-8. O nome do arquivo não deve conter caracteres japoneses.                                                                                                                                                                                        |
| Arquivo de texto alinhado | . ALINHA       | A extensão `.ALIGN` é uma extensão especial que você pode usar se souber que as frases no par de documentos estão perfeitamente alinhadas. Se você fornecer um `.ALIGN` arquivo, o tradutor personalizado não alinhará as frases para você. |
| Ficheiro do Excel        | .XLSX        | Arquivo do Excel (2013 ou posterior). A primeira linha/linha da planilha deve ser um código de idioma.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formatos de dicionário

Para dicionários, o tradutor personalizado dá suporte a todos os formatos de arquivo que têm suporte para conjuntos de treinamento. Se você estiver usando um dicionário do Excel, a primeira linha/linha da planilha deverá ser de códigos de idioma.

## <a name="zip-file-formats"></a>Formatos de arquivo zip

Os documentos podem ser agrupados em um único arquivo zip e carregados. O tradutor personalizado dá suporte aos formatos de arquivo zip (ZIP, GZ e TGZ).

Cada documento no arquivo zip com a extensão TXT, HTML, HTM, PDF, DOCX, ALINHAr deve seguir esta Convenção de nomenclatura:

{nome do documento} \_{código de idioma} em que {nome do documento} é o nome do documento, {código de idioma} é o idioma ISO (dois caracteres), indicando que o documento contém sentenças nesse idioma. Deve haver um sublinhado (_) antes do código de idioma.

Por exemplo, para carregar dois documentos paralelos em um zip para um sistema em inglês para espanhol, os arquivos devem ser nomeados como "data_en" e "data_es".

Os arquivos de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não são necessários para seguir a Convenção de nomenclatura de idioma específica.  

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criá-los e gerenciá-los.
