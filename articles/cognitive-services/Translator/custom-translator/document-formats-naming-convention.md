---
title: Formatos documentais e convenções de nomeação - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia sobre formatos de documentos e convenção de nomeação em Tradutor Personalizado. Este conceito ajuda a gerir os nomes de documentos melhor abd evitar nomear conflitos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595849"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formatos documentais e orientação da convenção de nomeação

Qualquer ficheiro utilizado para tradução personalizada deve ter pelo menos **quatro** caracteres de comprimento.

Esta tabela inclui todos os formatos de ficheirosuportados que pode utilizar para construir o seu sistema de tradução:

| Formato            | Extensões   | Descrição                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Um formato de documento paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.                                                                                                                                                              |
| TMX               | . TMX         | Um formato de documento paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.                                                                                                                                                              |
| ZIP               | . ZIP         | ZIP é um formato de arquivo.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Um formato Microsoft para documentos paralelos                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Documento do Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . PDF         | Documento portátil Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . HTML, . HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| Ficheiro de texto         | . TXT         | UTF-16 ou UTF-8 ficheiros de texto codificados. O nome do ficheiro não deve conter caracteres japoneses.                                                                                                                                                                                        |
| Ficheiro de texto alinhado | . ALINHAR       | A `.ALIGN` extensão é uma extensão especial que pode usar se souber que as frases no par de documentos estão perfeitamente alinhadas. Se fornecer `.ALIGN` um ficheiro, o Tradutor Personalizado não irá alinhar as frases para si. |
| Arquivo Excel        | . XLSX        | Arquivo Excel (2013 ou mais tarde). Primeira linha/linha da folha de cálculo deve ser o código de idioma.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formatos dicionários

Para dicionários, o Custom Tradutor suporta todos os formatos de ficheiros que são suportados para conjuntos de formação. Se estiver a utilizar um dicionário Excel, a primeira linha/linha da folha de cálculo deve ser códigos linguísticos.

## <a name="zip-file-formats"></a>Formatos de ficheirozip

Os documentos podem ser agrupados num único ficheiro zip e enviados. O Tradutor Personalizado suporta formatos de ficheirozip (ZIP, GZ e TGZ).

Cada documento no ficheiro zip com a extensão TXT, HTML, HTM, PDF, DOCX, ALIGN deve seguir esta convenção de nomeação:

{nome do documento} \_{código de idioma} onde {nome de documento} é o nome do seu documento, {código de idioma} é o ID de Idioma ISO (dois caracteres), indicando que o documento contém frases nessa língua. Deve haver um sublinhado (_) antes do código linguístico.

Por exemplo, para fazer o upload de dois documentos paralelos dentro de um zip para um sistema inglês para o espanhol, os ficheiros devem ser denominados "data_en" e "data_es".

Os ficheiros de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não são obrigados a seguir a convenção específica de nomeação de línguas.  

## <a name="next-steps"></a>Passos seguintes

- Leia sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criá-los e geri-los.
