---
title: Mensagens de falha da máquina virtual SAS - Azure Marketplace
description: Muitas vezes se fazem perguntas ao trabalhar com assinaturas de acesso partilhado (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93126843"
---
# <a name="virtual-machine-sas-failure-messages"></a>Mensagens de falha da máquina virtual SAS

Seguem-se questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com as resoluções sugeridas.

| Problema | Mensagem de falha | Correção |
| --------- | ------------------- | ------- |
| *Falha na cópia de imagens* |  |  |
| "?" não é encontrado em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI utilizando ferramentas recomendadas. |
| parâmetros "st" e "se" não no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com os **valores adequados de Data de Início** e Data de **Fim.** |
| "sp=rl" não em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com permissões definidas como `Read` e `List` . |
| SAS URI tem espaços brancos em nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI para remover espaços brancos. |
| Erro de autorização SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Reveja e corrija o formato SAS URI. Regenerar se necessário. |
| Os parâmetros SAS URI "st" e "se" não têm especificação completa da data | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os parâmetros **sas** uri data de início e **data de fim** `st` (e `se` sublamentos) devem ter formato de data completa, tais como `11-02-2017T00:00:00Z` . As versões encurtadas são inválidas (alguns comandos no CLI Azure podem gerar valores encurtados por padrão). |
|  |  |  |

Para mais informações, consulte [a utilização de assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Gerar SAS URI](azure-vm-get-sas-uri.md)