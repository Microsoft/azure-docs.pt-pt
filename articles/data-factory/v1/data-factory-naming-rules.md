---
title: Regras para nomear entidades da Fábrica de Dados Azure
description: Descreve regras de nomeação para entidades da Fábrica de Dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931892"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomeação
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte as regras de [nomeação na Data Factory](../naming-rules.md).

A tabela seguinte prevê regras de nomeação para artefactos da Fábrica de Dados.

| Nome | Nome Singularidade | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Único em todo o Microsoft Azure. Os nomes são insensíveis `MyDF` `mydf` a casos, isto é, e referem-se à mesma fábrica de dados. |<ul><li>Cada fábrica de dados está ligada a exatamente uma subscrição Azure.</li><li>Os nomes dos objetos devem começar com uma letra ou um número, e podem conter apenas letras, números e o caráter do traço (-)</li><li>Cada caracter (-) deve ser imediatamente precedido e seguido de uma letra ou de um número. Traços consecutivos não são permitidos em nomes de contentores.</li><li>O nome pode ter 3-63 caracteres de comprimento.</li></ul> |
| Serviços/Tabelas/Oleodutos Ligados |Único com uma fábrica de dados. Os nomes são insensíveis aos casos. |<ul><li>Número máximo de caracteres em nome de mesa: 260.</li><li>Os nomes dos objetos devem começar com uma letra, número ou um sublinhado (_).</li><li>Seguir personagens não são permitidos: ".", "+", "?", "/", "<", ">", "%", "&","""""\\</li></ul> |
| Grupo de Recursos |Único em todo o Microsoft Azure. Os nomes são insensíveis aos casos. |<ul><li>Número máximo de caracteres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes caracteres: "-", "_", "e "".</li></ul> |

