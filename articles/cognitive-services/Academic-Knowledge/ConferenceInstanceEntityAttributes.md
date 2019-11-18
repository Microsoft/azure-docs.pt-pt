---
title: Atributos de entidade da instância de conferência – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Conheça os atributos que você pode usar com a entidade de instância de conferência na API Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146556"
---
# <a name="conference-instance-entity"></a>Entidade de instância de conferência

> [!NOTE]
> Os atributos a seguir são específicos para a entidade de instância de conferência. (Ty = ' 4 ')

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
CC      |Contagem de citação total da instância de conferência           |Int32      |Nenhum  
CD.D    |Data de um evento de instância de conferência    |Data       |Equals, isBetween
CD. T    |Título de um evento de instância de conferência   |Data       |Equals, isBetween
CIARD   |Data de conclusão do registro abstrato da instância de conferência  |Data       |Equals, isBetween
CIED    |Data de término da instância de conferência    |Data       |Equals, isBetween
CIFVD   |Data de conclusão da versão final da instância de conferência  |Data       |Equals, isBetween
CIL     |Local da instância de conferência    |Cadeia     |Equals, StartsWith
CIN     |Nome normalizado da instância de conferência |Cadeia        |Igual a
CINDD   |Data de notificação da instância de conferência   |Data       |Equals, isBetween
CISD    |Data de início da instância de conferência  |Data       |Equals, isBetween
CISDD   |Data de vencimento de envio da instância de conferência     |Data       |Equals, isBetween
DCN     |Nome de exibição da instância de conferência  |Cadeia      |Nenhum
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | Nenhum
ControlCenter     |Contagem de citação total estimada da instância de conferência |Int32      |Nenhum
FN | Nome completo da instância de conferência | Cadeia | Nenhum
Id      |ID da entidade                              |Int64      |Igual a
PC | Contagem de publicação total da instância de conferência | Int32 | Nenhum
PCS.CN  |Nome da série de conferência pai da instância |Cadeia  |Igual a
PCS.CId |ID da série de conferências pai da instância |Int64     |Igual a

## <a name="extended"></a>Tensível

> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

Nome | Descrição | Tipo | Operações
--- | --- | --- | ---
FN | Nome completo da instância de conferência | Cadeia | Nenhum
PC | Contagem de publicação total da instância de conferência | Int32 | Nenhum
