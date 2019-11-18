---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de afiliação na API Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143930"
---
# <a name="affiliation-entity"></a>Entidade de afiliação

> [!NOTE]
> Os atributos a seguir são específicos para a entidade de afiliação. (Ty = ' 5 ')

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
AfN | Nome normalizado de afiliação |Cadeia |Igual a
CC | Contagem de citação total de afiliação |Int32        |nenhuma  
DAfN | Nome de exibição da afiliação |Cadeia |nenhuma
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | Nenhum
ControlCenter | Contagem total de citação estimada de afiliação |Int32 |nenhuma
Id | ID da entidade |Int64 |Igual a
PC | Número total de publicações escritas em filiais com esta entidade | Int32 | Nenhum

## <a name="extended"></a>Tensível

> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
PC | Número total de publicações escritas em filiais com esta entidade | Int32 | Nenhum
