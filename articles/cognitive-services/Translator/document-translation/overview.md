---
title: O que é tradução de documentos?
description: Uma visão geral do serviço de tradução e processo de tradução de documentos baseados na nuvem.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968174"
---
# <a name="what-is-document-translation-preview"></a>O que é Tradução de Documentos (Pré-visualização)?

Document Translation é uma característica baseada na nuvem do serviço [Azure Tradutor](../translator-info-overview.md) e faz parte da família Azure Cognitive Service das APIs REST. A API de Tradução documental traduz documentos de e para 90 línguas e dialetos, preservando a estrutura documental e o formato de dados.

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](get-started-with-document-translation.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.
* [**Os guias de como**](create-sas-tokens.md) fazer contêm instruções para utilizar a funcionalidade de formas mais específicas ou personalizadas.  

## <a name="document-translation-key-features"></a>Características-chave da tradução de documentos

| Funcionalidade | Descrição |
| ---------| -------------|
| **Traduzir ficheiros grandes**| Traduza documentos inteiros assíncronosamente.|
|**Traduzir inúmeros ficheiros**|Traduza vários ficheiros para e a partir de 90 idiomas e dialetos.|
|**Preservar apresentação de ficheiros de origem**| Traduza os ficheiros preservando o layout e o formato originais.|
|**Aplicar tradução personalizada**| Traduza documentos utilizando modelos de tradução geral e [personalizado.](../customization.md#custom-translator)|
|**Aplicar glossários personalizados**|Traduza documentos utilizando glossários personalizados.|

## <a name="how-to-get-started"></a>Como começar?

No nosso guia de como fazer, aprenderá a começar rapidamente a usar o Document Tradutor. Para começar, vai precisar de uma [conta Azure](https://azure.microsoft.com/free/cognitive-services/)ativa.  Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Introdução](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formatos de documento suportados

Os seguintes tipos de ficheiros de documentos são suportados pela Tradução documental:

| Tipo de arquivo| Extensão de nome de ficheiro|Description|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat formato de documento portátil|
|HTML|.html|Linguagem de marcação de texto hiper.|
|Formato de ficheiro de intercâmbio de localização|.xlf. , xliff| Um formato documental paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.|
|Microsoft Excel|.xlsx|Um ficheiro de folha de cálculo para análise de dados e documentação.|
|Microsoft Outlook|.msg|Uma mensagem de e-mail criada ou guardada dentro do Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| Um ficheiro de apresentação utilizado para exibir conteúdo num formato de apresentação de diapositivos.|
|Microsoft Word|.docx| Um ficheiro de documento de texto.|
|Separados valores/SEPARADOS|.tsv/.tab| Um ficheiro de dados brutos delimitado por separadores utilizado por programas de folha de cálculo.|
|Texto|.txt| Um documento de texto não testado.|

## <a name="supported-glossary-formats"></a>Formatos glossários suportados

Os seguintes tipos de ficheiros glossários são suportados pela Tradução documental:

| Tipo de arquivo| Extensão de nome de ficheiro|Description|
|---|---|--|
|Formato de ficheiro de intercâmbio de localização|.xlf. , xliff| Um formato documental paralelo, exportação de sistemas de memória de tradução. As línguas utilizadas são definidas dentro do ficheiro.|
|Separados valores/SEPARADOS|.tsv/.tab| Um ficheiro de dados brutos delimitado por separadores utilizado por programas de folha de cálculo.|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](get-started-with-document-translation.md)
