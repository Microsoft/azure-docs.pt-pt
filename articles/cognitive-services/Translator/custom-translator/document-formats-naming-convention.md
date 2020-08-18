---
title: Formatos de documentos e convenções de nomeação - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia sobre formatos de documentos e convenção de nomeação em Custom Tradutor. Este conceito ajuda a gerir melhor os nomes dos documentos e a evitar nomear conflitos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 46a19cd4396e598a74f9099727ac80d44c0a44f5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507251"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formatos de documentos e orientação da convenção de nomeação

Qualquer ficheiro utilizado para tradução personalizada deve ter pelo menos **quatro** caracteres de comprimento.

Esta tabela inclui todos os formatos de ficheiros suportados que pode utilizar para construir o seu sistema de tradução:

| Formato            | Extensões   | Descrição                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Um formato documental paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.                                                                                                                                                              |
| TMX               | . TMX         | Um formato documental paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.                                                                                                                                                              |
| ZIP               | . ZIP         | ZIP é um formato de arquivo de arquivo.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Um formato Microsoft para documentos paralelos                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Documento do Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . PDF         | Documento portátil Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . HTML, . HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| Ficheiro de texto         | . TXT         | UtF-16 ou UTF-8 ficheiros de texto codificados. O nome do ficheiro não deve conter caracteres japoneses.                                                                                                                                                                                        |
| Arquivo de texto alinhado | . ALINHAR       | A extensão `.ALIGN` é uma extensão especial que pode usar se souber que as frases no par do documento estão perfeitamente alinhadas. Se fornecer um `.ALIGN` ficheiro, o Tradutor Personalizado não alinhará as frases para si. |
| Arquivo Excel        | . XLSX        | Ficheiro Excel (2013 ou mais tarde). A primeira linha/linha da folha de cálculo deve ser o código linguístico.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formatos dicionários

Para dicionários, o Custom Tradutor suporta todos os formatos de ficheiros suportados para conjuntos de treino. Se estiver a utilizar um dicionário Excel, a primeira linha/linha da folha de cálculo deve ser códigos linguísticos.

## <a name="zip-file-formats"></a>Formatos de ficheiro zip

Os documentos podem ser agrupados num único ficheiro zip e carregados. O Tradutor Personalizado suporta formatos de ficheiros zip (ZIP, GZ e TGZ).

Cada documento no ficheiro zip com a extensão TXT, HTML, HTM, PDF, DOCX, ALIGN deve seguir esta convenção de nomeação:

{nome do documento} \_ {language code} where {document name} is the name of your document, {language code} is the ISO LanguageID (dois caracteres), indicando que o documento contém frases nesse idioma. Deve haver um sublinhado (_) antes do código linguístico.

Por exemplo, para carregar dois documentos paralelos dentro de um zip para um sistema inglês para espanhol, os ficheiros devem ser denominados "data_en" e "data_es".

Os ficheiros de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não são obrigados a seguir a convenção específica de nomeação de idiomas.  

## <a name="next-steps"></a>Passos seguintes

- Leia sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criá-los e geri-los.
