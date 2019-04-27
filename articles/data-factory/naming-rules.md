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
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787007"
---
# <a name="azure-data-factory---naming-rules"></a>O Azure Data Factory - regras de nomenclatura
A tabela seguinte fornece regras de nomenclatura dos artefactos do Data Factory.

| Name | Exclusividade de nome | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Exclusivo em todo o Microsoft Azure. Nomes diferenciam maiúsculas de minúsculas, ou seja, `MyDF` e `mydf` fazer referência à fábrica de dados mesmo. |<ul><li>Cada fábrica de dados está associada a exatamente uma subscrição do Azure.</li><li>Nomes de objeto tem de começar com uma letra ou um número e só podem conter letras, números e o caráter de travessão (-).</li><li>Cada caráter de travessão (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contentor.</li><li>Nome pode ter entre 3 e 63 carateres.</li></ul> |
| Serviços ligados/conjuntos de dados/Pipelines |Exclusivo numa fábrica de dados. Nomes diferenciam maiúsculas de minúsculas. |<ul><li>Nomes de objeto tem de começar com uma letra, número ou um caráter de sublinhado (_).</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li><li>Traços ("-") não são permitidas nos nomes dos serviços ligados e dos conjuntos de dados apenas.</li></ul>  |
| Grupo de Recursos |Exclusivo em todo o Microsoft Azure. Nomes diferenciam maiúsculas de minúsculas. | Para mais informações, veja [restrições e regras de nomenclatura do Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Passos Seguintes
Saiba como criar fábricas de dados com as instruções passo a passo na [início rápido: criar uma fábrica de dados](quickstart-create-data-factory-powershell.md) artigo. 
