---
title: Regras para nomear entidades da Fábrica de Dados Azure
description: Descreve regras de nomeação para entidades da Fábrica de Dados.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837839"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomeação
A tabela seguinte prevê regras de nomeação para artefactos da Fábrica de Dados.

| Nome | Nome Singularidade | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Único em todo o Microsoft Azure. Os nomes são insensíveis `MyDF` `mydf` a casos, isto é, e referem-se à mesma fábrica de dados. |<ul><li>Cada fábrica de dados está ligada a exatamente uma subscrição Azure.</li><li>Os nomes dos objetos devem começar com uma letra ou um número, e podem conter apenas letras, números e o caráter do traço (-)</li><li>Cada caracter (-) deve ser imediatamente precedido e seguido de uma letra ou de um número. Traços consecutivos não são permitidos em nomes de contentores.</li><li>O nome pode ter 3-63 caracteres de comprimento.</li></ul> |
| Serviços Ligados/Conjuntos de Dados/Oleodutos |Único com uma fábrica de dados. Os nomes são insensíveis aos casos. |<ul><li>Os nomes dos objetos devem começar com uma letra, número ou um sublinhado (_).</li><li>Seguir personagens não são permitidos: ".", "+", "?", "/", "<", ">", "%", "&","""""\\</li><li>Os traços ("-") não são permitidos apenas nos nomes dos serviços ligados e dos conjuntos de dados.</li></ul>  |
| Grupo de Recursos |Único em todo o Microsoft Azure. Os nomes são insensíveis aos casos. | Para mais informações, consulte as regras e restrições de [nomeação do Azure.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) |

## <a name="next-steps"></a>Passos seguintes
Aprenda a criar fábricas de dados seguindo instruções passo a passo em [Quickstart: crie um](quickstart-create-data-factory-powershell.md) artigo de fábrica de dados. 
