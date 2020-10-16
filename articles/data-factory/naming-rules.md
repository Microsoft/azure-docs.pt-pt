---
title: Regras para nomear entidades da Azure Data Factory
description: Descreve regras de nomeação para entidades da Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 85e1e7fbea6033fde7c5f46b2ef566672bbe1fea
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105877"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomeação

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A tabela a seguir fornece regras de nomeação para artefactos da Data Factory.

| Nome | Nome Singular | Verificações de validação |
|:--- |:--- |:--- |
| Fábrica de dados | Único em toda a Microsoft Azure. Os nomes são insensíveis a casos, isto é, `MyDF` e `mydf` referem-se à mesma fábrica de dados. |<ul><li>Cada fábrica de dados está ligada a exatamente uma subscrição do Azure.</li><li>Os nomes dos objetos devem começar com uma letra ou um número, e podem conter apenas letras, números e o caráter (-) do traço.</li><li>Cada traço (-) caracteres deve ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contentores.</li><li>O nome pode ter 3-63 caracteres de comprimento.</li></ul> |
| Serviços ligados/Conjuntos de Dados/Oleodutos/Fluxos de Dados | Único com uma fábrica de dados. Os nomes são insensíveis a casos. |<ul><li>Os nomes dos objetos devem começar com uma carta.</li><li>Não são permitidos os seguintes caracteres: ".", "+", "" "/", "<", ">","*","%", "&", \\ ""</li><li>Os traços ("-") não são permitidos nos nomes de serviços ligados, fluxos de dados e conjuntos de dados.</li></ul>  |
| Tempo de execução de integração |Único com uma fábrica de dados. Os nomes são insensíveis a casos. |<ul><li>Integração tempo de execução O nome pode conter apenas letras, números e o caráter traço (-) do caráter.</li><li>O primeiro e último caracteres deve ser uma letra ou número. Cada traço (-) caracteres deve ser imediatamente precedido e seguido por uma letra ou um número.</li><li>Traços consecutivos não são permitidos em nome de execução de integração. </li></ul> |
| Transformações de fluxo de dados | Único dentro de um fluxo de dados. Os nomes são insensíveis ao caso. | <ul><li>Os nomes de transformação do fluxo de dados só podem conter letras e números</li><li>O primeiro caráter tem de ser uma letra. </li></ul> |
| Grupo de Recursos |Único em toda a Microsoft Azure. Os nomes são insensíveis a casos. | Para mais informações, consulte [as regras e restrições de nomeação Azure.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) |

## <a name="next-steps"></a>Passos seguintes
Saiba como criar fábricas de dados seguindo instruções passo a passo no [Quickstart: crie um](quickstart-create-data-factory-powershell.md) artigo de fábrica de dados. 
