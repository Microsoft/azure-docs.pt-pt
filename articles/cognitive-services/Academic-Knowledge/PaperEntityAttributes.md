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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123076"
---
# <a name="paper-entity"></a>Entidade de papel

> [!NOTE]
> Os atributos abaixo são específicos da entidade de papel. (Ty = ' 0 ')

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
AA.AfId | ID de afiliação do autor | Int64 | Igual a
AA.AfN | Nome da afiliação do autor | Cadeia | Equals, StartsWith
AA.AuId | ID do autor | Int64 | Igual a
AA.AuN | Nome do autor normalizado | Cadeia | Equals, StartsWith
Suaviza. DAuN | Nome do autor original | Cadeia | Nenhum
Suaviza. DAfN | Nome da afiliação original | Cadeia | Nenhum
AA.S | Posição numérica na lista de autores | Int32 | Igual a
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência) | Cadeia | Nenhum
BV | Nome do local do BibTex | Cadeia | Nenhum
C. CId | ID da série de conferências | Int64 | Igual a
C.CN | Nome da série de conferências | Cadeia | Equals, StartsWith
CC | Contagem de citações | Int32 | Nenhum  
CitCon | Contextos de citação</br></br>Lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "os cachorros lentos são um informativo histórico, conforme mostrado no artigo 123"]}) | Personalizado | Nenhum
D | Data de publicação no formato AAAA-MM-DD | Data | Equals, isBetween
DN | Título do papel original | Cadeia | Nenhum
DOI | Identificador de objeto digital | Cadeia | Equals, StartsWith
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | Nenhum
ControlCenter | Contagem estimada de citações | Int32 | Nenhum
F. DFN | Campo original do nome do estudo | Cadeia | Nenhum
F.FId | Campo da ID do estudo | Int64 | Igual a
F.FN | Campo normalizado do nome do estudo | Cadeia | Equals, StartsWith
FP | Primeira página de papel na publicação | Cadeia | Igual a
I | Problema de publicação | Cadeia | Igual a
IA | Resumo invertido | [InvertedAbstract](#invertedabstract) | Nenhum
Id | ID do papel | Int64 | Igual a
J.JId | ID do diário | Int64 | Igual a
J.JN | Nome do diário | Cadeia | Equals, StartsWith
LP | Última página de papel na publicação | Cadeia | Igual a
PB | Publicador | Cadeia | Nenhum
Pt | Tipo de publicação (0: desconhecido, 1: artigo do diário, 2: patente, 3: papel da conferência, 4: capítulo do livro, 5: livro, 6: entrada de referência de livro, 7: DataSet, 8: Repository | Cadeia | Igual a
Eliminá | Lista de IDs de papel referenciados | Int64[] | Igual a
S | Lista de URLs de origem do documento, classificada por relevância | [Fonte](#source)[] | Nenhum
It | Título normalizado | Cadeia | Equals, StartsWith
L | Volume da publicação | Cadeia | Igual a
VFN | Nome completo do local do diário ou da conferência | Cadeia | Nenhum
VSN | Nome curto do local do diário ou da conferência | Cadeia | Nenhum
W | Palavras exclusivas no título | Cadeia de caracteres [] | Igual a
S | Ano publicado | Int32 | Equals, isBetween

## <a name="extended"></a>Tensível
> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência) | Cadeia | Nenhum
BV | Nome do local do BibTex | Cadeia | Nenhum
CC | Contextos de citação</br></br>Lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "os cachorros lentos são um informativo histórico, conforme mostrado no artigo 123"]}) | Personalizado | Nenhum
DN | Título do papel original | Cadeia | Nenhum
DOI | Identificador de objeto digital | Cadeia | Nenhum
FP | Primeira página de papel na publicação | Cadeia | Nenhum
I | Problema de publicação | Cadeia | Nenhum
IA | Resumo invertido | [InvertedAbstract](#invertedabstract) | Nenhum
LP | Última página de papel na publicação | Cadeia | Nenhum
PB | Publicador | Cadeia | Nenhum
S | Lista de URLs de origem do documento, classificada por relevância | [Fonte](#source)[] | Nenhum
L | Volume da publicação | Cadeia | Nenhum
VFN | Nome completo do local do diário ou da conferência | Cadeia | Nenhum
VSN | Nome curto do local do diário ou da conferência | Cadeia | Nenhum

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Os atributos InvertedAbstract não podem ser solicitados diretamente como um atributo de retorno. Se você precisar de um atributo individual, deverá solicitar o atributo "IA" de nível superior, ou seja, para obter IA. Atributos de solicitação IndexLength = IA

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
IndexLength | Número de itens no índice (contagem de palavras abstratas) | Int32 | Nenhum
InvertedIndex | Lista de palavras abstratas e sua posição correspondente no resumo original (por exemplo, [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}]) | Personalizado | Nenhum

## <a name="source"></a>Origem

> [!IMPORTANT]
> Os atributos de origem não podem ser solicitados diretamente como um atributo de retorno. Se você precisar de um atributo individual, deverá solicitar o atributo "S" de nível superior, ou seja, obter os atributos de solicitação S. U = S

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
Ty | Tipo de URL de origem (1: HTML, 2: texto, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Cadeia | Nenhum
U | URL de origem | Cadeia | Nenhum
