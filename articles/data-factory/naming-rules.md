---
title: Regras para nomear entidades de Azure Data Factory
description: Descreve regras de nomenclatura para entidades de Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837839"
---
# <a name="azure-data-factory---naming-rules"></a>Regras de nomenclatura de Azure Data Factory
A tabela a seguir fornece regras de nomenclatura para artefatos de Data Factory.

| Nome | Exclusividade do nome | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Exclusivo entre Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas, ou seja, `MyDF` e `mydf` se referem ao mesmo data factory. |<ul><li>Cada data factory está vinculado a exatamente uma assinatura do Azure.</li><li>Os nomes de objeto devem começar com uma letra ou um número e podem conter apenas letras, números e o caractere de traço (-).</li><li>Cada caractere de traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contêiner.</li><li>O nome pode ter de 3-63 a caracteres de comprimento.</li></ul> |
| Serviços vinculados/conjuntos de itens/pipelines |Exclusivo com em um data factory. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>Os nomes de objeto devem começar com uma letra, um número ou um sublinhado (_).</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>Os traços ("-") não são permitidos nos nomes dos serviços vinculados e dos conjuntos de apenas.</li></ul>  |
| Grupo de Recursos |Exclusivo entre Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. | Para obter mais informações, consulte [restrições e regras de nomenclatura do Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Passos seguintes
Saiba como criar data factories seguindo as instruções passo a passo no [início rápido: criar um data Factory](quickstart-create-data-factory-powershell.md) artigo. 
