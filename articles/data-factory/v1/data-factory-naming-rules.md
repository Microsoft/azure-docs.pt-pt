---
title: Regras para nomear entidades de Azure Data Factory | Microsoft Docs
description: Descreve regras de nomenclatura para entidades de Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: fd1cdfac91a48d016857d06d5c9843fcdc4f5d9f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139211"
---
# <a name="azure-data-factory---naming-rules"></a>Regras de nomenclatura de Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [regras de nomenclatura no data Factory](../naming-rules.md).

A tabela a seguir fornece regras de nomenclatura para artefatos de Data Factory.

| Name | Exclusividade do nome | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Exclusivo entre Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas `MyDF` , `mydf` ou seja, e fazem referência ao mesmo data Factory. |<ul><li>Cada data factory está vinculado a exatamente uma assinatura do Azure.</li><li>Os nomes de objeto devem começar com uma letra ou um número e podem conter apenas letras, números e o caractere de traço (-).</li><li>Cada caractere de traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contêiner.</li><li>O nome pode ter de 3-63 a caracteres de comprimento.</li></ul> |
| Serviços/tabelas/pipelines vinculados |Exclusivo com em um data factory. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>Número máximo de caracteres em um nome de tabela: 260.</li><li>Os nomes de objeto devem começar com uma letra, um número ou um sublinhado (_).</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":",\\""</li></ul> |
| Grupo de Recursos |Exclusivo entre Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>Número máximo de caracteres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes caracteres: "-", "_", "," e "."</li></ul> |

