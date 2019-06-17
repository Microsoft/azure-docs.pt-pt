---
title: Regras para atribuição de nomes de entidades do Azure Data Factory | Documentos da Microsoft
description: Descreve as regras de nomenclatura para entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: daf0b1c12ab10230690a62eb5dc772417d8b92f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824240"
---
# <a name="azure-data-factory---naming-rules"></a>O Azure Data Factory - regras de nomenclatura
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [regras na fábrica de dados de nomenclatura](../naming-rules.md).

A tabela seguinte fornece regras de nomenclatura dos artefactos do Data Factory.

| Name | Exclusividade de nome | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Exclusivo em todo o Microsoft Azure. Nomes diferenciam maiúsculas de minúsculas, ou seja, `MyDF` e `mydf` fazer referência à fábrica de dados mesmo. |<ul><li>Cada fábrica de dados está associada a exatamente uma subscrição do Azure.</li><li>Nomes de objeto tem de começar com uma letra ou um número e só podem conter letras, números e o caráter de travessão (-).</li><li>Cada caráter de travessão (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contentor.</li><li>Nome pode ter entre 3 e 63 carateres.</li></ul> |
| Serviços ligados/tabelas/Pipelines |Exclusivo numa fábrica de dados. Nomes diferenciam maiúsculas de minúsculas. |<ul><li>Número máximo de carateres de um nome de tabela: 260.</li><li>Nomes de objeto tem de começar com uma letra, número ou um caráter de sublinhado (_).</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Grupo de Recursos |Exclusivo em todo o Microsoft Azure. Nomes diferenciam maiúsculas de minúsculas. |<ul><li>Número máximo de carateres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes carateres: "-", "_",","e"."</li></ul> |

