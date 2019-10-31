---
title: Atributos de entidade de papel – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de papel na API de Conhecimento Acadêmico.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161726"
---
# <a name="paper-entity"></a>Entidade de papel

<sub>* Os atributos abaixo são específicos para a entidade de papel. (Ty = ' 0 ')</sub>

Nome | Descrição | Tipo | Operations
--- | --- | --- | ---
Suaviza. AfId | ID de afiliação do autor | Int64 | Igual a
Suaviza. AfN | Nome da afiliação do autor | String | Equals, StartsWith
Suaviza. AuId | ID do autor | Int64 | Igual a
Suaviza. AuN | Nome do autor normalizado | String | Equals, StartsWith
Suaviza. DAuN | Nome do autor original | String | Nenhuma
Suaviza. DAfN | Nome da afiliação original | String | Nenhuma
Suaviza. & | Posição numérica na lista de autores | Int32 | Igual a
CC | Contagem de citações | Int32 | Nenhuma  
C. CId | ID da série de conferências | Int64 | Igual a
C.CN | Nome da série de conferências | String | Equals, StartsWith
D | Data de publicação no formato AAAA-MM-DD | Date | Equals, isBetween
Oriental | Metadados estendidos (consulte a tabela abaixo) | String | N/A  
ControlCenter | Contagem estimada de citações | Int32 | Nenhuma
F. DFN | Campo original do nome do estudo | String | Nenhuma
F. FId | Campo da ID do estudo | Int64 | Igual a
c. FN | Campo normalizado do nome do estudo | String | Equals, StartsWith
Id | ID do papel | Int64 | Igual a
J. JId | ID do diário | Int64 | Igual a
J. JN | Nome do diário | String | Equals, StartsWith
Pt | Tipo de publicação (0: desconhecido, 1: artigo do diário, 2: patente, 3: papel da conferência, 4: capítulo do livro, 5: livro, 6: entrada de referência de livro, 7: DataSet, 8: Repository | String | Igual a
Eliminá | Lista de IDs de papel referenciados | Int64 [] | Igual a
It | Título normalizado | String | Equals, StartsWith
W | Palavras exclusivas no título | Cadeia de caracteres [] | Igual a
S | Ano publicado | Int32 | Equals, isBetween

## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

Nome | Descrição               
--- | ---
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência)
BV | Nome do local do BibTex
CC | Contextos de citação – lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "as cachorros lentas são um informativo histórico, conforme mostrado no artigo 123"]})
IGNORA | Título do papel original
DOI | Identificador de objeto digital
FP | Primeira página de papel na publicação
I | Problema de publicação
IA | Resumo invertido
IA. IndexLength | Número de itens no índice (contagem de palavras abstratas)
IA. InvertedIndex | Lista de palavras abstratas e sua posição correspondente no resumo original (por exemplo, [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}])
LP | Última página de papel na publicação
PB | Publicador
S | Fontes-lista de fontes da Web do documento, classificadas por classificação estática
S. Ty | Tipo de origem (1: HTML, 2: texto, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | URL de origem
L | Volume da publicação
VFN | Nome completo do local do diário ou da conferência
VSN | Nome curto do local do diário ou da conferência
