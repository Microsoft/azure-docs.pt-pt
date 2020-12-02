---
title: Regras para nomear entidades da Azure Data Factory - versão 1
description: Descreve regras de nomeação para entidades de Data Factory v1.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0ef997f54deafc56f1b3af603071c0e650be40d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495400"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Regras para nomear entidades da Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as regras de nomeação na Data Factory](../naming-rules.md).

A tabela a seguir fornece regras de nomeação para artefactos da Data Factory.

| Name | Nome Singular | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Único em toda a Microsoft Azure. Os nomes são insensíveis a casos, isto é, `MyDF` e `mydf` referem-se à mesma fábrica de dados. |<ul><li>Cada fábrica de dados está ligada a exatamente uma subscrição do Azure.</li><li>Os nomes dos objetos devem começar com uma letra ou um número, e podem conter apenas letras, números e o caráter (-) do traço.</li><li>Cada traço (-) caracteres deve ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contentores.</li><li>O nome pode ter 3-63 caracteres de comprimento.</li></ul> |
| Serviços/Tabelas/Oleodutos Ligados |Único com uma fábrica de dados. Os nomes são insensíveis a casos. |<ul><li>Número máximo de caracteres num nome de mesa: 260.</li><li>Os nomes dos objetos devem começar com uma letra, número ou um sublinhado (_).</li><li>Não são permitidos personagens seguintes: ".", "+", "?", "/", "<", ">", "*","%", "&", \\ ""</li></ul> |
| Grupo de Recursos |Único em toda a Microsoft Azure. Os nomes são insensíveis a casos. |<ul><li>Número máximo de caracteres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes caracteres: "-", "_", "" e "".</li></ul> |

