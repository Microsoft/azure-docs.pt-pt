---
title: Atributos de entidade de papel – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de papel na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704945"
---
# <a name="paper-entity"></a>Entidade de papel

<sub>* Os atributos abaixo são específicos para a entidade de papel. (Ty = ' 0 ')</sub>


Nome    |Descrição                                        |Type       | Operações
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |ID de entidade                                          |Int64      |É igual a
It      |Título do papel                                        |Cadeia     |Seja<br/>StartsWith
B       |Código de idioma de papel separado\@por ""\@\@          |Cadeia     |É igual a
S       |Ano do papel                                         |Int32      |Seja<br/>IsBetween
D       |Data do papel                                         |Date       |Seja<br/>IsBetween
CC      |Contagem de citações                                     |Int32      |nenhum  
CONTROLCENTER     |Contagem estimada de citações                           |Int32      |nenhum
AA.AuN  |Nome do autor                                        |Cadeia     |Seja<br/>StartsWith
AA.AuId |ID do autor                                          |Int64      |É igual a
AA.AfN  |Nome da afiliação do autor                            |Cadeia     |Seja<br/>StartsWith
AA.AfId |ID de afiliação do autor                              |Int64      |É igual a
AA.S    |Ordem de autor do papel                         |Int32      |É igual a
F.FN    |Campo do nome do estudo                                |Cadeia     |Seja<br/>StartsWith
F.FId   |Campo da ID do estudo                                  |Int64      |É igual a
J.JN    |Nome do diário                                       |Cadeia     |Seja<br/>StartsWith
J.JId   |ID do diário                                         |Int64      |É igual a
C.CN    |Nome da série de conferências                             |Cadeia     |Seja<br/>StartsWith
C. CId   |ID da série de conferências                               |Int64      |É igual a
Eliminá     |ID dos papéis referenciados                              |Int64[]    |É igual a
Q       |Palavras do título e do resumo do papel                |Cadeia de caracteres []   |É igual a
E       |Metadados estendidos (consulte a tabela abaixo)                |Cadeia     |nenhum  
        


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

Nome    | Descrição               
--------|---------------------------    
DN      | Nome para exibição do papel 
D       | Fontes-lista de fontes da Web do documento, classificadas por classificação estática
S. Ty    | Tipo de origem (1: HTML, 2: texto, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U     | URL de Origem
VFN     | Nome completo do local-nome completo do diário ou da conferência
VSN     | Nome curto do local-nome curto do diário ou da conferência
L       | Volume do diário de volume
BV      | Nome do diário
BT      | 
PB      | Abreviações de diário
I       | Problema de diário de emissão
FP      | FirstPage – primeira página de papel
LP      | LastPage – última página de papel
DOI     | Identificador de objeto digital
CC      | Contextos de citação – lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "as cachorros lentas são um informativo histórico, conforme mostrado no artigo 123"]})
IA      | Resumo invertido
IA.IndexLength| Número de itens no índice (contagem de palavras abstratas)
IA. InvertedIndex| Lista de palavras abstratas e sua posição correspondente no resumo original (por exemplo, [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}])
